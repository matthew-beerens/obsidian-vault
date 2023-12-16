
## .byte

can store a single character or single byte `.byte b1, b2, ... ,bn`

## .word 

store 4 bytes

words are used to simulate simulate an integer type when neeed as mips is not a native data type in mips

you can assign a word to a label like so

`myword: .word 15` - strored integer at myword address space

## .space

allocated n byte of space in memory `.space n`

## .ascii/.asciiz

store a string in memory, asciiz has a null byte at the end 
`.asciiz "string"`

## .globl

allows other files to use this varibles, allows access from other files

## .kdata/.ktext

data and text usage for privileged kernel mode 