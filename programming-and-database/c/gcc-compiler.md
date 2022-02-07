# GCC Compiler

#### GCC (upper case) refers to the GNU Compiler Collection. This is an open source compiler suite which include compilers for C, C++, Objective C, Fortran, Ada, Go and Java. gcc (lower case) is the C compiler in the GNU Compiler Collection. Historically GCC and gcc have been used interchangeably, but efforts are being made to separate the two terms as GCC contains tools to compile more than C.

#### The GCC project has detailed documentation at [https://gcc.gnu.org](https://gcc.gnu.org) which document installation, general usage, and every command line option. Please refer to the official GCC documentation on any question not answered here.

## Compilation Steps

C is a compiled language. Its source code is written using any editor of a programmer’s choice in the form of a text file, then it has to be compiled into machine code. C source files are by convention named with **.c extension** and we use the command “gcc” to compile C source files. (GCC stands for GNU Compiler Collection and it is a compiler system produced by the GNU Project.)

### 1) Preprocessing

Preprocessing is the first step. The preprocessor obeys commands that begin with # (known as directives) by:

* removing comments
* expanding macros
* expanding included files

If you included a header file such as #include \<stdio.h>, it will look for the stdio.h file and copy the header file into the source code file.

The preprocessor also generates macro code and replaces symbolic constants defined using #define with their values.

### 2) **Compiling:**

Compiling is the second step. It takes the output of the preprocessor and generates assembly language, an intermediate human readable language, specific to the target processor.

### 3) Assembly

Assembly is the third step of compilation. The assembler will convert the assembly code into pure binary code or machine code (zeros and ones). This code is also known as object code.

### 4) Linking

Linking is the final step of compilation. The linker merges all the object code from multiple modules into a single one. If we are using a function from libraries, linker will link our code with that library function code.

In static linking, the linker makes a copy of all used library functions to the executable file. In dynamic linking, the code is not copied, it is done by just placing the name of the library in the binary file.

## Basic GCC Syntax

here is a basic usage of gcc for compiling a non-threaded C code with no external linking and libraries (except for standard C libraries ) on linux systems:

```
gcc app.c -o app
```

this will do all the steps discussed above and generate an executable (ELF) file named app.

## Useful GCC Options

these are some of the most used gcc compilation options that you will usually use, for more info and options refer to gcc official documentation.

```
-Wall → enables all warnings

-E → shows the code that preprocessors use

-S → only assembly code

-save-temp → save all the .o files (produce everything)

-l → link to a file

m → for math library while using -l (link to math library)

-fPIC → create a shared library ( then use -shared -o prog.so prog.o)

-g → generates debugging info

-v → verbose on every step

-ansi → make sure thing are compatible with the ansi standard

-funsigned-char →  char type is threated as unsigned type

-fsigned-char → the aposite of the above options

-DMY_MACRO → define MY_MACRO 

-Werror → all warnings will be threated as errors
```

## Using a Config File

to avoid specifying the options each time you want to compile a file, you can specify a configuration file including all the options like this:

```
@ → use with a file name containing gcc options for example a file with this content:

opt_file:

-Wall -g 

use it like this:

gcc test.c @opt_file → use the options in opt_file  
```

## C and Disassembly

​**The gcc option -O enables different levels of optimization. Use -O0 to disable them and use -S to output assembly. -O3 is the highest level of optimization.**

Starting with gcc 4.8 the optimization level -Og is available. It enables optimizations that do not interfere with debugging and is the recommended default for the standard edit-compile-debug cycle.

**To change the dialect of the assembly to either intel or att use -masm=intel or -masm=att.**

**You can also enable certain optimizations manually with -fname.**

## ​Optimization Flags

use gcc with -Q --help=optimizers to find the exact set of optimizations at each level

```
-O → tries to reduce code size and execution time without performing any optimizations that make a great deal of compilation time

-O1 → takes more time and a lot more memory

-O2 → optimize even more. performs nearlly all supported optimizations that do not involve a spade-speed tradeoff

-O3 → turns on all optimizations

-Ofast → turns on everything including the optimizations that are not valid for all standard-compilant programs

-Og → optimize debugging experience.
```

## GCC Environment Variables

PATH → for searching the executables and run-time shared libraries (.dll, .so)

CPATH → for searching the include-paths for headers searched after paths specified in -l options C\_INCLUDE\_PATH can be used to specify C headers if the particular language was indicated in pre-processing

LIBRARY\_PATH → for searching library-paths for link libraries. it is searching after paths specified in -L options
