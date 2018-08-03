# Concurrency additions

While concurrency and parallelism has always been a strong suit of Rust,
it often requires a lot of boilerplate. In Rust 2018, two new keywords,
`async` and `await`, help you write code that appears sequential,
but executes concurrently.

Note that `async` and `await` will not be stable in the initial release of
Rust 2018. See the [tracking issue](https://github.com/rust-lang/rust/issues/50547)
for more information.