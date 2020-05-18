# No jemalloc by default

![Minimum Rust version: 1.32](https://img.shields.io/badge/Minimum%20Rust%20Version-1.32-brightgreen.svg)

Long, long ago, Rust had a large, Erlang-like runtime. We chose to use
jemalloc instead of the system allocator, because it often improved
performance over the default system one. Over time, we shed more and more of
this runtime, and eventually almost all of it was removed, but jemalloc was
not. We didn't have a way to choose a custom allocator, and so we couldn't
really remove it without causing a regression for people who do need
jemalloc.

Also, saying that jemalloc was always the default is a bit UNIX-centric, as
it was only the default on some platforms. Notably, the MSVC target on
Windows has shipped the system allocator for a long time.

While jemalloc usually has great performance, that's not always the case.
Additionally, it adds about 300kb to every Rust binary. We've also had a host
of other issues with jemalloc in the past. It has also felt a little strange
that a systems language does not default to the system's allocator.

For all of these reasons, once Rust 1.28 shipped a way to choose a global
allocator, we started making plans to switch the default to the system
allocator, and allow you to use jemalloc via a crate. In Rust 1.32, we've
finally finished this work, and by default, you will get the system allocator
for your programs.

If you'd like to continue to use jemalloc, use the jemallocator crate. In
your Cargo.toml:

```toml
jemallocator = "0.1.8"
```

And in your crate root:

```rust,ignore
#[global_allocator]
static ALLOC: jemallocator::Jemalloc = jemallocator::Jemalloc;
```

That's it! If you don't need jemalloc, it's not forced upon you, and if you
do need it, it's a few lines of code away.
