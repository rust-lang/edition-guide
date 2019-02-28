# Lifetime elision in impl

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

When writing `impl` blocks, you can now elide lifetime annotations in some
situations.

Consider a trait like `MyIterator`:

```rust,ignore
trait MyIterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
}
```

In Rust 2015, if we wanted to implement this iterator for mutable references
to `Iterators`, we'd need to write this:

```rust,ignore
impl<'a, I: MyIterator> MyIterator for &'a mut I {
    type Item = I::Item;
    fn next(&mut self) -> Option<Self::Item> {
        (*self).next()
    }
}
```

Note all of the `'a` annotations. In Rust 2018, we can write this:

```rust,ignore
impl<I: MyIterator> MyIterator for &mut I {
    type Item = I::Item;
    fn next(&mut self) -> Option<Self::Item> {
        (*self).next()
    }
}
```

Similarly, lifetime annotations can appear due to a struct that contains
references:

```rust,ignore
struct SetOnDrop<'a, T> {
    borrow: &'a mut T,
    value: Option<T>,
}
```

In Rust 2015, to implement `Drop` on this struct, we'd write:

```rust,ignore
impl<'a, T> Drop for SetOnDrop<'a, T> {
    fn drop(&mut self) {
        if let Some(x) = self.value.take() {
            *self.borrow = x;
        }
    }
}
```

But in Rust 2018, we can combine elision with [the anonymous lifetime] and
write this instead.

```rust,ignore
impl<T> Drop for SetOnDrop<'_, T> {
    fn drop(&mut self) {
        if let Some(x) = self.value.take() {
            *self.borrow = x;
        }
    }
}
```

[the anonymous lifetime]: the-anonymous-lifetime.md