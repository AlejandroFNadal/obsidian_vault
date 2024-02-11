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