# What are Editions?

Rust ships releases on a six-week cycle. This means that users get a constant
stream of new features. This is much faster than updates for other languages,
but this also means that each update is smaller.  After a while, all of those
tiny changes add up. But, from release to release, it can be hard to look back
and say *"Wow, between Rust 1.10 and Rust 1.20, Rust has changed a lot!"*

Every two or three years, we'll be producing a new *edition* of Rust. Each
edition brings together the features that have landed into a clear package, with
fully updated documentation and tooling. New editions ship through the usual
release process.

This serves different purposes for different people:

- For active Rust users, it brings together incremental changes into an
  easy-to-understand package.

- For non-users, it signals that some major advancements have landed, which
  might make Rust worth another look.

- For those developing Rust itself, it provides a rallying point for the project as a
  whole.

## Compatibility

When a new edition becomes available in the compiler, crates must explicitly opt
in to it to take full advantage. This opt in enables editions to contain
incompatible changes, like adding a new keyword that might conflict with
identifiers in code, or turning warnings into errors. A Rust compiler will
support all editions that existed prior to the compiler's release, and can link
crates of any supported editions together.
Edition changes only affect the way the compiler initially parses the code.
Therefore, if you're using Rust 2015, and
one of your dependencies uses Rust 2018, it all works just fine. The opposite
situation works as well.

Just to be clear: most features will be available on all editions.
People using any edition of Rust will continue to see improvements as new
stable releases are made.  In some cases however, mainly when new keywords are
added, but sometimes for other reasons, there may be new features that are only
available in later editions.  You only need to upgrade if you want to take
advantage of such features.