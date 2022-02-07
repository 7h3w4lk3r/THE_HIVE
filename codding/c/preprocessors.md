# Preprocessors

these statements are identified by the presence of a pound sign ,#, which must be the first non-space character on the line like:

```
#include <stdio.h>
```

there are two ways to #include header files in a program

**using angle brackets ( #include ) tells the preprocessor to look for the file in one or more standard system directories**

**using double quotes ( #include "jason.h" ) tells the preprocessor to first look in the current working directory**

## conditional compilation

`#define`\
`#undef`\
`#ifdef`\
`#ifndef`\
`#if`\
`#elif`\
`#else`\
`#endif`\
\
used to create one program that can be compiled to run on different computer systems. if you had a large program that had many dependencies on specific hardware or software . we might end up with many defines whose values would have to be changed when the program was moved to another computer system.\
\
we can help reduce this problem by incorporating the values of these defines for each different machine into the program by using the conditional compilation capabilities of the preprocessor.\
\
its also used to switch on or off various statements in the program, trace the flow of a program or debug statements that print out the values of various variables



#### #if → test the value of a constant expression

{% hint style="info" %}
#### every #if construct ends with an #endif
{% endhint %}

#### #else → to complement #ifdef/#ifndef and #if

#### #define → defines an identifier, we can define them from the command line too

#### #ifdef → checks wether an identifier is currently defined

#### #ifndef → the negative of the ifdef

{% hint style="info" %}
#### directives #ifdef and #ifndef are provided as shorthand for

`if defined(name)`

`if !defined(name)`
{% endhint %}

#### example:

`#ifndef SIZE `

`#define SIZE 100`

#### example:

`#define UNIX 1 OR #define UNIX`\


#### example:

```
#ifdef UNIX
# define DATADIR "/dev/data"
#elif UNIX = "/data"
#define DATADIR "/usr/data"
#else
#define DATADIR "/dev/null"
#endif
```

most compilers also permit you to define a name to the preprocessor when the program is compiled using the special option -D:

`gcc -D UNIX main.c`\


## #pragma

lets you place compiler instructions in the source code. to control the amount of memory set aside for automatic variables to set the strictness of error checking to enable nonstandard language features

#### pragma token\_name → token\_name is a command for the compiler to obey

gcc has the following pragmas:

```
#pragma GCC dependency
#pragma GCC poison
#pragma GCC system_header
#pragma once
#pragma GCC warning
#pragma GCC error
#pragma message
```

#### pragma GCC dependency → check the relative date of the current file and another file, if the other file is more recent than the current file, a warning is issued

example:

```
pragma GCC dependency "parse.y"
```

#### pragma GCC poison → used to remove an identifier completely from the program.

example:

```
#pragma GCC poison printf sprintf fprintf
sprintf(some_string,"hello"); // this will raise an error
```

#### pragma GCC system\_header → tells the compiler to consider the rest of the current include file as a system header

#### pragma once → specifies that the header file containing the directive is included only once even if the programmer includes it multiple times

#### pragma GCC warning "message" →  causes the preprocessor to issue a warning diagnostic with the text "message"

#### pragma GCC error "message" → cause the preprocessor to issue an error with the text "message"

#### pragma GCC message "message" → prints string as a compiler message on compilation. the message is information only not warning or error

## error directive

causes the preprocessor to issue an error message that includes any text in the directive. error message is a sequence of characters by spaces. you do not have to in-close the text in quotes the message is optional.

example:

```
if STDC_VERSION != 201112L // failes if the compiler uses an older standard and successes when it uses C11
error Not C11
endif
```

## # and ## operators

used for concatenation. often useful to merge two tokens into one while expanding macros ( called token pasting or token concatenation)

#### # →  used within a macro definition causes a replacement text token to be converted to a string surrounded by quotes

example:

```
#define HELLO(x) printf("hello," #x "\n");

when HELLO(john) apears in a program file it is expanded to:

printf("Hello," "john" "\n");
```

#### ##→ performs token pasting. cats two tokens.

example:

```

#define concat(x,y) x ## y
...
int xy = 10;
...

this will make the compiler turn:
printf("%d",concat(x,y));
to 
printf("%d",xy);
```
