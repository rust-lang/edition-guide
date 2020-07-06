# No more FnBox

![Minimum Rust version: 1.35](https://img.shields.io/badge/Minimum%20Rust%20Version-1.35-brightgreen.svg)

The book used to have this code in Chapter 20, section 2:

```rust
trait FnBox {
    fn call_box(self: Box<Self>);
}

impl<F: FnOnce()> FnBox for F {
    fn call_box(self: Box<F>) {
        (*self)()
    }
}

type Job = Box<dyn FnBox + Send + 'static>;
```

Here, we define a new trait called `FnBox`, and then implement it for all
`FnOnce` closures. All the implementation does is call the closure. These
sorts of hacks were needed because a `Box<dyn FnOnce>` didn't implement
`FnOnce`. This was true for all three posibilities:

* `Box<dyn Fn>` and `Fn`
* `Box<dyn FnMut>` and `FnMut`
* `Box<dyn FnOnce>` and `FnOnce`

However, as of Rust 1.35, these traits are implemented for these types,
and so the `FnBox` trick is no longer required. In the latest version of
the book, the `Job` type looks like this:

```rust
type Job = Box<dyn FnOnce() + Send + 'static>;
```

No need for all that other code.
