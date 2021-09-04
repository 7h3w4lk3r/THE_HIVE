---
description: all initialized variables are defined in the .data section of code
---

# Variables and Data Types

## Data Types

![](../../.gitbook/assets/image%20%28143%29.png)

![](../../.gitbook/assets/image%20%28137%29.png)

## Numeric Values

Number values may be specified in decimal, hex, or octal.

**When specifying hex or base-16 values**, they are preceded with a 0x . For example, to specify 127 as hex, it would be 0x7f .

**When specifying octal, or-base-8 values**, they are followed by a q . For example,to specify 511 as octal, it would be 777q .

**The default radix \(base\) is decimal**, so no special notation is required for decimal \(base-10\) numbers.

## Characters, Strings and Integers

in assembly there is no need to define the variable type like C, instead the only thing that matters here is the variable length:

```text
section .data

string  db    "hello world!",0    --> 0 is the null terminator
number  dw    2535333
character    db    'c'
float    dd    23.2323r
```

## Arrays

Initialized arrays are defined with comma separated values.

![](../../.gitbook/assets/image%20%28135%29.png)

#### as you can see the 'arr' array has 3 elements in index 0 to 2. the 'arr' variable will point to the first element and for next elements we increment the the index. for example element number 1 will be arr+1.

## Defining Constants

Constants are defined with equ . The general format is:

```text
<name> equ <value>
SIZE equ 10000
```

#### The value of a constant cannot be changed during program execution.

The constants are substituted for their defined values during the assembly process. As such, a constant is not assigned a memory location. This makes the constant more flexible since it is not assigned a specific type/size \(byte, word, double-word, etc.\). The values are subject to the range limitations of the intended use.

example:

```text
section .data
msg1	db	"this is a test", 0xa
len1	equ	$ -msg1
msg2	db	"and this is another test :)", 0xa
len2	equ	$ -msg2
STDIN	equ	2		; for ebx when taking input
STDOUT	equ	1		; for ebx when printing output
SYS_EXIT	equ	1	; system call for sys_exit (kernel opcode 0)
SYS_WRITE	equ	4	; system call for sys_write (kernel opcode 4)
SYS_READ	equ	3	; system call for sys_read (kernel opcode 3)

section .text
global _start


_start:
; print first message
mov	eax, SYS_WRITE
mov	ebx, STDOUT
mov	ecx, msg1
mov	edx, len1
int	80h

; print second message
mov     eax, SYS_WRITE
mov     ebx, STDOUT
mov     ecx, msg2
mov     edx, len2
int     80h

; exit
mov	eax, SYS_EXIT
mov	ebx, 0
int	80h

```

## Length of Variables

#### when working with strings the length will be the length of the string minus one \(the null byte\):

```text
msg  db  “Hello, World!”,10,0
msgLen  equ   $-msg-1         → minus the NULL byte (0)
```

#### when working with integers and floats the length is equal to the length of the variable:

```text
num  db  100
numLen equ $-num
```

 the length calculation and declaration in the .data section will be constant when using 'equ'.

## Newline Character

we can use both hex and decimal representation of ASCII characters including the newline character:

```text
10 
0xa
0ah
```

example:

```text
msg    db    "hello world!",0ah    --> printing 'msg' will add a newline after
```

## Uninitialized Variables

Uninitialized data is declared in the "section .bss" section.

The general format is:

![](../../.gitbook/assets/image%20%28139%29.png)

Some simple examples include:

![](../../.gitbook/assets/image%20%28131%29.png)

## Multiple Initialization

The TIMES directive allows multiple initializations to the same value

```text
marks TIMES 9 DW 0   --> marks = 9 * '0'
```





