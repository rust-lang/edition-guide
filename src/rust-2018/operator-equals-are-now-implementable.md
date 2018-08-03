# "Operator-equals" are now implementable

![Minimum Rust version: 1.8](https://img.shields.io/badge/Minimum%20Rust%20Version-1.8-brightgreen.svg)

The various “operator equals” operators, such as `+=` and `-=`, are
implementable via various traits. For example, to implement `+=` on
a type of your own:

```rust
use std::ops::AddAssign;

#[derive(Debug)]
struct Count { 
    value: i32,
}

impl AddAssign for Count {
    fn add_assign(&mut self, other: Count) {
        self.value += other.value;
    }
}

fn main() {
    let mut c1 = Count { value: 1 };
    let c2 = Count { value: 5 };

    c1 += c2;

    println!("{:?}", c1);
}
```

This will print `Count { value: 6 }`.