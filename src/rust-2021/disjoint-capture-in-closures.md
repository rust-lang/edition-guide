# Disjoint capture in closures

## Summary

## Details

[Closures](https://doc.rust-lang.org/book/ch13-01-closures.html)
automatically capture anything that you refer to from within their body.
For example, `|| a + 1` automatically captures a reference to `a` from the surrounding context.

Currently, this applies to whole structs, even when only using one field.
For example, `|| a.x + 1` captures a reference to `a` and not just `a.x`.
In some situations, this is a problem.
When a field of the struct is already borrowed (mutably) or moved out of,
the other fields can no longer be used in a closure,
since that would capture the whole struct, which is no longer available.

```rust,ignore
let a = SomeStruct::new();

drop(a.x); // Move out of one field of the struct

println!("{}", a.y); // Ok: Still use another field of the struct

let c = || println!("{}", a.y); // Error: Tries to capture all of `a`
c();
```

Starting in Rust 2021, closures will only capture the fields that they use.
So, the above example will compile fine in Rust 2021.

This new behavior is only activated in the new edition,
since it can change the order in which fields are dropped.
As for all edition changes, an automatic migration is available,
which will update your closures for which this matters.
It can insert `let _ = &a;` inside the closure to force the entire
struct to be captured as before.
