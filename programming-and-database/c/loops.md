# Loops

## there are 3 types of loops in c:

1.  **for**
2.  **while** 
3. **do while**

## while loop

#### while \(condition\){ do this }

also has break and continue operands

```text
#include <stdio.h>
int main()
{
    int index = 0;
    while (index < 10)
    {
        printf("%d\n",index);
        index += 1;
    }
return 0;
```

## while true loop \( infinite while loop\)

#### while\(1\){ ... }

## do-while loop

is a loop where the body is executed for the first time unconditionally. always guaranteed to execute at least once.

#### do { statement } while \(expression\);

example:

```text
do
{printf("counter: %d\n",counter);
    counter++;}
while (counter <= 10);
```

## for loop

#### for \( statement ; condition; action\){ do this until reaching the condition }

example:

```text
for (int count=1 ; count <= 10 ; ++count)
{
    printf("%d\n",count);
}
```

## infinite for loop

#### ​​for\(;;\) { / _statements_ / }

## nested loop \(for loop example\)

example:

```text
for (int x = 0; x <= 9; ++x) {
    for (int y = 0; y <= 9; ++y)
    {
    printf(">>> %d\n",x*y);
    x+=x;
    y+=y;
    }
}
```



