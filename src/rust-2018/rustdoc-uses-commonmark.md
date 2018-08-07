# Rustdoc uses CommonMark

![Minimum Rust version: 1.25](https://img.shields.io/badge/Minimum%20Rust%20Version-1.25-brightgreen.svg) for support by default

![Minimum Rust version: 1.23](https://img.shields.io/badge/Minimum%20Rust%20Version-1.23-red.svg) for support via a flag

Rustdoc lets you write documentation comments in Markdown. At Rust 1.0, we
were using the `hoedown` markdown implementation, written in C. Markdown is
more of a family of implementations of an idea, and so `hoedown` had its own
dialect, like many parsers. The [CommonMark project](https://commonmark.org/)
has attempted to define a more strict version of Markdown, and so now, Rustdoc
uses it by default.

As of Rust 1.23, we still defaulted to `hoedown`, but you could enable
Commonmark via a flag, `--enable-commonmark`. Today, we only support
CommonMark.