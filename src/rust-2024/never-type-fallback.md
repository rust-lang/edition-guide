# Never type fallback change

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".

## Summary

- Never type (`!`) to any type coercions fallback to never type (`!`).

## Details

When the compiler sees a value of type ! in a [coercion site], it implicitly inserts a coercion to allow the type checker to infer any type:

```rust,ignore (has placeholders)
// this
let x: u8 = panic!();

// is (essentially) turned by the compiler into
let x: u8 = absurd(panic!());

// where absurd is a function with the following signature
// (it's sound, because `!` always marks unreachable code):
fn absurd<T>(_: !) -> T { ... }
```

This can lead to compilation errors if the type cannot be inferred:

```rust,ignore (uses code from previous example)
// this
{ panic!() };

// gets turned into this
{ absurd(panic!()) }; // error: can't infer the type of `absurd`
```

To prevent such errors, the compiler remembers where it inserted absurd calls, and if it can’t infer the type, it uses the fallback type instead:

```rust,ignore (has placeholders, uses code from previous example)
type Fallback = /* An arbitrarily selected type! */;
{ absurd::<Fallback>(panic!()) }
```

This is what is known as “never type fallback”.

Historically, the fallback type was `()`, causing confusing behavior where `!` spontaneously coerced to `()`, even when it would not infer `()` without the fallback.

In the 2024 edition (and possibly in all editions on a later date) the fallback is now `!`.  This makes it work more intuitively, now when you pass `!` and there is no reason to coerce it to something else, it is kept as `!`.

In some cases your code might depend on the fallback being `()`, so this can cause compilation errors or changes in behavior.

[coercion site]: https://doc.rust-lang.org/reference/type-coercions.html#coercion-sites

## Migration

There is no automatic fix, but there is automatic detection of code which will be broken by the edition change.  While still on a previous edition you should see warnings if your code will be broken.

In either case the fix is to specify the type explicitly, so the fallback is not used.  The complication is that it might not be trivial to see which type needs to be specified.

One of the most common patterns which are broken by this change is using `f()?;` where `f` is generic over the ok-part of the return type:

```rust,ignore (can't compile outside of a result-returning function)
fn f<T: Default>() -> Result<T, ()> {
    Ok(T::default())
}

f()?;
```

You might think that in this example type `T` can't be inferred, however due to the current desugaring of `?` operator it used to be inferred to `()`, but it will be inferred to `!` now.

To fix the issue you need to specify the `T` type explicitly:

```rust,ignore (can't compile outside of a result-returning function, mentions function from previous example)
f::<()>()?;
// OR
() = f()?;
```

Another relatively common case is `panic`king in a closure:

```rust,edition2015,should_panic
trait Unit {}
impl Unit for () {}

fn run<R: Unit>(f: impl FnOnce() -> R) {
    f();
}

run(|| panic!());
```

Previously `!` from the `panic!` coerced to `()` which implements `Unit`.  However now the `!` is kept as `!` so this code fails because `!` does not implement `Unit`.  To fix this you can specify return type of the closure:

```rust,ignore (uses function from the previous example)
run(|| -> () { panic!() });
```

A similar case to the `f()?` can be seen when using a `!`-typed expression in a branch and a function with unconstrained return in the other:

```rust,edition2015
if true {
    Default::default()
} else {
    return
};
```

Previously `()` was inferred as the return type of `Default::default()` because `!` from `return` got spuriously coerced to `()`.  Now, `!` will be inferred instead causing this code to not compile, because `!` does not implement `Default`.

Again, this can be fixed by specifying the type explicitly:

```rust
() = if true {
    Default::default()
} else {
    return
};

// OR

if true {
    <() as Default>::default()
} else {
    return
};
```
