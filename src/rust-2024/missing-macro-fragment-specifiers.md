# Missing macro fragment specifiers

> **NOTE**: This was originally made a hard error only for the 2024 Edition. In Rust 1.89, released after Rust 2024, the lint was made into a hard error in all editions.

## Summary

- The `missing_fragment_specifier` lint is now a hard error.

## Details

The `missing_fragment_specifier` lint detected a situation when an **unused** pattern in a `macro_rules!` macro definition had a meta-variable (e.g. `$e`) that was not followed by a fragment specifier (e.g. `:expr`). This was made into a hard error in the 2024 Edition.

```rust,compile_fail
macro_rules! foo {
   () => {};
   ($name) => { }; // ERROR: missing fragment specifier
}

fn main() {
   foo!();
}
```

Calling the macro with arguments that would match a rule with a missing specifier (e.g., `foo!($name)`) was a hard error in all editions. However, simply defining a macro with missing fragment specifiers was not, though we did add a lint in Rust 1.17.

## Migration

To migrate your code to the 2024 Edition, remove the unused matcher rule from the macro.

There is no automatic migration for this change. We expect that this style of macro is extremely rare. The lint was a future-incompatibility lint since Rust 1.17, a deny-by-default lint since Rust 1.20, since Rust 1.82 it warned about dependencies using this pattern, and in Rust 1.89 it became a hard error.
