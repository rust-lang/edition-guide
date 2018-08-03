# `async` and `await`

You can try out the `async` / `await` feature on a nightly compiler:

```rust,ignore
#![feature(async_await, futures_api)]

async fn foo(x: &usize) -> usize {
    x + 1
}
```

## More details

Note that `async` and `await` will not be stable in the initial release of
Rust 2018. See the [tracking issue](https://github.com/rust-lang/rust/issues/50547)
for more information.
