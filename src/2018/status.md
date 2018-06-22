# Rust 2018 Feature Status

## Language

| **Feature** | **Status** |
| ----------- | ---------- |
| [`impl Trait`](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/traits/impl-trait.html) | [Shipped, 1.26](https://blog.rust-lang.org/2018/05/10/Rust-1.26.html) |
| [Basic slice patterns](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/slice-patterns.html) | [Shipped, 1.26](https://blog.rust-lang.org/2018/05/10/Rust-1.26.html) |
| [Default match bindings](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/ownership-and-lifetimes/default-match-bindings.html) | [Shipped, 1.26](https://blog.rust-lang.org/2018/05/10/Rust-1.26.html) |
| [Anonymous lifetimes](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/ownership-and-lifetimes/anonymous-lifetime.html)| [Shipped, 1.26](https://github.com/rust-lang/rust/blob/master/RELEASES.md#version-1260-2018-05-10) |
| [`dyn Trait`](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/traits/dyn-trait.html)| [Shipped, 1.27](https://blog.rust-lang.org/2018/06/21/Rust-1.27.html) |
| SIMD support | [Shipped, 1.27](https://blog.rust-lang.org/2018/06/21/Rust-1.27.html) |
| [`?` in `main`/tests](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/errors/question-mark.html)| [Shipping, 1.26](https://blog.rust-lang.org/2018/05/10/Rust-1.26.html) and 1.28 |
| [Module system path changes](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/modules/path-clarity.html) | Unstable; [tracking issue](https://github.com/rust-lang/rust/issues/44660)|
| [Import macros via `use`](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/modules/macros.html)| Unstable; [tracking issue](https://github.com/rust-lang/rust/issues/35896)|
| [In-band lifetimes](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/ownership-and-lifetimes/in-band-lifetimes.html) | Unstable; [tracking issue](https://github.com/rust-lang/rust/issues/44524)|
| [Lifetime elision in `impl`s](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/ownership-and-lifetimes/lifetime-elision-in-impl.html)| Unstable; [tracking issue](https://github.com/rust-lang/rust/issues/44524)|
| [Raw identifiers](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/raw-identifiers.html) | Unstable; [tracking issue](https://github.com/rust-lang/rust/issues/48589)|
| Non-lexical lifetimes| [Implemented but not ready for preview](http://smallcultfollowing.com/babysteps/blog/2018/06/15/mir-based-borrow-check-nll-status-update/)|
| [`T: 'a` inference in `struct`s](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/ownership-and-lifetimes/struct-inference.html) | Not fully implemented |
| [`async`/`await`](https://rust-lang-nursery.github.io/edition-guide/2018/transitioning/concurrency/async-await.html) | [Not fully implemented](https://github.com/rust-lang/rust/issues/50547) |

## Standard library

| **Feature** | **Status** |
| ----------- | ---------- |
| [Custom global allocators](https://github.com/rust-lang/rust/issues/49668) | Will ship in 1.28 |

## Tooling

| **Tool** | **Status** |
| ----------- | ---------- |
| [RLS](https://github.com/rust-lang-nursery/rls) 1.0 | Feature-complete; see [1.0 milestone](https://github.com/rust-lang-nursery/rls/milestone/7) |
| [rustfmt](https://github.com/rust-lang-nursery/rustfmt) 1.0 | Finalizing spec; [1.0 milestone](https://github.com/rust-lang-nursery/rustfmt/milestone/2), [style guide RFC](https://github.com/rust-lang/rfcs/pull/2436), [stability RFC](https://github.com/rust-lang/rfcs/pull/2437) |
| [Clippy](https://github.com/rust-lang-nursery/rust-clippy) 1.0 | [RFC](https://github.com/rust-lang/rfcs/pull/2476) |

## Documentation

| **Tool** | **Status** |
| ----------- | ---------- |
| [Edition Guide](https://rust-lang-nursery.github.io/edition-guide/) | Initial draft complete |
| [TRPL](https://github.com/rust-lang/book/) | Updated as features stabilize |

## Web site

The visual design is being finalized, and early rounds of content brainstorming are complete.
