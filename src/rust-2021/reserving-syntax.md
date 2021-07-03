# Reserving syntax

## Summary

- `any_prefix#`, `any_prefix"..."`, and `any_prefix'...'` are now reserved
  syntax, and no longer tokenize.
- This is mostly relevant to macros. E.g. `quote!{ #a#b }` is no longer accepted.
- It doesn't treat keywords specially, so e.g. `match".." {}` is no longer accepted.
- Insert whitespace to avoid errors.

## Details

To make space for new syntax in the future,
we've decided to reserve syntax for prefixed identifiers and literals:
`prefix#identifier`, `prefix"string"`, `prefix'c'`, and `prefix#123`,
where `prefix` can be any identifier.
(Except those that already have a meaning, such as `b'…'` and `r"…"`.)
This provides syntax we can expand into in the future without requiring an
edition boundary. We may use that for temporary syntax until the next edition,
or for permanent syntax if appropriate.

Without an edition, this would be a breaking change, since macros can currently
accept syntax such as `hello"world"`, which they will see as two separate
tokens: `hello` and `"world"`. The (automatic) fix is simple though: just
insert a space: `hello "world"`. Likewise, `prefix#ident` should become
`prefix #ident`.

Other than turning these into a tokenization error,
[the RFC][10] does not attach a meaning to any prefix yet.
Assigning meaning to specific prefixes is left to future proposals,
which will now&mdash;thanks to reserving these prefixes&mdash;not be breaking changes.

Some new prefixes you might potentially see in the future (though we haven't
committed to any of them yet):

- `k#keyword` to allow writing keywords that don't exist yet in the current edition.
  For example, while `async` is not a keyword in edition 2015,
  this prefix would've allowed us to accept `k#async` in edition 2015
  without having to wait for edition 2018 to reserve `async` as a keyword.

- `f""` as a short-hand for a format string.
  For example, `f"hello {name}"` as a short-hand for the equivalent `format!()` invocation.

- `s""` for `String` literals.

- `c""` or `z""` for null-terminated C strings.

[10]: https://github.com/rust-lang/rfcs/pull/3101
