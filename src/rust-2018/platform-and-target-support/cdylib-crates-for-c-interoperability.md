# cdylib crates for C interoperability

![Minimum Rust version: 1.10](https://img.shields.io/badge/Minimum%20Rust%20Version-1.10-brightgreen.svg) for `rustc`

![Minimum Rust version: 1.11](https://img.shields.io/badge/Minimum%20Rust%20Version-1.11-brightgreen.svg) for `cargo`

If you're producing a library that you intend to be used from C (or another
language through a C FFI), there's no need for Rust to include Rust-specific
stuff in the final object code. For libraries like that, you'll want to use
the `cdylib` crate type in your `Cargo.toml`:

```toml
[lib]
crate-type = ["cdylib"]
```

This will produce a smaller binary, with no Rust-specific information inside
of it.
