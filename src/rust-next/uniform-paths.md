# Uniform Paths

![Minimum Rust version: 1.32](https://img.shields.io/badge/Minimum%20Rust%20Version-1.32-brightgreen.svg)

Rust 2018 added several improvements to the module system. We have one last
tweak landing in 1.32.0. Nicknamed "uniform paths", it permits previously
invalid import path statements to be resolved exactly the same way as
non-import paths. For example:

```rust,edition2018
enum Color {
    Red,
    Green,
    Blue,
}

use Color::*;
```

This code did not previously compile, as use statements had to start with
`super`, `self`, or `crate`. Now that the compiler supports uniform paths,
this code will work, and do what you probably expect: import the variants of
the Color enum defined above the `use` statement.
