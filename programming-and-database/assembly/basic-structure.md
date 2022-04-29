# Basic Structure

## Assembly IDE

best cross-platform IDE for assembly is SASM which supports multiple assemblers and is available for linux, windows and mac. you can find it here:

{% embed url="https://github.com/Dman95/SASM" %}

if you are on linux, install it with your package manager:

```
apt install sasm
```

## Assemble, Link, Load

the process of assembling, linking and loading an assembly program is like this:

![](<../../.gitbook/assets/image (138).png>)

## Hello World

```
; hello world in nasm

; this section holds the declared variables, we can use 'segment' instead of 'section'
section .data
msg db 'hello world!', 0ah      ; assign msg variable with string, 0ah is the new-line character
len equ $ - msg                 ; assign the length of the msg to len variable

 ; this section contains the main code of the program and instructions
 section .text
 global _start              ; defined an entry point for the program

 _start:                    ; start of the entry point
    mov     edx, len        ; edx holds the length of the message for printing ( could use binary value>
    mov     ecx, msg        ; ecx holds the message
    mov     ebx, 1          ; 1 is for writing to STDOUT file
    mov     eax, 4          ; invokes SYS_WRITE (kernel opcode 4), eax executes the syscalls by opcode
    int     80h             ; execute the instructions, actually executes the opcode in eax

    mov     ebx, 0          ; return exit status 0 - indicating no errors
    mov     eax, 1          ; invokes SYS_EXIT (kernel opcode 1) to exit the program gracefully
    int     80h
```

## Structure of an Assembly Program

The following are the main parts of an assembly program:

* **section .data**
* **section .bss**
* **section .text**

{% hint style="info" %}
you can tell that this structure is related to the program execution stack in memory. so data section is for initialized variables, bss section is for uninitialized variables and text section is the program instructions.
{% endhint %}

**section .data -** In section .data , initialized data is declared and defined, in the following format:

```
<variable name>    <type>   <value>
```

section .data can also contain constants, which are values that cannot be changed in the program. They are declared in the following format:

```
<constant name>   equ   <value>
```

**section .bss** - The acronym bss stands for Block Started by Symbol , and its history goes back to the fifties, when it was part of assembly language developed for the IBM 704. In this section go the uninitialized variables. Space for uninitialized variables is declared in this section, in the following format:

```
<variable name>   <type>   <number>
```

section .text - this is the actual instructions of the program where the functions (including main ) are declared and defined. it should always start with an entry point which is defined right after the section .text definition like this:

```
section .text
global main:      --> define the program entry point
main:       --> declare main function     
```

{% hint style="info" %}
in NASM, you can use the keywords 'segment' and 'section' interchangeably. so section .text can be segment .text
{% endhint %}

here is a basic example of an assembly code with the 3 sections we talked about:

```
section .data            ; initialized data
msg db "sum: "
len equ $ - msg

section .bss             ; uninitialized data
sum resb 1

section .text            ; code section
global _start            ; define entry point

_start:                  ; declare 'start' function
mov     eax, '3'        
sub     eax, '0'        
mov     ebx, '4'
sub     ebx, '0'

; add two values
add     eax, ebx
add     eax, '0'        
mov     [sum], eax      

; print the message
mov     ecx,msg
mov     edx, len
mov     ebx,1
mov     eax, 4
int     80h

; print the sum
mov     ecx, sum
mov     edx, 1
mov     ebx, 1
mov     eax, 4
int     80h

; exit
mov     eax, 1
mov     ebx, 0
int     80h

```

you can compile and run this program with:

```
nasm -f elf app.asm
ld -m elf_i386 app.o
./a.out
```

{% hint style="info" %}
for the rest of this section we are going to use these commands to compile and run our x86 programs, remember that the syntax is different for x64.
{% endhint %}

{% embed url="https://filippo.io/linux-syscall-table" %}

## Comments

in all assemblers the single-line comment sign is a semicolon.

```
; this is a comment
```
