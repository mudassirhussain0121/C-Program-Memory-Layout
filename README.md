# Memory Layout of C Program

To analyze the memory layout of a C program effectively, a foundational understanding of the microcontroller's memory architecture is essential.
For reference, the memory map of the ARM Cortex-M4 microcontroller is provided below. This diagram illustrates the address space distribution:

## Cortex-M4 Memory map

Mirco-controller memory map divided into several different region and each region has specific purpose or function.

```
 	0xFFFFFFFF   ----------------------------------
  	            |                                  |
  	            |              SYSTEM              |
  	            |                                  |
	0xE0100000  |----------------------------------|
  	            |  PRIVATE PERIPHERAL BUS EXTERNAL |
	0xE0040000  |----------------------------------|
  	            |  PRIVATE PERIPHERAL BUS INTERNAL |
	0xE0000000  |----------------------------------|
  	            |                                  |
  	            |                                  |
  	            |          EXTERNAL DEVICE         |
  	            |                                  |
  	            |                                  |
	0xA0000000  |----------------------------------|
  	            |                                  |
  	            |                                  |
  	            |           EXTERNAL RAM           |
  	            |                                  |
  	            |                                  |
	0x60000000  |----------------------------------|
  	            |                                  |
  	            |            PERIPHERAL            |
  	            |                                  |
	0x40000000  |----------------------------------|
  	            |                                  |
  	            |               SRAM               |
  	            |                                  |
	0x20000000  |----------------------------------|
  	            |                                  |
  	            |               CODE               |
  	            |                                  |
	0x00000000   ----------------------------------
```

- <ins>Code/Flash</ins>:
	- The Code/Flash region stores the executable firmware (program code) and read-only data.
	- This region is non-volatile, meaning it retains data even when power is removed.

- <ins>SRAM</ins>:
	- SRAM is memory region used for runtime data storage, including static variables, the heap, and the stack in C programs.
	- This region is volatile, meaning data in this region will be erased when power is removed.

- <ins>PERIPHERAL</ins>:
	- The Peripheral is region where hardware peripherals (e.g., GPIO, UART, ADC, Timers) are memory-mapped for software control.
	- Unlike RAM or Flash, this region does not store data but provides register-level access to on-chip peripherals.

- <ins>EXTERNAL RAM</ins>:
	- The External RAM (XRAM) region provides an interface for connecting addition external RAM, located outside the chip memory.
	- This is also volatile memory, it is optional expansion capability when on-chip RAM is insufficient.

- <ins>EXTERNAL DEVICE</ins>:
	- The External Device (XDEV) region provides an interface to connect external (locared outside the sysmtem chip) peripherals or memory mapped devices.
	- External devices supports including: displays (LCD, OLED), sensors, FPGAs, additional memory.

- <ins>PRIVATE PERIPHERAL BUS INTERNAL</ins>:
	- The PPB-Internal region is used by system for processor operations and this region is not typically used by general application code.
	- Purpose: Controls core-related functions (e.g., NVIC, SysTick, Debug).

- <ins>PRIVATE PERIPHERAL BUS EXTERNAL</ins>:
	- The PPB-External region is certain special external devices connected to your microcontroller. 
	- These devices aren't regular sensors or displays - they're privileged components that need direct access to the processor's inner workings.
	- Provides a private highway (fast access) between the CPU core and selected external devices.
	- Used for specialized components like:
		- External debug probes
		- Performance monitoring tools
		- Security co-processors
		- High-priority system components

- <ins>SYSTEM</ins>:
	- The SYSTEM region contains core system control registers and critical processor functions. 
	- This region manages the chip’s fundamental operations, such as clock control, power management, and debug features.


## C program Memory Model

In embedded C programming, the Code (Flash/ROM) and RAM (SRAM) regions are the most critical memory areas because they directly store and execute the application.

A typical memory map of a C program consists of the following sections:
- A text segment
- Initialized data segment
- Uninitialized data segment
- Stack
- Heap

```
	High Memory  ----------------------------------  |----------------------------------|
  	 Address    |                                  |                             |
  	            |              STACK               |                             |
  	            |         (Growns Downwards)       |                             |
  	            |                |                 |                             |
  	            |                v                 |                             |
	            |                                  |                             |
  	            |                                  |                             |
  	            |                                  |                             |
  	            |                ^                 |                             |
  	            |                |                 |
  	            |         (Growns Upward)          |                            RAM
  	            |               HEAP               |
  	            |                                  |                             |
	            |----------------------------------|                             |
  	            |                                  |                             |
  	            |     Uninitialized data (.bss)    |                             |
  	            |                                  |                             |
	            |----------------------------------| |--------------------|      |
  	            |                                  |                  |          |
  	            |     Initialized data (.data)     |                  |          |
  	            |                                  |                             |
	            |----------------------------------| |---------|    FLASH   |----------|
  	            |                                  |     |
  	            |           Text (.text)           |    CODE          |
  	            |                                  |     |            |
	Low Memory   ----------------------------------  |--------------------|
	 Address
```

1. Flash (Non-Volatile Memory)

- .text: Executable code (machine instructions).
- .rodata: Read-only constants (const variables).
- .data (initialized data): Initial values of global/static variables (copied to RAM at startup).

```
const int x = 5;    // → `.rodata` (Flash)
int y = 10;         // Initial value → `.data` (Flash), runtime value → RAM
void foo() { ... }  // → `.text` (Flash)
```

2. RAM (Volatile Memory)

- .data (runtime): Actual storage for initialized global/static variables (loaded from Flash at startup).
- .bss: Zero-initialized or uninitialized global/static variables (RAM only, no Flash footprint).
- Heap: Dynamically allocated memory (malloc/free).
- Stack: Local variables, function call frames.

```
int a;                  // → `.bss` (RAM, zero-initialized)
static int b = 20;      // Initial value → `.data` (Flash), runtime → RAM
int main() {
    int c = 30;         // → Stack (RAM)
    int *d = (int*)malloc(4*sizeof(int)); // → Heap (RAM)
}
```

- <ins>Code or Text Segment (.text)</ins>: (RO section)
	- The .text segment is the memory region containing:
		- Executable machine code (compiled program instructions or final binary)
		- Read-only program data (constants, literals)
	- Storage Location:
		- Resides in FLASH/ROM memory (non-volatile)

```
void main() { /* Function code */ }  // Executable instructions
const int config = 10;               // Read-only constant
char *str = "Hello";                 // String literal
```

- <ins>Initialized Data Segment (.data)</ins>: (RW section)
	- The .data section stores all explicitly initialized global and static variables in a C/C++ program. These variables are modifiable at runtime, read-write (RW) section.
	- Key characteristics is dual memory allocation:
		- Initial values are stored in Flash/ROM (included in program image)
		- At runtime values are occupy RAM (copied during startup)

```
int global_init = 10;         // → .data (RW)
static int static_init = 20;  // → .data (RW)
```

- <ins>Uninitialized Data Segment (.bss)</ins>: (ZI section)
	- Stores global and static variables that are either:
		- Explicitly initialized to zero (e.g., int x = 0;)
		- Implicitly uninitialized (e.g., int y; in global/static scope)
	- Key Behavior:
		- The compiler/linker allocates space for these variables in RAM but does not store their initial values in Flash (unlike .data).
		- At startup, the C runtime (CRT) initializes the entire .bss section to zero before main() executes.

```
int global_var;         // → .bss (RAM, implicitly zero-initialized)
static int static_var;  // → .bss (RAM, implicitly zero-initialized)

int main() {
  int local_var;        // → Stack (RAM, *uninitialized*, contains garbage!)
}  
```

- <ins>Downloadable Image Size (dec)</ins>:
	- In embedded systems, the total on-chip memory footprint of a program (often called dec or "downloadable image size") is the sum of three key sections:
		- .text (Code)
		- .data (Initialized Data)
		- .bss (Zero-Initialized Data)
	- This relationship is expressed as:
		- dec = .text + .data + .bss

- <ins>Summary</ins>:
	- .text : is your code, and constants (and also the vector table).
	- .data : is for initialized variables. This is count towards both RAM and FLASH. The initialized value allocates space in FLASH which then is copied from ROM to RAM in the startup code.
	- .bss  : is for the uninitialized data in RAM which is initialized with zero in the startup code.

```
- Flash (ROM) = .text (code) + .rodata (constants) + initial values of .data.
- RAM (SRAM)  = Runtime .data + .bss + heap + stack.
- Rule of Thumb:
    - Read-only?  → Flash.
    - Read-write? → RAM.
```
