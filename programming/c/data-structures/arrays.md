# Arrays

is a very common in a program to store many data values of a specific type. arrays allow grouping values under a single name. the data items in an array are referred to as elements

```
long numbers[10]; → the number 10 specifies the size of array so for array numbers we have 10 elements from 0 to 10
```

to call or assign values to each element:

```
number[4] = 14; → assign int 14 to element 4 of numbers array
```

example:

```
/* take 10 grades and print average and sum*/
 
     int grades[10];
    int count = 10;
    long sum = 0;
    float average = 0.0f;
    printf("enter the grade: \n");
    for( int i = 0 ; i < count ; ++i){
        printf("%2u> ",i + 1);
    scanf("%d",&grades[i]);
    sum += grades[i];
    }
average = (float)sum/count;
    printf("average: %f\n",average);
    printf("sum : %i\n",sum);
```

## Initializing an array (lists)

**float sample\_data\[500] = {1,2,3,4,5}; →** the capacity is 500 values but we declared only 5 so there will be 495 empty values available and these numbers ( 1 to 5) are assigned to the first three elements of the array

## Designated initializers

c99 added a feature called designated initiazlizers allowing us to pick and choose which elements are initialized

we can assign array elements values in any order by enclosing an element number in a pair of brackets.

```
 float simple_data[500] = { [2] = 500.5, [1] = 300.0, [0] = 1000.0 };
```

so here element number 2 of array simple\_data is 500.5 and so on.

```
 #define months 12
int days[months] = {31,28,[4]=31,30,31,[1]=29};
```

to initialize a range in array :

```
int a[] = {start..end};
 
int a[] = { [0..9] = 1, [10..100] = 23 };
```

## Two-dimensional arrays

the c language allows arrays of any dimensions to be defined two dimension arrays are the most common one and the most natural application of this is the case of a matrix

```
int matrix[4][5]; → 4 rows and 5 columns for a total elements of 20
int matrix[4][3] = {[0][0] = 1, [1][1] = 5 ,[2][2]= 9};
```

example:

```
 int numbers[3][4]= {
        {10,20,30,40}, //values for first row
      {15,25,35,45}, //values for second row
      {47,48,49,50} //values for third row
};
```

## Three-dimensional arrays

for storing x,y,z values

```
 int matrix[4][5][3]; 
 int matrix[4][2][3] = {[0][0][0] = 1, [1][1][1] = 5 ,[2][2][2]= 9};
```

example:

```
int numbers[2][3][4]= {
        {   // first block of 3 rows
            
                {10, 20, 30, 40}, //values for first row
                {15, 25, 35, 45}, //values for second row
                {47, 48, 49, 50} //values for third row
        },
        
        {   //second block of 3 rows
            
                {10, 20, 30, 40}, //values for first row
                {15, 25, 35, 45}, //values for second row
                {47, 48, 49, 50} //values for third row
            
        }
```

## Processing elements in a n dimensional array

```
int numbers[2][3][4]
 int sum =0;
for(int i=0; i<2; ++i) 
    {
        for(int j = 0 ; j < 3 ; ++j)
        {
            for(int k = 0 ;k < 4 ; ++k)
            {
                sum += numbers[i][j][k];
            }
        }
    }
```

## Variable length arrays (VLA)

introduced in c99. it allows you to specify the size of an array with a variable when creating an array. a VLA keeps the same size after creation.

```
 float letters[n]; →  n is a variable which specifies the length of letters array
```

## Flexible array members

a feature introduced in the c99 standard of the c language. when using a structure we can declare an array without a dimension and whose size is flexible in nature. a flexible array members size is variable (can be changed at runtime)

```
 struct s {
 int arraySize;
 int array[];
 }; 
```

#### a flexible array member is declared by specifying empty square brackets \[]

{% hint style="info" %}
a flexible array can be declared only as the last member if a struct

each struct can contain at most one flexible array member

a flexible array cannot be the only member of a struct

any struct containing a flexible array member cannot be a member of another struct

a struct with a flexible array member cannot be statically initialized, it must be allocated dynamically.

you cannot fix the size of the flexible array member at compile time
{% endhint %}
