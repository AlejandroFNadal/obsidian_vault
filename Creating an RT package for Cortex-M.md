### Prev
[[Memory Management w-o OS Rust]]

Rename main.rs to lib.rs, and rename the name of the app in Cargo.toml.
rt stands for runtime.

Now, the Reset function should call the main function 
```
#![no_std]

use core::panic::PanicInfo;

// CHANGED!
#[no_mangle]
pub unsafe extern "C" fn Reset() -> ! {
    extern "Rust" {
        fn main() -> !;
    }

    main()
}
```

We also create a build script for the rt package, so that it will search for the linker script on its own:
build.rs
```
use std::{env, error::Error, fs::File, io::Write, path::PathBuf};

fn main() -> Result<(), Box<dyn Error>> {
    // build directory for this crate
    let out_dir = PathBuf::from(env::var_os("OUT_DIR").unwrap());

    // extend the library search path
    println!("cargo:rustc-link-search={}", out_dir.display());

    // put `link.x` in the build directory
    File::create(out_dir.join("link.x"))?.write_all(include_bytes!("link.x"))?;

    Ok(())
}

```
We create a new app with cargo init and we set rt as dependency:
```
[dependencies]
rt = { path = "../rt"}
```

We call the main function from the new app, importing rt:
```
#![no_std]
#![no_main]

extern crate rt;

#[no_mangle]
pub fn main() -> ! {
    let _x = 42;

    loop {}
}
```
Compiling, we can see the dissassembly of section text:
```
cargo objdump --bin use_rt -- -d --no-show-raw-insn
   Compiling use_rt v0.1.0 (/home/synics-dev/Documents/Synics/rust_demos/trunk/embedded_scratch/use_rt)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.05s

use_rt:	file format elf32-littlearm

Disassembly of section .text:

08000018 <main>:
 8000018:      	sub	sp, #0x4
 800001a:      	movs	r0, #0x2a
 800001c:      	str	r0, [sp]
 800001e:      	b	0x8000020 <main+0x8>    @ imm = #-0x2
 8000020:      	b	0x8000020 <main+0x8>    @ imm = #-0x4

08000022 <Reset>:
 8000022:      	push	{r7, lr}
 8000024:      	mov	r7, sp
 8000026:      	bl	0x8000018 <main>        @ imm = #-0x12
```

To make it type safe, we generate a macro in the rt

```
// required so that the macro can be used when this package is imported
#[macro_export]
macro_rules! entry {
    // match like construct. $path:path essentially matches whatever we pass.
    ($path:path) => {
        #[export_name = "main"]
        pub unsafe fn __main() -> ! {
        // here, we try to assign whatever we receive to a function f where f returns nothing. If
        // that fails, it means that path was not divergent (it returned sth)
            let f: fn() -> ! = $path;
            f()
        }
    }
}
```
The program then is changed:
```
#![no_std]
#![no_main]

use rt::entry;

entry!(main);
fn main() -> ! {
    let _x = 42;

    loop{}
}

```
The function now is not public anymore and entry is called to check for mains divergent condition

Finally, some modifications are required so that we can run statics:
```
  .bss :
  {
    _sbss = .;
    *(.bss .bss.*);
    _ebss = .;
  } > RAM
  /*Set data (static variables with non zero initial value)*/
  .data : AT(ADDR(.rodata) + SIZEOF(.rodata))
  {
    _sdata = .;
    *(.data .data.*);
    _edata = .;
  } > RAM
 /*Static values in Flash*/
  _sidata = LOADADDR(.data);

```
Those underscore names define the borders of .bss and .data. They will allow us to erase the memory in the code of our rt
```
pub unsafe extern "C" fn Reset() -> ! {
    // Initialize RAM
    extern "C" {
        static mut _sbss: u8;
        static mut _ebss: u8;
        
        static mut _sdata: u8;
        static mut _edata: u8;
        static _sidata: u8;
    }

    let count = &_ebss as *const u8 as usize - &_sbss as *const u8 as usize;
    ptr::write_bytes(&mut _sbss as *mut u8, 0, count);

    let count = &_edata as *const u8 as usize - &_sdata as *const u8 as usize;
    ptr::copy_nonoverlapping(&_sidata as *const u8, &mut _sdata as *mut u8, count);
    // All this section before allows to use static variables in the code, because they are copied  
    extern "Rust" {
        fn main() -> !;
    }

    main()
}


```
You will see that we first set all bytes in bss to zero, and then, we copy the content of sidata (flash statics) to data (ram statics.) This will allow for static variables to work.
You will also see that we are not assigning any values to those variables ourselves, the linker script is doing that for us. (actually, we are using only addresses)