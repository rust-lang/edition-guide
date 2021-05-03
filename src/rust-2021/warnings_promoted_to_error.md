# Warnings promoted to errors

## Summary

## Details

Two existing lints are becoming hard errors in Rust 2021.
These lints will remain warnings in older editions.

* `bare-trait-objects`:
  The use of the `dyn` keyword to identify [trait objects](https://doc.rust-lang.org/book/ch17-02-trait-objects.html)
  will be mandatory in Rust 2021.

* `ellipsis-inclusive-range-patterns`:
  The [deprecated `...` syntax](https://doc.rust-lang.org/stable/reference/patterns.html#range-patterns)
  for inclusive range patterns is no longer accepted in Rust 2021.
  It has been superseded by `..=`, which is consistent with expressions.