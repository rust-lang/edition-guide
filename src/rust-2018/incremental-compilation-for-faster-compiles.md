# Incremental Compilation

![Minimum Rust version: 1.24](https://img.shields.io/badge/Minimum%20Rust%20Version-1.24-brightgreen.svg)

Back in September of 2016, we [blogged about Incremental
Compilation](https://blog.rust-lang.org/2016/09/08/incremental.html). While
that post goes into the details, the idea is basically this: when you’re
working on a project, you often compile it, then change something small, then
compile again. Historically, the compiler has compiled your entire project,
no matter how little you’ve changed the code. The idea with incremental
compilation is that you only need to compile the code you’ve actually
changed, which means that that second build is faster.

This is now turned on by default. This means that your builds should be
faster! Don’t forget about cargo check when trying to get the lowest possible
build times.

This is still not the end story for compiler performance generally, nor
incremental compilation specifically. We have a lot more work planned in the
future.

One small note about this change: it makes builds faster, but makes the final
binary a bit slower. For this reason, it's not turned on in release builds.