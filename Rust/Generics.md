Allow to represent a set of types, instead of a single type
```
fn largest(list: &[i32]) -> &i32 {
    let mut largest = &list[0];

    for item in list {
        if item > largest {
            largest = item;
        }
    }

    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let result = largest(&number_list);
    println!("The largest number is {}", result);

    let number_list = vec![102, 34, 6000, 89, 54, 2, 43, 8];

    let result = largest(&number_list);
    println!("The largest number is {}", result);
}
```
Given this function, largest, now only works with integers. But what would happen if we wanted it also to work with chars?

```
fn largest<T>(list: &[T]) -> &T {
// this could would not compile, because in the function, we compare two elements of the type T, and not all elements are comparable.
}
```

For structs:
```
struct Point<T>{
	x:T,
	y:T,
}
```
This would allow this:
```
let a = Point{x:1, b:2};
let b = Point{x:1.3, b:4.3};
```
It would not allow, however:
```
let a = Point{x;1, b:2.2};
```
For that, we would need to redefine the struct as:
```
struct Point <T, U>{
	x:T,
	y:U,
}
```

**In methods**
```
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

fn main() {
    let p = Point { x: 5, y: 10 };

    println!("p.x = {}", p.x());
}
```

**How does this work internally?**
The compiler applies *monomorphization*, this is, that during compilation time, it will look at all the possible types to be used and it will unify the code, generating all necessary structures. For example, given this code:
```
let integer = Some(5);
let float = Some(5.0);

```
After monomorphization, it becomes:
```
enum Option_i32 {
    Some(i32),
    None,
}

enum Option_f64 {
    Some(f64),
    None,
}

fn main() {
    let integer = Option_i32::Some(5);
    let float = Option_f64::Some(5.0);
}
```

This is what makes Rust exceedingly efficient at runtime, there is no need for code to check all these typing systems during runtime.
