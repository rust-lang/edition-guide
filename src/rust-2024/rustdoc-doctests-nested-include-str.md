# Rustdoc doctest nested `include_str!` change

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".
More information may be found at <https://github.com/rust-lang/rust/pull/132210>.

## Summary

When a doctest is included with `include_str!`, if that doctest itself
also uses `include!` / `include_str!` / `include_bytes!`, the path is
resolved relative to the Markdown file, rather than the Rust
source file.

## Details

Prior to the 2024 edition, adding documentation with
`#[doc=include_str!("path/file.md")]` didn't carry span information
into the doctests. As a result, if the Markdown file is in a different
directory than the source, any `include!`ed paths need to be relative
to the Rust file.

For example, consider a library crate with these files:

* Cargo.toml
* README.md
* src/
  * lib.rs
* examples/
  * data.bin

Let `lib.rs` contain this:

```rust,ignore
#![doc=include_str!("../README.md")]
```

And assume this `README.md` file:

````markdown
```
let _ = include_bytes!("../examples/data.bin");
//                      ^^^ notice this
```
````

Prior to the 2024 edition, the path in `README.md` needed to be
relative to the `lib.rs` file. In 2024 and later, it is now relative
to `README.md` iself.

## Migration

After migrating, you'll see the following error:

```text
error: couldn't read `../examples/data.bin`: No such file or directory (os error 2)
 --> src/../README.md:2:24
  |
2 | let _ = include_bytes!("../examples/data.bin");
  |         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  = note: this error originates in the macro `include_bytes` (in Nightly builds, run with -Z macro-backtrace for more info)
help: there is a file with the same name in a different directory
  |
2 | let _ = include_bytes!("examples/data.bin");
  |                        ~~~~~~~~~~~~~~~~~~~
```
