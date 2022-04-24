# Stack

A stack is the opposite of a queue because it uses last-in, first-out accessing, which is sometimes called LIFO. To visualize a stack, just imagine a stack of plates. The first plate on the table is the last to be used, and the last plate placed on the stack is the first to be used. Stacks are used frequently in system software, including compilers and interpreters.

When working with stacks, the two basic operations— store and retrieve— are traditionally called push and pop, respectively. Therefore, to implement a stack you need two functions: push( ), which places a value on the stack, and pop( ), which retrieves a value from the stack. You also need a region of memory to use as the stack. You can use an array for this purpose or allocate a region of memory using C's dynamic allocation functions. As with the queue, the retrieval function takes a value off the list and destroys it if it is not stored elsewhere. The general forms of push( ) and pop( ) that use an integer array follow. You can maintain stacks of other data types by changing the base type of the array on which push( ) and pop( ) operate.

```
functions:
push → insert an elemnt at one end of the stack called top
pop → remove and return the element at the top of the stack if it is not empty
peek → return the element at the top of the stack without removing it if the stack is not empty
size → return the number of elements in the stack
isEmpty → return true if the stack is empty
```

![](<../../../.gitbook/assets/image (33) (1) (1) (1) (1) (1) (1) (1).png>)

```
int stack[MAX];
int tos=0;
/* top of stack */
/* Put an element on the stack. */
void push(int i)
{
    if(t.os >= MAX) {
        printf ("Stack Full\n");
        return;
    }
    stack[tos] = i;
    tos++;
}
/* Retrieve the top element from the stack. */
int pop (void)
{
    tos--;
    if(tos < 0) {
        printf("Stack Underflow\n");
        return 0;
    }
    return stack[tos];
}
```

The variable tos is the index of the top of the stack. When implementing these functions, you must remember to prevent overflow and underflow. In these routines, an empty stack is signaled by tos being zero and a full stack by tos being greater than the last storage location.

An excellent example of stack usage is a four-function calculator. Most calculators today accept a standard form of an expression called infix notation, which takes the general form operand- operator-operand. For example, to add 200 to 100, enter 100, then press the plus (+) key, then 200, and press the equals (=) key. In contrast, many early calculators (and some still made today) use postfix notation, in which both operands are entered first, and then the operator is entered. For example, to add 200 to 100 by using postfix notation, you enter 100, then 200, and then press the plus key. In this method, as operands are entered, they are placed on a stack. Each time an operator is entered, two operands are removed from the stack, and the result is pushed back on the stack. One advantage of the postfix form is that long, complex expressions can be easily entered by the user.

The following example demonstrates a stack by implementing a postfix calculator for integer expressions. To begin, the push( ) and pop( ) functions must be modified, as shown here. They also will use dynamically allocated memory (instead of a fixed-size array) for the stack. Although the use of dynamically allocated memory is not necessary for this simple example, it illustrates how dynamically allocated memory can be used to support a stack.

![](<../../../.gitbook/assets/image (25).png>)

The entire postfix-based calculator program is shown here:

```
/* A simple four-function calculator. */
#include <stdio.h>
#include <stdlib.h>
#define MAX 100
int *p;
/* will point to a region of free memory */
int *tos; /* points to top of stack */
int *bos; /* points to bottom of stack */

void push(int i);
int pop(void);
int main(void)
{
int a, b;
char s[80];
p = (int *) malloc(MAX*sizeof(int)); /* get stack memory */
if(!p){
printf("Allocation Failure\n");
exit(1);
}

tos = p;
bos = p + MAX-1;
printf("Four Function Calculator\n");
printf("Enter 'q' to quit\n");
do {
printf(": ");
gets(s);
switch(*s) {
case '+':
a = pop();
b = pop();
printf("%d\n", a+b);
push(a+b);
break;
case '-':
a = pop();
b = pop();
printf("%d\n", b-a);
push(b-a);
break;
case '*':
a = pop();
b = pop();
printf("%d\n", b*a);
push(b*a);
break;
case '/':
a = pop();
b = pop();
if(a==0) {
printf("Divide by 0.\n");
break;
}
printf("%d\n", b/a);
push(b/a);
break;
case '.': /* show contents of top of stack */
a = pop();
push(a);
printf("Current value on top of stack: %d\n", a);
break;
default:
push(atoi(s));
}
} while(*s != 'q');
return 0;
}
/* Put an element on the stack. */
void push(int i)
{
if(p > bos) {
printf("Stack Full\n");
return;
}
*p = i;
p++;
}
/* Retrieve the top element from the stack. */
int pop(void)
{
p--;
if(p < tos) {
printf("Stack Underflow\n");
return 0;
}
return *p;
}
```

example:

```
// C program for array implementation of stack
#include <limits.h>
#include <stdio.h>
#include <stdlib.h>

// A structure to represent a stack
struct Stack {
    int top;
    unsigned capacity;
    int* array;
};

// function to create a stack of given capacity. It initializes size of
// stack as 0
struct Stack* createStack(unsigned capacity)
{
    struct Stack* stack = (struct Stack*)malloc(sizeof(struct Stack));
    stack->capacity = capacity;
    stack->top = -1;
    stack->array = (int*)malloc(stack->capacity * sizeof(int));
    return stack;
}

// Stack is full when top is equal to the last index
int isFull(struct Stack* stack)
{
    return stack->top == stack->capacity - 1;
}

// Stack is empty when top is equal to -1
int isEmpty(struct Stack* stack)
{
    return stack->top == -1;
}

// Function to add an item to stack.  It increases top by 1
void push(struct Stack* stack, int item)
{
    if (isFull(stack))
        return;
    stack->array[++stack->top] = item;
    printf("%d pushed to stack\n", item);
}

// Function to remove an item from stack.  It decreases top by 1
int pop(struct Stack* stack)
{
    if (isEmpty(stack))
        return INT_MIN;
    return stack->array[stack->top--];
}

// Function to return the top from stack without removing it
int peek(struct Stack* stack)
{
    if (isEmpty(stack))
        return INT_MIN;
    return stack->array[stack->top];
}

// Driver program to test above functions
int main()
{
    struct Stack* stack = createStack(100);

    push(stack, 10);
    push(stack, 20);
    push(stack, 30);

    printf("%d popped from stack\n", pop(stack));

    return 0;
}
```

example:

```
#include <stdio.h>

int MAXSIZE = 8;
int stack[8];
int top = -1;

int isempty() {

    if(top == -1)
        return 1;
    else
        return 0;
}

int isfull() {

    if(top == MAXSIZE)
        return 1;
    else
        return 0;
}

int peek() {
    return stack[top];
}

int pop() {
    int data;

    if(!isempty()) {
        data = stack[top];
        top = top - 1;
        return data;
    } else {
        printf("Could not retrieve data, Stack is empty.\n");
    }
}

int push(int data) {

    if(!isfull()) {
        top = top + 1;
        stack[top] = data;
    } else {
        printf("Could not insert data, Stack is full.\n");
    }
}

int main() {
    // push items on to the stack
    push(3);
    push(5);
    push(9);
    push(1);
    push(12);
    push(15);

    printf("Element at top of the stack: %d\n" ,peek());
    printf("Elements: \n");

    // print stack data
    while(!isempty()) {
        int data = pop();
        printf("%d\n",data);
    }

    printf("Stack full: %s\n" , isfull()?"true":"false");
    printf("Stack empty: %s\n" , isempty()?"true":"false");

    return 0;
}
```

example:

```
#include <stdio.h>
#include <stdlib.h>

// Data structure for stack
struct stack
{
    int maxsize;    // define max capacity of stack
    int top;
    int *items;
};

// Utility function to initialize stack
struct stack* newStack(int capacity)
{
    struct stack *pt = (struct stack*)malloc(sizeof(struct stack));

    pt->maxsize = capacity;
    pt->top = -1;
    pt->items = (int*)malloc(sizeof(int) * capacity);

    return pt;
}

// Utility function to return the size of the stack
int size(struct stack *pt)
{
    return pt->top + 1;
}

// Utility function to check if the stack is empty or not
int isEmpty(struct stack *pt)
{
    return pt->top == -1;    // or return size(pt) == 0;
}

// Utility function to check if the stack is full or not
int isFull(struct stack *pt)
{
    return pt->top == pt->maxsize - 1;    // or return size(pt) == pt->maxsize;
}

// Utility function to add an element x in the stack
void push(struct stack *pt, int x)
{
    // check if the stack is already full. Then inserting an element would
    // lead to stack overflow
    if (isFull(pt))
    {
        printf("OverFlow\nProgram Terminated\n");
        exit(EXIT_FAILURE);
    }

    printf("Inserting %d\n", x);

    // add an element and increments the top index
    pt->items[++pt->top] = x;
}

// Utility function to return top element in a stack
int peek(struct stack *pt)
{
    // check for empty stack
    if (!isEmpty(pt))
        return pt->items[pt->top];
    else
        exit(EXIT_FAILURE);
}

// Utility function to pop top element from the stack
int pop(struct stack *pt)
{
    // check for stack underflow
    if (isEmpty(pt))
    {
        printf("UnderFlow\nProgram Terminated\n");
        exit(EXIT_FAILURE);
    }

    printf("Removing %d\n", peek(pt));

    // decrement stack size by 1 and (optionally) return the popped element
    return pt->items[pt->top--];
}

int main()
{
    // create a stack of capacity 5
    struct stack *pt = newStack(5);

    push(pt, 1);
    push(pt, 2);
    push(pt, 3);

    printf("Top element is %d\n", peek(pt));
    printf("Stack size is %d\n", size(pt));

    pop(pt);
    pop(pt);
    pop(pt);

    if (isEmpty(pt))
        printf("Stack is empty");
    else
        printf("Stack is not empty");

    return 0;
}
```
