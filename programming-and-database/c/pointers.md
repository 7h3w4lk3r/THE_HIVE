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





























