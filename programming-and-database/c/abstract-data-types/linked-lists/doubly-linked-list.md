# Doubly Linked List

Doubly linked lists consist of data plus links to the next item as well as the preceding item.

Having two links instead of just one has several advantages. Perhaps the most important is that the list can be read in either direction. This simplifies list management, making insertions and deletions easier. It also allows a user to scan the list in either direction. Another advantage is meaningful only in the case of some type of failure. Since the entire list can be read using either forward links or backward links, should one of the links become invalid, the list could be reconstructed by using the other.

![](<../../../../.gitbook/assets/image (32).png>)

A new element can be inserted into a doubly linked list in three ways: insert a new first element,

Building a doubly linked list is similar to building a singly linked list except that two links are maintained. Therefore, the structure must have room for both links. Using the mailing list example again, you can modify the structure address as shown here to accommodate both links:

```
struct address {
    char name[40];
    char street[40];
    char city[20];
    char state[3];
    char zip[11];
    struct address *next;
    struct address *prior;
} info;
```

Using address as the basic data item, the following function, dlstore( ), builds a doubly linked list:

```
void dlstore(struct address *i, struct address **last){
    if(!*last) 
        *last = i; /* is first item in list */
    else 
        (*last)->next = i;
    i->next = NULL;
    i->prior = *last;
    *last = i;
}
```

![](<../../../../.gitbook/assets/image (34) (1) (1) (1).png>)

The function dlstore( ) puts each new entry on the end of the list. You must call it with a pointer to the data to be stored as well as a pointer to the end of the list, which must be null on the first call.

Like singly linked lists, a doubly linked list can be built by a function that stores each element in a specific location in the list instead of always placing each new item on the end. The function shown here, dls\_store( ), creates a list that is sorted in ascending order:

```
* Create a doubly linked list in sorted order. */
void dls_store(
    struct address *i,
    /* new element */
    struct address **start, /* first element in list */
    struct address **last /* last element in list */
    }
    
{
    struct address *old, *p;
    if(*last==NULL) { /* first element in list */
        i->next = NULL;
        i->prior = NULL;
        *last = i;
        *start = i;
        return;
        }

    p = *start; /* start at top of list */
    old = NULL;
    while(p) {
        if(strcmp(p->name, i->name)<O){
        old = p;
        p = p->next;
            }
    else {
        if(p->prior) {
            p->prior->next = i;
            i->next = p;
            i->prior = p->prior;
            p->prior = i;
            return;
            }
    i->next = p; /* new first element */
    i->prior = NULL;
    p->prior = i;
    *start = i;

    return;
        }
    }
    old->next = i; /* put on end */
    i->next = NULL;
    i->prior = old;
    *last = i;
}
```

Because the first or last element in the list can change, the dls\_store( ) function automatically updates pointers to the beginning and ending elements of the list through the start and last parameters. You must call the function with a pointer to the data to be stored and a pointer to the pointers to the first and last items in the list. When called the first time, the objects pointed to by first and last must be null. As in singly linked lists, retrieving a specific data item in a doubly linked list is simply the process of following the links until the proper element is found. There are three cases to consider when deleting an element from a doubly linked list: deleting the first item, deleting an item from the middle, and deleting the last item

```
void dldelete(
struct address *i, /* item to delete */
struct address **start, /* first item */
struct address **last) /* last item */
{
if(i->prior) i->prior->next = i->next;
else { /* new first item */
*start = i->next;
if(start) start->prior = NULL;
}
if(i->next) i->next->prior = i->prior;
else
/* deleting last element */
*last = i->prior;
}
```

Because the first or last element in the list could be deleted, the dldelete( ) function automatically updates pointers to the beginning and ending elements of the list through the start and last parameters. You must call the function with a pointer to the data to be deleted and a pointer to the pointers to the first and last items in the list.

![](<../../../../.gitbook/assets/image (35).png>)

example:

this is an example of a doubly linked list used to store some values with capabilities to add, delete and display each node:

```
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

/* self-referential structure */
struct node
{
    int value;
    struct node *next;
};

struct node* createNode(int);
void insertNodeAtTheBeginning();
void insertNodeAtTheEnd();
void insertNodeAtPosition();
void deletePosition();
void search();
void updateValue();
void display();

struct node *newnode, *ptr, *prev, *temp;
struct node *head = NULL, *tail = NULL;

int main() {
    int ch = '\0';

    while (true)
    {
        printf("\n---------------------------------\n");
        printf("\nOperations on a linked list\n");
        printf("\n---------------------------------\n");
        printf("\n1.Insert node at beginning");
        printf("\n2.Insert node at end");
        printf("\n3.Insert node at a specific position");
        printf("\n4.Delete Node from any Position");
        printf("\n5.Update Node Value");
        printf("\n6.Search Element in the linked list");
        printf("\n7.Display List");
        printf("\n8.Exit\n");
        printf("\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n");
        printf("\nEnter your choice: ");
        scanf("%d", &ch);

        switch (ch)
        {
            case 1:
                insertNodeAtTheBeginning();
                break;
            case 2:
                insertNodeAtTheEnd();
                break;
            case 3:
                insertNodeAtPosition();
                break;
            case 4:
                deletePosition();
                break;
            case 5:
                updateValue();
                break;
            case 6:
                search();
                break;
            case 7:
                display();
                break;
            case 8:
                printf("\n...Exiting...\n");
                return 0;
            default:
                printf("\n...Invalid Choice...\n");
                break;
        }
    }
    return 0;
}

/*
 * Creating Node
 */
struct node* createNode(int val)
{
    newnode = (struct node *)malloc(sizeof(struct node));
    if (newnode == NULL)
    {
        printf("\nMemory was not allocated");
        return 0;
    }
    else
    {
        newnode->value = val;
        newnode->next = NULL;
        return newnode;
    }
}

void insertNodeAtTheBeginning()
{
    int val = 0;

    printf("\nEnter the value for the node: ");
    scanf("%d", &val);
    newnode = createNode(val);
    if (head== tail && head == NULL)
    {
        head = tail = newnode;
        head->next = NULL;
        tail->next = NULL;
    }
    else
    {
        temp = head;
        head = newnode;
        head->next = temp;
    }
}

void insertNodeAtTheEnd()
{
    int val = 0;

    printf("\nEnter the value for the Node: ");
    scanf("%d", &val);
    newnode = createNode(val);
    if (head == tail && tail == NULL)
    {
        head = tail = newnode;
        head->next = NULL;
        tail->next = NULL;
    }
    else
    {
        tail->next = newnode;
        tail = newnode;
        tail->next = NULL;
    }

    printf("\n----INSERTED----");
}

void insertNodeAtPosition()
{
    int pos, val, cnt = 0, i;

    printf("\nEnter the value for the Node: ");
    scanf("%d", &val);
    newnode = createNode(val);
    printf("\nEnter the position ");
    scanf("%d", &pos);
    ptr = head;
    while (ptr != NULL)
    {
        ptr = ptr->next;
        cnt++;
    }
    if (pos == 1)
    {
        if (head == tail && head == NULL)
        {
            head = tail = newnode;
            head->next = NULL;
            tail->next = NULL;
        }
        else
        {
            temp = head;
            head = newnode;
            head->next = temp;
        }
        printf("\nInserted");
    }
    else if (pos>1 && pos<=cnt)
    {
        ptr = head;
        for (i = 1;i < pos;i++)
        {
            prev = ptr;
            ptr = ptr->next;
        }
        prev->next = newnode;
        newnode->next = ptr;
        printf("\n----INSERTED----");
    }
    else
    {
        printf("Position is out of range");
    }
}


void deletePosition()
{
    int pos, cnt = 0, i;

    if (head == NULL)
    {
        printf("List is empy\n");
        printf(":No node to delete\n");
    }
    else
    {
        printf("\nEnter the position of value to be deleted: ");
        scanf(" %d", &pos);
        ptr = head;
        if (pos == 1)
        {
            head = ptr->next;
            printf("\nElement deleted");
        }
        else
        {
            while (ptr != NULL)
            {
                ptr = ptr->next;
                cnt = cnt + 1;
            }
            if (pos > 0 && pos <= cnt)
            {
                ptr = head;
                for (i = 1;i < pos;i++)
                {
                    prev = ptr;
                    ptr = ptr->next;
                }
                prev->next = ptr->next;
            }
            else
            {
                printf("Position is out of range ");
            }
            free(ptr);
            printf("\nElement deleted");
        }
    }
}


void updateValue()
{
    int oldval, newval, flag = 0;

    if (head == NULL)
    {
        printf("List is empty\n");
        printf(":No nodes in the list to update\n");
    }
    else
    {
        printf("\nEnter the value to be updated: ");
        scanf("%d", &oldval);
        printf("\nEnter the new value:");
        scanf("%d", &newval);
        for (ptr = head;ptr != NULL;ptr = ptr->next)
        {
            if (ptr->value == oldval)
            {
                ptr->value = newval;
                flag = 1;
                break;
            }
        }
        if (flag == 1)
        {
            printf("\nUpdated Successfully");
        }
        else
        {
            printf("\nValue not found in List");
        }
    }
}

void search()
{
    int flag = 0, key, pos = 0;

    if (head == NULL)
    {
        printf("List is empty\n");
        printf(":No nodes in the list\n");
    }
    else
    {
        printf("\nEnter the value to search ");
        scanf("%d", &key);
        for (ptr = head;ptr != NULL;ptr = ptr->next)
        {
            pos = pos + 1;
            if (ptr->value == key)
            {
                flag = 1;
                break;
            }
        }
        if (flag == 1)
        {
            printf("\nElement %d found at %d position\n", key, pos);
        }
        else
        {
            printf("\nElement %d not found in list\n", key);
        }
    }
}

void display()
{
    if (head == NULL)
    {
        printf("List is empty\n");
        printf(":No nodes in the list to display\n");
    }
    else
    {
        for (ptr = head;ptr != NULL;ptr = ptr->next)
        {
            printf("%d\t", ptr->value);
        }
    }
}
```
