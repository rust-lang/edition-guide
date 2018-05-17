# `T: 'a` inference in structs

An annotation in the form of `T: 'a`, where `T` is either a type or another
lifetime, is called an *"outlives"* requirement. Note that *"outlives"* does
not imply that `'a: 'a` does not hold, it does hold.

One way in which edition 2018 helps you out in maintaining flow when writing
programs is by removing the need to explicitly annotate these `T: 'a` outlives
requirements in `struct` definitions. Instead, the requirements will be
inferred from the fields present in the definitions.

Consider the following `struct` definitions which you wrote in edition 2015:

```rust
struct Ref<'a, T: 'a> {
    field: &'a T
}

// or written with a `where` clause:

struct WhereRef<'a, T> where T: 'a {
  data: &'a T
}

// with nested references:

struct RefRef<'a, 'b: 'a, T: 'b> {
    field: &'a &'b T,
}

// using an associated type:

struct ItemRef<'a, T: Iterator>
where
    T::Item: 'a
{
    field: &'a T::Item
}
```

In edition 2018, since the requirements are inferred, you can instead write:

```rust,ignore
struct Ref<'a, T> {
    field: &'a T
}

struct WhereRef<'a, T> {
  data: &'a T
}

struct RefRef<'a, 'b, T> {
    field: &'a &'b T,
}

struct ItemRef<'a, T: Iterator> {
    field: &'a T::Item
}
```

If you prefer to be more explicit in some cases, that is still possible.

## More details

For more details, see [the tracking issue](https://github.com/rust-lang/rust/issues/44493)
and [the RFC](https://github.com/rust-lang/rfcs/pull/2093).
