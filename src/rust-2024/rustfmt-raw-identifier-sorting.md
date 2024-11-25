# Rustfmt: Raw identifier sorting

## Summary

`rustfmt` now properly sorts [raw identifiers].

[raw identifiers]: ../../reference/identifiers.html#raw-identifiers

## Details

The [Rust Style Guide] includes [rules for sorting][sorting] that `rustfmt` applies in various contexts, such as on imports.

Prior to the 2024 Edition, when sorting rustfmt would use the leading `r#` token instead of the ident which led to unwanted results. For example:

```rust,ignore
use websocket::client::ClientBuilder;
use websocket::r#async::futures::Stream;
use websocket::result::WebSocketError;
```

In the 2024 Edition, `rustfmt` now produces:

```rust,ignore
use websocket::r#async::futures::Stream;
use websocket::client::ClientBuilder;
use websocket::result::WebSocketError;
```

[Rust Style Guide]: ../../style-guide/index.html
[sorting]: ../../style-guide/index.html#sorting

## Migration

The change can be applied automatically by running `cargo fmt` or `rustfmt` with the 2024 Edition. See the [Style edition] chapter for more information on migrating and how style editions work.

[Style edition]: rustfmt-style-edition.md
