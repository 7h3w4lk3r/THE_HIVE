---
description: >-
  a tool for grouping elements together.( like an object but just for data
  types)
---

# Structures

## Creating a structure

```text
struct struct_name
{
int var1;
int var2;
int var3; 
};
```

## Using a structure

variables can now be declared to be a type of "struct\_name"

```text
struct struct_name var1
```

to set the value of the day in the variable :

```text
struct_name.var1 = value1;
```

to test the value of the variable:

```text
if ( struct_name.var1 == value )
...
```

example:

```text
struct date {    // a structure with 3 parameters
int month;
int day;
int year;
};

struct date today;    // a variable of type date

today.month = 5;
today.day = 21;
today.year = 2015;
printf("todays date is %i/%i/%i.\n",today.month, today.day,today.year);
```

we can also do it this way:

```text
 struct date {   
int month;
int day;
int year;
} today;
```

#### we do not have to give a structure a tag name if all of the variables of a particular structure type are defined when the structure is defined the structure name can be omitted

```text
 struct {   
int month;
int day;
int year;
} today;
```

#### a disadvantage of this is that we can no longer define further instances of the structure in another statement all the variables of this structure type that you want in your program must be defined in the one statement

## Initializing structures

```text
struct date today = { 1,5,2019};
```

just like an array initialization fewer values might be listed than are contained in the structure:

```text
struct date today = { 1,5}; → no value for year
```

we can do it this way too:

```text
.member = value
i.e:
struct date date1 = {.month = 12, .day = 23};
```

## Compound literals \( C11 only \)

we can assign one or more values to a structure in a single statement using what is known as compound literals

```text
 today = (struct date){9,12,2016}; 
```

## Structure arrays

declaring an array of structures is like declaring any other kind of array:

```text
struct date {
    int day;
    int month;
    int year;
};

struct date myDates[10];

myDates[1].month = 6;
myDates[1].day = 5;
myDates[1].year = 2014;
```

or

```text
struct  date myDates[5] = {{12,1,1985},{12,5,1984},{11,12,1998}};
```

also the inner pairs of braces are optional:

```text
struct  date myDates[5] = {12,1,1985,12,5,1984,11,12,1998}; → initialized just the third element of the array to the specified value

struct  date myDates[5] = {[2] = {11,12,1998}}; → sets just the month and day of the second element of the myDates array to 12 and 30

struct  date myDates[5] = {[2].month = 12 };
```

example:

```text
 struct date {
    int day;
    int month;
    int year;
};
struct date myDates[10] = {[1]=12,2,1998,[2]=19,11,1975,[3]=24,5,1966};
for(int i = 0; i <= 4; ++i)
printf("date: %i/%i/%i\n",myDates[i].day,myDates[i].month,myDates[i].year);
```

## Structures containing arrays

```text
struct month
 {
 int    numberOfDays;
 char name[3];
 };
```

this sets up a month structure that contains an integer member called numberOfDays and a character member called name member name is actually an array of three characters

```text
struct month aMonth;
 aMonth.numberOfDays = 31;
 aMonth.name[0] = 'j';
 aMonth.name[1] = 'a';
 aMonth.name[2] = 'n';
```

you can also initialize this variable to the same values:

```text
 struct month aMonth = {31, {'j','a','n'}};
```

you can setup 12 month structures inside an array to represent each month of the year

```text
 struct month months[12]; 
```

example:

```text
 struct month {
    int numberOfMonth;
    int numberOfDays;
    char name[3];

};
struct month aMonth;
aMonth.numberOfDays = 31;
aMonth.name[0] = 'j';
aMonth.name[1] = 'a';
aMonth.name[2] = 'n';
aMonth.numberOfMonth = 1;

printf("month name: %i number: %i days: %i",aMonth.name,aMonth.numberOfMonth,aMonth.numberOfDays);
```

## Nested structures

C allows us to define a structure that itself contains other structures as one or more of its members

```text
struct dateAndTime
 {
struct date sdate;
struct time stime;
 };
 
 struct dateAndTme event;
 
 event.sdate.month = 10;
 ++event.stime.secends;
 
 struct dateAndTime event = {{ 2,1,2015}, {3,30,0}};
```

or

```text
 struct dateAndTime event = {
 {.month = 2 , .day = 1 , .year = 2019},
 {.hour = 3, .minutes = 30, seconds = 0}
 };
```

we can use members names in initialization

example: an event scheduler with nested structures:

```text
#include <stdio.h>

int main(){

    struct date{
        int day;
        int month;
        int year;
    };

struct time{
    int second;
    int minute;
    int hour;
};


    struct datetime{
        struct date sdate;
        struct time stime;
    };

    struct datetime event;

    printf("enter a second: ");
    scanf("%d",&event.stime.second);
    printf("enter a minute: ");
    scanf("%d",&event.stime.minute);
    printf("enter a hour: ");
    scanf("%d",&event.stime.hour);
    printf("enter a day: ");
    scanf("%d",&event.sdate.day);
    printf("enter a month: ");
    scanf("%d",&event.sdate.month);
    printf("enter a year: ");
    scanf("%d",&event.sdate.year);

    printf("your even scheduled for: %d/%d/%d %d:%d:%d\n",event.sdate.day,event.sdate.month,event.sdate.year,event.stime.second,event.stime.minute,event.stime.hour);

return 0;
};
```

### Array of nested structures

```text
 events[0].stime.hour = 12;
 events[0].stime.minutes = 0;
 events[0].stime.seconds = 2;
```

## Declaring a structure within a structure

```text
struct time
 {
 struct Date
 {
 int day;
 int month;
 int year;
 
 } dob;   // a variable
 
 int hour;
 int minutes;
 int seconds;
 };
```

#### the declaration is enclosed within the scope of the time structure definition it doesn't exist outside it it becomes impossible to declare a date variable external to the time structure

## Structures and pointers

declaring a struct as a pointer

```text
 struct date *datePtr;
 
 datePtr = &todaysDate;
 
 (*datePtr).day=21;
```

### using structs as pointers

test the value of month stored in the date struct pointed to by datePtr:

```text
 if ((*date*Ptr).month ==12 )
```

a special operator exists for pointers to structures:

```text
 (*x).y
```

to be more clearly expressed as:

```text
 x->y
```

example:

```text
struct date{
    int month;
    int day;
    int year;
};

struct date today, *datePtr;

datePtr = &today;

datePtr->month = 12;
datePtr->day = 4;
datePtr->year = 1998;

printf("today is : %i/%i/%i\n",datePtr->day,datePtr->month,datePtr->year);
```

## Structures containing pointers

example:

```text
struct intPtrs
 {
 int *p1;
 int *p2;
 };

 struct intPtrs pointers;
int i1 = 100, i2;

pointers.p1 = &i1;
pointers.p2=&i2;
*pointers.p2 = -97;

printf("i1 = %i, *pointers.p1=%i\n",i1,*pointers.p1);
printf("i2 = %i, *pointers.p2=%i\n",i2,*pointers.p2);
```

## Structures as arguments to functions

```text
 struct family{
    char name[20];
    int age;
    char father[20];
    char mother[20];
};

bool siblings(struct family member1, struct family member2){
    if (strcmp(member1.mother,member2.mother) == 0)
        return true;
    else
        return false;
}
```

## Returning a structure from a function

the function prototype has to indicate this return value in the normal way

```text
struct Date my_fun(void);
```

#### this is a prototype for a function taking no arguments that returns a structureof type Date

#### its more convenient to return a pointer to a structure.

example:

```text
#include <stdio.h>

struct date{
    int day;
    int month;
    int year;
};

struct time{
    int second;
    int minute;
    int hour;
};


struct datetime{
    struct date sdate;
    struct time stime;
};

char scheduler(struct datetime event){
    printf("your event scheduled for: %d/%d/%d %d:%d:%d\n",event.sdate.day,event.sdate.month,event.sdate.year,event.stime.second,event.stime.minute,event.stime.hour);
};


int main(){
    struct datetime event;
    printf("enter a second: ");
    scanf("%d",&event.stime.second);
    printf("enter a minute: ");
    scanf("%d",&event.stime.minute);
    printf("enter a hour: ");
    scanf("%d",&event.stime.hour);
    printf("enter a day: ");
    scanf("%d",&event.sdate.day);
    printf("enter a month: ");
    scanf("%d",&event.sdate.month);
    printf("enter a year: ");
    scanf("%d",&event.sdate.year);
    scheduler(event);
return 0;
};
```

example:

```text
#include <stdio.h>

struct funds{
char bank[1000000];
double bankfund;
char save[1000000];
double savefund;
};

double sum(struct funds moolah){
    return(moolah.bankfund + moolah.savefund);
}


int main() {
struct funds stan = {
        "garlic-melon bank",
        47432.34,
        "luckys savings and loan",
        854.435
};

printf("stan has a total of $%.2f.\n",sum(stan));
return 0;
}
```

