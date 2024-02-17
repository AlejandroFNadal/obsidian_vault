#rust 

Like a lambda function or anonymous function. The magic here is that they can capture the environment variables, unlike a traditional function in Rust, that would require more work. Example:
```
fn main() {
    let mut list = vec![1,2,3];
    println!("Before defining closure {:?}", list);
    let mut borrows_mutably = || list.push(7);
    borrows_mutably();
    println!("After defining closure {:?}", list);
}
```
Without a closure, this would be
```
fn my_func(list: &mut Vec<32>){
	list.push(7);
}
fn main(){
	let mut list = vec![1,2,3];
    println!("Before defining closure {:?}", list);
    my_func(&mut list);
    println!("After defining closure {:?}", list);
}
```
The variable is captured without the need to pass it by reference. 

Closures can take ownership of the variables using the move keyword.
```
fn main() {
    let list = vec![1,2,3];
    println!("Before defining close {:?}", list);

    thread::spawn(move || println!("From thread {:?}", list)).join().unwrap();
    // This next line causes a compilation error, because the closure has
    // taken ownership
    // println!("After defining close {:?}", list);
}
```
As the thread takes ownership now of the list variable, we cannot call a print afterward, as ownership was lost.


## Traits for closures
A closure can have the trait **FnOnce**, it means it can be called at most, once. It does not move anything out of the environment (it does not move anything inside of itself) We can see this with the definition of unwrap_or_else()

```
impl<T> Option<T> {
    pub fn unwrap_or_else<F>(self, f: F) -> T
    where
        F: FnOnce() -> T
    {
        match self {
            Some(x) => x,
            None => f(),
        }
    }
}
```

In essence, here we accept anything for the F function that has the trait FnOnce. That can actually be a closure too, and that is why we use it in the thread spawn method.

Then we also have **FnMut**, that means that the function/closure does not move values out of its body, but it might mutate the captured values.

For example:
```
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let mut list = [
        Rectangle { width: 10, height: 1 },
        Rectangle { width: 3, height: 5 },
        Rectangle { width: 7, height: 12 },
    ];

    list.sort_by_key(|r| r.width);
    println!("{:#?}", list);
}
```

Here sort_by_key is a closure, with a parameter r that is borrowed. It does not capture values or move them outside of its environment, but it has to be called multiple times, one per each element of the list. 

If a closure moves a value from place A to place B, it cannot be FnMut, because it cannot move it twice from A, as the second time it is not in place A anymore.