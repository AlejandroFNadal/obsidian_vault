It allows for multiple ownership by keeping track of the references to a value to determine whether or not the value is still in use.

We use it when we want to allocate some data on the heap for multiple parts of our program to read and we can't determine at compile time which part will finish using the data last.

Cannot be used in multithreaded.

Let's check the example of [[Box]]:
```
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use crate::List::{Cons, Nil};

fn main() {
    let a = Cons(5, Box::new(Cons(10, Box::new(Nil))));
    let b = Cons(3, Box::new(a));
    let c = Cons(4, Box::new(a));
}
```

Here we are attempting to connect two nodes of our list to A. It will not work because the value is being moved once already. We could pass references and specify lifetimes, but that would make all elements of the list to last as much as the others do.

Instead we will use RC. With the clone method, RC counts how many references we have. The internal data will not be cleaned up until all references are deleted. This is similar to the way [[Hardlinks]] work in Linux.

```
enum List {
    Cons(i32, Rc<List>),
    Nil,
}

use crate::List::{Cons, Nil};
use std::rc::Rc;

fn main() {
    let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
    let b = Cons(3, Rc::clone(&a));
    let c = Cons(4, Rc::clone(&a));
}
```
**Important**: Clone does not do a deep copy, instead, it just increases the refrence counter. 