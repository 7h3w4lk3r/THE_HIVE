# Singly Linked List

#### A singly linked list requires that each item of information contain a link to the next element in the list. Each data item usually consists of a structure that includes information fields and a link pointer.

Basically, there are two ways to build a singly linked list. The first is simply to put each new item on the end of the list. The other is to insert items into specific places in the list— in ascending sorted order, for example. How you build the list determines the way the storage function is coded. Let's start with the simpler case of creating a linked list by adding items on the end.

The items stored in a linked list generally consist of structures because each item must carry with it a link to the next item in the list as well as the data itself. Therefore, we will need to define a structure that will be used in the examples that follow. Since mailing lists are commonly stored in a linked list, an address structure makes a good choice. The data structure for each element in the mailing list is defined here:

```
struct address {
    char name[40];
    char street[40];
    char city[20];
    char state[3];
    char zip[11];
    struct address *next; /* link to next address */
} info;
```

The slstore( ) function, shown next, builds a singly linked list by placing each new element on the end. It must be passed a pointer to a structure of type address that contains the new entry, and a pointer to the last element in the list. If the list is empty, the pointer to the last element in the list must be null.

```
void slstore(struct address *i,
struct address **last)
{
if(!*last) *last = i; /* first item in list */
else (*last)->next = i;
i->next = NULL;
*last = i;
}
```

Although you can sort the list created with the function slstore( ) as a separate operation, it is easier to sort the list while building it by inserting each new item in the proper sequence of the chain. Also, if the list is already sorted, it would be advantageous to keep it sorted by inserting new items in their proper location. You do this by sequentially scanning the list until the proper location is found, inserting the new entry at that point, and rearranging the links as necessary.

{% hint style="info" %}
Three possible situations can occur when you insert an item in a singly linked list. First, it may become the new first item; second, it can go between two other items; or third, it can become the last element.
{% endhint %}

Keep in mind that if you change the first item in the list, you must update the entry point to the list elsewhere in your program. To avoid this overhead, you can use a sentinel as a first item. In this case, choose a special value that will always be first in the list to keep the entry point to the list from changing. This method has the disadvantage of using one extra storage location to hold the sentinel, but this is usually not an important factor.

![](<../../../../.gitbook/assets/image (29).png>)

The function shown next, sls\_store( ), will insert address structures into the mailing list in ascending order based on the name field. The function must be passed a pointer to the pointer to the first element and the last element in the list along with a pointer to the information to be stored. Since the first or last element in the list could change, sls\_store( ) automatically updates the pointers to the beginning and end of the list if necessary. The first time your program calls sls\_store( ), first and last must point to null.

```
/* Store in sorted order. */
void sls_store(struct address *i, /* new element to store */
    struct address **start, /* start of list */
    struct address **last) /* end of list */ {
        struct address *old, *p;
        p = *start;
        if(!*last) { /* first element in list */
        i->next = NULL;
        *last = i;
        *start = i;
        return;
    }
        old = NULL;
        while(p) {
        if(strcmp(p->name, i->name)<0) {
        old = p;
        p = p->next;
    }
else {
    if(old) { /* goes in middle */
    old->next = i;
    i->next = p;
    return;
    }
    i->next = p; /* new first element */
    *start = i;
    return;
        }
    }
    (*last)->next = i; /* put on end */
    i->next = NULL;
    *last = i;
}
```

It is quite easy to step through a linked list: Begin at the top of the list, and then follow the links. Usually this code is so short that it is simply placed inside another routine such as a search, delete, or display function. For example, the routine shown here displays all of the names in a mailing list:

```
void display(struct address *start)
{
    while(start) {
        printf(''%s\n", start->name);
        start = start->next;
    }
}
```

When display( ) is called, start must be a pointer to the first structure in the list. After that, the next field points to the next item. The process stops when next is null. Retrieving items from the list is as simple as following a chain. A search routine based on the name field could be written like this:

```
struct address *search(struct address *start, char *n)
{
    while(start) {
        if(!strcmp(n, start->name)) return start;
        start = start->next;
    }
return NULL; /* no match */
}
```

Because search( ) returns a pointer to the list item that matches the search name, it must be declared as returning a structure pointer of type address. If there is no match, null is returned. Deleting an item from a singly linked list is straightforward. As with insertion, there are three cases: deleting the first item, deleting an item in the middle, and deleting the last item. Figure 22-4 shows each of these operations. The function that follows deletes a given item from a list of structures of type address:

```
void sldelete(
    struct address *p, /* previous item */
    struct address *i, /* item to delete */
    struct address **start, /* start of list */
    struct address **last) /* end of list */
    {
        if(p) 
            p->next = i->next;
        else 
            *start = i->next;
            
        if(i==*last && p)
            *last = p;
}
```

![](<../../../../.gitbook/assets/image (31).png>)

sldelete( ) must be sent pointers to the deleted item, the item before it in the chain, and the first and last items in the list. If the first item is to be removed, the previous pointer must be null. The function automatically updates start and last when the item one of them points to is deleted. Singly linked lists have one major drawback that prevents their extensive use: The list cannot be read in reverse order. For this reason, doubly linked lists are usually used.
