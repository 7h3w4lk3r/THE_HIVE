# Functions

### the overal form is like this:

#### `return_type function_name( parameters - separated by commas) { statements }`

example:

```text
 #include <stdio.h>

void printMessage (void)  // void means function returns no value
{
    printf(" test message \n");
}

int main() {
    printMessage(); → call the function
    return 0;​
```



## Function arguments

example:

```text
#include <stdio.h>

void multiply(int x , int y) → were not returning anything, just printing, so we use void

{
    int result = x*y;
    printf("%d multiplied by %d is: %d\n",x,y,result);
}

int main(void){
    multiply(2,4);
    multiply(6,8);
    multiply(98,34);
    return 0;
}
```

#### we can also asign a variable to the return of a function:

```text
int x = myFunction();
```

####  

## Returning data

return type is the first parameter that the function returns.it can be any data type like double, int, char, pointers, void, etc.

the return statement provides the means of exiting from a function:

```text
 return variable/expression;
```

example:

```text
 #include <stdio.h>

int multiply(int x , int y){
    int result = x*y;
    return result;
}
int main (void){
    int result = 0;
    result = multiply(10,20);
    printf("result of %d * %d is : %d\n ",10,20,result);
    return 0;
}
```

{% hint style="info" %}
all variables defined inside a function are known as automatic local variables
{% endhint %}

## Global variables

the opposite of a local variable, defined outside of a function. **the syntax is the same, global variables are just defined outside of all functions. if you want to make a variable global bot inaccessible from other C files in the program, use the 'restrict' type qualifier explained in type qualifiers section.**

example:

```text
 #include <stdio.h>

int myglobal = 0; //global variable

int main()
{
    int mylocal = 0; // local variable
    // can access  mylocal and myglobal
    return 0;
}

void myfunc()
{
    int x;  //local variable
    //can access myglobal and x, cannot access mylocal
}
```

{% hint style="info" %}
avoid using global variables, it might get very hard to keep track of global variables specially when the program grows.
{% endhint %}

## Utility functions

here are some of the useful utility functions used in C programming language.

```text
  <stdlib.h>
 
 
 int abs(n) → returns the absolute value of its int argument
 
 void exit(n) → terminate the program and return exit status of n (integer).
 
 EXIT_SUCCESS and EXIT_FAILURE defined in <stdlib.h>
 
 char *getenv(s) → returns a pointer to the value of the environment variable pointed to by s, or a null pointer if the variable doesnt exist.
 
 void qsort(arr, n, size, comp_fn) → sort the data array arr
 there are n elements in the array,each size bytes in length, n and size are of type size_t .the fourth arg is of type pointer to a function that returns int and tales two void pointers as arguments
 qsort calls this function whenever it needs to compare elements in  th array.
 
 int rand(void) → returns a random number in the range [0, RAND_MAX] where RAND_MAX is defined in <stdlib.h> and has a minimum value of 32767 
 
 void srand(seed) → seeds the random number generator to the unsigned int value seed
 
 int system(s) → execute system commands
```

## Variadic function

#### the variation or change here is that we are dealing with unknown number of arguments that will be used for a function.several Cs built-in functions are variatic functions like printf\(\).

#### a variatic function has 2 parts: mandatory args \(at least 1 is required and is the first one listed\) optional args \(listed after mandatory args\)

#### &lt;stdarg.h&gt; must be included

### va\_list

 used when we need to access optional parameters and it is an arg list. represents a data object used to hold the parameters for the ellipsis part of the parameter list

### va\_start 

will connect our argument list with some argument pointer, the list specified in va\_list is the first arg and the second arg is the last fixed parameter

### va\_arg 

returns the value of the current argument. will fetch the current argument connected to the argument list. we would need to know the type of the argument that we are reading

### va\_end 

used in situation when we would like to stop using a variable argument list \(cleanup\)

### va\_copy 

used in situations for which we need to save our current location

## Creating a variadic function

####  provide a function prototype using a ellipsis\(three dots\), this indicates that a variable number of args may follow any number of fixed args, must have at least one fixed argument.

```text
void f1(int n,...);     // one mandatory arg
int f2(const char * s, int k,...);  // two mandatory args
```

{% hint style="info" %}
ellipsis should always be last, the function must have at least one mandatory arg
{% endhint %}

#### create a va\_list type variable in the function definition. initialize the var to an arg list.need to copy the arg list to the va\_list variable using va\_start

```text
 double average(double v1, doublev2,...){
va_list parg;       //pointer for var arg list 
// more code here
 va_start(parg, v2);
 // more code here
 }
```

#### access the content of the arg list using va\_arg\(\) takes 2 args: a type va\_list var and a type name the first time it is called it returns the first item in the list the next time it is called it returns the second item in the list and so on

```text
 double someFunctio(int lim,...){
va_list ap;     // object to hold args
va_start(ap, lim);  // initialize ap to arg list
 
 double tic = va_arg(ap, double);   // retrives first argument
 int toc = va_arg(ap, int);     // retrives second argument
 }
```

#### the automatic conversion of double to int that works for assignment doesn't work here

#### you should clean by using the va\_end macro as your last step.resets the pointer to NULL.

```text
va_end(ap) // clean up
```



example take at least 2 numbers and print their sum:

```text
#include <stdio.h>
#include <stdarg.h>

double average(double v1, double v2, ...);

int main(){
    double v1 = 10.5, v2 = 2.5;
    int num1 = 6, num2 = 5;
    long num3 = 121, num4 = 20L;

    printf("average = %.2lf\n",average(v1, 3.5, v2, 4.5, 0.0));
    printf("average = %.2lf\n",average(1.0 , 2.0, 0.0));
    printf("average = %.2lf\n",average((double)num2, v2, (double)num1, (double)v1));

    return 0;
}

double average(double v1, double v2, ...){
    va_list parg;                   // create an arg list
    double sum = v1 + v2;       //sum of mandatory arg 1 and 2 
    double value = 0.0;       // set the value for not-decleared args

    int count = 2;      // counter for sum
    va_start(parg,v2);  // take the va_list and last fixed arg

    while((value = va_arg(parg,double)) != 0.0){    // while the value of the args in the list are not equal to 0.0 (the last arg), calculate the sum

        sum += value;
        ++count;
    }
    va_end(parg);
    return sum/count;
}
```

### va\_copy 

its possible that we may need to process a var arg list more than once. may be useful to preserve a copy of the va\_list type var.

```text
va_list parg;
va_list parg_copy;
va_copy(parg_copy, parg);  → pass the copy to the original list
```

the first statement creates a new va\_list variable, parg\_copy the next statement copies the content of parg to parg\_copy

we can then process parg and parg\_copy independently to extract argument values from the list prior to using the va\_copy\(\) routine, parg\_copy will be in an identical state to parg with some arg values already extracted

do not use the va\_list object parg\_copy as the destination for another copy operation before you have executed va\_end\(\) for parg\_copy



example:

#### compile this with gcc main.c -o main -lm

```text
#include <stdio.h>
#include <stdarg.h>
#include <math.h>

double sample_stddev(int count, ...);

int main(){
    printf("%f\n",sample_stddev(4,6.6,54.78,3456.7,8878.8));
    return 0;
}

double sample_stddev(int count, ...){
    double sum = 0;

    va_list args1;
    va_start(args1,count);

    va_list args2;
    va_copy(args2, args1);

    for (int  i = 0; i < count ; ++i){
        double num = va_arg(args1,double);
        sum += sum;
    }

    va_end(args1);
    double mean = sum / count;
    double sum_sq_diff = 0;

    for (int i = 0; i < count ; ++i){
        double num = va_arg(args2, double);
        sum_sq_diff += (num-mean) * (num-mean);
    }
    va_end(args2);
    return sqrt(sum_sq_diff/count);
}
```



## Recursion

when a function calls itself directly or indirectly

example:

```text
#include <stdio.h>

int factorial(int n){
    int result = 0;
    if (n==0)
        result = 1;
    else
    result = n * factorial(n -1);
    return result;
    }

int main(){
    int j = 0;
    for ( j = 0; j < 8; ++j)
        printf("%d! = %d\n",j,factorial(j));
    return 0;
}
```

## Inline functions

helps avoiding the amount of overhead that comes along with invoking a function, the point is to hint the compiler that it is worth making some form of extra effort to call the function faster than it would otherwise.

when using inline functions, the compiler will substitute the code of the function into its caller \(eliminating the need for a call and return sequence\). the program no longer calls the code of the function, instead, the compiler replaces every call to an inline function with the code body of that function.

inline declaration is only advice to compiler which can be decided to be ignored. may cause the compiler to replace the function call with inline code and/or perform some other sort of optimizations.

```text
 inline int function();
```



```text
 // foo.h
 #ifdef FOO_H_
 #define FOO_H_
 
 inline static void foo(){
 // do something
 }
 #endif
```

## Noreturn functions

c11 added a second function specifier named \_Noreturn

the perpose of this is to inform the user and compiler that a particular function will not return control to the calling program when it completes execution.

just like inline function this is just a hint for the compiler which can be ignored.

the exit\(\) function is and example of a \_Noreturn function, once called, never resumes



```text
 _Noreturn int function();
```

example using noreturn macro:

```text
#include <stdio.h>
#include <stdlib.h>
#include <stdnoreturn.h>

noreturn void my_exit(void);

void my_exit(void){
    printf("Exiting...\n");
    exit(0);
}

int main(void){
    my_exit();
    return 0;
}
```





if we have a multi-file program we need an inline definition in each file that calls the function.







