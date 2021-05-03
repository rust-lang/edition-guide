# `union` for an unsafe form of `enum`

![Minimum Rust version: 1.19](https://img.shields.io/badge/Minimum%20Rust%20Version-1.19-brightgreen.svg)

Rust now supports `unions`:

```rust
union MyUnion {
    f1: u32,
    f2: f32,
}
```

Unions are kind of like enums, but they are “untagged”. Enums have a “tag”
that stores which variant is the correct one at runtime; unions don't have
this tag.

Since we can interpret the data held in the union using the wrong variant and
Rust can’t check this for us, that means reading a union’s field is unsafe:

```rust
# union MyUnion {
#     f1: u32,
#     f2: f32,
# }
let mut u = MyUnion { f1: 1 };

u.f1 = 5;

let value = unsafe { u.f1 };
```

Pattern matching works too:

```rust
# union MyUnion {
#     f1: u32,
#     f2: f32,
# }
fn f(u: MyUnion) {
    unsafe {
        match u {
            MyUnion { f1: 10 } => { println!("ten"); }
            MyUnion { f2 } => { println!("{}", f2); }
        }
    }
}
```

When are unions useful? One major use-case is interoperability with C. C APIs
can (and depending on the area, often do) expose unions, and so this makes
writing API wrappers for those libraries significantly easier. Additionally,
unions also simplify Rust implementations of space-efficient or
cache-efficient structures relying on value representation, such as
machine-word-sized unions using the least-significant bits of aligned
pointers to distinguish cases.

There’s still more improvements to come. For now, unions can only include
`Copy` types and may not implement `Drop`. We expect to lift these
restrictions in the future.
