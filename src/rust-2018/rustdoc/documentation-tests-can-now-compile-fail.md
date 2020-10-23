# Documentation tests can now compile-fail

![Minimum Rust version: 1.22](https://img.shields.io/badge/Minimum%20Rust%20Version-1.22-brightgreen.svg)

You can now create `compile-fail` tests in Rustdoc, like this:

```rust
/// ```compile_fail
/// let x = 5;
/// x += 2; // shouldn't compile!
/// ```
# fn foo() {}
```

Please note that these kinds of tests can be more fragile than others, as
additions to Rust may cause code to compile when it previously would not.
Consider the first release with `?`, for example: code using `?` would fail
to compile on Rust 1.21, but compile successfully on Rust 1.22, causing your
test suite to start failing.
