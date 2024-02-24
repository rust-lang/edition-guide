# Disallow references to static mut

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".

## Summary

- The [`static_mut_refs`] lint is now a hard error that cannot be disabled.
  This prevents taking a shared or mutable reference to a `static mut`.

[`static_mut_refs`]: ../../rustc/lints/listing/warn-by-default.html#static-mut-refs

## Details

Taking a reference to a [`static mut`] is no longer allowed:

<!-- edition2024,E0796 -->
```rust
static mut X: i32 = 23;
static mut Y: i32 = 24;

unsafe {
    let y = &X;             // ERROR: reference of mutable static
    let ref x = X;          // ERROR: reference of mutable static
    let (x, y) = (&X, &Y);  // ERROR: reference of mutable static
}
```

Shared or mutable references of mutable static are almost always a mistake and can lead to undefined behavior and various other problems in your code.
For example, another thread writing to the `static mut` will cause an aliasing violation and incur [Undefined Behavior].

<!-- TODO: Discuss possible alternatives. -->

[Undefined Behavior]: ../../reference/behavior-considered-undefined.html
[`static mut`]: ../../reference/items/static-items.html#mutable-statics

## Migration

🚧 The automatic migration for this has not yet been implemented.

<!-- TODO: Discuss alternatives around rewriting your code. -->
