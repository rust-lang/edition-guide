# 2018-Specific Changes

The following is a summary of changes that only apply to code compiled with
the 2018 edition compared to the 2015 edition.

- [Non-lexical lifetimes]&nbsp;(future inclusion planned for 2015 edition)
- [At most once] `?` macro repetition operator.
- [Path changes]:
    - Paths in `use` declarations work the same as other paths.
    - Paths starting with `::` must be followed with an external crate.
    - Paths in `pub(in path)` visibility modifiers must start with `crate`,
      `self`, or `super`.
- [Anonymous trait function parameters] are not allowed.
    - [Trait function parameters] may use any irrefutable pattern when the
      function has a body.
- [`dyn`] is a [strict keyword], in 2015 it is a [weak keyword].
- `async`, `await`, and `try` are [reserved keywords].
- The following lints are now deny by default:
    - [tyvar_behind_raw_pointer]

## Cargo
- If there is a target definition in a `Cargo.toml` manifest, it no longer
  automatically disables automatic discovery of other targets.
- Target paths of the form `src/{target_name}.rs` are no longer inferred for
  targets where the `path` field is not set.
- `cargo install` for the current directory is no longer allowed, you must
  specify `cargo install --path .` to install the current package.

[Anonymous trait function parameters]: rust-2018/trait-system/no-anon-params.html
[At most once]: rust-2018/macros/at-most-once.html
[Non-lexical lifetimes]: rust-2018/ownership-and-lifetimes/non-lexical-lifetimes.html
[Path changes]: rust-2018/module-system/path-clarity.html
[Trait function parameters]: https://doc.rust-lang.org/stable/reference/items/traits.html#parameter-patterns
[`dyn`]: rust-2018/trait-system/dyn-trait-for-trait-objects.html
[reserved keywords]: https://doc.rust-lang.org/reference/keywords.html#reserved-keywords
[strict keyword]: https://doc.rust-lang.org/reference/keywords.html#strict-keywords
[tyvar_behind_raw_pointer]: https://github.com/rust-lang/rust/issues/46906
[weak keyword]: https://doc.rust-lang.org/reference/keywords.html#weak-keywords
