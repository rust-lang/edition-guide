# Never type fallback change

## Summary

> [!NOTE]
> The changes previously documented in this chapter are no longer edition-specific as of Rust 1.100.
>
> Never type (`!`) to any type ("never-to-any") coercions now fall back to never type (`!`) rather than to unit type (`()`) on all editions.
>
> The `dependency_on_unit_never_type_fallback` lint, which handled the migration, has also been removed.
>
> The description of "never-to-any" coercions below is kept for informational purposes.

## Details

When the compiler sees a value of type `!` (never) in a [coercion site][], it implicitly inserts a coercion to allow the type checker to infer any type:

```rust,should_panic
// This:
let x: u8 = panic!();

// ...is (essentially) turned by the compiler into:
let x: u8 = absurd(panic!());

// ...where `absurd` is the following function
// (it's sound because `!` always marks unreachable code):
fn absurd<T>(x: !) -> T { x }
```

This can lead to compilation errors if the type cannot be inferred:

```rust,compile_fail,E0282
# fn absurd<T>(x: !) -> T { x }
// This:
{ panic!() };

// ...gets turned into this:
{ absurd(panic!()) }; //~ ERROR can't infer the type of `absurd`
```

To prevent such errors, the compiler remembers where it inserted `absurd` calls, and if it can't infer the type, it uses the fallback type instead:

```rust,should_panic
# fn absurd<T>(x: !) -> T { x }
type Fallback = /* An arbitrarily selected type! */ !;
{ absurd::<Fallback>(panic!()) }
```

This is what is known as "never type fallback".

Historically, the fallback type has been `()` (unit).  This caused `!` to spontaneously coerce to `()` even when the compiler would not infer `()` without the fallback.  That was confusing and has prevented the stabilization of the `!` type.

On all editions, the fallback type is now `!`.  This makes things work more intuitively.  Now when you pass `!` and there is no reason to coerce it to something else, it is kept as `!`.

In some cases your code might depend on the fallback type being `()`, so this can cause compilation errors or changes in behavior.

[coercion site]: ../../reference/type-coercions.html#coercion-sites

## Migration

> [!NOTE]
> Migration is no longer applicable because the never type now falls back to `!` on all editions. The information on translating code that assumed a fallback to `()` is preserved below in case you have legacy code that needs updating.

There is no automatic fix to update code that assumed a fallback to `()`.

The manual fix is to specify the type explicitly so that the fallback type is not used.  Unfortunately, it might not be trivial to see which type needs to be specified.

One of the most common patterns broken by this change is using `f()?;` where `f` is generic over the `Ok`-part of the return type:

```rust,compile_fail,E0277
# fn outer<T>(x: T) -> Result<T, ()> {
fn f<T: Default>() -> Result<T, ()> {
    Ok(T::default())
}

f()?;
# Ok(x)
# }
```

You might think that, in this example, type `T` can't be inferred.  However, due to the current desugaring of the `?` operator, it is now inferred as `!` (whereas in previous versions of Rust it was inferred as `()`).

To fix the issue you need to specify the `T` type explicitly:

```rust,edition2024
# fn outer<T>(x: T) -> Result<T, ()> {
# fn f<T: Default>() -> Result<T, ()> {
#     Ok(T::default())
# }
f::<()>()?;
// ...or:
() = f()?;
# Ok(x)
# }
```

Another relatively common case is panicking in a closure:

```rust,compile_fail,E0277
trait Unit {}
impl Unit for () {}

fn run<R: Unit>(f: impl FnOnce() -> R) {
    f();
}

run(|| panic!());
```

Previously `!` from the `panic!` coerced to `()` which implements `Unit`.  However now the `!` is kept as `!` so this code fails because `!` doesn't implement `Unit`.  To fix this you can specify the return type of the closure:

```rust,edition2024,should_panic
# trait Unit {}
# impl Unit for () {}
#
# fn run<R: Unit>(f: impl FnOnce() -> R) {
#     f();
# }
run(|| -> () { panic!() });
```

A similar case to that of `f()?` can be seen when using a `!`-typed expression in one branch and a function with an unconstrained return type in the other:

```rust,compile_fail,E0277
if true {
    Default::default()
} else {
    return
};
```

Previously `()` was inferred as the return type of `Default::default()` because `!` from `return` was spuriously coerced to `()`.  Now, `!` will be inferred instead causing this code to not compile because `!` does not implement `Default`.

Again, this can be fixed by specifying the type explicitly:

```rust,edition2024
() = if true {
    Default::default()
} else {
    return
};

// ...or:

if true {
    <() as Default>::default()
} else {
    return
};
```
