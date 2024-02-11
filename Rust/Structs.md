#rust
### Extending a struct

```
Struct user{
	name: str,
	email: str,
	age: i32
}

fn main(){

	let user1 = user{name: "hi", email:'str@', age:2 };
	// struct extension
	let user2 = user{..user1}
	let user3 = user{name: "bye", ..user1}
}
```