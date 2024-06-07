# Unsafe functions

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".
More information may be found in the tracking issue at <https://github.com/rust-lang/rust/issues/124866>.

## Summary

- The following functions are now marked [`unsafe`]:
    - [`std::env::set_var`]
    - [`std::env::remove_var`]

[`unsafe`]: ../../reference/unsafe-keyword.html#unsafe-functions-unsafe-fn
[`std::env::set_var`]: ../../std/env/fn.set_var.html
[`std::env::remove_var`]: ../../std/env/fn.remove_var.html

## Details

It can be unsound to call [`std::env::set_var`] or [`std::env::remove_var`] in a multi-threaded program due to safety limitations of the way the process environment is handled on some platforms. The standard library originally defined these as safe functions, but it was later determined that was not correct.

It is important to ensure that these functions are not called when any other thread might be running. See the [Safety] section of the function documentation for more details.

Ordinarily it would be a backwards-incompatible change to add `unsafe` to these functions. To address that problem, they are marked as `unsafe` only in the 2024 Edition.

[Safety]: ../../std/env/fn.set_var.html#safety

## Migration

To make your code compile in both the 2021 and 2024 editions, you will need to make sure that `set_var` and `remove_var` are called only from within `unsafe` blocks.

**⚠ Caution**: It is important that you manually inspect the calls to `set_var` and `remove_var` and possibly rewrite your code to satisfy the preconditions of those functions. In particular, they should not be called if there might be multiple threads running. You may need to elect to use a different mechanism other than environment variables to manage your use case.

The [`deprecated_safe_2024`] lint will automatically modify any use of `set_var` or `remove_var` to be wrapped in an `unsafe` block so that it can compile on both editions. This lint is part of the `rust-2024-compatibility` lint group, which will automatically be applied when running `cargo fix --edition`. To migrate your code to be Rust 2024 Edition compatible, run:

```sh
cargo fix --edition
```

For example, this will change:

```rust
fn main() {
    std::env::set_var("FOO", "123");
}
```

to be:

```rust
fn main() {
    unsafe { std::env::set_var("FOO", "123") };
}
```

Just beware that this automatic migration will not be able to verify that these functions are being used correctly. It is still your responsibility to manually review their usage.

Alternatively, you can manually enable the lint to find places these functions are called:

```rust
// Add this to the root of your crate to do a manual migration.
#![warn(deprecated_safe_2024)]
```

[`deprecated_safe_2024`]: ../../rustc/lints/listing/allowed-by-default.html#deprecated-safe
