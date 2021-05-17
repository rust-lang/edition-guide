# Default Cargo feature resolver

## Summary

## Details

Since Rust 1.51.0, Cargo has opt-in support for a [new feature resolver][4]
which can be activated with `resolver = "2"` in `Cargo.toml`.

Starting in Rust 2021, this will be the default.
That is, writing `edition = "2021"` in `Cargo.toml` will imply `resolver = "2"`.

The new feature resolver no longer merges all requested features for
crates that are depended on in multiple ways.
See [the announcement of Rust 1.51][5] for details.

[4]: https://doc.rust-lang.org/cargo/reference/resolver.html#feature-resolver-version-2
[5]: https://blog.rust-lang.org/2021/03/25/Rust-1.51.0.html#cargos-new-feature-resolver