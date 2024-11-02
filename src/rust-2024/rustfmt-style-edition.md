# Rustfmt: Style Edition

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".

More information may be found in the tracking issue at <https://github.com/rust-lang/rust/issues/123799>.

## Summary

User can now control which style edition to use with `rustfmt`. 

## Details

The default formatting produced by Rustfmt is governed
by the rules in the [Rust Style Guide].

Additionally, Rustfmt has a formatting stability guarantee that aims
to avoid causing noisy formatting churn for users when updating a
Rust toolchain. This stability guarantee essentially means that a newer
version of Rustfmt cannot modify the _successfully formatted_ output
that was produced by a previous version of Rustfmt.

The combination of those two constraints had historically locked both
the Style Guide and the default formatting behavior in Rustfmt. This
impasse caused various challenges, such as preventing the ability to
iterate on style improvements, and requiring Rustfmt to maintain legacy
formatting quirks that were obviated long ago (e.g. nested tuple access).

[RFC 3338] resolved this impasse by establishing a mechanism for the
Rust Style Guide to be aligned to Rust's Edition model wherein the
Style Guide could evolve across Editions, and `rustfmt` would allow users
to specify their desired Edition of the Style Guide, referred to as the Style Edition. 

In the 2024 Edition, `rustfmt` now supports the ability for users to control
the Style Edition used for formatting. The 2024 Edition of the Style Guide also
includes enhancements to the Style Guide which are detailed elsewhere in this Edition Guide.

By default `rustfmt` will use the same Style Edition as the standard Rust Edition
used for parsing, but the Style Edition can also be overridden and configured separately. 

There are multiple ways to run `rustfmt` with the 2024 Style Edition:

With a `Cargo.toml` file that has `edition` set to `2024`, run:

```sh
cargo fmt
```

Or run `rustfmt` directly with `2024` for the edition to use the 2024 edition
for both parsing and the 2024 edition of the Style Guide:

```sh
rustfmt lib.rs --edition 2024
```

The style edition can also be set in a `rustfmt.toml` configuration file:
```toml
style_edition = "2024"
``` 

Which is then used when running `rustfmt` directly:
```sh
rustfmt lib.rs
```

Alternatively, the style edition can be specified directly from `rustfmt` options:

```sh
rustfmt lib.rs --style-edition 2024
```

[Rust Style Guide]: ../../style-guide/index.html
[RFC 3338]: https://rust-lang.github.io/rfcs/3338-style-evolution.html

## Migration

Running `cargo fmt` or `rustfmt` with the 2024 edition or style edition will
automatically migrate formatting over to the 2024 style edition formatting.

Projects who have contributors that may utilize their editor's format-on-save
features are also strongly encouraged to add a `.rustfmt.toml` file to their project
that includes the corresponding `style_edition` utilized within their project, or to
encourage their users to ensure their local editor format-on-save feature is
configured to use that same `style_edition`.

This is to ensure that the editor format-on-save output is consistent with the
output when `cargo fmt` is manually executed by the developer, or the project's CI
process (many editors will run `rustfmt` directly which by default uses the 2015
edition, whereas `cargo fmt` uses the edition specified in the `Cargo.toml` file)
