# The alloc crate

Initially added: ![Minimum Rust version: 1.36](https://img.shields.io/badge/Minimum%20Rust%20Version-1.36-brightgreen.svg)

Before 1.36.0, the standard library consisted of the crates `std`, `core`, and `proc_macro`.
The `core` crate provided core functionality such as `Iterator` and `Copy`
and could be used in `#![no_std]` environments since it did not impose any requirements.
Meanwhile, the `std` crate provided types like `Box<T>` and OS functionality
but required a global allocator and other OS capabilities in return.

Starting with Rust 1.36.0, the parts of `std` that depend on a global allocator, e.g. `Vec<T>`,
are now available in the `alloc` crate. The `std` crate then re-exports these parts.
While `#![no_std]` *binaries* using `alloc` still require nightly Rust,
`#![no_std]` *library* crates can use the `alloc` crate in stable Rust.
Meanwhile, normal binaries, without `#![no_std]`, can depend on such library crates.
We hope this will facilitate the development of a `#![no_std]` compatible ecosystem of libraries
prior to stabilizing support for `#![no_std]` binaries using `alloc`.

If you are the maintainer of a library that only relies on some allocation primitives to function,
consider making your library `#[no_std]` compatible by using the following at the top of your `lib.rs` file:

```rust,ignore
#![no_std]

extern crate alloc;

use alloc::vec::Vec;
```
