# Raw lifetimes

## Summary

- `'r#ident_or_keyword` is now allowed as a lifetime, which allows using keywords such as `'r#fn`.

## Details

Raw lifetimes are introduced in the 2021 edition to support the ability to migrate to newer editions that introduce new keywords. This is analogous to [raw identifiers] which provide the same functionality for identifiers. For example, the 2024 edition introduced the `gen` keyword. Since lifetimes cannot be keywords, this would cause code that use a lifetime `'gen` to fail to compile. Raw lifetimes allow the migration lint to modify those lifetimes to `'r#gen` which do allow keywords.

In editions prior to 2021, raw lifetimes are parsed as separate tokens. For example `'r#foo` is parsed as three tokens: `'r`, `#`, and `foo`.

[raw identifiers]: ../../reference/identifiers.html#raw-identifiers

## Migration

As a part of the 2021 edition a migration lint, [`rust_2021_prefixes_incompatible_syntax`], has been added in order to aid in automatic migration of Rust 2018 codebases to Rust 2021.

In order to migrate your code to be Rust 2021 Edition compatible, run:

```sh
cargo fix --edition
```

Should you want or need to manually migrate your code, migration is fairly straight-forward.

Let's say you have a macro that is defined like so:

```rust
macro_rules! my_macro {
    ($a:tt $b:tt $c:tt) => {};
}
```

In Rust 2015 and 2018 it's legal for this macro to be called like so with no space between the tokens:

```rust,ignore
my_macro!('r#foo);
```

In the 2021 edition, this is now parsed as a single token. In order to call this macro, you must add a space before the identifier like so:

```rust,ignore
my_macro!('r# foo);
```

[`rust_2021_prefixes_incompatible_syntax`]: ../../rustc/lints/listing/allowed-by-default.html#rust-2021-prefixes-incompatible-syntax
