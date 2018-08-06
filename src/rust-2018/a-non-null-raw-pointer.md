# A non-null raw pointer

![Minimum Rust version: 1.25](https://img.shields.io/badge/Minimum%20Rust%20Version-1.25-brightgreen.svg)

There's a new pointer type in the standard library:
[`std::ptr::NonNull<T>](https://doc.rust-lang.org/std/ptr/struct.NonNull.html).
This type is similar to `*mut T`, but can't be null. This is so that enums
may use this forbidden value as a discriminant -- `Option<NonNull<T>>` has the
same size as `*mut T`. It is allowed to dangle if it isn't dereferenced.

If you’re building a data structure with unsafe code, `NonNull<T>` is often
the right type for you!