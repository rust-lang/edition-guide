# literal macro matcher

![Minimum Rust version: 1.32](https://img.shields.io/badge/Minimum%20Rust%20Version-1.32-brightgreen.svg)

A new `literal` matcher was added for macros:

```rust
macro_rules! m {
    ($lt:literal) => {};
}

fn main() {
    m!("some string literal");
}
```

`literal` matches against literals of any type; string literals, numeric
literals, `char` literals.
