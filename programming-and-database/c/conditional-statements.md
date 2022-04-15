# ⭕ Conditional Statements

## if statement

#### `if (condition) do this`

#### `else if (condition2)` `do this`

#### `else`

#### `do`

`example:`

```
int target = 7;
int number;
while (number != target)
{
printf("guess a number between 1 and 10: ");
scanf("%i",&number);
if (number > target)
    printf("the number is higher than target\n");
else if (number < target)
    printf("the number is lower than target\n");
else
    printf("well done, nice guess\n");
}
return 0;
```

## Conditional operator (ternary statement)

`condition ? expression1 : expression2`

example:

```
x = y >7 ? 25 : 50 ;
 if x = y is greater than 7, set x to 25, otherwise set x to 50
 
 same as:
 
if (y > 7)
    x=25;
else
    x=50;
```

## switch statement

`switch (expression) {`

`case value 1: do this break;`

`case value 2: do this`\
`break;`

`case value n: do this break;`

`default: do this break; }`

example:

```
 enum weekday {sat, sun, mon, tue, wed, thu, fri };
enum weekday today = mon;

switch (today){
    case sat:
        printf("its saturday");
        break;
    case sun:
        printf("its sunday");
        break;
    case mon:
        printf("its monday");
        break;
    case tue:
        printf("its tuesday");
        break;
    default:
        printf("whatever");
        break;
```

## Go To

goes to the given part of the program and starts execution from there should never need to use the goto statement in C.

example:

```
 #include <stdio.h>

int main(){
    const int maxIndex = 5;
    int i = 0;
    double number,average,sum = 0.0;

    for(i = 1; i <= maxIndex; ++i){
        printf("%d enter a number",i);
        scanf("%lf",&number);
        if (number < 0.0)
            goto jump;
            sum += number;
    }

    jump:
    average = sum/(i-1);
    printf("sum = %.2f\n",sum);
    printf("average = %.2f\n",average);

return 0;
}
```

{% hint style="info" %}
**using 'goto' is a bad idea for making conditional statements, try to use it only when you want to break out of nested loops or statements ( just to make the code more clean and readable )**
{% endhint %}
