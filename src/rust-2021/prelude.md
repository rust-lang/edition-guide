# Additions to the prelude

## Summary

- The `TryInto`, `TryFrom` and `FromIterator` traits are now part of the prelude.
- This might make calls to trait methods ambiguous which could make some code fail to compile.

## Details

The [prelude of the standard library](https://doc.rust-lang.org/stable/std/prelude/index.html)
is the module containing everything that is automatically imported in every module.
It contains commonly used items such as `Option`, `Vec`, `drop`, and `Clone`.

The Rust compiler prioritizes any manually imported items over those
from the prelude, to make sure additions to the prelude will not break any existing code.
For example, if you have a crate or module called `example` containing a `pub struct Option;`,
then `use example::*;` will make `Option` unambiguously refer to the one from `example`;
not the one from the standard library.

However, adding a _trait_ to the prelude can break existing code in a subtle way.
For example, a call to `x.try_into()` which comes from a `MyTryInto` trait might fail 
to compile if `std`'s `TryInto` is also imported, because the call to `try_into` is now 
ambiguous and could come from either trait. This is the reason we haven't added `TryInto` 
to the prelude yet, since there is a lot of code that would break this way.

As a solution, Rust 2021 will use a new prelude.
It's identical to the current one, except for three new additions:

- [`std::convert::TryInto`](https://doc.rust-lang.org/stable/std/convert/trait.TryInto.html)
- [`std::convert::TryFrom`](https://doc.rust-lang.org/stable/std/convert/trait.TryFrom.html)
- [`std::iter::FromIterator`](https://doc.rust-lang.org/stable/std/iter/trait.FromIterator.html)

The tracking issue [can be found here](https://github.com/rust-lang/rust/issues/85684).

## Migration to Rust 2021

As a part of the 2021 edition a migration lint, `rust_2021_prelude_collisions`, has been added in order to aid in automatic migration of Rust 2018 codebases to Rust 2021.

In order to have rustfix migrate your code to be Rust 2021 Edition compatible, run:

```
cargo fix --edition
```

The lint detects cases where functions or methods are called that have the same name as the methods defined in one of the new prelude traits. In some cases, it may rewrite your calls in various ways to ensure that you continue to call the same function you did before.

### Fully qualified calls to inherent methods

Many types define their own inherent methods with the name `from_iter` which shares the same name with `std::iter::FromIterator::from_iter`:

```rust
struct MyStruct {
  data: Vec<u32>
}

impl MyStruct {
  // This has the same name as `std::iter::FromIterator::from_iter`
  fn from_iter(x: impl Iterator<Item = u32>) -> Self {
    Self {
      data: x.collect()
    }
  }
}
```

In case that already use a fully qualified inherent method syntax (e.g., calls like `MyStruct::from_iter`), there is not ambiguity with calls to trait methods (e.g., `<MyStruct as FromIter>::from_iter`), because inherent methods take precedence over trait methods. Therefore, no migration is needed.

A migration is necessary when using "dot method" syntax where the method name is the same as the trait method name for a trait which is now
in scope. For example, a call like `my_struct.into_iter()` where `into_iter()` used to refer to an inherent method on `MyStruct` but now conflicts with
the trait method from `IntoIter`. To make these calls unambiguous, fully qualified inherent method syntax must be used:

```rust
// Before:
my_struct.into_iter();
// After:
MyStruct::into_iter(my_struct);
```

Note: this only impacts methods which take `&self` and `&mut self` but not `self`.

### Inherent methods on `dyn Trait` objects

Some users invoke methods on a `dyn Trait` value where the method name overlaps with a new prelude trait:

```rust
trait MyTrait {
  // This has the same name as `std::iter::IntoIterator::into_iter`
  fn into_iter(&self);
}

fn bar(f: &dyn MyTrait) {
  f.into_iter();
}
```

In these cases, the lint will sometimes rewrite to introduce additional dereferences or otherwise clarify the type of the method receiver. This ensures that the `dyn Trait` method is chosen, versus the methods from the prelude trait. For example, `f.into_iter()` above would become `(*f).into_iter()`.

### Implementation Reference

The lint needs to take a couple of factors into account when determining whether or not introducing 2021 Edition to a codebase will cause a name resolution collision (thus breaking the code after changing edition). These factors include:

- Is the call a [fully-qualified call] or does it use [dot-call method syntax]?
  - This will affect how the name is resolved due to auto-reference and auto-dereferencing on method call syntax. Manually dereferencing/referencing will allow specifying priority in the case of dot-call method syntax, while fully-qualified call requires specification of the type and the trait name in the method path (e.g. `<Type as Trait>::method`)
- Is this an [inherent method] or [a trait method]?
  - Inherent methods that take `self` will take priority over `TryInto::try_into` as inherent methods take priority over trait methods, but inherent methods that take `&self` or `&mut self` won't take priority due to requiring a auto-reference (while `TryInto::try_into` does not, as it takes `self`)
- Is the origin of this method from `core`/`std`? (As the traits can't have a collision with themselves)
- Does the given type implement the trait it could have a collision against?
- Is the method being called via dynamic dispatch? (i.e. is the `self` type `dyn Trait`)
  - If so, trait imports don't affect resolution, and no migration lint needs to occur

[fully-qualified call]: https://doc.rust-lang.org/reference/expressions/call-expr.html#disambiguating-function-calls
[dot-call method syntax]: https://doc.rust-lang.org/reference/expressions/method-call-expr.html
[inherent method]: https://doc.rust-lang.org/reference/items/implementations.html#inherent-implementations
[a trait method]: https://doc.rust-lang.org/reference/items/implementations.html#trait-implementations
