# Error Recovery

## setjmp / longjmp

using these 2 we can jump out of deeply nested call chain without needing to deal with handling errors in every function in the chain.

setjmp saves a copy of program counter and current pointer to the top of the stack

```
 int setjmp(jmp_buf j)
```

used the variable j to remember where you are now must be called first.

longjmp is then invoked after setjmp, longjmp(jmp\_buf j, int i) saysgor back to the place that the j is remembering restores the process in the state that it existed when it called setjmp returns the value of i so the coe can tell when we got back here via longjmp() the constents of the j are distroyed when it is used in a longjmp()

this is often reffered to as unwinding the stack because we unroll activation records from the stack until we get to the saved one.

{% hint style="info" %}
\<setjmp.h> should be included
{% endhint %}

#### we can only longjmp back to somewhere we have already been, where we did setjmp and that still has a live activation record.

jumping from a loop:

```
#include <setjmp.h>
#include <stdio.h>
#include <stdlib.h>

jmp_buf buff;

int main(){
    int i = _setjmp(buff);  \\ set a point with buff=0 
    printf("i = %d\n",i);
    if ( i != 0) exit(0);       \\ prevents an infinit loop
    longjmp(buff, 42);      \\ returns to setjmp with i=42
    printf("this line doesnt get printed");
    return 1;
}
```

jumping between functions:

```
#include <setjmp.h>
#include <stdio.h>

jmp_buf buff;

void myFunction() {
    printf("in my function()\n");
    longjmp(buff, 1);
    printf("this line doesnt get printed");
}

int main(){

    if (setjmp(buff))
        printf("back in main\n");
    else{
        printf("first time through\n");
        myFunction();
        }
    return 1;
}
```

example:

```
#include <setjmp.h>
#include <stdio.h>
jmp_buf ebuf;
void f2(void);
int main(void)
{
    int i;
    printf("1 ");
    i = setjmp(ebuf);
    if(i == 0) {
        f2();
        printf("This will not be printed.");
    }
    printf("%d", i);
    return 0;
}
void f2(void)
{
    printf("2 ");
    longjmp(ebuf, 3);
}
```
