# MaybeUninit

Initially added: ![Minimum Rust version: 1.36](https://img.shields.io/badge/Minimum%20Rust%20Version-1.36-brightgreen.svg)

In previous releases of Rust, the [`mem::uninitialized`] function has allowed
you to bypass Rust's initialization checks by pretending that you've
initialized a value at type `T` without doing anything. One of the main uses
of this function has been to lazily allocate arrays.

However, [`mem::uninitialized`] is an incredibly dangerous operation that
essentially cannot be used correctly as the Rust compiler assumes that values
are properly initialized. For example, calling `mem::uninitialized::<bool>()`
causes *instantaneous __undefined behavior__* as, from Rust's point of view,
the uninitialized bits are neither `0` (for `false`) nor `1` (for `true`) -
the only two allowed bit patterns for `bool`.

To remedy this situation, in Rust 1.36.0, the type [`MaybeUninit<T>`] has
been stabilized. The Rust compiler will understand that it should not assume
that a [`MaybeUninit<T>`] is a properly initialized `T`. Therefore, you can
do gradual initialization more safely and eventually use `.assume_init()`
once you are certain that `maybe_t: MaybeUninit<T>` contains an initialized
`T`.

As [`MaybeUninit<T>`] is the safer alternative, starting with Rust 1.39, the
function [`mem::uninitialized`] will be deprecated.

[`MaybeUninit<T>`]: https://doc.rust-lang.org/std/mem/union.MaybeUninit.html
[`mem::uninitialized`]: https://doc.rust-lang.org/std/mem/fn.uninitialized.html
