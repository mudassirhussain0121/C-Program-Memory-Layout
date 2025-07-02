# Memory Layout of C Program

To analyze the memory layout of a C program effectively, a foundational understanding of the microcontroller's memory architecture is essential.
For reference, the memory map of the ARM Cortex-M4 microcontroller is provided below. This diagram illustrates the address space distribution:

## Cortex-M4 Memory map

Mirco-controller memory map divided into several different region and each region has specific purpose or function.

```sh

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




