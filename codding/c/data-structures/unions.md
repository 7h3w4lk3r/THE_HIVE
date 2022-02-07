# Unions

a union is a derived type \(similar to structure\) with members that share the same storage space. sometimes the same type of construct needs different types of data. used when its necessary to store different types of data in the same storage area.

#### unions have members. only one member can contain a value at any time so only one access of a member at a given time. a member can have any data type.

#### unions are useful in embedded programming, situations where direct access to the hardware/memory is needed

![](../../../.gitbook/assets/1%20%282%29.png)

![](../../../.gitbook/assets/1%20%281%29.png)



## Defining a union

the declaration is like structure

```text
union [uniontag] {
type1 identifier1;
type2 identifier2;
...
} optional_vriable_definitions;
```

#### the union tag is optional and each member definition is a normal variable definition

example:

```text
union data {
        int i;
        float f;
        char str[20];
    }data;
```

the length of this is same as 'str' variable cause it hase the largest size in the union.

## Creating union variables

example:

```text
#include <stdio.h>

union car {
    int i;
    float f;
    char c[40];
};

int main(){
    union car car1, car2, *car3; // note: car3 is a pointer
    printf("memory size occupied by data union %zu\n", sizeof(car1));
return 0;
}
```

## Anonymous unions

in c11 an unnamed memory union of structure.

example:

```text
#include <stdio.h>

struct owner {
    char socsecurity[12];
};

struct leasecompany {
    char name[40];
    char headquarters[40];
};

struct car_data {
    char make[15];
    int status;
    union {     // anonymous uniondefined in a struct with 
        // no name and cant be user anywhere else
        struct owner owncr;
        struct leasecompany leasecar;
    };
};
```

## Accessing union members

exactly like accessing struct members and values

```text
union {
int code;
float cost;
} item;

item.code = 1245;  
```

```text
union {
int code;
float cost;
} item, *ptrst;

ptrst = &item;
ptrst->code = 432;
```

#### we have three ways:

* ptrst-&gt;code
* item.code
* \(\*ptrst\).code

example:

```text
#include <stdio.h>

union mixed {
    char c;
    float f;
    int i;
};

int main(){
    union mixed x;
    x.c = 'd';
    printf("character: %c\n",x.c);
    return 0;
}
```

 

















