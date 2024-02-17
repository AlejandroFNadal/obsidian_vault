#rust
Scalars can be copied and reused:
`let mut x = 2;
`let mut y = x;`
`println!("x {} y {}")`

Stuff stored in the heap cannot, because only the stack references to the heap are kept:
`let mut s1 = String::from('hello');
`let mut s2 = s1`
`println("s2 {s2}")` // WRONG

Instead, here we would use the Clone method. Values in the stack are Copied, values in the heap are Cloned.

When we pass a parameter, we are changing ownership. When we create and pass a reference, we are borrowing.
## Borrowing
We pass a value as a reference to a function. It borrows the value, but it does not own it. As it does not own it, it is not destroyed afterwards.
```
let s = String::from('Hello');
mag_func(&s)
println!("String: {s}")// the ownership of s was not lost, and therefore, there is no need of returning s in the function
```
References are inmmutable by default:
```
fn mag_func(s : &String){
	s.push_str("World");
}
let s = String::from('Hello');
mag_func(&s)
println!("String: {s}")// the ownership of s was not lost, and therefore, there is no need of returning s in the function

This will not work because we are trying to modify a reference
```

## Mutable references
```
fn mag_func(s : &mut String){
	s.push_str("World");
}
let s = String::from('Hello');
mag_func(&mut s)
println!("String: {s}")// the ownership of s was not lost, and therefore, there is no need of returning s in the function

```
You cannot have more than mutable reference to a value. However, the scope of a reference is not until the end of the brackets, but until the last line where the reference is used. That means:

```
let s = String::from('Hello');
let s1 = &s;
let s2 = %s; // okay, inmutable reference.
println!("{} {}", s1,s2);
// above is the last moment in which these references are used. Then, their scope ends here. That means that now, we can:
let s3 = &mut s;
```

String slices are a type of reference too.

```
let mut s1 = String.from('hello');
let s2 = &s1[1..3];
s1.clear(); // this will fail because s2 is a inmutable reference to s1 who is being muted, and the reference has a longer lifetime than this line
println!("{}",s2);
```

All String literals are slices, because they are just references to a part of the binary. 

### Multiple ownership
Things can have multiple owners through the [[Reference Counted Smart Pointer | RC]]
