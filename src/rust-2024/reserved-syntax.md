# Reserved syntax

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".
More information may be found in the tracking issue at <https://github.com/rust-lang/rust/issues/123735>.

## Summary

- Unprefixed guarded strings of the form `#"foo"#` are reserved for future use.
- Two or more `#` characters are reserved for future use.

## Details

[RFC 3593] reserved syntax in the 2024 Edition for guarded string literals that do not have a prefix to make room for possible future language changes. The 2021 Edition [reserved syntax][2021] for guarded strings with a prefix, such as `ident##"foo"##`. The 2024 Edition extends that to also reserve strings without the `ident` prefix.

There are two reserved syntaxes:

- One or more `#` characters immediately followed by a [string literal].
- Two or more `#` characters in a row (not separated by whitespace).

This reservation is done across an edition boundary because of interactions with tokenization and macros. For example, consider this macro:

```rust
macro_rules! demo {
    ( $a:tt ) => { println!("one token") };
    ( $a:tt $b:tt $c:tt ) => { println!("three tokens") };
}

demo!("foo");
demo!(r#"foo"#);
demo!(#"foo"#);
demo!(###)
```

Prior to the 2024 Edition, this produces:

```text
one token
one token
three tokens
three tokens
```

Starting in the 2024 Edition, the `#"foo"#` line and the `###` line now generates a compile error because those forms are now reserved.

[2021]: ../rust-2021/reserved-syntax.md
[string literal]: ../../reference/tokens.html#string-literals
[RFC 3593]: https://rust-lang.github.io/rfcs/3593-unprefixed-guarded-strings.html

## Migration

The [`rust_2024_guarded_string_incompatible_syntax`] lint will identify any tokens that match the reserved syntax, and will suggest a modification to insert spaces where necessary to ensure the tokens continue to be parsed separately.

The lint is part of the `rust-2024-compatibility` lint group which is included in the automatic edition migration. In order to migrate your code to be Rust 2024 Edition compatible, run:

```sh
cargo fix --edition
```

Alternatively, you can manually enable the lint to find macro calls where you may need to update the tokens:

```rust
// Add this to the root of your crate to do a manual migration.
#![warn(rust_2024_guarded_string_incompatible_syntax)]
```

[`rust_2024_guarded_string_incompatible_syntax`]: ../../rustc/lints/listing/allowed-by-default.html#rust-2024-guarded-string-incompatible-syntax
