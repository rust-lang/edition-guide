# Or patterns in macro-rules

## Summary

## Details

Starting in Rust 1.53.0, [patterns](https://doc.rust-lang.org/stable/reference/patterns.html)
are extended to support `|` nested anywhere in the pattern.
This enables you to write `Some(1 | 2)` instead of `Some(1) | Some(2)`.
Since this was simply not allowed before, this is not a breaking change.

However, this change also affects [`macro_rules` macros](https://doc.rust-lang.org/stable/reference/macros-by-example.html).
Such macros can accept patterns using the `:pat` fragment specifier.
Currently, `:pat` does *not* match `|`, since before Rust 1.53,
not all patterns (at all nested levels) could contain a `|`.
Macros that accept patterns like `A | B`,
such as [`matches!()`](https://doc.rust-lang.org/1.51.0/std/macro.matches.html)
use something like `$($_:pat)|+`.
Because we don't want to break any existing macros,
we did *not* change the meaning of `:pat` in Rust 1.53.0 to include `|`.

Instead, we will make that change as part of Rust 2021.
In the new edition, the `:pat` fragment specifier *will* match `A | B`.

Since there are times that one still wishes to match a single pattern
variant without `|`, the fragment specified `:pat_param` has been added
to retain the older behavior.
The name refers to its main use case: a pattern in a closure parameter.
