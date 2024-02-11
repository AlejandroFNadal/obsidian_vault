Lifetimes ensure that references are valid as long as we want them to be.
They use apostrophes.
```
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```
In this function, the references x and y should last the same, the minimum of either of them. 
```
fn main() {
    let string1 = String::from("long string is long");

    {
        let string2 = String::from("xyz");
        let result = longest(string1.as_str(), string2.as_str());
        println!("The longest string is {}", result);
    }
}

```
The compiler needs this stuff, because without it, it does not know which from the strings will be returned and then, which of the references lives needs to be long enough for the code to be valid. For example, this is not valid:

```
fn main() {
    let f1 = String::from("test");
    let result;
    {
        let f2 = String::from("another_test");
        result = longest(&f1.as_str(), &f2.as_str());
    }
    println!("{}", result);
}
```
Because the lifetime of f2 is not enough to get to result print.  The lifetime stuff does not seem to help us, only the bloody compiler.

The compiler can apply the lifetime elision rules to reduce the need to set lifetimes manually. The rules are:

- The compiler assigns to each parameter in the signature, a lifetime parameter, if the parameter is a reference.
- If there is one input parameter, its lifetime is given to all output parameters.
- If there are multiple input parameters, but one of them is &self (method), the lifetime of self is applied to all output parameters.

There is one extra thing: the 'static' lifetime. This tells the compiler that the lifetime of the reference will last during all the program.