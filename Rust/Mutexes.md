Mutual Exclusion: Only one thread can access at some data at any given time. 

Mutex lock operation fails if another thread holding the lock panics. 
Mutexes are unlocked at the end of scope:

```
let m = Mutex::new(5);
{
	let mut num = m.lock().unwrap();
	*num = 6;
}
```
lock() returns a smart pointer called MutexGuard, wrapped in LockResult. Unwrap deals with the LockResult.

MutexGuard is a smart pointer, that is why we can dereferenciate it, and it implements Deref, so it is dereferenciated at the end of scope. Thus, we can never forget to unlock a lock.

They provide interior mutability, like [[RefCell]]. 