
MIPs is reduced instruction set computing language
also knows as load/store architecture
all instructions are simple and the same length

in RISC data can only be operated on in the registers

### typical operations

- data movement 
	- load (memory to reg)
	- store (reg to memory)
	- memory to memory move
	- reg to reg move
	- input (from I/O device)
	- output (to I/O device)
	- push, pop (to/from stack)
- arithmetic
	- add
	- subtract
	- multiply
	- divide integer (binary + decimal) or FP
- shift
	- left/right (logical/arithmetic)
	- rotate (left/right)
- logical
	- not
	- and
	- or
	- xor
	- set
	- clear
- control
	- conditional
	- unconditional
- subroutine linkage
	- call
	- return
- interrupt
	- trap
	- return
- synchronization 
	- test & set (atomic read-mod-write)
- string
	- search
	- translate
- graphics
	- parrallel subword ops (4 16bit add)