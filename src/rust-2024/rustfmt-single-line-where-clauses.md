# Rustfmt: Single-line where clauses

## Summary

When an associated type or method declaration has a single bound in the `where` clause, it can now sometimes be formatted on one line.

## Details

In general, the [Rust Style Guide](../../style-guide/index.html) states to wrap `where` clauses onto subsequent lines, with the keyword `where` on a line of its own and then the individual clauses indented on subsequent lines.

However, in the 2024 Edition, when writing an associated type declaration or a method declaration (with no body), a short `where` clause can appear on the same line.

This is particularly useful for generic associated types (GATs), which often need `Self: Sized` bounds.

In Rust 2021 and before, this would look like:

```rust
trait MyTrait {
    fn new(&self) -> Self
    where
        Self: Sized;

    type Item<'a>: Send
    where
        Self: 'a;
}
```

In the 2024 Edition, `rustfmt` now produces:

```rust
trait MyTrait {
    fn new(&self) -> Self where Self: Sized;

    type Item<'a>: Send where Self: 'a;
}
```

## Migration

The change can be applied automatically by running `cargo fmt` or `rustfmt` with the 2024 Edition. See the [Style edition] chapter for more information on migrating and how style editions work.

[Style edition]: rustfmt-style-edition.md
