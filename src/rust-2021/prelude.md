# Additions to the prelude

## Summary

## Details

The [prelude of the standard library](https://doc.rust-lang.org/stable/std/prelude/index.html)
is the module containing everything that is automatically imported in every module.
It contains commonly used items such as `Option`, `Vec`, `drop`, and `Clone`.

The Rust compiler prioritizes any manually imported items over those
from the prelude, to make sure additions to the prelude will not break any existing code.
For example, if you have a crate or module called `example` containing a `pub struct Option;`,
then `use example::*;` will make `Option` unambiguously refer to the one from `example`;
not the one from the standard library.

However, adding a *trait* to the prelude can break existing code in a subtle way.
A call to `x.try_into()` using a `MyTryInto` trait might become ambiguous and
fail to compile if `std`'s `TryInto` is also imported,
since it provides a method with the same name.
This is the reason we haven't added `TryInto` to the prelude yet,
since there is a lot of code that would break this way.

As a solution, Rust 2021 will use a new prelude.
It's identical to the current one, except for three new additions:

- [`std::convert::TryInto`](https://doc.rust-lang.org/stable/std/convert/trait.TryInto.html)
- [`std::convert::TryFrom`](https://doc.rust-lang.org/stable/std/convert/trait.TryFrom.html)
- [`std::iter::FromIterator`](https://doc.rust-lang.org/stable/std/iter/trait.FromIterator.html)

The library team still needs to formally approve these, which will likely happen soon.
<!-- TODO: hopefully this happens before we publish this -->
