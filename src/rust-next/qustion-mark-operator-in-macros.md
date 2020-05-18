# ? operator in macros

![Minimum Rust version: 1.32](https://img.shields.io/badge/Minimum%20Rust%20Version-1.32-brightgreen.svg)

`macro_rules` macros can use `?`, like this:

```rust
macro_rules! bar {
    ($(a)?) => {}
}
```

The `?` will match zero or one repetitions of the pattern, similar to the
already-existing `*` for "zero or more" and `+` for "one or more."
