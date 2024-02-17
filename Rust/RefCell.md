#rust 
# Interior mutability
It allows to mutate data even when there are immutable references to that data. This is [[Unsafe Rust]]. 

The invariability, this is, the borrowing rules, are enforced at compile time with [[Box]] and with [[Reference Counted Smart Pointer | RC]] . With RefCell, you need to check yourself that the rules are enforced. If they are not, a runtime error will appear.

