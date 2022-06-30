# Most-used Instructions

## Basic Instructions

### NOP

NOP - No Operation! No registers, no values, no nothin'! used there to pad/align bytes, or to delay time. in exploit development used for allocating space for the shellcode.

### PUSH

Push Word, Doubleword or Quadword onto the Stack " Can either be an immediate (a numeric constant), or the value in a register" The push instruction automatically decrements the stack pointer, esp, by 4 because the value in the register is sent to stack so the stack pointer now points to 4 bytes lower in the address space. push doesn't remove the content of the register, just copies it.

![](<../../.gitbook/assets/image (134).png>)

### POP

Pop a Value from the Stack Take a DWORD off the stack, put it in a register, and increment esp by 4 because unlike push, pop will take a value off the stack so the address will become undefined because the esp now points to a higher memory address, again pop doesn't remove the value from the stack but because the esp is decremented we cant see the value in the next lower address.

![](<../../.gitbook/assets/image (140).png>)

the following example shows a demo of using PUSH and POP to swap the value of 2 variables:

```
section .text
global main:
main:
mov    eax, 1    ; move 1 to eax
mov    ebx, 2    ; mov 2 to ebx
push    eax      ; push the value of eax to the stack, esp decremented
push    ebx     ; push the value of ebx to the stack (after eax value), esp decrimented
pop    eax     ; pop the last pushed value from stack to eax (2), esp incremented
pop    ebx    ; pop the last pushed value from stack to ebx (1), esp incremented again
```

### CALL

CALL's job is to transfer control to a different function, in a way that control can later be resumed where it left off. First it pushes the address of the next instruction onto the stack – For use by RET for when the procedure is done. Then it changes eip to the address given in the instruction. Destination address can be specified in multiple ways.

### RET

#### Return from Procedure, it has two forms:

Pop the top of the stack into eip (remember pop increments stack pointer), In this form, the instruction is just written as “ret”. Typically used by cdecl functions

Pop the top of the stack into eip and add a constant number of bytes to esp. In this form, the instruction is written as “ret 0x8”, or “ret 0x20”, etc. Typically used by stdcall functions.

### MOV

Can move:

* register to register
* memory to register
* register to memory
* immediate to register
* immediate to memory

{% hint style="info" %}
Intel in Intel ISA you can never move or change values directly in memory. MOV instruction can not move from one memory address to another memory address.
{% endhint %}

MOV can move data in one of the following forms:

```
mov eax, ebx
mov eax, [ebx]
mov eax, [ebx+ecx*X] --> (X=1, 2, 4, 8)
mov eax, [ebx+ecx*X+Y] --> (Y= one byte, 0-255 or 4 bytes, 0-2^32-1)
```

#### In Intel syntax, most of the time square brackets \[] means to treat the value within as a memory address, and fetch the value at that address (like dereferencing a pointer)

example:

```
section .text
global start:
_start:

; move immediate to register
mov	eax, 0xaaaaaaaa
mov	al, 0xbb
mov	ah, 0xcc
mov	ax, 0xdddd

mov	ebx, 0
mov	ecx, 0


; move register to register
mov	ebx, eax,
mov	cl, al
mov	ch, ah
mov	cx, ax

mov	eax, 0
mov	ebx, 0
mov	ecx, 0

; move from memory to register
mov	al, [sample]
mov	ah, [sample +1]
mov	bx, [sample]
mov	ecx, [sample]

; move from register into memory
mov	eax, 0x33445566
mov	byte [sample], al
mov	word [sample], ax
mov	dword [sample],eax

; move immediate value to register
mov	dword [sample], 0x33445566

; lea
lea eax, [sample]
lea ebx, [eax]

;xchg
mov	eax, 0x11223344
mov	ebx, 0xaabbccdd

xchg eax, ebx

; exit
mov	eax, 1
mov	ebx, 0
int	80h
```

### LEA - Load Effective Address

Frequently used with pointer arithmetic, sometimes for just arithmetic in general. Uses the r/m32 form but is the exception to the rule that the square brackets \[ ] syntax means dereference (“value at”)

example:

```
ebx = 0x2, edx = 0x1000
lea eax, [edx+ebx*2]
eax = 0x1004, not the value at 0x1004
```

## Arithmetic Instructions

### The INC Instruction

is used for incrementing an operand by one. It works on a single operand that can be either in a register or in memory.

```
INC destination
INC EBX
```

### The DEC Instruction

used for decrementing an operand by one. It works on a single operand that can be either in a register or in memory

```
DEC destination
```

### ADD and SUB

These instructions are used for performing simple addition/subtraction of binary data in byte, word and doubleword size, i.e., for adding or subtracting 8-bit, 16-bit or 32-bit operands respectively.

```
ADD/SUB destination, source
```

### MUL/IMUL

There are two instructions for multiplying binary data. The MUL (Multiply) instruction handles unsigned data and the IMUL (Integer Multiply) handles signed data. Both instructions affect the Carry and Overflow flag.

```
MUL/IMUL multiplier
```

![](<../../.gitbook/assets/image (142) (1).png>)

example:

```
section .data
msg db "result: "
len equ $ -msg

section .bss
result resb 1

section .test
global _start

_start:
mov	al, '3'		; put ascii '3' in al
sub	al, '0'		; convert ascii '3' to decimal 3
mov	bl, '2'
sub	bl, '0'

mul	bl		; multiply the value in AL by the value in BL ( 3 = 2 )
add	al, '0'		; convert the value back to ascii for printing
mov	[result], al	; store in 'result'

; print the message
mov	eax, 4
mov	ebx, 1
mov	edx, len
mov	ecx, msg
int	80h

; print the result
mov     eax, 4
mov     ebx, 1
mov     edx, 1
mov     ecx, result
int     80h

; exit
mov	eax, 1
mov	ebx, 0
int	80h
```

### DIV/IDIV

The division operation generates two elements - a quotient and a remainder. In case of multiplication, overflow does not occur because double-length registers are used to keep the product. However, in case of division, overflow may occur. The processor generates an interrupt if overflow occurs. The DIV (Divide) instruction is used or unsigned data and the IDIV (Integer Divide) is used for signed data

```
DIV/IDIV divisor
```

![](<../../.gitbook/assets/image (145).png>)

![](<../../.gitbook/assets/image (141) (1).png>)

example:

```
section .data
msg db "result: "
len equ $ - msg

section .bss
result resb 1

section .text
global _start

_start:

mov	ax, '8'		; put ascii '8' in ax
sub	ax, '0'		; convert ascii '8' to decimal 8
mov	bl, '2'
sub	bl, '0'
div	bl		; devide the value in AX (8) by the value in BL (2), result goes into AX
add	ax, '0'		; convert the result back to ascii for printing
mov	[result], ax	; put the result in variable

; print the message
mov	ecx, msg
mov	eax, 4
mov	ebx, 1
mov	edx, len
int	80h

; print the result
mov	eax, 4
mov	ebx, 1
mov	edx, 1
mov	ecx, result
int	80h

; exit
mov	eax, 1
mov	ebx, 0
int	80h
```

## Logical Instructions

we really don't care about them here :)

![](<../../.gitbook/assets/image (144) (1).png>)
