# Pointers

## Indirection

 is the ability to reference something using a name,reference or container,instead of the value itself.the most common form of indirection is the act of manipulating a value through its memory address.

a pointer provides an indirect means of accessing the value of a particular data item a variable whose value is a memory address.

C provides a remarkably useful type of variable called a pointer, a variable that stores a memory address. its value is the address of another location in memory that can contain a value.

![](../../.gitbook/assets/86986%20%281%29.png)

pointers allow functions to modify data passed to them as variables. pass by reference passing arguments to function in a way they can be changed by fucntion. can also be used to optimize program performance and speed by using less memory.

## Declaring pointers

```text
int number = 2; int *numberPointer = &number;
```

"numberPointer" now contains the address where variable "number" is stored

_**poniter = 25** → this "_" access the data stored at the address specified by a pointer

#### pointers must have the same variable type of the address values that contain the data. i.e: for float: float _pointer; for char: char_ pointer; etc...

**%p** → represents the format specified for a pointer

**int \*pointer = NULL;** → is the equivalent of zero for a pointer, it doesn't point to any location in the memory.



## Address of operator

the pointer simply points to the address of the variable and can act as an operator for using that variable.

```text
int number = 00; 
int *pnumber = &number;
```

## Dereferencing pointer to value

**printf\("%p",pnumber\);** → prints the pointer value printf\("%d",\*pnumber\); → prints the value that its pointed to \(number\)

## **D**isplaying an address without a pointer

 **printf\("number address: %p",&number\);** 

or

 **printf\("number address: %p",\(void**_**\)&number\);** → \(void_\) cast is to prevent possible warning from the compiler

example:

```text
int number = 0; // a variable of type int
int *pnumber = NULL; // a pointer that can point to type int

number = 10;
printf("number address: %p\n",&number); // output the address
printf("number value: %d\n",number);  //output the value

pnumber = &number;  //store the address of number in pnumber

printf("pnumbers address: %p\n",&pnumber);  //output pnumber address
printf("pnumber size: %d bytes\n", sizeof(pnumber)); //output size of pnumber
printf("pnumber value: %p\n",pnumber); // output the value of pnumber
printf("value pointed to: %d\n",*pnumber); // value at the address
```



## Pointers in expressions

**int value = 999;** 

**pnumber = &value;** 

**\*pnumber += 25;** → the value of "value" variable is incremented by 25.

example:

```text
 long num1 = 0l;
long num2 = 0l;
long *pnum = NULL;

pnum = &num1;   // get address of num1
*pnum = 2l;     // set num1 to 2
pnum = &num2;   // get address of num2
++*pnum;        // encriment num2 indirectly
printf("num1 = %ld num2=%ld *pnum = %ld *pnum + num2 = %ld\n",num1,num2,*pnum,*pnum+num2);
```



## **P**ointers and const

we can use the const keyword when declaring a pointer to indicate that the value pointed to must not be changed

```text
long value = 999l; 
const long *pvalue = &value;
```

the compiler will check for any statements that attempt to modify the value pointed to by pvalue and flag such statements as an error.

## Pointers to constants

#### we can still modify value, we have only applied const to the pointer not the variable itself.

```text
value=7777l;
```

#### the value pointed to has changed but we didn't use the pointer to make the change. the pointer itself is not constant so we can still change what it points to:

```text
long number = 888l; 
pvalue = &number;
```

#### we can create a constant pointer that points to a value that is also constant:

```text
 int item = 25;
 const int *const pitem = &item;
```

now even though item variable is not defined as constant but the \*const pointer makes it constant and we cant change the pointer or variable values

## Void pointers

the type name void means absence of any type a pointer of type void can contain the address of data item of any type. void is often used as a parameter type or return value type with functions that deal with data in a type-independent way

any kind of pointer can be passed around as a value of type void the void pointer can be doesn't know what type of object it is pointing to so it cant be de-referenced directly the void pointer must first be explicitly cast to another pointer type variable of type void

when we want to access the integer value at the address stored in the void pointer, we must first cast the pointer to type int

example:

```text
int i = 10;
float f = 3.34;
char ch = 'k';

void *vptr;
vptr = &i;
printf("value of i=%d\n",*(int *)vptr);
vptr = &f;
printf("value of f=%f\n",*(float *)vptr);
vptr = &ch;
printf("value of ch=%c\n",*(char *)vptr);
```

**void** _**pointer;**_ **\(data\_type \*\)pointer** → use this format to assign a data type to a void pointer

## Pointers and arrays

an array is a collection of objects of the same type that you can refer to using a single name we can use a pointer to hold the address of different variables at different times\(must be same type\) arrays and pointers seem quit different but they are very closely related and can sometimes be used interchangeably

the main reason for using pointers to arrays are ones of notional convenience and of program efficiency, pointers to arrays generally result in code that uses less memory and executes faster.

in pointers to arrays we point to each value of the array specifically and not the whole array at once.

```text
 int values[100];
 int *pvalues;
 pvalues = values;  → point to the first element of the values array
 or
 pvalue  = &values[0]
  pvalue  = &values[1] → second element
 pvalue  = &values[1] → third element
 ...
```

example:

```text
 int values[100]={[1]=324};
int *pointer;
pointer = &values[1];
printf("value 1 : %d\n",*pointer);
 
```

if "ar" is an array the two expressions ar\[i\] and \*\(ar+i\) are equivalent in meaning, both work if ar is the name of an array, both work if ar is a pointer variable using an expression such as ar++ only works if ar is a pointer variable.

## Pointer arithmetic

```text
int values[100];
 int *pvalues;
 pvalues = values; 
```

#### to reference values\[3\] through the pvalues variable we can add 3 to pvalues and then apply the indirection operator.

```text
*(pvalues + 3)
```

```text
int values[100]={[3]=352};
int *pointer;
pointer = &values;
printf("value 3 : %d\n",*(pointer+3));
```

this expression can be used to access the value contained in values\[i\]

for example to set vales\[10\] to 27:

```text
values[10]=27;
or
*(pvalues + 10) = 27;
```

```text
*(pointer+3) = 321;
printf("set value 3 to : %d\n",*(pointer+3)); 
```

the increment and decrement operators ++ and -- are particularly usefull when dealing with pointers.

**++pvaluea;** → sets pvalues pointing to the next integer in the values array \(values\[1\]\)

**--pvalues;** → sets pvalues pointing to the previous integer in the values array assuming that pvalues was not pointing to the beginning of the values array

example:

```text
// loop trough an array and get a sum using an array
#include <stdio.h>

int sum( int ar[], int size);

int main(){
    int array[] = {23,67,3,46,25,5};
    printf("%d\n",sum(array,6));
    return 0;
}

int sum(int ar[],  int size){
    int s = 0;
    for(int i = 0; i < size ; i++)
        s += ar[i];
    int avg = s/size;
    return avg;
}
```



example:

```text
// loop trough an array and get a sum using a pointer (less code)
#include <stdio.h>

int sum( int *ar, int size);

int main(){
    int array[] = {23,67,3,46,25,5};
    printf("%d\n",sum(array,6));
    return 0;
}

int sum(int *ar,  int size){
    int s = 0;
    for(int i = 0; i < size ; i++)
        s += *(ar+i);
    int avg = s/size;
    return avg;
}
```

#### example assigning and resolving array using pointers:

```text
#include <stdio.h>


int main(){

    int size=0, counter=0;
    printf("\nenter the size of the array: ");
    scanf("%d",&size);
    int array[size];
    int *ptr = array;

    while(counter < size ){
        printf("\nenter element number %d : ",counter);
        scanf("%d",&(*(ptr+counter)));
        counter += 1;
    }

for ( int j=0; j < size; j++){
    printf("\nelement number %d: %d\n",j,*(ptr+j));
}

    return 0;
}
```



## Pointers and strings





