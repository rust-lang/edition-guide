# SIMD for faster computing

![Minimum Rust version: 1.27](https://img.shields.io/badge/Minimum%20Rust%20Version-1.27-brightgreen.svg)

The basics of [SIMD](https://en.wikipedia.org/wiki/SIMD) are now available!
SIMD stands for “single instruction, multiple data.” Consider a function like
this:

```rust
pub fn foo(a: &[u8], b: &[u8], c: &mut [u8]) {
    for ((a, b), c) in a.iter().zip(b).zip(c) {
        *c = *a + *b;
    }
}
```

Here, we’re taking two slices, and adding the numbers together, placing the
result in a third slice. The simplest possible way to do this would be to do
exactly what the code does, and loop through each set of elements, add them
together, and store it in the result. However, compilers can often do better.
LLVM will usually “autovectorize” code like this, which is a fancy term for
“use SIMD.” Imagine that `a` and `b` were both 16 elements long. Each element
is a `u8`, and so that means that each slice would be 128 bits of data. Using
SIMD, we could put both `a` and `b` into 128 bit registers, add them together
in a *single* instruction, and then copy the resulting 128 bits into `c`.
That’d be much faster!

While stable Rust has always been able to take advantage of
autovectorization, sometimes, the compiler just isn’t smart enough to realize
that we can do something like this. Additionally, not every CPU has these
features, and so LLVM may not use them so your program can be used on a wide
variety of hardware. The `std::arch` module allows us to use these kinds of
instructions directly, which means we don’t need to rely on a smart compiler.
Additionally, it includes some features that allow us to choose a particular
implementation based on various criteria. For example:

```rust,ignore
#[cfg(all(any(target_arch = "x86", target_arch = "x86_64"),
      target_feature = "avx2"))]
fn foo() {
    #[cfg(target_arch = "x86")]
    use std::arch::x86::_mm256_add_epi64;
    #[cfg(target_arch = "x86_64")]
    use std::arch::x86_64::_mm256_add_epi64;

    unsafe {
        _mm256_add_epi64(...);
    }
}
```

Here, we use cfg flags to choose the correct version based on the machine
we’re targeting; on x86 we use that version, and on x86_64 we use its
version. We can also choose at runtime:

```rust,ignore
fn foo() {
    #[cfg(any(target_arch = "x86", target_arch = "x86_64"))]
    {
        if is_x86_feature_detected!("avx2") {
            return unsafe { foo_avx2() };
        }
    }

    foo_fallback();
}
```

Here, we have two versions of the function: one which uses AVX2, a specific
kind of SIMD feature that lets you do 256-bit operations. The
`is_x86_feature_detected!` macro will generate code that detects if your CPU
supports AVX2, and if so, calls the foo_avx2 function. If not, then we fall
back to a non-AVX implementation, foo_fallback. This means that our code will
run super fast on CPUs that support AVX2, but still work on ones that don’t,
albeit slower.

If all of this seems a bit low-level and fiddly, well, it is! `std::arch` is
specifically primitives for building these kinds of things. We hope to
eventually stabilize a `std::simd` module with higher-level stuff in the
future. But landing the basics now lets the ecosystem experiment with higher
level libraries starting today. For example, check out the
[faster](https://github.com/AdamNiederer/faster) crate. Here’s a code snippet
with no SIMD:

```rust,ignore
let lots_of_3s = (&[-123.456f32; 128][..]).iter()
    .map(|v| {
        9.0 * v.abs().sqrt().sqrt().recip().ceil().sqrt() - 4.0 - 2.0
    })
    .collect::<Vec<f32>>();
```

To use SIMD with this code via faster, you’d change it to this:

```rust,ignore
let lots_of_3s = (&[-123.456f32; 128][..]).simd_iter()
    .simd_map(f32s(0.0), |v| {
        f32s(9.0) * v.abs().sqrt().rsqrt().ceil().sqrt() - f32s(4.0) - f32s(2.0)
    })
    .scalar_collect();
```

It looks almost the same: `simd_iter` instead of `iter`, `simd_map` instead of `map`,
`f32s(2.0)` instead of `2.0`. But you get a SIMD-ified version generated for you.

Beyond that, you may never write any of this yourself, but as always, the
libraries you depend on may. For example, the regex crate contains these SIMD
speedups without you needing to do anything at all!
