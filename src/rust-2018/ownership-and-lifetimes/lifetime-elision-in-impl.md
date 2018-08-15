# Lifetime elision in impl

![Minimum Rust version: nightly](https://img.shields.io/badge/Minimum%20Rust%20Version-nightly-red.svg)

When writing an `impl`, you can mention lifetimes without them being bound in
the argument list.

In Rust 2015:

```rust,ignore
impl<'a> Iterator for MyIter<'a> { ... }
impl<'a, 'b> SomeTrait<'a> for SomeType<'a, 'b> { ... }
```

In Rust 2018:

```rust,ignore
impl Iterator for MyIter<'iter> { ... }
impl SomeTrait<'tcx> for SomeType<'tcx, 'gcx> { ... }
```