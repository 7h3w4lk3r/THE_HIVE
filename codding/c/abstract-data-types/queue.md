# Queue

A queue is simply a linear list of information that is accessed in first-in, first-out order, which is sometimes called FIFO. That is, the first item placed on the queue is the first item retrieved, the second item put in is the second item retrieved, and so on. This is the only means of storage and retrieval in a queue; random access of any specific item is not allowed.

```
functions
equeue → insert an element at the end of the queue
dequeue → remove and return the first element of the queue if the queue is not empty
peek → return the element of the queue without removing
size → return the number of elements in the queue
isEmpty → return true if the queue is empty
```

![](<../../../.gitbook/assets/image (28).png>)

the functions qstore( ) and qretrieve( ) shown here are needed for the simple scheduling program. They will store pointers to the strings that describe the appointments.

![](<../../../.gitbook/assets/image (27).png>)

example:

a queue for entering,removing and listing apointments

```
/* Mini Appointment-Scheduler */
#include <string.h>
#include <stdlib.h>
#include <stdio.h>
#include <ctype.h>

#define MAX 100
char *p[MAX], *qretrieve(void);
int spos = 0;
int rpos = 0;
void enter(void), qstore(char *q), review(void), delete_ap(void);
int main(void)
{
char s[80];
register int t;
for(t=0; t < MAX; ++t) p[t] = NULL; /* init array to nulls */
for(;;) {
printf("Enter, List, Remove, Quit: ");
gets(s);
*s = toupper(*s);
switch(*s) {
case 'E':
enter();
break;
case 'L':
review();
break;
case 'R':
delete_ap();
break;
case 'Q':
exit(0);
}
}
return 0;
}
/* Enter appointments in queue. */
void enter(void)
{
char s[256], *p;
do {
printf("Enter appointment %d: ", spos+1);
gets(s);
if(*s==0) break; /* no entry */
p = (char *) malloc(strlen(s)+1);
if(!p) {
printf("Out of memory.\n");return;
}
strcpy(p, s);
if(*s) qstore(p);
} while(*s);
}
/* See what's in the queue. */
void review(void)
{
register int t;
for(t=rpos; t < spos; ++t)
printf("%d. %s\n", t+1, p
[t]);
}
/* Delete an appointment from the queue. */
void delete_ap(void)
{
char *p;
if((p=qretrieve()) ==NULL) return;
printf("%s\n", p);
}
/* Store an appointment. */
void qstore(char *q)
{
if(spos==MAX) {
printf ("List Full\n");
return;
}
p[spos] = q;
spos++;
}
/* Retrieve an appointment. */
char *qretrieve(void)
{
if(rpos==spos) {
printf("No more appointments.\n");
return NULL;
}
rpos++;
return p[rpos-1];
}
```

example:

```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <stdbool.h>

#define MAX 6

int intArray[MAX];
int front = 0;
int rear = -1;
int itemCount = 0;

int peek() {
    return intArray[front];
}

bool isEmpty() {
    return itemCount == 0;
}

bool isFull() {
    return itemCount == MAX;
}

int size() {
    return itemCount;
}

void insert(int data) {

    if(!isFull()) {

        if(rear == MAX-1) {
            rear = -1;
        }

        intArray[++rear] = data;
        itemCount++;
    }
}

int removeData() {
    int data = intArray[front++];

    if(front == MAX) {
        front = 0;
    }

    itemCount--;
    return data;
}

int main() {
    /* insert 5 items */
    insert(3);
    insert(5);
    insert(9);
    insert(1);
    insert(12);

    // front : 0
    // rear  : 4
    // ------------------
    // index : 0 1 2 3 4
    // ------------------
    // queue : 3 5 9 1 12
    insert(15);

    // front : 0
    // rear  : 5
    // ---------------------
    // index : 0 1 2 3 4  5
    // ---------------------
    // queue : 3 5 9 1 12 15

    if(isFull()) {
        printf("Queue is full!\n");
    }

    // remove one item
    int num = removeData();

    printf("Element removed: %d\n",num);
    // front : 1
    // rear  : 5
    // -------------------
    // index : 1 2 3 4  5
    // -------------------
    // queue : 5 9 1 12 15

    // insert more items
    insert(16);

    // front : 1
    // rear  : -1
    // ----------------------
    // index : 0  1 2 3 4  5
    // ----------------------
    // queue : 16 5 9 1 12 15

    // As queue is full, elements will not be inserted.
    insert(17);
    insert(18);

    // ----------------------
    // index : 0  1 2 3 4  5
    // ----------------------
    // queue : 16 5 9 1 12 15
    printf("Element at front: %d\n",peek());

    printf("----------------------\n");
    printf("index : 5 4 3 2  1  0\n");
    printf("----------------------\n");
    printf("Queue:  ");

    while(!isEmpty()) {
        int n = removeData();
        printf("%d ",n);
    }
}
```

example:

```
// C program for array implementation of queue 
#include <limits.h>
#include <stdio.h>
#include <stdlib.h>

// A structure to represent a queue 
struct Queue {
    int front, rear, size;
    unsigned capacity;
    int* array;
};

// function to create a queue 
// of given capacity. 
// It initializes size of queue as 0 
struct Queue* createQueue(unsigned capacity)
{
    struct Queue* queue = (struct Queue*)malloc(
            sizeof(struct Queue));
    queue->capacity = capacity;
    queue->front = queue->size = 0;

    // This is important, see the enqueue 
    queue->rear = capacity - 1;
    queue->array = (int*)malloc(
            queue->capacity * sizeof(int));
    return queue;
}

// Queue is full when size becomes 
// equal to the capacity 
int isFull(struct Queue* queue)
{
    return (queue->size == queue->capacity);
}

// Queue is empty when size is 0 
int isEmpty(struct Queue* queue)
{
    return (queue->size == 0);
}

// Function to add an item to the queue. 
// It changes rear and size 
void enqueue(struct Queue* queue, int item)
{
    if (isFull(queue))
        return;
    queue->rear = (queue->rear + 1)
                  % queue->capacity;
    queue->array[queue->rear] = item;
    queue->size = queue->size + 1;
    printf("%d enqueued to queue\n", item);
}

// Function to remove an item from queue. 
// It changes front and size 
int dequeue(struct Queue* queue)
{
    if (isEmpty(queue))
        return INT_MIN;
    int item = queue->array[queue->front];
    queue->front = (queue->front + 1)
                   % queue->capacity;
    queue->size = queue->size - 1;
    return item;
}

// Function to get front of queue 
int front(struct Queue* queue)
{
    if (isEmpty(queue))
        return INT_MIN;
    return queue->array[queue->front];
}

// Function to get rear of queue 
int rear(struct Queue* queue)
{
    if (isEmpty(queue))
        return INT_MIN;
    return queue->array[queue->rear];
}

// Driver program to test above functions./ 
int main()
{
    struct Queue* queue = createQueue(1000);

    enqueue(queue, 10);
    enqueue(queue, 20);
    enqueue(queue, 30);
    enqueue(queue, 40);

    printf("%d dequeued from queue\n\n",
           dequeue(queue));

    printf("Front item is %d\n", front(queue));
    printf("Rear item is %d\n", rear(queue));

    return 0;
} 

```

## The Circular Queue

Instead of having the program stop when the limit of the array used to store the queue is reached, you could have both the store index (spos) and the retrieve index (rpos) loop back to the start of the array. In this way, any number of items could be placed on the queue, so long as items were also being taken off. This implementation of a queue is called a circular queue because it uses its storage array as if it were a circle instead of a linear list.

example:

```
/* Mini Appointment-Scheduler */
#include <string.h>
#include <stdlib.h>
#include <stdio.h>
#include <ctype.h>

#define MAX 100
char *p[MAX], *qretrieve(void);
int spos = 0;
int rpos = 0;
void enter(void), qstore(char *q), review(void), delete_ap(void);
int main(void)
{
char s[80];
register int t;
for(t=0; t < MAX; ++t) p[t] = NULL; /* init array to nulls */
for(;;) {
printf("Enter, List, Remove, Quit: ");
gets(s);
*s = toupper(*s);
switch(*s) {
case 'E':
enter();
break;
case 'L':
review();
break;
case 'R':
delete_ap();
break;
case 'Q':
exit(0);
}
}
return 0;
}
/* Enter appointments in queue. */
void enter(void)
{
char s[256], *p;
do {
printf("Enter appointment %d: ", spos+1);
gets(s);
if(*s==0) break; /* no entry */
p = (char *) malloc(strlen(s)+1);
if(!p) {
printf("Out of memory.\n");return;
}
strcpy(p, s);
if(*s) qstore(p);
} while(*s);
}
/* See what's in the queue. */
void review(void)
{
register int t;
for(t=rpos; t < spos; ++t)
printf("%d. %s\n", t+1, p
[t]);
}
/* Delete an appointment from the queue. */
void delete_ap(void)
{
char *p;
if((p=qretrieve()) ==NULL) return;
printf("%s\n", p);
}
/* Store an appointment. */
void qstore(char *q)
{
/* The queue is full if either spos is one less than rpos
or if spos is at the end of the queue array and rpos
is at the beginning.
*/
    if(spos+1== rpos || (spos+1==MAX && !rpos)) {
        printf("List Full\n");
        return;
    }
    p[spos] = q;
    spos++;
    if(spos==MAX) spos = 0; /* loop back */
}
char *qretrieve(void)
{
    if(rpos==MAX) rpos = 0; /* loop back */
    if(rpos==spos) {
        printf("No events to retrieve.\n");
        return NULL;
    }
    rpos++;
    return p[rpos-1];
}
```
