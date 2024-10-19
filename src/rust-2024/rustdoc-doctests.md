# Rustdoc combined tests

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".
More information may be found in the tracking issue at <https://github.com/rust-lang/rust/issues/124853>.

## Summary

- [Doctests] are now combined into a single binary which should result in a significant performance improvement.

## Details

Prior the the 2024 Edition, rustdoc's "test" mode would compile each code block in your documentation as a separate executable. Although this was relatively simple to implement, it resulted in a significant performance burden when there were a large number of documentation tests. Starting with the 2024 Edition, rustdoc will attempt to combine documentation tests into a single binary, significantly reducing the overhead for compiling doctests.

```rust
/// Adds two numbers
///
/// ```
/// assert_eq!(add(1, 1), 2);
/// ```
pub fn add(left: u64, right: u64) -> u64 {
    left + right
}

/// Subtracts two numbers
///
/// ```
/// assert_eq!(subtract(2, 1), 1);
/// ```
pub fn subtract(left: u64, right: u64) -> u64 {
    left - right
}
```

In this example, the two doctests will now be compiled into a single executable. Rustdoc will essentially place each example in a separate function within a single binary. The tests still run in independent processes as they did before, so any global state (like global statics) should still continue to work correctly.[^implementation]

This change is only available in the 2024 Edition to avoid potential incompatibilities with existing doctests which may not work in a combined executable. However, these incompatibilities are expected to be extremely rare.

[doctests]: ../../rustdoc/write-documentation/documentation-tests.html
[libtest harness]: ../../rustc/tests/index.html

[^implementation]: For more information on the details of how this work, see ["Doctests - How were they improved?"](https://blog.guillaume-gomez.fr/articles/2024-08-17+Doctests+-+How+were+they+improved%3F).

### `standalone_crate` tag

In some situations it is not possible for rustdoc to combine examples into a single executable. Rustdoc will attempt to automatically detect if this is not possible. For example, a test will not be combined with others if it:

* Uses the [`compile_fail`][tags] tag, which indicates that the example should fail to compile.
* Uses an [`edition`][tags] tag, which indicates the edition of the example.[^edition-tag]
* Uses global attributes, like the [`global_allocator`] attribute, which could potentially interfere with other tests.
* Defines any crate-wide attributes (like `#![feature(...)]`).
* Defines a macro that uses `$crate`, because the `$crate` path will not work correctly.

However, rustdoc is not able to automatically determine *all* situations where an example cannot be combined with other examples. In these situations, you can add the `standalone_crate` language tag to indicate that the example should be built as a separate executable. For example:

```rust
//! ```
//! let location = std::panic::Location::caller();
//! assert_eq!(location.line(), 5);
//! ```
```

This is sensitive to the code structure of how the example is compiled and won't work with the "combined" approach because the line numbers will shift depending on how the doctests are combined. In these situations, you can add the `standalone_crate` tag to force the example to be built separately just as it was in previous editions. E.g.:

```rust
//! ```standalone_crate
//! let location = std::panic::Location::caller();
//! assert_eq!(location.line(), 5);
//! ```
```

[tags]: ../../rustdoc/write-documentation/documentation-tests.html#attributes
[`global_allocator`]: ../../std/alloc/trait.GlobalAlloc.html

[^edition-tag]: Note that rustdoc will only combine tests if the entire crate is Edition 2024 or greater. Using the `edition2024` tag in older editions will not result in those tests being combined.

## Migration

There is no automatic migration to determine which doctests need to be annotated with the `standalone_crate` tag. It's very unlikely that any given doctest will not work correctly when migrated. We suggest that you update your crate to the 2024 Edition and then run your documentation tests and see if any fail. If one does, you will need to analyze whether it can be rewritten to be compatible with the combined approach, or alternatively, add the `standalone_crate` tag to retain the previous behavior.

Some things to watch out for and avoid are:

- Checking the values of [`std::panic::Location`](https://doc.rust-lang.org/std/panic/struct.Location.html) or things that make use of `Location`. The location of the code is now different since multiple tests are now located in the same test crate.
- Checking the value of [`std::any::type_name`](https://doc.rust-lang.org/std/any/fn.type_name.html), which now has a different module path.
