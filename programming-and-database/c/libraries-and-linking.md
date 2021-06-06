---
description: >-
  a library is a collection (group) of header files and implementation files
  exposed for use by other programs
---

# Libraries & Linking

interface expressed in a header file implementation expressed in a .c file

a library consists of one or more object files which consist of object code.

C functions that can be shared by more than one application should be broken out of an applications source code and compiled and bundled into a library.

libraries may call functions in other libraries such as the standard C or math libraries to do various tasks

a programmer has the interface file \(headers\) to figure out how to use a library



## Linking

• when a C program is compiled the compiler generates object code \(.o or .obj\)

• after generating the object code the compiler also invokes the linker.

• linking is the processes of codding and combining multiple object files in order to create a single executable file

• one of the main tasks for linker is to make the code of library functions available to your program

### a linker can accomplish this task in two ways

1.  by copying the code of library function to your object code 
2.  by making some arrangements so that the complete code of library functions is not copied but made available at run time.

####  • static linking is the result of the linker making copy of all used library functions to the executable file.

#### • dynamic linking does not require the code to be copied, it is done by just placing the name of the library in the binary file.

{% hint style="info" %}
linking happens when program is run, when both the binary file and the library are in memory.
{% endhint %}

## Static linking

when you link your application to another library at compile time the library code is part of your application

### the advantages of static linking:

1. no symbol resolution at runtime
2. every piece of the library is part of the binary image \(executable\)
3. once everything is bundled into your application you don't have to worry that the client will have the right library \(and version\) available on their system.

static linking creates larger binary files that utilize more disk space and main memory

once the library is linked and the program is loaded there is no way of changing any of the library code without rebuilding the whole program

more time consuming to fix the whole program



## Dynamic linking

dynamic linking is the more modern approach and has the advantage of a much smaller executable size.

dynamic linking trades off more efficient use of the disk and a quicker linking phase for a small runtime penalty

### dynamic linking helps overall performance in many ways:

1. it saves on disk and virtual memory, libraries are mapped in to the process when needed.
2. ​​all executables dynamically linked to a particular library share a single copy of the library at runtime
3. ensure that libraries mapped into memory are shared by all processes using them
4. provides better I/O and swap space utilization and is sparing of physical memory, improving overall system throughput

## Library types

there are 2 types which can be created

### Static libraries:

 • uses static linking \(compile-time, becomes pat of the application\) • each process gets its own copy of the code and data • known as an archive

### Shared object files / dynamic libraries:

 • dynamically linked at run time • shared objects are not included into the executable component but are tied to the execution • code shared data is specific to each process

{% hint style="info" %}
dynamic libraries are called lib sometimes .so, in windows .dll static libraries are called lib something .a or .lib object files are called .o, .obj in windows
{% endhint %}

## Creating a static library \(archive\)

a static library is an archive, a bunch of object files wrapped up into a single file, similar to a .zip or a .tar file, a file that contains other files.

its created and updated by the ar \(for archive\) utility, a program that takes files and stores them in a bigger file without regard to compression.

standard convention is to name static libraries lib.a for example:

```text
libc.a → standard C library
libm.a → mathematics functions
```

after creating an archive we can store it in a library directory:

```text
/usr/local/lib (system irectory)
/my_lib (your own directory)
```

first we create a directory for the project and another one for the library:

![](../../.gitbook/assets/1%20%287%29.png)

then we create the source code of our library in the lib directory:

```text
cd lib
nano main.c
```

![](../../.gitbook/assets/1%20%2817%29.png)

and we create a header file for the main.c source code:

![](../../.gitbook/assets/1%20%2812%29.png)

now we have to compile the library:

![](../../.gitbook/assets/1%20%2816%29.png)

now we go back to the project directory and create an app which is going to use this library

```text
nano main.c
```

![](../../.gitbook/assets/1%20%2813%29.png)

we are using double quotes in here because the library is static and can be found in the executable at the end so we don't need to look for it anywhere else

remember to type the right header file name otherwise it wont find the library

now we can compile the executable:

![](../../.gitbook/assets/1%20%2810%29.png)

the final step is to link the executable to our library:

![](../../.gitbook/assets/1%20%283%29.png)

finally we execute the file:

![](../../.gitbook/assets/1%20%285%29.png)

the compiler expects to find the libraries in certain directories like /usr/lib/

the compiler option -Lpathname is used to tell the linker a list of other directories in which to search for libraries

## Creating a dynamic library \(shared object\)

every program linked against this library shares the same one copy. contrast to static linking in which everyone is \(wastefully\) given their own copy of the contents of the library

a dynamically linked library \(shared object\) is created by the link editor, the name of the link editor is the command ld

standard convention is to name dynamic libraries lib.so \(shared object\)

we can use the ldd command on linux to list all of the shared objects for a given binary/executable

```text
ldd [name of the file]
```

create 2 directories for project and library files

create a main.c fime for the executable and a library just like the example for static lib:

first the library:

![](../../.gitbook/assets/1%20%2811%29.png)

![](../../.gitbook/assets/1%20%2815%29.png)

then the executable:

![](../../.gitbook/assets/1%20%2814%29.png)

![](../../.gitbook/assets/1%20%289%29.png)

now we have to append a library path to the env LD\_LIBRARY\_PATH variable

![](../../.gitbook/assets/1%20%2818%29.png)

so now the executable will look into /root/.local/share/Trash/files/test/lib for the needed shared library .

## Library loading

**loading of a library is of two types:** 

**dynamic loading**

 **static loading**

### these are different from static/dynamic linking:

linking tells us where we can find a function it has nothing to do with loading that function

whenever we run the executable the loader will try to resolve against all the symbols at program start up. loading will store every required library into memory, along with our executable itself.

if we didn't want to link libraries at compile time \(via copy or sharing\) and load symbols at program start-up time we can instead load them ourselves as needed during runtime.

instead of a predefined dependency on a library we could make its presence optional and adjust our programs functionality accordingly.

### dynamic loading

dynamic loading is a mechanism by which a computer program can at run time load a library or other binary into memory. can retrieve the address of functions and variables contained in the library can execute those functions or access those variables can unload the library from memory

#### there are 3 mechanisms by which a computer program can use some other software:

1. static linking
2. dynamic linking
3. dynamic loading

unlike static/dynamic linking, dynamic loading allows a computer program to start up in the absence of these libraries and can discover available libraries and potentially gain additional functionality

the main difference of dynamic linking to a shared object is that the libraries are not automatically loaded at program startup

dynamic loading is useful for implementing plugins or modules, they permit waiting to load the plugin until it is needed.

the pluggable authentication modules \(PAM\) sustem uses DL libraries to permit admins to config and reconfig authentication



## Dynamic loading API

there is an API for opening a library, looking up symbols handling errors and closing the library.

we need to include the header file  &lt;dlfcn.h&gt;

if the libraries depend on each other \(e.g: x depends on y\) we need to load y before x

#### dlopen\(\)

opens a library and prepares it for use

```text
void * dlopen(const char *filename, int flag);
```

if filename begins with "/" \( an absolute path\), dlopen\(\) will just try to use it, otherwise it will search for the library in the LD\_LIBRARY\_PATH env and other directories

the value of flag must be **either RTLD\_LAZY or RTL\_NOW**

**RTLD\_LAZY** means to resolve all undefined symbols as code from the dynamic library is executed

RTL\_NOW means to resolve all undefined symbols before dlopen\(\) returns and fail if this cannot be done \(used if debugging\)

the return value of dlopen\(\) is a "handle" that is used by the other DL librariy routines. this will return NULL if the attempt to load does not succeed \( should always check for this\)

if the same library is loaded more than once with dlopen\(\) the same file handle is returned

#### **dlerror\(\)**

errors can be reported by calling dlerror\(\) returns a string describing the error from the last call to dlopen\(\), dlsym\(\) or dlclose\(\)

#### **dlsym\(\)**

the main routine for using a DL library is the dlsym\(\) function looks up the value of a symbol in a given \(opened\) library

```text
void * dlsym(void *handle, char char * symbol);
```

the handle is the value returned from dlopen and symbol is a NIL-terminated string do not store the result of dlsym\(\) into a void pointer you will have to cast it each time you use it \(and youll give less information to other people trying to maintain the program\)

#### **dlclose\(\)**

the converse of dlopen\(\) closes a DL library

example:

to find library paths in linux look in /lib/x86\_64-linux-gnu here we use math system library:

```text
#include <stdlib.h>
#include <stdio.h>
#include <dlfcn.h>

int main(){

    void *handle = NULL;
    double  (*cosine)(double) = NULL;
    char *error = NULL;

    handle = dlopen("/usr/lib/x86_64-linux-gnu/libm.so.6",RTLD_LAZY);

    if (! handle){
        fputs(dlerror(),stderr);
        exit(1);

    }

    dlerror();

    cosine = dlsym(handle,"cos");

    if ((error = dlerror()) != NULL){
        fputs(error,stderr);
        exit(1);
    }

    printf("%f\n",(*cosine)(2.0));

    dlclose(handle);

        return 0;
}
```

compile the code:

```text
gcc -o main main.c -ldl
```







