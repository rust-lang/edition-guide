# Rustfmt: Version sorting

## Summary

`rustfmt` utilizes a new sorting algorithm.

## Details

The [Rust Style Guide] includes [rules for sorting][sorting] that `rustfmt` applies in various contexts, such as on imports.

Previous versions of the Style Guide and Rustfmt generally used an "ASCIIbetical" based approach. In the 2024 Edition this is changed to use a version-sort like algorithm that compares Unicode characters lexicographically and provides better results in ASCII digit comparisons.

For example with a given (unsorted) input:

```rust,ignore
use std::num::{NonZeroU32, NonZeroU16, NonZeroU8, NonZeroU64};
use std::io::{Write, Read, stdout, self};
```

In the prior Editions, `rustfmt` would have produced:

```rust,ignore
use std::io::{self, stdout, Read, Write};
use std::num::{NonZeroU16, NonZeroU32, NonZeroU64, NonZeroU8};
```

In the 2024 Edition, `rustfmt` now produces:

```rust,ignore
use std::io::{self, Read, Write, stdout};
use std::num::{NonZeroU8, NonZeroU16, NonZeroU32, NonZeroU64};
```

[Rust Style Guide]: ../../style-guide/index.html
[sorting]: ../../style-guide/index.html#sorting

## Migration

The change can be applied automatically by running `cargo fmt` or `rustfmt` with the 2024 Edition. See the [Style edition] chapter for more information on migrating and how style editions work.

[Style edition]: rustfmt-style-edition.md
