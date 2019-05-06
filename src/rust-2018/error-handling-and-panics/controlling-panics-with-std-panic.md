# Controlling panics with `std::panic`

![Minimum Rust version: 1.9](https://img.shields.io/badge/Minimum%20Rust%20Version-1.9-brightgreen.svg)

There is a `std::panic` module, which includes methods for halting the
unwinding process started by a panic:

```rust
use std::panic;

let result = panic::catch_unwind(|| {
    println!("hello!");
});
assert!(result.is_ok());

let result = panic::catch_unwind(|| {
    panic!("oh no!");
});
assert!(result.is_err());
```

In general, Rust distinguishes between two ways that an operation can fail:

- Due to an *expected problem*, like a file not being found.
- Due to an *unexpected problem*, like an index being out of bounds for an array.

Expected problems usually arise from conditions that are outside of your
control; robust code should be prepared for anything its environment might throw
at it. In Rust, expected problems are handled via [the `Result` type][result],
which allows a function to return information about the problem to its caller,
which can then handle the error in a fine-grained way.

[result]: http://doc.rust-lang.org/std/result/index.html

Unexpected problems are *bugs*: they arise due to a contract or assertion being
violated. Since they are unexpected, it doesn't make sense to handle them in a
fine-grained way. Instead, Rust employs a "fail fast" approach by *panicking*,
which by default unwinds the stack (running destructors but no other code) of
the thread which discovered the error. Other threads continue running, but will
discover the panic any time they try to communicate with the panicked thread
(whether through channels or shared memory). Panics thus abort execution up to
some "isolation boundary", with code on the other side of the boundary still
able to run, and perhaps to "recover" from the panic in some very coarse-grained
way. A server, for example, does not necessarily need to go down just because of
an assertion failure in one of its threads.

It's also worth noting that programs may choose to *abort* instead of unwind,
and so catching panics may not work. If your code relies on `catch_unwind`, you
should add this to your Cargo.toml:

```toml
[profile.dev]
panic = "unwind"

[profile.release]
panic = "unwind"
```

If any of your users choose to abort, they'll get a compile-time failure.

The `catch_unwind` API offers a way to introduce new isolation boundaries
*within a thread*. There are a couple of key motivating examples:

* Embedding Rust in other languages
* Abstractions that manage threads
* Test frameworks, because tests may panic and you don't want that to kill the test runner

For the first case, unwinding across a language boundary is undefined behavior,
and often leads to segfaults in practice. Allowing panics to be caught means
that you can safely expose Rust code via a C API, and translate unwinding into
an error on the C side.

For the second case, consider a threadpool library. If a thread in the pool
panics, you generally don't want to kill the thread itself, but rather catch the
panic and communicate it to the client of the pool. The `catch_unwind` API is
paired with `resume_unwind`, which can then be used to restart the panicking
process on the client of the pool, where it belongs.

In both cases, you're introducing a new isolation boundary within a thread, and
then translating the panic into some other form of error elsewhere.
