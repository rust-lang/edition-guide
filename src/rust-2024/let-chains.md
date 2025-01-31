# `let` chains in `if` and `while`

## Summary

- Allow chaining of `let` expressions in the condition operand of `if` and `while`.

## Details

Starting with the 2024 Edition, it is now allowed to have chaining of `let` expressions inside `if` and `while` condition operands,
where chaining refers to `&&` chains. The `let` expressions still have to appear at the top level,
so `if (let Some(hi) = foo || let Some(hi) = bar)` is not allowed.

Before 2024, the `let` had to appear directly after the `if` or `while`, forming a `if let` or `while let` special variant.
Now, `if` and `while` allow chains of one or more `let` expressions, possibly mixed with expressions that are `bool` typed.

```rust,edition2024
fn sum_first_two(nums: &[u8]) -> Option<u8> {
    let mut iter = nums.iter();
    if let Some(first) = iter.next()
        && let Some(second) = iter.next()
    {
        first.checked_add(second)
    } else {
        None
    }
}
```

The feature is edition gated due to requiring [if let rescoping], which is a Edition 2024 change.

## Migration

The switch to Edition 2024 doesn't neccessitate any migrations due to this feature,
as it creates a true extension of the set of allowed Rust programs.

[if let rescoping]: temporary-if-let-scope.html
