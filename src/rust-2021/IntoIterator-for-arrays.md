# IntoIterator for arrays

## Summary

## Details

Until Rust 1.53, only *references* to arrays implement `IntoIterator`.
This means you can iterate over `&[1, 2, 3]` and `&mut [1, 2, 3]`,
but not over `[1, 2, 3]` directly.

```rust,ignore
for &e in &[1, 2, 3] {} // Ok :)

for e in [1, 2, 3] {} // Error :(
```

This has been [a long-standing issue][25], but the solution is not as simple as it seems.
Just [adding the trait implementation][20] would break existing code.
`array.into_iter()` already compiles today because that implicitly calls
`(&array).into_iter()` due to [how method call syntax works][22].
Adding the trait implementation would change the meaning.

Usually we categorize this type of breakage
(adding a trait implementation) 'minor' and acceptable.
But in this case there is too much code that would be broken by it.

It has been suggested many times to "only implement `IntoIterator` for arrays in Rust 2021".
However, this is simply not possible.
You can't have a trait implementation exist in one edition and not in another,
since editions can be mixed.

Instead, we decided to add the trait implementation in *all* editions (starting in Rust 1.53.0),
but add a small hack to avoid breakage until Rust 2021.
In Rust 2015 and 2018 code, the compiler will still resolve `array.into_iter()`
to `(&array).into_iter()` like before, as if the trait implementation does not exist.
This *only* applies to the `.into_iter()` method call syntax.
It does not affect any other syntax such as `for e in [1, 2, 3]`, `iter.zip([1, 2, 3])` or
`IntoIterator::into_iter([1, 2, 3])`.
Those will start to work in *all* editions.

While it's a shame that this required a small hack to avoid breakage,
we're very happy with how this solution keeps the difference between
the editions to an absolute minimum.
Since the hack is only present in the older editions,
there is no added complexity in the new edition.

[25]: https://github.com/rust-lang/rust/issues/25725
[20]: https://github.com/rust-lang/rust/pull/65819
[22]: https://doc.rust-lang.org/book/ch05-03-method-syntax.html#wheres-the---operator