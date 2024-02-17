 Simplest smart pointer. Used in the following situations:
- Type whose size cannot be known at compile time and we want to use it in a context that requires an exact size.
- Large amount of data whose ownership must be transferred but ensuring that the data won't be copied when doing it.
- Owning a value when we want the value only to implement a specific trait and not to care about it being of an specific type.

```
fn main(){
	let b = Box::new(5);
	println!("b = {}",b);
}
```

Box allows for [[Recursive Types]]
