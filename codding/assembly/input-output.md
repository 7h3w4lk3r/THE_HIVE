# input / output

## Linux System Calls

unlike C/C++ in assembly we use the system call number instead of system call functions. here the basic linux system calls for x86 architecture:

![](<../../.gitbook/assets/image (133).png>)

for a full list of linux system calls:

[http://asm.sourceforge.net/syscall.html](http://asm.sourceforge.net/syscall.html)\


{% hint style="info" %}
in assembly input/output (I/O) is done by using multiple registers with sys\_read and sys\_write system calls
{% endhint %}

## Output

#### the process of printing output in console is:

1. store the message/variable in ECX
2. store the length in EDX
3. store sys\_write syscall number in EAX ( number 4 in x86 )
4. store the I/O type in EBX ( number 1 for stdout )
5. execute the syscall to write the output in the console ( 'int 80h' in x86)

example:

```
section .data
msg db "sum: "
len equ $ - msg

section .bss
sum resb 1

section .text
global _start

_start:
mov	eax, '3'	; put ascii '3' into eax
sub	eax, '0'	; convert ascii '3' to decimal 3
mov	ebx, '4'
sub	ebx, '0'

; add two values
add	eax, ebx
add	eax, '0'	; convert eax value back to ascii code for printing
mov	[sum], eax	; store the value in 'sum'

; print the message
mov	ecx,msg
mov	edx, len
mov	ebx,1
mov	eax, 4
int	80h

; print the sum
mov	ecx, sum
mov	edx, 1
mov	ebx, 1
mov	eax, 4
int	80h

mov	eax, 1
mov	ebx, 0
int	80h

```

## Input

#### the process of taking input from user:

1. declare uninitialized variable/s in .bss section (to store the input)
2. store sys\_read syscall numer to EAX (number 3 for x86)
3. store I/O type in EBX (number 0 for stdin )
4. move uninitialized variable to ECX to use as input
5. store length of variable in EDX
6. execute sys\_read system call (int 80h)

example:

```
; asks two digits from the user ,stores in EAX and EBX, adds the values, stores and prints the sum.
; remember these:
; for converting ascii to decimal --> subtract '0'
; for converting decimal to ascii --> add '0'


; const values

SYS_EXIT equ 1	; syscall for exit
SYS_READ equ 3	; syscall for reading input
SYS_WRITE equ 4	; syscall for writing output
STDIN equ 0	; ebx value for taking input
STDOUT equ 1	; ebx value for printing output


section .data

msg1 db "enter first number: "
len1 equ $ - msg1

msg2 db "enter second number: "
len2 equ $ - msg2

msg3 db "sum: "
len3 equ $ - msg3

section .bss
num1 resb 2		; first number
num2 resb 2		; second number
result resb 1		; result of sum (all variable types are bytes)

section .text
global _start

_start:

; print first message
mov	eax, SYS_WRITE
mov	ebx, STDOUT
mov	ecx, msg1
mov	edx, len1
int	80h

; take first input
mov	eax, SYS_READ
mov	ebx, STDIN
mov	ecx, num1
mov	edx, 2
int	80h

; print second message
mov     eax, SYS_WRITE
mov     ebx, STDOUT
mov     ecx, msg2
mov     edx, len2
int     80h

; take second input
mov     eax, SYS_READ
mov     ebx, STDIN
mov     ecx, num2
mov     edx, 2
int     80h


; print sum message
mov     eax, SYS_WRITE
mov     ebx, STDOUT
mov     ecx, msg3
mov     edx, len3
int     80h

; move numbers to two registers for converting from character to a decimal number
mov	eax, [num1]
mov	ebx, [num2]

; subtracting ascii '0' to convert to decimal, because ascii values can be treated like integers
sub	eax, '0'
sub	ebx, '0'

; adding two values, doesnt mather which register you use as source
add	eax, ebx
add	eax, '0'	; convert decimal back to ascii code for printing

; store the sum in variable 'result'
mov	[result], eax

; print the sum
mov	eax, SYS_WRITE
mov	ebx, STDOUT
mov	ecx, result
mov	edx, 1
int	80h

; exit
mov	eax, 1
mov	ebx, 0
int	80h

```

