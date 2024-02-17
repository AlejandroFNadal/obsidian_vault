#rust 
Iterators in Rust are lazy: They do not run until you try to consume the elements of the iterator.

```
let v1 = vec![1,2,3];
let v1_iter = v1.iter();
for val in v1_iter {
	println!("{}",val);
}
```

## Iterator Trait
```
pub trait Iterator{
	type Item;
	fn next(&mut self) -> Option<Self::Item>;
}
```
We deifne a [[Associated Type]] . The trait requires only the next method. Next just always gives up one element from the iterator. 
The iter method always produces immutable references. To get owned values we use `into_iter`, and for mutable references, we use `iter_mut`.

### Consumer Adaptors

There are methods that take ownership of the iterator, use them up and then they are dropped. For example, the sum method:

`v1.iter().sum()`

### Iterator adaptors

Some methods create iterators from another iterator. For example:
```
let v1 : Vec<i32> = vec![1,2,3]
v1.iter().map(|x| x + 1);
```
**Important**: this piece of code would cause a warning, because this iterator is not being used. If we want to actually make the array, we use the collect method at the end.
```
v1.iter().map(|x| x + 1).collect()
```

Another example:
Given the following structure
```
struct Shoe {
	size: u32,
	style: String
}
fn shoes_in_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
	shoes.into_iter().filter(|s| s.size == shoe_size).collect()
}
```

