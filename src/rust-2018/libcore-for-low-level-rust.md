# libcore for low-level Rust

![Minimum Rust version: 1.6](https://img.shields.io/badge/Minimum%20Rust%20Version-1.6-brightgreen.svg)

Rust’s standard library is two-tiered: there’s a small core library,
`libcore`, and the full standard library, `libstd`, that builds on top of it.
`libcore` is completely platform agnostic, and requires only a handful of
external symbols to be defined. Rust’s `libstd` builds on top of `libcore`,
adding support for things like memory allocation and I/O. Applications using
Rust in the embedded space, as well as those writing operating systems, often
eschew `libstd`, using only `libcore`.

As an additional note, while building *libraries* with `libcore` is supported
today, building full applications is not yet stable.

To use `libcore`, add this flag to your crate root:

```rust,ignore
#![no_std]
```

This will remove the standard library, and bring the `core` crate into your
namespace for use:

```rust,ignore
#![no_std]

use core::cell::Cell;
```

You can find `libcore`'s documentation [here](https://doc.rust-lang.org/core/).
