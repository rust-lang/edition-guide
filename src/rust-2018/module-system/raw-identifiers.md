# Raw identifiers

![Minimum Rust version: 1.30](https://img.shields.io/badge/Minimum%20Rust%20Version-1.30-brightgreen.svg)

Rust, like many programming languages, has the concept of "keywords".
These identifiers mean something to the language, and so you cannot use them in
places like variable names, function names, and other places.
Raw identifiers let you use keywords where they would not normally be allowed.

For example, `match` is a keyword. If you try to compile this function:

```rust,ignore
fn match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}
```

You'll get this error:

```text
error: expected identifier, found keyword `match`
 --> src/main.rs:4:4
  |
4 | fn match(needle: &str, haystack: &str) -> bool {
  |    ^^^^^ expected identifier, found keyword
```

You can write this with a raw identifier:

```rust
fn r#match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}

fn main() {
    assert!(r#match("foo", "foobar"));
}
```

Note the `r#` prefix on both the function name as well as the call.

## Motivation

This feature is useful for a few reasons, but the primary motivation was
inter-edition situations. For example, `try` is not a keyword in the 2015
edition, but is in the 2018 edition. So if you have a library that is written
in Rust 2015 and has a `try` function, to call it in Rust 2018, you'll need
to use the raw identifier.

## New keywords

The new confirmed keywords in edition 2018 are:

### `async` and `await`

[RFC 2394]: https://github.com/rust-lang/rfcs/blob/master/text/2394-async_await.md#final-syntax-for-the-await-expression

Here, `async` is reserved for use in `async fn` as well as in `async ||` closures and
`async { .. }` blocks. Meanwhile, `await` is reserved to keep our options open
with respect to `await!(expr)` syntax. See [RFC 2394] for more details.

### `try`

[RFC 2388]: https://github.com/rust-lang/rfcs/pull/2388

The `do catch { .. }` blocks have been renamed to `try { .. }` and to support
that, the keyword `try` is reserved in edition 2018.
See [RFC 2388] for more details.
