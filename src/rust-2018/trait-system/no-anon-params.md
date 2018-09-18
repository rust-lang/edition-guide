# No more anonymous trait parameters

![Minimum Rust version: beta](https://img.shields.io/badge/Minimum%20Rust%20Version-beta-orange.svg)

In accordance with RFC [#1685](https://github.com/rust-lang/rfcs/pull/1685),
parameters in trait method declarations are no longer allowed to be anonymous.

For example, in the 2015 edition, this was allowed:

```rust
trait Foo {
    fn foo(&self, u8);
}
```

In the 2018 edition, all parameters must be given an argument name  (even if it's just
`_`):

```rust
trait Foo {
    fn foo(&self, baz: u8);
}
```
