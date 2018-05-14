# Raw identifiers

Rust, like many programming languages, has the concept of "keywords." These
identifiers mean something to the language, and so you cannot use them in
places like variable names, function names, and other places. Raw identifiers
let you use keywords where they would not normally be allowed.

For example, `match` is a keyword. if you try to compile this function:

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
#![feature(rust_2018_preview)]
#![feature(raw_identifiers)]

fn r#match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}

fn main() {
    assert!(r#match("foo", "foobar"));
}
```

Note the `r#` prefix on both the function name, as well as the call.

## More details

This feature is useful for a few reasons, but the primary motivation was
inter-edition situations. For example, `catch` is not a keyword in the 2015
edition, but is in the 2018 edition. So if you have a library that is written
in Rust 2015 and has a `catch` function, to call it in Rust 2018, you'll need
to use the raw identifier.