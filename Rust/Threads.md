Threads are created through the spawn function and they receive a closure as parameter.
```
use std::thread;
use std::time::Duration;

fn main() {
    thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
}
```

Technically it could happen that the main thread ends here and the spawned thread does not get to finish. We can get our program to wait using the join handle.  Spawn returns a JoinHandle method, and if we call the join method on it, it will wait until the thread finishes.

```
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }

    handle.join().unwrap();
}
```

### Using Move
The following code will not compile:
```
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(|| {
        println!("Here's a vector: {:?}", v);
    });

    handle.join().unwrap();
}
```
This is because vec's lifetime is unknown. The compiler does not like working with a reference to an object that might change or be destroyed outside the thread. The compiler does not know how long the thread will run. 

A more crazy example:

```
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(|| {
        println!("Here's a vector: {:?}", v);
    });

    drop(v); // oh no!

    handle.join().unwrap();
}
```
We need then to move the value inside the thread:
```
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(move || {
        println!("Here's a vector: {:?}", v);
    });

    handle.join().unwrap();
}
```

## Channels

Way to send data from one thread to the other.
Instantiated through: ` let (tx, rx) = mpsc::channel();
mpsc means multiple producer single consumer.

Sends data with: `tx.send(val).unwrap()`
Receives data with either recv() or try_recv. The first is blocking.

Sending takes ownership of what you send: it cannot be used after sent. 

```
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let vals = vec![
            String::from("hi"),
            String::from("from"),
            String::from("the"),
            String::from("thread"),
        ];

        for val in vals {
            tx.send(val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    for received in rx {
        println!("Got: {}", received);
    }
}
```
rx is used as an iterator.
Multiple transmiters can be used by cloning tx with the clone method.

### Using Mutexes
[[Mutexes]]
[[Atomic Mutexes]]
