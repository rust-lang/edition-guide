# TryFrom and TryInto

Initially added: ![Minimum Rust version: 1.34](https://img.shields.io/badge/Minimum%20Rust%20Version-1.34-brightgreen.svg)

The [`TryFrom`](../../std/convert/trait.TryFrom.html) and
[`TryInto`](../../std/convert/trait.TryInto.html) traits are like the
[`From`](../../std/convert/trait.From.html) and
[`Into`](../../std/convert/trait.Into.html) traits, except that they return a
result, meaning that they may fail.

For example, the `from_be_bytes` and related methods on integer types take
arrays, but data is often read in via slices. Converting between slices and
arrays is tedious to do manually. With the new traits, it can be done inline
with `.try_into()`:

```rust
use std::convert::TryInto;
# fn main() -> Result<(), Box<dyn std::error::Error>> {
# let slice = &[1, 2, 3, 4][..];
let num = u32::from_be_bytes(slice.try_into()?);
# Ok(())
# }
```
