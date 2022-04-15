# ⭕ Bit Manipulation

bit manipulation is the act of algorithmically manipulating bits or other pieces of data shorter than a word.

tasks that require bit manipulation:

* low-level device control
* error detection
* correction algorithms
* data compression
* encryption
* algorithms optimization

## binary numbers system

each position value in a binary number are the powers of two

```
 128      64      32        16          8           4         2       1
 2^7     2^6     2^5        2^4        2^3         2^2        2^1    2^0
```

![](<../../.gitbook/assets/image (26).png>)

{% hint style="info" %}
1 byte = 8 bits

0 is off, 1 is on
{% endhint %}

the rightmost bit of a byte is known as the least significant or low-order bit, the leftmost bit is known as the most significant or high-order bit

changing a bit to 1 is called setting a bit changing a bit to 0 is called resetting a bit

## bits for basic C data types

```
 BIT            _Bool           1           0 to 1
 Byte           char            8           -128 to 127 → ACSII table
 word           short int       16          -32768   to 32767
 long           long int        32          -2147483648 to 2147483647
```

## negative numbers (signed)

the representation of negative number:

```
 computer represents such numbers using a "twos complement" notation:
 the leftmost bit represents the sign bit
 if its 1 number is negative
 if its 0 number is positive
```

a way to convert negative numbers from decimal to binary is to first add 1 to the value express the absolute value of the result in binary complement all the bits change all the 1s to 0s and 0s to 1s

example:

```
 to convert -5 to binary 1 is added which gives -4 
 4 expressed in binary is 00000100
 complementing the bits produces 11111011
```

example:

```
 // convert decimal to binary
 
 #include <stdio.h>
 #include <math.h>
long long converter(int n){
long long bin = 0;
int remainder, i=1;
while(n != 0){
    remainder = n % 2;
    n = n/2;
    bin += remainder * i;
    i *= 10;}
return bin;}

int main(){
    int n = 0;
    long long result = 0;
    printf("enter a decimal number: ");
    scanf("%d",&n);
    result = converter(n);
    printf("%d in decimal = %ld to binary",n,result);
    return 0;}
```

example:

```
 // converting binary to decimal
 
 #include <math.h>
#include <stdio.h>

long long converter(long long n){
    int dec = 0, i = 0 , remainder = 0;
    while (n != 0){
        remainder = n % 10;
        n = n / 10;
        dec += remainder*pow(2,i);
        ++i;}
    return dec;
}
int main() {
    long long n = 0;
    int result = 0;
    printf("enter a binary number: ");
    scanf("%lld",&n);
    result = converter(n);
    printf("%lld in binary = %d in decimal",n,result);
    return 0;
}
 
```

if you see the error about pow() in IDE You need to link with the math library:

```
gcc -o sphere sphere.c -lm
```

The error you are seeing: error: ld returned 1 exit status is from the linker ld (part of gcc that combines the object files) because it is unable to find where the function pow is defined.

Including math.h brings in the declaration of the various functions and not their definition. The def is present in the math library libm.a. You need to link your program with this library so that the calls to functions like pow() are resolved.
