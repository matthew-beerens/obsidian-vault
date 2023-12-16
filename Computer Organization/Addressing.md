
near label vs far label
=
branch instructions can only handle 64k for address jumping from the current instruction (PC) 32k in each direction
jump instructions can handle 64 million for address jumping from PC
PC register is relative addressing
the main label is the starting point of a program

when jumping distance is too far for branch instruction, use a jump instruction instead

![[Pasted image 20230821165411.png]]