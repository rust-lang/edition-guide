# Reserving syntax

## Summary

## Details

To make space for some new syntax in the future,
we've decided to reserve syntax for prefixed identifiers and literals:
`prefix#identifier`, `prefix"string"`, `prefix'c'`, and `prefix#123`,
where `prefix` can be any identifier.
(Except those that already have a meaning, such as `b'…'` and `r"…"`.)

This is a breaking change, since macros can currently accept `hello"world"`,
which they will see as two separate tokens: `hello` and `"world"`.
The (automatic) fix is simple though. Just insert a space: `hello "world"`.

<!--
The original plan was to reserve only `k#` and `f""` for future use,
but reserving *all* possible prefixes did not have many downsides.
It leaves more space for new syntax which would otherwise need to wait for another edition.
-->

Other than turning these into a tokenization error,
[the RFC][10] does not attach a meaning to any prefix yet.
Assigning meaning to specific prefixes is left to future proposals,
which will&mdash;thanks to reserving these prefixes now&mdash;not be breaking changes.

These are some new prefixes you might see in the future:

- `f""` as a short-hand for a format string.
  For example, `f"hello {name}"` as a short-hand for the equivalent `format_args!()` invocation.

- `c""` or `z""` for null-terminated C strings.

- `k#keyword` to allow writing keywords that don't exist yet in the current edition.
  For example, while `async` is not a keyword in edition 2015,
  this prefix would've allowed us to accept `k#async` in edition 2015
  without having to wait for edition 2018 to reserve `async` as a keyword.

[10]: https://github.com/rust-lang/rfcs/pull/3101