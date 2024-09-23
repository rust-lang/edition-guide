# Rustfmt: raw identifier sorting

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".

More information may be found in <https://github.com/rust-lang/rust/issues/124764>.

## Summary

`rustfmt` now properly sorts [raw identifiers].

[raw identifiers]: https://doc.rust-lang.org/rust-by-example/compatibility/raw_identifiers.html

## Details

The [Rust Style Guide] includes [rules for sorting][sorting] that `rustfmt` applies in various contexts, such as on imports.

Prior to the 2024 Edition, when sorting rustfmt would use the leading `r#` token instead of the ident which led to specious results.

For example:

```rust
use websocket::client::ClientBuilder;
use websocket::r#async::futures::Stream;
use websocket::result::WebSocketError;
```

Which is now corrected in the 2024 Edition:

```rust
use websocket::r#async::futures::Stream;
use websocket::client::ClientBuilder;
use websocket::result::WebSocketError;
```

[Rust Style Guide]: https://doc.rust-lang.org/nightly/style-guide/index.html
[sorting]: https://doc.rust-lang.org/stable/style-guide/index.html?highlight=sort#sorting

## Migration

The change can be applied automatically by running `cargo fmt` or `rustfmt` with the 2024 Edition.

With a Cargo.toml file that has `edition` set to `2024`:

```sh
cargo fmt
```

Or by running `rustfmt` directly:

```sh
rustfmt foo.rs --style-edition 2024
```
