# An attribute for deprecation

![Minimum Rust version: 1.9](https://img.shields.io/badge/Minimum%20Rust%20Version-1.9-brightgreen.svg)

If you're writing a library, and you'd like to deprecate something, you can
use the `deprecated` attribute:

```rust
#[deprecated(
    since = "0.2.1",
    note = "Please use the bar function instead"
)]
pub fn foo() {
    // ...
}
```

This will give your users a warning if they use the deprecated functionality:

```rust
   Compiling playground v0.0.1 (file:///playground)
warning: use of deprecated item 'foo': Please use the bar function instead
  --> src/main.rs:10:5
   |
10 |     foo();
   |     ^^^
   |
   = note: #[warn(deprecated)] on by default

```

Both `since` and `note` are optional.