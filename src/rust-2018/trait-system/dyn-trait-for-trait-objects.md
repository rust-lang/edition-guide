# `dyn Trait` for trait objects

![Minimum Rust version: 1.27](https://img.shields.io/badge/Minimum%20Rust%20Version-1.27-brightgreen.svg)

The `dyn Trait` feature is the new syntax for using trait objects. In short:

* `Box<Trait>` becomes `Box<dyn Trait>`
* `&Trait` and `&mut Trait` become `&dyn Trait` and `&mut dyn Trait`

And so on. In code:

```rust
trait Trait {}

impl Trait for i32 {}

// old
fn function1() -> Box<Trait> {
# unimplemented!()
}

// new
fn function2() -> Box<dyn Trait> {
# unimplemented!()
}
```

That's it!

## More details

Using just the trait name for trait objects turned out to be a bad decision.
The current syntax is often ambiguous and confusing, even to veterans,
and favors a feature that is not more frequently used than its alternatives,
is sometimes slower, and often cannot be used at all when its alternatives can.

Furthermore, with `impl Trait` arriving, "`impl Trait` vs `dyn Trait`" is much
more symmetric, and therefore a bit nicer, than "`impl Trait` vs `Trait`".
`impl Trait` is explained [here](impl-trait-for-returning-complex-types-with-ease.md).

In the new edition, you should therefore prefer `dyn Trait` to just `Trait`
where you need a trait object.