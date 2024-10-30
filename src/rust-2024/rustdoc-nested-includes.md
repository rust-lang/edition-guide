# Rustdoc nested `include!` change

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".
More information may be found in the tracking issue at <https://github.com/rust-lang/rust/issues/132230>.

## Summary

When a doctest is included with `include_str!`, if that doctest itself also uses `include!`, `include_str!`, or `include_bytes!`, the path is resolved relative to the Markdown file, rather than to the Rust source file.

## Details

Prior to the 2024 edition, adding documentation with `#[doc=include_str!("path/file.md")]` didn't carry span information into any doctests in that file. As a result, if the Markdown file was in a different directory than the source, any paths included had to be specified relative to the source file.

For example, consider a library crate with these files:

- `Cargo.toml`
- `README.md`
- `src/`
  - `lib.rs`
- `examples/`
  - `data.bin`

Let's say that `lib.rs` contains this:

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

Prior to the 2024 edition, the path in `README.md` needed to be relative to the `lib.rs` file. In 2024 and later, it is now relative to `README.md` itself, so we would update `README.md` to:

````markdown
```
let _ = include_bytes!("examples/data.bin");
```
````

## Migration

There is no automatic migration to convert the paths in affected doctests. If one of your doctests is affected, you'll see an error like this after migrating to the new edition when building your tests:

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

To migrate your doctests to Rust 2024, update any affected paths to be relative to the file containing the doctests.
