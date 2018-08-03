# Rust 2018 Feature Status

## Language

[Shipped, 1.26]: https://blog.rust-lang.org/2018/05/10/Rust-1.26.html
[Shipped, 1.27]: https://blog.rust-lang.org/2018/06/21/Rust-1.27.html

[`impl Trait`]: transitioning/traits/impl-trait.html
[Basic slice patterns]: transitioning/slice-patterns.html
[Default match bindings]: transitioning/ownership-and-lifetimes/default-match-bindings.html
[Anonymous lifetimes]: transitioning/ownership-and-lifetimes/anonymous-lifetime.html
[relnotes_1.26]: https://github.com/rust-lang/rust/blob/master/RELEASES.md#version-1260-2018-05-10
[`dyn Trait`]: transitioning/traits/dyn-trait.html
[`?` in `main`/tests]: transitioning/errors/question-mark.html
[Module system path changes]: transitioning/modules/path-clarity.html
[issue#44660]: https://github.com/rust-lang/rust/issues/44660
[Import macros via `use`]: transitioning/modules/macros.html
[issue#35896]: https://github.com/rust-lang/rust/issues/35896
[In-band lifetimes]: transitioning/ownership-and-lifetimes/in-band-lifetimes.html
[issue#44524]: https://github.com/rust-lang/rust/issues/44524
[Lifetime elision in `impl`s]: transitioning/ownership-and-lifetimes/lifetime-elision-in-impl.html
[Raw identifiers]: transitioning/raw-identifiers.html
[issue#48589]: https://github.com/rust-lang/rust/issues/48589
[nll_status]: http://smallcultfollowing.com/babysteps/blog/2018/06/15/mir-based-borrow-check-nll-status-update/
[`T: 'a` inference in `struct`s]: transitioning/ownership-and-lifetimes/struct-inference.html
[issue#44493]: https://github.com/rust-lang/rust/issues/44493

| **Feature** | **Status** | **Minimum Edition** |
| ----------- | ---------- | -------------------------- |
| [`impl Trait`] | [Shipped, 1.26] | 2015 |
| [Basic slice patterns] | [Shipped, 1.26] | 2015 |
| [Default match bindings] | [Shipped, 1.26] | 2015 |
| [Anonymous lifetimes] | [Shipped, 1.26][relnotes_1.26] | 2015 |
| [`dyn Trait`] | [Shipped, 1.27] | 2015 |
| SIMD support | [Shipped, 1.27] | 2015 |
| [`?` in `main`/tests] | [Shipping, 1.26][Shipped, 1.26] and 1.28 | 2015 |
| [In-band lifetimes] | Unstable; [tracking issue][issue#44524] | 2015 |
| [Lifetime elision in `impl`s] | Unstable; [tracking issue][issue#44524] | 2015 |
| Non-lexical lifetimes | [Implemented but not ready for preview][nll_status] | 2015 |
| [`T: 'a` inference in `struct`s] | Unstable; [tracking issue][issue#44493] | 2015 |
| [Raw identifiers] | Unstable; [tracking issue][issue#48589] | ? |
| [Import macros via `use`] | Unstable; [tracking issue][issue#35896] | ? |
| [Module system path changes] | Unstable; [tracking issue][issue#44660] | 2018 |

While some of these features are already available in Rust 2015, they are tracked here
because they are being promoted as part of the Rust 2018 edition.  Accordingly, they
will be discussed in subsequent sections of this guide book. The features marked as
"Shipped" are all available today in stable Rust, so you can start using them right now!

## Standard library

[issue#49668]: https://github.com/rust-lang/rust/issues/49668

| **Feature** | **Status** |
| ----------- | ---------- |
| [Custom global allocators][issue#49668] | Will ship in 1.28 |

## Tooling

[RLS]: https://github.com/rust-lang-nursery/rls
[1.0 milestone]: https://github.com/rust-lang-nursery/rls/milestone/7
[rustfmt]: https://github.com/rust-lang-nursery/rustfmt
[style guide RFC]: https://github.com/rust-lang/rfcs/pull/2436
[stability RFC]: https://github.com/rust-lang/rfcs/pull/2437
[Clippy]: https://github.com/rust-lang-nursery/rust-clippy
[RFC#2476]: https://github.com/rust-lang/rfcs/pull/2476

| **Tool** | **Status** |
| ----------- | ---------- |
| [RLS] 1.0 | Feature-complete; see [1.0 milestone] |
| [rustfmt] 1.0 | Finalizing spec; [1.0 milestone](https://github.com/rust-lang-nursery/rustfmt/milestone/2), [style guide RFC], [stability RFC] |
| [Clippy] 1.0 | [RFC][RFC#2476] |

## Documentation

[Edition Guide]: https://rust-lang-nursery.github.io/edition-guide/
[TRPL]: https://github.com/rust-lang/book/

| **Tool** | **Status** |
| ----------- | ---------- |
| [Edition Guide] | Initial draft complete |
| [TRPL] | Updated as features stabilize |

## Web site

The visual design is being finalized, and early rounds of content brainstorming are complete.
