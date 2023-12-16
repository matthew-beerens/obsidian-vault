
![[Pasted image 20230802175714.png]]

methods (index * 4 will give correct address of array item):
`a: .word 0,5,-2,13,3,7,9,-6,4`
`la $s1,a` - load address of array into s0
`lw $s0, 4($s1)` - get the second element of an array stored in s1 and store in s0 = 5

variants of syntax that can be used

![[Pasted image 20230802180859.png]]

![[Pasted image 20230802184534.png]]