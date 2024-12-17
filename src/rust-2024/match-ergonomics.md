# Match ergonomics reservations

## Summary

- Writing `mut`, `ref`, or `ref mut` on a binding is only allowed within a pattern when the pattern leading up to that binding is fully explicit (i.e. when it does not use match ergonomics).
  - Put differently, when the default binding mode is not `move`, writing `mut`, `ref`, or `ref mut` on a binding is an error.
- Reference patterns (`&` or `&mut`) are only allowed within the fully-explicit prefix of a pattern.
  - Put differently, reference patterns can only match against references in the scrutinee when the default binding mode is `move`.

## Details

### Background

Within `match`, `let`, and other constructs, we match a *pattern* against a *scrutinee*.  E.g.:

```rust
let &[&mut [ref x]] = &[&mut [()]]; // x: &()
//  ~~~~~~~~~~~~~~~   ~~~~~~~~~~~~
//      Pattern        Scrutinee
```

Such a pattern is called fully explicit because it does not elide (i.e. "skip" or "pass") any references within the scrutinee.  By contrast, this otherwise-equivalent pattern is not fully explicit:

```rust
let [[x]] = &[&mut [()]]; // x: &()
```

Patterns such as this are said to be using match ergonomics, originally introduced in [RFC 2005][].

Under match ergonomics, as we incrementally match a pattern against a scrutinee, we keep track of the default binding mode.  This mode can be one of `move`, `ref mut`, or `ref`, and it starts as `move`.  When we reach a binding, unless an explicit binding mode is provided, the default binding mode is used to decide the binding's type.

For example, here we provide an explicit binding mode, causing `x` to be bound by reference:

```rust
let ref x = (); // &()
```

By contrast:

```rust
let [x] = &[()]; // &()
```

Here, in the pattern, we pass the outer shared reference in the scrutinee.  This causes the default binding mode to switch from `move` to `ref`.  Since there is no explicit binding mode specified, the `ref` binding mode is used when binding `x`.

[RFC 2005]: https://github.com/rust-lang/rfcs/pull/2005

### `mut` restriction

In Rust 2021 and earlier editions, we allow this oddity:

```rust,edition2021
let [x, mut y] = &[(), ()]; // x: &(), mut y: ()
```

Here, because we pass the shared reference in the pattern, the default binding mode switches to `ref`.  But then, in these editions, writing `mut` on the binding resets the default binding mode to `move`.

This can be surprising as it's not intuitive that mutability should affect the type.

To leave space to fix this, in Rust 2024 it's an error to write `mut` on a binding when the default binding mode is not `move`.  That is, `mut` can only be written on a binding when the pattern (leading up to that binding) is fully explicit.

In Rust 2024, we can write the above example as:

```rust
let &[ref x, mut y] = &[(), ()]; // x: &(), mut y: ()
```

### `ref` / `ref mut` restriction

In Rust 2021 and earlier editions, we allow:

```rust,edition2021
let [ref x] = &[()]; // x: &()
```

Here, the `ref` explicit binding mode is redundant, as by passing the shared reference (i.e. not mentioning it in the pattern), the binding mode switches to `ref`.

To leave space for other language possibilities, we are disallowing explicit binding modes where they are redundant in Rust 2024.  We can rewrite the above example as simply:

```rust
let [x] = &[()]; // x: &()
```

### Reference patterns restriction

In Rust 2021 and earlier editions, we allow this oddity:

```rust,edition2021
let [&x, y] = &[&(), &()]; // x: (), y: &&()
```

Here, the `&` in the pattern both matches against the reference on `&()` and resets the default binding mode to `move`.  This can be surprising because the single `&` in the pattern causes a larger than expected change in the type by removing both layers of references.

To leave space to fix this, in Rust 2024 it's an error to write `&` or `&mut` in the pattern when the default binding mode is not `move`.  That is, `&` or `&mut` can only be written when the pattern (leading up to that point) is fully explicit.

In Rust 2024, we can write the above example as:

```rust
let &[&x, ref y] = &[&(), &()];
```

## Migration

The [`rust_2024_incompatible_pat`][] lint flags patterns that are not allowed in Rust 2024.  This lint is part of the `rust-2024-compatibility` lint group which is automatically applied when running `cargo fix --edition`.  This lint will automatically convert affected patterns to fully explicit patterns that work correctly in Rust 2024 and in all prior editions.

To migrate your code to be compatible with Rust 2024, run:

```sh
cargo fix --edition
```

For example, this will convert this...

```rust,edition2021
let [x, mut y] = &[(), ()];
let [ref x] = &[()];
let [&x, y] = &[&(), &()];
```

...into this:

```rust
let &[ref x, mut y] = &[(), ()];
let &[ref x] = &[()];
let &[&x, ref y] = &[&(), &()];
```

Alternatively, you can manually enable the lint to find patterns that will need to be migrated:

```rust
// Add this to the root of your crate to do a manual migration.
#![warn(rust_2024_incompatible_pat)]
```

[`rust_2024_incompatible_pat`]: ../../rustc/lints/listing/allowed-by-default.html#rust-2024-incompatible-pat
