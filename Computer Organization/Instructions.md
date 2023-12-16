![[Pasted image 20230802173656.png]] 

`add a,b,c` - adds b and c stored into a
`sub a,b,c` - subtracts c from b stored in a
`lw a, h` - grabs word from memory stores in a
`sw a, h` - stores word a in memory labelled h
`addi a,$0,5` - add immediately 5 to a 
`li $v0,4` - load immediate value to return register
`la $a0,mess` - load address into argument register
`add $a0,$0,$s0` - add a result of arithmetic into argument register for printing
`syscall` - execute instruction indicated by value into $v0
`jr $ra` - jump to return address (should be save prior)
`addu $s7, $0, $ra` - saves return address into register to be jumped to later

## Flow Control

`beq reg1,reg2,l1` - branch if equal
`bne reg1,reg2,l1` - branch if not equal
`blt reg1,reg2,less` - pseudo instruction - branch if less than (combines below instructions)
	`slt signalreg,reg1,reg2` - sets signal reg true if reg1 < reg2
	`bne signalreg,$zero,Less` - branch to less if signal is true


## If-then-else statement

![[Pasted image 20230802194006.png]]


## For Looping

![[Pasted image 20230802194053.png]]


## While Looping

![[Pasted image 20230802194512.png]]

## Switch

![[Pasted image 20230802195028.png]]