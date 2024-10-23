# Cargo: Rust-version aware resolver

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".

## Summary

- `edition = "2024"` implies `resolver = "3"` in `Cargo.toml` which enables a Rust-version aware dependency resolver.

## Details

Since Rust 1.84.0, Cargo has opt-in support for compatibility with
[`package.rust-version`] to be considered when selecting dependency versions
by setting [`resolver.incompatible-rust-version = "fallback"`] in `.cargo/config.toml`.

Starting in Rust 2024, this will be the default.
That is, writing `edition = "2024"` in `Cargo.toml` will imply `resolver = "3"`
which will imply [`resolver.incompatible-rust-version = "fallback"`].

The resolver is a global setting for a [workspace], and the setting is ignored in dependencies.
The setting is only honored for the top-level package of the workspace.
If you are using a [virtual workspace], you will still need to explicitly set the [`resolver` field]
in the `[workspace]` definition if you want to opt-in to the new resolver.

For more details on how Rust-version aware dependency resolution works, see [the Cargo book](../..//cargo/reference/resolver.html#rust-version).

[`package.rust-version`]: ../../cargo/reference/rust-version.html
[`resolver.incompatible-rust-version = "fallback"`]: ../../cargo/reference/config.html#resolverincompatible-rust-versions
[workspace]: ../../cargo/reference/workspaces.html
[virtual workspace]: ../../cargo/reference/workspaces.html#virtual-workspace
[`resolver` field]: ../../cargo/reference/resolver.html#resolver-versions

## Migration

There are no automated migration tools for updating for the new resolver.

We recommend projects
[verify against the latest dependencies in CI](../../cargo/guide/continuous-integration.html#verifying-latest-dependencies)
to catch bugs in dependencies as soon as possible.
