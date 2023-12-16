
## Major Components of a computer

- CPU (Processor)
	- control
	- datapath
		- ALU (Arithemtic / Logic Unit)
		- Register file (architecture registers)
		- Program Counter (PC)
		- Other special registers
- Memory
- I/O devices (I/O system)

These all connect to the system bus. as long as there is these three devices, it is a computer.

#### Abstraction:

![[Pasted image 20230725144318.png]]

#### Expanded Abstraction:

![[Pasted image 20230725144246.png]]

## MIPS Organization

I/O devices are mapped to memory in MIP arch

![[Pasted image 20230725145722.png]]

## CPU

a CPU is comprised of:
- Registers
- ALU
- Control unit
	- sequencing logic
	- Control Unit Registers and Decoders
	- Control Memory

These are connected via the internal bus


## Memory Abstraction

a cell in a cpu memory is 1 byte (8 bits)
memory addresses can be tied to labels


## MIPS

- .data organizes variables
- .text organizes the code statements