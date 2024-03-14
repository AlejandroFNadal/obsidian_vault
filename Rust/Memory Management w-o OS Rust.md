In Cortex M devices, we need a vector table at the start of the code memory region.
The vector table is an array of function pointers. The first must be a pointer to the Stack
The second, the pointer to the first function that is executed.

```
#![no_main]
#![no_std]

use core::panic::PanicInfo;
#[no_mangle]
pub unsafe extern "C" fn Reset() -> ! {
    let _x = 42;

    loop {}
}

// Reset Vector, pointer into the reset handler
#[link_section = ".vector_table.reset_vector"]
#[no_mangle]
pub static RESET_VECTOR: unsafe extern "C" fn() -> ! = Reset;

#[panic_handler]
fn panic(_panic: &PanicInfo<'_>) -> ! {
    loop {}
}
```

The linker with detailed comments:
```
/*STM32H747xI*/
MEMORY
{
  FLASH : ORIGIN = 0x08000000, LENGTH = 2M
  /*Can this be expanded? Technically it has 1024K*/
  RAM : ORIGIN = 0x20000000, LENGTH = 128K
}

ENTRY(Reset);
EXTERN(RESET_VECTOR);

SECTIONS
{
  .vector_table ORIGIN(FLASH):
  {
    /* STACK POINTER LOCATION: It descends, that is why it is at the end*/
    /* > FLASH means inserted into FLASH*/
    LONG(ORIGIN(RAM) + LENGTH(RAM));
    /* Second entry: reset vector */
    KEEP(*(.vector_table.reset_vector));
  } > FLASH

  /*Include all input sections named .text and .text.* */
  .text :
  {
    *(.text .text.*);
  } > FLASH
}
```
It is also useful to modify the .cargo/config.toml so that the linker script is setup there:
```
[target.thumbv7em-none-eabihf]
rustflags = ["-C", "link-arg=-Tlink.x"]
[build]
target = "thumbv7em-none-eabihf"
```

To look at the format of the vector table:
```
cargo objdump --bin memory_layout -- -s --section .vector_table
```
This, gives me the following:
```
memory_layout:	file format elf32-littlearm
Contents of section .vector_table:
 8000000 00000220 19000008                    ... ....
```
Where we can see the pointer to the stack position (end address of RAM) and the pointer to the Reset function (little endian)

To see the disassembly:
```
cargo objdump --bin memory_layout -- -d --no-show-raw-insn
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.01s

memory_layout:	file format elf32-littlearm

Disassembly of section .text:

08000018 <Reset>:
 8000018:      	sub	sp, #0x4
 800001a:      	movs	r0, #0x2a
 800001c:      	str	r0, [sp]
 800001e:      	b	0x8000020 <Reset+0x8>   @ imm = #-0x2
 8000020:      	b	0x8000020 <Reset+0x8>   @ imm = #-0x4
```

This can be debugged:
```
gdb-multiarch -q memory_layout
Reading symbols from memory_layout...
(gdb) target remote :3333
Remote debugging using :3333
0x080177d6 in ?? ()
(gdb) monitor arm semihosting enable
semihosting is enabled
(gdb) load
Loading section .vector_table, size 0x8 lma 0x8000000
Loading section .ARM.exidx, size 0x10 lma 0x8000008
Loading section .text, size 0xa lma 0x8000018
Start address 0x08000018, load size 34
Transfer rate: 35 bytes/sec, 11 bytes/write.
(gdb) l
1	#![no_main]
2	#![no_std]
3	
4	use core::panic::PanicInfo;
5	#[no_mangle]
6	pub unsafe extern "C" fn Reset() -> ! {
7	    let _x = 42;
8	
9	    loop {}
10	}
(gdb) print/x $sp
$1 = 0x20016280
(gdb) s
halted: PC: 0x0800001a
halted: PC: 0x0800001c
memory_layout::Reset () at src/main.rs:7
7	    let _x = 42;
(gdb) print _x
$2 = 134218453
(gdb) s
halted: PC: 0x0800001e
9	    loop {}
(gdb) print _x
$3 = 42
(gdb) print &_x
$4 = (*mut i32) 0x2001fffc
```