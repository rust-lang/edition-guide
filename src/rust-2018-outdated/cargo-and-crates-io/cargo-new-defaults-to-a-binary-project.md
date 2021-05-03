# `cargo new` defaults to a binary project

![Minimum Rust version: 1.25](https://img.shields.io/badge/Minimum%20Rust%20Version-1.25-brightgreen.svg)

`cargo new` will now default to generating a binary, rather than a library.
We try to keep Cargo’s CLI quite stable, but this change is important, and is
unlikely to cause breakage.

For some background, `cargo new` accepts two flags: `--lib`, for creating
libraries, and `--bin`, for creating binaries, or executables. If you don’t
pass one of these flags, it used to default to `--lib`. At the time, we made
this decision because each binary (often) depends on many libraries, and so
we thought the library case would be more common. However, this is incorrect;
each library is depended upon by many binaries. Furthermore, when getting
started, what you often want is a program you can run and play around with.
It’s not just new Rustaceans though; even very long-time community members
have said that they find this default surprising. As such, we’ve changed it,
and it now defaults to `--bin`.
