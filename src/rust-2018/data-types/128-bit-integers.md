# 128 bit integers

![Minimum Rust version: 1.26](https://img.shields.io/badge/Minimum%20Rust%20Version-1.26-brightgreen.svg)

A very simple feature: Rust now has 128 bit integers!

```rust
let x: i128 = 0;
let y: u128 = 0;
```

These are twice the size of `u64`, and so can hold more values. More specifically,

* `u128`: `0` - `340,282,366,920,938,463,463,374,607,431,768,211,455`
* `i128`: `−170,141,183,460,469,231,731,687,303,715,884,105,728` - `170,141,183,460,469,231,731,687,303,715,884,105,727`

Whew!