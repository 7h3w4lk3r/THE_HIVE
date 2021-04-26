# Basic Structure

## hello world in C

```text
#include <stdio.h>

int main()
{
    printf ("hello world");
    return 0;
}
```

## Detect the Version of C in use

```text
#include <stdio.h>

int main(int argc, char** argv) {

#if __STDC_VERSION__ >=  201710L
  printf("We are using C18!\n");
  
#elif __STDC_VERSION__ >= 201112L
  printf("We are using C11!\n");
  
#elif __STDC_VERSION__ >= 199901L
  printf("We are using C99!\n");
#else

  printf("We are using C89/C90!\n");
#endif
  return 0;
}
```

## Comments

```text
  /* multiline 
  *comment */
  
 // single line comment
```

## Taking command line arguments

```text
#include <stdio.h>

int main(int argc,char *argv[])

{ int numberOfArgs = argc;
 char *argument = argv[0];
 char *argument2 = argv[1];

 printf("number of arguments: %d\n",numberOfArgs);
 printf("argument number 1 is the programname: %s\n", argument);
 printf("argument2 is the command line argument %s\n",argument2);
 
 return 0;
}
```



##  NULL character and NULL

###  null character \0

a special character with the code value 0 is added to the end of each string to mark where it ends,a string is always terminated by a null character so the length of a string is always one greater than the number of characters in the string.we can add a \0 to the end of a string, this will create two strings but only the first one will be printed.

###  NULL

NULL is a symbol that represents a memory address that doesnt reference anything

## Modular programming

we can put our code in multiple separate files and include the headers in the main file and use another source file to import functions and other instructions. to do this :  
  
 1. create a header file pointing to a source file:  
 create other.h → header file  


```text
#ifndef UNTITLED_OTHER_H
#define UNTITLED_OTHER_H
int getme(void); // the function that this header referse to
#endif //UNTITLED_OTHER_H

```

  
this is pointing to a source file with the name other.c \(same as header.c\) which contains the source instructions for this header. when we include this header in the main.c source file it referse to the heaeder and the header referse to the other.c source file.  
  
2. create the source file for the header:  
create other.c → source file for header

```text
 int getme(void){
return 3;}
```

  
 here we define the getme function which was refered to by header and will be used in the main.c source file. we dont need to include anything in this source file  
  
 3. include the header in main.c and use the function:  
 create main.c, include header.h and use getme\(\)  


```text
#include <stdio.h>
#include "other.h"

int main(){

 printf("%d\n",getme());

 return 0;
}
```

  
 here we have to include the header file with double quotations cause we know its in the same directory so we dont need to look for it in the wholw system. then we can use the getme\(\) function defined in other.c source file which is refered to by the other.c header.  
  
 4. to compile without an IDE from command line:  
  
 **gcc \*.c -o \[program name\]** → compile all .c source files, headers are checked in compile time and are not included in the command  
  
 use with -c without -o option to keep the object files if you want, we can use object files only to compile the program same as we did with the source files, .o and .c is the same here.



## 

