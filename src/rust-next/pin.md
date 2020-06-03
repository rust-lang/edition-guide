# Pinning

![Minimum Rust version: 1.33](https://img.shields.io/badge/Minimum%20Rust%20Version-1.33-brightgreen.svg)

Rust 1.33 introduced a new concept, implemented as two types: 

* [`Pin<P>`](https://doc.rust-lang.org/std/pin/struct.Pin.html), a wrapper
  around a kind of pointer which makes that pointer "pin" its value in place,
  preventing the value referenced by that pointer from being moved.
* [`Unpin`](https://doc.rust-lang.org/std/marker/trait.Unpin.html), types that
  are safe to be moved, even if they're pinned.

Most users will not interact with pinning directly, and so we won't explain
more here. For the details, see the [documentation for
`std::pin`](https://doc.rust-lang.org/std/pin/index.html).

What *is* useful to know about pinning is that it's a pre-requisite for
`async`/`await`. Folks who write async libraries may need to learn about
pinning, but folks using them generally shouldn't need to interact with this
feature at all.

