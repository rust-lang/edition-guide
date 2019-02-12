# 2018-Specific Changes

The following is a summary of changes that only apply to code compiled with
the 2018 edition compared to the 2015 edition.

## Rust
- [Non-lexical lifetimes]&nbsp;(future inclusion planned for 2015 edition)
- [At most once] `?` macro repetition operator.
- [Path changes]:
    - `use` declarations must begin with:
        - `crate` – refers to the current crate.
        - `self` – refers to the current module.
        - `super` – refers to the parent module.
        - An external crate name.
        - `::` – must be followed by an external crate name. This is required
          if an external crate has the same name as an in-scope item, to catch
          possible ambiguities with [uniform paths]&nbsp;(which is planned for
          inclusion soon [#55618]).
    - Paths in `pub(in path)` visibility modifiers must start with `crate`,
      `self`, or `super`.
- [Anonymous trait function parameters] are not allowed.
    - [Trait function parameters] may use any irrefutable pattern when the
      function has a body.
- [`dyn`] is a [strict keyword], in 2015 it is a [weak keyword].
- `async`, `await`, and `try` are [reserved keywords].
- The following lints are now deny by default:
    - [overflowing_literals]
    - [tyvar_behind_raw_pointer]

## Cargo
- If there is a target definition in a `Cargo.toml` manifest, it no longer
  automatically disables automatic discovery of other targets.
- Target paths of the form `src/{target_name}.rs` are no longer inferred for
  targets where the `path` field is not set.
- `cargo install` for the current directory is no longer allowed, you must
  specify `cargo install --path .` to install the current package.

[#55618]: https://github.com/rust-lang/rust/issues/55618
[Anonymous trait function parameters]: trait-system/no-anon-params.html
[At most once]: macros/at-most-once.html
[Non-lexical lifetimes]: ownership-and-lifetimes/non-lexical-lifetimes.html
[Path changes]: module-system/path-clarity.html
[Trait function parameters]: https://doc.rust-lang.org/stable/reference/items/traits.html#parameter-patterns
[`dyn`]: trait-system/dyn-trait-for-trait-objects.html
[overflowing_literals]: https://github.com/rust-lang/rfcs/blob/master/text/2438-deny-integer-literal-overflow-lint.md
[reserved keywords]: https://doc.rust-lang.org/reference/keywords.html#reserved-keywords
[strict keyword]: https://doc.rust-lang.org/reference/keywords.html#strict-keywords
[tyvar_behind_raw_pointer]: https://github.com/rust-lang/rust/issues/46906
[uniform paths]: module-system/path-clarity.html#uniform-paths
[weak keyword]: https://doc.rust-lang.org/reference/keywords.html#weak-keywords
