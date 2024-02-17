#rust 
# Cons List

Nested pairs: (1,(2,(3,(Nil))))

To define it
```
use crate::List::{Cons, Nil};
enum List {
	Cons(i32, List),
	Nil,
}
fn main(){
	let list = Cons(1, Cons(2, Cons(3,Nil)));
}
```
This code up top will not work: the compiler cannot know how big the Cons List is, and then it does not know how big a space does it need to prepare in the stack.

This can be solved with a [[Box]]. It has a defined space, and then the pointers can be held in the stack:
```
enum List {
	Cons(i32, Box<List>),
	Nil,
}
use create::List::{Cons, Nil};

 fn main(){
 let list = Cons(1, Box::new(Cons(2, Box::new(3, Box::new(Nil)))));
 }
```

