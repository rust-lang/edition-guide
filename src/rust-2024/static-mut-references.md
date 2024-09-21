# Disallow references to static mut

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".
More information may be found in the tracking issue at <https://github.com/rust-lang/rust/issues/123758>.

## Summary

- The [`static_mut_refs`] lint level is now `deny` by default.
  This checks for taking a shared or mutable reference to a `static mut`.

[`static_mut_refs`]: ../../rustc/lints/listing/warn-by-default.html#static-mut-refs

## Details

The [`static_mut_refs`] lint detects taking a reference to a [`static mut`]. In the 2024 Edition, this lint is now `deny` by default to emphasize that you should avoid making these references.

<!-- edition2024 -->
```rust
static mut X: i32 = 23;
static mut Y: i32 = 24;

unsafe {
    let y = &X;             // ERROR: shared reference to mutable static
    let ref x = X;          // ERROR: shared reference to mutable static
    let (x, y) = (&X, &Y);  // ERROR: shared reference to mutable static
}
```

Merely taking such a reference in violation of Rust's mutability XOR aliasing requirement has always been *instantaneous* [undefined behavior], **even if the reference is never read from or written to**.  Furthermore, upholding mutability XOR aliasing for a `static mut` requires *reasoning about your code globally*, which can be particularly difficult in the face of reentrancy and/or multithreading.

Note that there are some cases where implicit references are automatically created without a visible `&` operator. For example, these situations will also trigger the lint:

<!-- edition2024 -->
```rust
static mut NUMS: &[u8; 3] = &[0, 1, 2];

unsafe {
    println!("{NUMS:?}");   // ERROR: shared reference to mutable static
    let n = NUMS.len();     // ERROR: shared reference to mutable static
}
```

## Alternatives

Wherever possible, it is **strongly recommended** to use instead an *immutable* `static` of a type that provides *interior mutability* behind some *locally-reasoned abstraction* (which greatly reduces the complexity of ensuring that Rust's mutability XOR aliasing requirement is upheld).

In situations where no locally-reasoned abstraction is possible and you are therefore compelled still to reason globally about accesses to your `static` variable, you must now use raw pointers such as can be obtained via the [`&raw const` or `&raw mut` operators][raw].  By first obtaining a raw pointer rather than directly taking a reference, (the safety requirements of) accesses through that pointer will be more familiar to `unsafe` developers and can be deferred until/limited to smaller regions of code.

[Undefined Behavior]: ../../reference/behavior-considered-undefined.html
[`static mut`]: ../../reference/items/static-items.html#mutable-statics
[`addr_of_mut!`]: https://docs.rust-lang.org/core/ptr/macro.addr_of_mut.html
[raw]: ../../reference/expressions/operator-expr.html#raw-borrow-operators

## Migration

There is no automatic migration to fix these references to `static mut`. To avoid undefined behavior you must rewrite your code to use a different approach as recommended in the [Alternatives](#alternatives) section.
