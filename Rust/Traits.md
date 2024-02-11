#rust 
Similar to what we would usually call *interfaces*. 

Given multiple text types, a Trait that summarizes them would be written as:
```
pub trait Summary {
    fn summarize(&self) -> String;
}
```
Here, we only define the function's signature. Then, we implement the types and their own implementation of the Summary Trait:

```
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```
A trait can, however, have a default implementation:
```
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}
```
A trait can implement multiple methods and they can call each other:
```
pub trait Summary {
    fn summarize_author(&self) -> String;

    fn summarize(&self) -> String {
        format!("(Read more from {}...)", self.summarize_author())
    }
}

```

Traits can be passed as parameters to define types in a signature:
```
pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```
Here, we only accept items that implement Summary. That is syntactic sugar for this:
```
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```
Sometimes, we have no other choice but to use the most expressive way. For example, if we had two parameters in our signature that implement the Summary Trait, we could do it like this:

```
pub fn notify(item1: &impl Summary, item2: &impl Summary){
	...
}
```
But here there is no restriction to say that both arguments should be of the same type.
To do that, we need:

```
pub fn notify<T:Summary>(item1: &T, item2: &T){...}
```

If we want our signature to say that an argument must have multiple traits, it can be done like this:

```
pub fn notify<T:Summary + Display>(item1: &T)
```

Sometimes, if we have many traits, a signature can become crowded. For that, we use the *where* keyword.
```
	pub fn notify<T, U>(item1:&T, item2:&U) -> i32
	where
		T: Display + Clone,
		U: Clone + Debug
	{
	}
```

We can also specify the trait that a returned element's type has:

`pub fn notify()-> impl Summary {}`
However, this does not work if your function can return multiple types. This is useful for closures or iterators.

We can even implement default traits conditionally, depending on which traits a type has. E.G:
```
use std::fmt::Display;

struct Pair<T> {
    x: T,
    y: T,
}

impl<T> Pair<T> {
    fn new(x: T, y: T) -> Self {
        Self { x, y }
    }
}

impl<T: Display + PartialOrd> Pair<T> {
    fn cmp_display(&self) {
        if self.x >= self.y {
            println!("The largest member is x = {}", self.x);
        } else {
            println!("The largest member is y = {}", self.y);
        }
    }
}
```
Here, we only Implement the method cmp_display, if the T type's chosen values for T have the Trait PartialOrd.

We can create *blanket implementations*, this means, implementing a trait for all types that have another given trait. For example, here we are giving to all types that have the display trait, the ToString trait:

```
impl<T: Display> ToString for T {
    // --snip--
}
```