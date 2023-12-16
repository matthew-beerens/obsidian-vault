
registers are faster than memory
- smaller is faster
- registers are faster to access and easier to use

![[Pasted image 20230725150046.png]]

## Register spilling

if we have more variables than registers : spilling registers

we can move data from register to the stack, and then retrieve again when register is free
this takes time and can cause latency 

register spilling must be reduced as much as possible

this requires compiler to support effective usage of registers

