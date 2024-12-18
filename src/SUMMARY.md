# The Rust Edition Guide

[Introduction](introduction.md)

## What are editions?

- [What are editions?](editions/index.md)
  - [Creating a new project](editions/creating-a-new-project.md)
  - [Transitioning an existing project to a new edition](editions/transitioning-an-existing-project-to-a-new-edition.md)
  - [Advanced migrations](editions/advanced-migrations.md)

## Rust 2015

- [Rust 2015](rust-2015/index.md)

## Rust 2018

- [Rust 2018](rust-2018/index.md)
  - [Path and module system changes](rust-2018/path-changes.md)
  - [Anonymous trait function parameters deprecated](rust-2018/trait-fn-parameters.md)
  - [New keywords](rust-2018/new-keywords.md)
  - [Method dispatch for raw pointers to inference variables](rust-2018/tyvar-behind-raw-pointer.md)
  - [Cargo changes](rust-2018/cargo.md)

## Rust 2021

- [Rust 2021](rust-2021/index.md)
  - [Additions to the prelude](rust-2021/prelude.md)
  - [Default Cargo feature resolver](rust-2021/default-cargo-resolver.md)
  - [IntoIterator for arrays](rust-2021/IntoIterator-for-arrays.md)
  - [Disjoint capture in closures](rust-2021/disjoint-capture-in-closures.md)
  - [Panic macro consistency](rust-2021/panic-macro-consistency.md)
  - [Reserved syntax](rust-2021/reserved-syntax.md)
  - [Raw lifetimes](rust-2021/raw-lifetimes.md)
  - [Warnings promoted to errors](rust-2021/warnings-promoted-to-error.md)
  - [Or patterns in macro-rules](rust-2021/or-patterns-macro-rules.md)
  - [C-string literals](rust-2021/c-string-literals.md)

## Rust 2024

- [Rust 2024](rust-2024/index.md)
  - [Language](rust-2024/language.md)
    - [RPIT lifetime capture rules](rust-2024/rpit-lifetime-capture.md)
    - [`if let` temporary scope](rust-2024/temporary-if-let-scope.md)
    - [Tail expression temporary scope](rust-2024/temporary-tail-expr-scope.md)
    - [Match ergonomics reservations](rust-2024/match-ergonomics.md)
    - [Unsafe `extern` blocks](rust-2024/unsafe-extern.md)
    - [Unsafe attributes](rust-2024/unsafe-attributes.md)
    - [`unsafe_op_in_unsafe_fn` warning](rust-2024/unsafe-op-in-unsafe-fn.md)
    - [Disallow references to `static mut`](rust-2024/static-mut-references.md)
    - [Never type fallback change](rust-2024/never-type-fallback.md)
    - [Macro fragment specifiers](rust-2024/macro-fragment-specifiers.md)
    - [Missing macro fragment specifiers](rust-2024/missing-macro-fragment-specifiers.md)
    - [`gen` keyword](rust-2024/gen-keyword.md)
    - [Reserved syntax](rust-2024/reserved-syntax.md)
  - [Standard library](rust-2024/standard-library.md)
    - [Changes to the prelude](rust-2024/prelude.md)
    - [Add `IntoIterator` for `Box<[T]>`](rust-2024/intoiterator-box-slice.md)
    - [Newly unsafe functions](rust-2024/newly-unsafe-functions.md)
  - [Cargo](rust-2024/cargo.md)
    - [Cargo: Rust-version aware resolver](rust-2024/cargo-resolver.md)
    - [Cargo: Table and key name consistency](rust-2024/cargo-table-key-names.md)
    - [Cargo: Reject unused inherited default-features](rust-2024/cargo-inherited-default-features.md)
  - [Rustdoc](rust-2024/rustdoc.md)
    - [Rustdoc combined tests](rust-2024/rustdoc-doctests.md)
    - [Rustdoc nested `include!` change](rust-2024/rustdoc-nested-includes.md)
  - [Rustfmt](rust-2024/rustfmt.md)
    - [Rustfmt: Style edition](rust-2024/rustfmt-style-edition.md)
    - [Rustfmt: Assignment operator RHS indentation](rust-2024/rustfmt-assignment-operator-rhs-indentation.md)
    - [Rustfmt: Combine all delimited exprs as last argument](rust-2024/rustfmt-overflow-delimited-expr.md)
    - [Rustfmt: Single-line `where` clauses](rust-2024/rustfmt-single-line-where-clauses.md)
    - [Rustfmt: Raw identifier sorting](rust-2024/rustfmt-raw-identifier-sorting.md)
    - [Rustfmt: Version sorting](rust-2024/rustfmt-version-sorting.md)
