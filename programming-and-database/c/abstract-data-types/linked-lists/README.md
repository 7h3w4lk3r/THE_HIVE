# Linked List

Queues and stacks share two traits: They both have strict rules for accessing the data stored in them, and the retrieval operations are, by nature, consumptive. In other words, accessing an item in a stack or queue requires its removal, and unless the item is stored elsewhere, it is destroyed. Also, stacks and queues both use a contiguous region of memory. Unlike a stack or a queue, a linked list can be accessed in a flexible fashion, because each piece of information carries with it a link to the next data item in the chain. In addition, a linked list retrieval operation does not remove and destroy an item from the list. In fact, you need to add a specific deletion operation to do this.

Linked lists can be either singly linked or doubly linked. A singly linked list contains a link to the next data item. A doubly linked list contains links to both the next and the previous element in the list. You will use one or the other of these linked list types, depending upon your application.

```
functions:
insert → adds an element at the beginning of the list
delete → deletes an element at the beginning of the list
deleteAt → removes the element using a given key
size → return the number of elements in the list
isEmpty → return true if list is empty otherwise return false
search → searchs an element using a key
display → display the complete list
```

![](<../../../../.gitbook/assets/image (30).png>)
