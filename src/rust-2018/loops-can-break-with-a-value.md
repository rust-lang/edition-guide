# `loop`s can break with a value

![Minimum Rust version: 1.19](https://img.shields.io/badge/Minimum%20Rust%20Version-1.19-brightgreen.svg)

`loop`s can now break with a value:

```rust
// old code
let x;

loop {
    x = 7;
    break;
}

// new code
let x = loop { break 7; };
```

Rust has traditionally positioned itself as an “expression oriented
language”, that is, most things are expressions that evaluate to a value,
rather than statements. `loop` stuck out as strange in this way, as it was
previously a statement.

For now, this only applies to `loop`, and not things like `while` or `for`.
It's not clear yet, but we may add this to those in the future.