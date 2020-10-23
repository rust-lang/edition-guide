# 2018-Specific Changes

The following is a summary of changes that only apply to code compiled with
the 2018 edition compared to the 2015 edition.

- [Path changes]:
    - Paths in `use` declarations work the same as other paths.
    - Paths starting with `::` must be followed with an external crate.
    - Paths in `pub(in path)` visibility modifiers must start with `crate`,
      `self`, or `super`.
- [Anonymous trait function parameters] are not allowed.
    - [Trait function parameters] may use any irrefutable pattern when the
      function has a body.
- Keyword changes:
    - [`dyn`] is a [strict keyword][strict], in 2015 it is a [weak keyword].
    - `async` and `await` are [strict keywords][strict].
    - `try` is a [reserved keyword].
- The following lints are now a hard error that you cannot silence:
    - [tyvar_behind_raw_pointer]

## Cargo
- If there is a target definition in a `Cargo.toml` manifest, it no longer
  automatically disables automatic discovery of other targets.
- Target paths of the form `src/{target_name}.rs` are no longer inferred for
  targets where the `path` field is not set.
- `cargo install` for the current directory is no longer allowed, you must
  specify `cargo install --path .` to install the current package.

[Anonymous trait function parameters]: trait-system/no-anon-params.md
[Path changes]: module-system/path-clarity.md
[Trait function parameters]: https://doc.rust-lang.org/stable/reference/items/traits.html#parameter-patterns
[`dyn`]: trait-system/dyn-trait-for-trait-objects.md
[reserved keyword]: https://doc.rust-lang.org/reference/keywords.html#reserved-keywords
[strict]: https://doc.rust-lang.org/reference/keywords.html#strict-keywords
[tyvar_behind_raw_pointer]: https://github.com/rust-lang/rust/issues/46906
[weak keyword]: https://doc.rust-lang.org/reference/keywords.html#weak-keywords
