# Intel IA-32 Environment

{% hint style="info" %}
most of these material come from Intel IA32 software development manual which is the best resource for learning the basics. chapters 2 to 7 of volume 1 will cover everything you need to know about the Intel structure. here i recap some of the key concepts.
{% endhint %}

## Memory

### Process Memory Layout

The general memory layout for a program executed in memory (a process) is like this:

![](<../../.gitbook/assets/image (119).png>)

a more detailed view will be like this, here you can see order of moving data to/from the stack:

![](<../../.gitbook/assets/image (105).png>)

###

### IA-32 Memory Addressing Modes

#### Flat memory model

Memory appears to a program as a single, continuous address space. This space is called a linear address space. Code, data, and stacks are all contained in this address space. Linear address space is byte addressable, with addresses running contiguously from 0 to 32 (if not in 64-bit mode). An address for any byte in linear address space is called a linear address.

#### Segmented memory model

Memory appears to a program as a group of independent address spaces called segments. Code, data, and stacks are typically contained in separate segments. To address a byte in a segment, a program issues a logical address. This consists of a segment selector and an offset (logical addresses are often referred to as far pointers). The segment selector identifies the segment to be accessed and the offset identifies a byte in the address space of the segment. Programs running on an IA-32 processor can address up to 16,383 segments of different sizes and types, and each segment can be as large as 2 32 bytes.

#### Real-address mode memory model

This is the memory model for the Intel 8086 processor. It is supported to provide compatibility with existing programs written to run on the Intel 8086 processor. The real-address mode uses a specific implementation of segmented memory in which the linear address space for the program and the operating system/executive consists of an array of segments of up to 64 KBytes in size each. The maximum size of the linear address space in real-address mode is 2 20 bytes.

![](<../../.gitbook/assets/image (109).png>)

### Segmentation

Segmentation provides a mechanism for dividing the processorʼs addressable memory space (called the linear address space) into smaller protected address spaces called segments.

![](<../../.gitbook/assets/image (115).png>)

To locate a byte in a particular segment, a logical address (also called a far pointer) must be provided. A logical address consists of a segment selector and an offset. The physical address space is defined as the range of addresses that the processor can generate on its address bus. Normally the physical address space is based on how much RAM you have installed, up to a maximum of 2^32 (4GB). But there is a mechanism (physical address extensions - PAE) which allows systems to access a space up to 2^36 (64GB). Linear address space is a flat 32 bit space. If paging is disabled, linear address space is mapped 1:1 to physical address space.

#### Segmentation is not optional. Segmentation translates logical addresses to linear addresses automatically in hardware by using table lookups. Logical address (also called a far pointer) = 16 bit segment selector + 32 bit offset. If paging is disabled, linear addresses map directly to physical addresses

![](<../../.gitbook/assets/image (111).png>)

![](<../../.gitbook/assets/image (106).png>)

### Segment Selectors

A segment selector is a 16 bit value held in a segment register. It is used to select an index for a segment descriptor from one of two tables:

* GDT - Global Descriptor Table - for use system-wide
* LDT - Local Descriptor Table - intended to be a table per-process and switched when the kernel switches between process contexts

### Paging and Virtual Memory

**With the flat or the segmented memory model, linear address space is mapped into the processor’s physical address space either directly or through paging**. When using direct mapping (paging disabled), each linear address has a one-to-one correspondence with a physical address. Linear addresses are sent out on the processor’s address lines without translation. **When using the IA-32 architecture’s paging mechanism (paging enabled), linear address space is divided into pages which are mapped to virtual memory.** The pages of virtual memory are then mapped as needed into physical memory. When an operating system or executive uses paging, the paging mechanism is transparent to an application program. All that the application sees is linear address space. In addition, IA-32 architecture’s paging mechanism includes extensions that support:

**• Physical Address Extensions (PAE)** to address physical address space greater than 4 GBytes.

**• Page Size Extensions (PSE)** to map linear address to physical address in 4-MBytes pages.

![](<../../.gitbook/assets/image (124).png>)

## CPU Operation Modes

#### in Intel IA32 architecture the CPU can operate in one of the following modes:

![](<../../.gitbook/assets/image (117).png>)

#### Protected mode :

This mode is the native state of the processor. Among the capabilities of protected mode is the ability to directly execute “real-address mode” 8086 software in a protected, multi-tasking environment. This feature is called virtual-8086 mode, although it is not actually a processor mode. Virtual-8086 mode is actually a protected mode attribute that can be enabled for any task.

#### Real-address mode :

This mode implements the programming environment of the Intel 8086 processor with extensions (such as the ability to switch to protected or system management mode). The processor is placed in real-address mode following power-up or a reset.

#### System management mode (SMM) :

This mode provides an operating system or executive with a transparent mechanism for implementing platform-specific functions such as power management and system security. The processor enters SMM when the external SMM interrupt pin (SMI#) is activated or an SMI is received from the advanced programmable interrupt controller (APIC). In SMM, the processor switches to a separate address space while saving the basic context of the currently running program or task. SMM-specific code may then be executed transparently. Upon returning from SMM, the processor is placed back into its state prior to the system management interrupt. SMM was introduced with the Intel386 ™ SL and Intel486 ™ SL processors and became a standard IA-32 feature with the Pentium processor family.

### Intel 64 Architecture (IA-32e) modes

* **Compatibility mode (sub-mode of IA-32e mode) :** Compatibility mode permits most legacy 16-bit and 32-bit applications to run without re-compilation under a 64-bit operating system. For brevity, the compatibility sub-mode is referred to as compatibility mode in IA-32 architecture. The execution environment of compati- bility mode is the same as described in Section 3.2. Compatibility mode also supports all of the privilege levels that are supported in 64-bit and protected modes. Legacy applications that run in Virtual 8086 mode or use hardware task management will not work in this mode. Compatibility mode is enabled by the operating system (OS) on a code segment basis. This means that a single 64-bit OS can support 64-bit applications running in 64-bit mode and support legacy 32-bit applications (not recompiled for 64-bits) running in compatibility mode. Compatibility mode is similar to 32-bit protected mode. Applications access only the first 4 GByte of linear- address space. Compatibility mode uses 16-bit and 32-bit address and operand sizes. Like protected mode, this mode allows applications to access physical memory greater than 4 GByte using PAE (Physical Address Exten- sions).
* **64-bit mode (sub-mode of IA-32e mode) :** This mode enables a 64-bit operating system to run applica- tions written to access 64-bit linear address space. For brevity, the 64-bit sub-mode is referred to as 64-bit mode in IA-32 architecture. 64-bit mode extends the number of general purpose registers and SIMD extension registers from 8 to 16. General purpose registers are widened to 64 bits. The mode also introduces a new opcode prefix (REX) to access the register extensions. See Section 3.2.1 for a detailed description. 64-bit mode is enabled by the operating system on a code-segment basis. Its default address size is 64 bits and its default operand size is 32 bits. The default operand size can be overridden on an instruction-by-instruction basis using a REX opcode prefix in conjunction with an operand size override prefix. REX prefixes allow a 64-bit operand to be specified when operating in 64-bit mode. By using this mechanism, many existing instructions have been promoted to allow the use of 64-bit registers and 64-bit addresses.

### Execution Environment for Non-64-bit Modes

an overview of Intel architecture in non-64 bit modes:

![](<../../.gitbook/assets/image (126).png>)

### 64-Bit Mode Execution Environment

![](<../../.gitbook/assets/image (121).png>)

## CISC vs. RISC

#### Intel is CISC - Complex Instruction Set Computer, other major architectures are typically RISC - Reduced Instruction Set Computer

![](<../../.gitbook/assets/image (125).png>)

## Endians

### Little Endian

0x12345678 stored in RAM “little end” first. The least significant byte of a word or larger is stored in the lowest address. E.g. 0x78563412 – Intel is Little Endian

### Big Endian

0x12345678 stored as is. – Network traffic is Big Endian – Almost anything other than Intel

{% hint style="info" %}
big and little endians are for storing in RAM, in CPU all addresses are stored as they are
{% endhint %}

![](<../../.gitbook/assets/image (110).png>)

## ​Registers

### General Purpose Registers (GPR)

![](<../../.gitbook/assets/image (127).png>)



|        Register       |        Name       |                                                                                        Use                                                                                        |
| :-------------------: | :---------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|          EAX          |    Accumulator    |                                                      Accumulator for operands and results data, Used in arithmetic operations                                                     |
|          EBX          |        Base       |                                                                         Pointer to data in the DS segment                                                                         |
|          ECX          |      Counter      |                                                                Counter for string, loop and shift/rotate operations                                                               |
|          EDX          |        Data       |                                                           I/O pointer, used in arithmetic operations and I/O operations.                                                          |
| **Stream Operations** |                   |                                                                                                                                                                                   |
|          ESI          |    Source Index   |               Pointer to data in the segment pointed to by the DS register; source pointer for string operations, Used as a pointer to a source in stream operations              |
|          EDI          | Destination Index | Pointer to data (or destination) in the segment pointed to by the ES register; destination pointer for string operations, Used as a pointer to a destination in stream operations |
|         Stack         |                   |                                                                                                                                                                                   |
|          ESP          |   Stack pointer   |                          Stack pointer (in the SS segment), Pointer to the top of the stack. As a general rule it should not be used for another purpose.                         |
|          EBP          |    Base Pointer   |                                               Base Pointer to data on the stack (in the SS segment), point to the base of the stack                                               |

When using data element sizes less than 64-bits (i.e., 32-bit, 16-bit, or 8-bit), the lower portion of the register can be accessed by using a different register name as shown in the table.

For example, when accessing the lower portions of the 64-bit rax register, the layout is as follows:

![](<../../.gitbook/assets/image (107).png>)

### Flags Register

The flags register is not meaningful as a unit rather it is bit wise significant and accordingly each bit is named separately. The bits not named are unused. The Intel FLAGS register has its bits organized as follows:

![](<../../.gitbook/assets/image (123).png>)

**(S) - The status flags** (bits 0, 2, 4, 6, 7, and 11) of the EFLAGS register indicate the results of arithmetic instructions, such as the ADD, SUB, MUL, and DIV instructions. The status flag functions are:

* **CF (bit 0) Carry flag** — Set if an arithmetic operation generates a carry or a borrow out of the most- significant bit of the result; cleared otherwise. This flag indicates an overflow condition for unsigned-integer arithmetic. It is also used in multiple-precision arithmetic.
* **PF (bit 2) Parity flag** — Set if the least-significant byte of the result contains an even number of 1 bits; cleared otherwise.
*   **AF (bit 4) Auxiliary Carry flag** — Set if an arithmetic operation generates a carry or a borrow out of bit 3 of the result; cleared otherwise. This flag is used in binary-coded decimal (BCD) arithmetic.

    \*\*\*\*
* **ZF (bit 6) Zero flag** — Set if the result is zero; cleared otherwise.
* **SF (bit 7) Sign flag** — Set equal to the most-significant bit of the result, which is the sign bit of a signed integer. (0 indicates a positive value and 1 indicates a negative value.)
* **OF (bit 11) Overflow flag** — Set if the integer result is too large a positive number or too small a negative number (excluding the sign-bit) to fit in the destination operand; cleared otherwise. This flag indicates an overflow condition for signed-integer (two’s complement) arithmetic. Of these status flags, only the CF flag can be modified directly, using the STC, CLC, and CMC instructions. Also the bit instructions (BT, BTS, BTR, and BTC) copy a specified bit into the CF flag.

***

**(C) - The direction/control flag** (DF, located in bit 10 of the EFLAGS register) controls string instructions (MOVS, CMPS, SCAS, LODS, and STOS). Setting the DF flag causes the string instructions to auto-decrement (to process strings from high addresses to low addresses). Clearing the DF flag causes the string instructions to auto-increment (process strings from low addresses to high addresses).The STD and CLD instructions set and clear the DF flag, respectively. The functions of the system flags are as follows:

* **TF (bit 8) Trap flag** — Set to enable single-step mode for debugging; clear to disable single-step mode.
* **IF (bit 9) Interrupt enable flag** — Controls the response of the processor to maskable interrupt requests. Set to respond to maskable interrupts; cleared to inhibit maskable interrupts.
* I**OPL (bits 12 and 13) I/O privilege level field** — Indicates the I/O privilege level of the currently running program or task. The current privilege level (CPL) of the currently running program or task must be less than or equal to the I/O privilege level to access the I/O address space. The POPF and IRET instructions can modify this field only when operating at a CPL of 0.
* **NT (bit 14) Nested task flag** — Controls the chaining of interrupted and called tasks. Set when the current task is linked to the previously executed task; cleared when the current task is not linked to another task. RF (bit 16) Resume flag — Controls the processor’s response to debug exceptions.
* **VM (bit 17) Virtual-8086 mode flag** — Set to enable virtual-8086 mode; clear to return to protected mode without virtual-8086 mode semantics.
* **AC (bit 18) Alignment check (or access control) flag** — If the AM bit is set in the CR0 register, align- ment checking of user-mode data accesses is enabled if and only if this flag is 1. If the SMAP bit is set in the CR4 register, explicit supervisor-mode data accesses to user-mode pages are allowed if and only if this bit is 1.
*   **VIF (bit 19) Virtual interrupt flag** — Virtual image of the IF flag. Used in conjunction with the VIP flag. (To use this flag and the VIP flag the virtual mode extensions are enabled by setting the VME flag in control register CR4.)

    ***
* **VIP (bit 20) Virtual interrupt pending flag** — Set to indicate that an interrupt is pending; clear when no interrupt is pending. (Software sets and clears this flag; the processor only reads it.) Used in conjunction with the VIF flag.
* **ID (bit 21) Identification flag** — The ability of a program to set or clear this flag indicates support for the CPUID instruction.

**(X) - The system flags** and IOPL field in the EFLAGS register control operating-system or executive operations. They should not be modified by application programs.

### XMM Registers

There are a set of dedicated registers used to support 64-bit and 32-bit floating-point operations and Single Instruction Multiple Data (SIMD) instructions. The SIMD instructions allow a single instruction to be applied simultaneously to multiple data items. Used effectively, this can result in a significant performance increase. Typical applications include some graphics processing and digital signal processing. The XMM registers as follows:

![](<../../.gitbook/assets/image (122).png>)

Note, some of the more recent X86-64 processors support 256-bit XMM registers. This will not be an issue for the programs. Additionally, the XMM registers are used to support the Streaming SIMD Extensions (SSE).

### Segment Registers

The segment registers (CS, DS, SS, ES, FS, and GS) hold 16-bit segment selectors. A segment selector is a special pointer that identifies a segment in memory. To access a particular segment in memory, the segment selector for that segment must be present in the appropriate segment register.

#### Use of Segment Registers for Flat Memory Mode

![](<../../.gitbook/assets/image (114).png>)

#### Use of Segment Registers in Segmented Memory Model

![](<../../.gitbook/assets/image (112).png>)

#### Segment Registers in 64-Bit Mode:

In 64-bit mode: CS, DS, ES, SS are treated as if each segment base is 0, regardless of the value of the associated segment descriptor base. This creates a flat address space for code, data, and stack. FS and GS are exceptions. Both segment registers may be used as additional base registers in linear address calculations (in the addressing of local data and certain operating system data structures). Even though segmentation is generally disabled, segment register loads may cause the processor to perform segment access assists. During these activities, enabled processors will still perform most of the legacy checks on loaded values (even if the checks are not applicable in 64-bit mode). Such checks are needed because a segment register loaded in 64-bit mode may be used by an application running in compatibility mode. Limit checks for CS, DS, ES, SS, FS, and GS are disabled in 64-bit mode.

![](<../../.gitbook/assets/image (120).png>)

###

### Descriptor table registers

The global descriptor table register (GDTR) and interrupt descriptor table register (IDTR) expand to 10 bytes so that they can hold a full 64-bit base address. The local descriptor table register (LDTR) and the task register (TR) also expand to hold a full 64-bit base address.

### caller-save vs callee-save

**Caller-save registers ( eax, edx, ecx ) :** If the caller has anything in the registers that it cares about, the caller is in charge of saving the value before a call to a subroutine, and restoring the value after the call returns

**Callee-save registers ( ebp, ebx, esi, edi ) :** If the callee needs to use more registers than are saved by the caller, the callee is responsible for making sure the values are stored/restored

## Calling Conventions

How code calls a subroutine is compiler-dependent and configurable. but there are a few conventions.

### CDECL

“C declaration” is the most common calling convention. function parameters pushed onto stack right to left. it saves the old stack frame pointer and sets up a new stack frame, eax or edx:eax returns the result for primitive data types. **caller is responsible for cleaning up the stack**

### STDCALL

only see this convention used by Microsoft C++ code (WIN32 API). function parameters pushed onto stack right to left. it saves the old stack frame pointer and sets up a new stack frame. eax or edx:eax returns the result for primitive data types. **callee responsible for cleaning up any stack parameters it takes**

## DATA

### Data Types

![](<../../.gitbook/assets/image (113).png>)

### Data Storage Sizes

​The x86-64 architecture supports a specific set of data storage size elements, all based on powers of two. The supported storage sizes are as follows:

![](<../../.gitbook/assets/image (128).png>)

Lists or arrays (sets of memory) can be reserved in any of these types.

## Protection Ring

In Computer Science, the ordered protection domains are referred to as Protection Rings. These mechanisms help in improving fault tolerance and provide Computer Security. Operating Systems provide different levels to access resources. Rings are hierarchically arranged from most privileged to least privileged.

### Levels of Protection Ring

There are basically 4 levels ranging from 0 which is the most privileged to 3 which is least privileged. Most Operating Systems use level 0 as the kernel or executive and use level 3 for application programs. A resource that is accessible to level n is also accessible to levels 0 to n and the privilege levels are rings.

![](<../../.gitbook/assets/image (116).png>)
