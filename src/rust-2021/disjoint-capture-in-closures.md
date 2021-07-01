# Disjoint capture in closures

## Summary

- `|| a.x + 1` now captures only `a.x` instead of `a`.
- This can subtly change the drop order of things, and auto traits usage with closures.

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

Starting in Rust 2021, closures will only capture the fields that they use, eliminating common borrow check errors.
So, the above example will compile fine in Rust 2021.

Disjoint capture was proposed as part of [RFC 2229](https://github.com/rust-lang/rfcs/blob/master/text/2229-capture-disjoint-fields.md), and we suggest reading the RFC to better understand motivations behind the feature.

## Changes to semantics because of disjoint capture
`#[feature(capture_disjoint_fields)]` introduces (minor) breaking change to the language. This means that there might be observable changes or valid Rust 2018 code that fails to compile once you move to Rust Edition 2021. You can use `cargo fix` with the `disjoint_capture_migrations` lint to migrate your existing code to Rust 2021.
### Wild Card Patterns
Closures now only capture data that needs to be read, which means the following closures will not capture `x`

```rust,ignore
let x = 10;
let c = || {
    let _ = x;
};

let c = || match x {
    _ => prinln!("Hello World!")
};
```

### Drop Order
Since only a part of a variable might be captured instead of the entire variable, when different fields or elements (in case of tuple) get drop, the drop order might be affected.

```rust,ignore
{
    let t = (vec![0], vec![0]);

    {
        let c = || {
            move(t.0);
        };
    } // c and t.0 dropped here
} // t.1 dropped here

```


### Auto Traits
Structs or tuples that implement (an) auto trait(s) and are passed along in a closure may no longer guarantee that the closure can also implement that/those auto trait(s).

For instance, a common way to allow passing around raw pointers between threads is to wrap them in a struct and then implement `Send`/`Sync` auto trait for the wrapper. The closure that is passed to `thread::spawn` uses the specific fields within the wrapper but the entire wrapper is captured regardless. Since the wrapper is `Send`/`Sync`, the code is considered safe and therefore compiles successfully.

With this feature only the specific field mentioned in the closure gets captured, which wasn't originally `Send`/`Sync` defeating the purpose of the wrapper.


```rust,ignore
struct Ptr(*mut i32);
unsafe impl Send for Ptr;


let mut x = 5;
let px = (&mut x as *mut i32);

let c = thread::spawn(move || {
    *(px.0) += 10;
}); // Closure captured px.0 which is not Send
```

## Migrations

This new behavior is only activated in the new edition, or by specifically enabling it using `#[feature(capture_disjoint_fields)]`,
since it can change the order in which fields are dropped and can impact auto trait usage with closures.

As for all edition changes, an automatic migration is available.
If you would like to be warned of semantics change that may impact your code, you can [use the lint](https://doc.rust-lang.org/rustc/lints/levels.html) `disjoint_capture_migrations`. The lint is also supported with [cargo fix](https://doc.rust-lang.org/cargo/commands/cargo-fix.html) to automatically migrate your code.

The migration fix involves adding `let _ = &a;` inside the closure to force the entire variable to be captured as before.