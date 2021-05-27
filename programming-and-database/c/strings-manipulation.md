# Strings Manipulation

## Summary

```text
strlen(a)                      → get the length of a

strncat(a,b,sizeOfA)     → append b to a 

strchr(a,'a')          → search for first occurence of 'a' in  string a

strrchr(a,'a')          → search for last occurence of 'a' in string a

strstr(a,b)             → search for string b in a

strncmp(a,b,sizeOfA)    → compare strings a and b

strncpy(a,b,sizeOfA)       → copy b to a (overwrite a)

memcmp(a,b,FirstNBytesToCompare) → compare first n bytes

memchr(a,'a',sizeOfA)  → search array a for character 'a'

strtok(a,t)         → tokenize string a by string t
```



## String representation

#### char word\[7\] = {"hellow"}; → always use double quotes and char type

 or 

#### char word\[7\] = "hellow"; → can remove brakets

 or 

#### char word\[\] = "hellow"; → this one is better



## String functions

[  
https://en.cppreference.com/w/c/string/byte](
https://en.cppreference.com/w/c/string/byte)

```text
#include <string.h>
```

the functions with an extra "n" in their names are safer to use because they check for size and can prevent errors or buffer overflow.

#### strlen\(\) → get the length of a string: strlen\(mystring\)

#### strcpy\(s1,s2\) / strncpy\(s1,s2,n\) → copy one character string to another \(replace\)

 n is usually the length of first variable replace first variable content with second one

```text
char src[20], dest[20]; → defines two empty strings
strcpy(src, "this is source"); → appends to strings
strcpy(dest, "this is destination");
```



#### strcat\(s1,s2\) / strncat\(s1,s2,n\) → combining two character strings together and puts the result in the first one: n is usually the length of first variable

strcat\(first,second\); → a copy of second string is appended to the first string and the result is saved to the first string, second one is not altered

```text
printf("string: %s", (strncat(var1,var2,__size_t 100)));

char var1[]="A",var2[] = "B";
printf("cat: %s",strcat(var1,var2));
```

#### strcmp\(s1,s2\) / strncomp\(s1,s2,n\) → determing if two strings are equal n is usually the length of first variable this function does for string what relational operators do for numbers: if it returns 0, the strings are same and none-zero if it returns &lt; 0 \(negative value\) then string1 is less than string2 if it returns &gt; 0 \(posetive value\) then string2 is less than string1

the "less than" here means in alphabetic order for example:

```text
 printf("%d\n",strcmp("A","B")); → returns -1, A is less than B
  printf("%d\n",strcmp("B","A")); → returns 1, B is greater than A
```

#### memchr\(\) → searches an array for the first occurrence of a character

example:

```text
#include <stdio.h>
#include <string.h>

int main(void)
{
    char str[] = "ABCDEFG";
    char *ps = memchr(str,'D',strlen(str));
    if (ps != NULL)
        printf ("search character found:  %s\n", ps);
    else
        printf ("search character not found\n");
    return 0;
} 
```

Output: 

search character found: DEFG



#### memcmp\(\) → compare the first n bytes of two strings if equal, returns 0 if var1 &gt; var2 returns positive if var2 &gt; var1 returns negative n is the number of first bytes to compare.e.g: size\_t 10

```text
unsigned char var1[]="12345", var2[]="12346";
printf("%d",memcmp(var1,var2,__size_t n));  
```

#### size\_t strlen\(s\) → returns the number of characters in s, excluding NULL character

#### strchr\(\) → searchs a given string for a specified character first arg is the string to be searched \(the address of a char array\) second arg is the character to search for the function will search the string starting at the beginning and return a pointer to the first position in the string where character is found. the address of this position in memory is of type char\* described as the pointer to char.

to store the value that's returned you must create a variable that can store the address of a character.

```text
char * ptr = strchr(a,'d'); 
```

if the character is not found the function returns a special value NULL. NULL is the equivalent of 0 for a pointer and represents a pointer that does not point to anything.

example:

```text
const char str[] = "http://www.tutorialspoint.com";
const char ch = '.';
char *ret;
ret = strchr(str, ch);
printf("String after |%c| is - |%s|\n", ch, ret);
```

String after \|.\| is - \|.tutorialspoint.com\|



#### strrchr\(string,character\) → searchs for the last occurence of the character c in string.

#### strstr\(\) → the most usefull searching function, searchs one string for the first occurrence of a substring , returns a pointer to the position in the first string where the substring is found, if no match, returns NULL.

example:

```text
char text[] = "evey dog has his day";
char word[] = "dog";
char *pFound = NULL;
pFound = strstr(text,word);
```

#### strtok\(\) → function is used for tokenizing strings a token is a sequence of characters within a string that is bound by a delimiter. a delimiter can be anything, but should be unique to the string. spaces, commas, and periods are good examples.

breaking a sentence into words is called tokenizing

example:

```text
char str[80] = "hello world - how are you - im w4lk3r";
const char s[2] = "-";
char *token;

token = strtok(str,s); // get the first token

while (token != NULL){
    printf("%s\n",token);
    token = strtok(NULL,s);
}
```

to extract all tokens:

```text

int main() {
    char string[50] = "Hello! We are learning about strtok";
    // Extract the first token
    char * token = strtok(string, " ");
    // loop through the string to extract all other tokens
    while( token != NULL ) {
        printf( " %s\n", token ); //printing each token
        token = strtok(NULL, " ");
    }
    return 0;
}
```

![](../../.gitbook/assets/246452%20%283%29.png)

#### c = getchar\(\); → gets a single character as input

#### putchar\(c\) → prints a single character as output

example:

```text
 // copy any input as output
 int main(){
int c;
c= getchar();
while (c != EOF){
    putchar(c);
    c=getchar();
```

example:

```text
// count the number of lines    
#include <stdio.h>

int main()
{

    int c, ln = 0;

    while((c = getchar()) != EOF){
        if(c == '\n'){
            ++ln;
            printf("number of lines: %d\n",ln);
        }
    }
    return 0;
}
```

## Reversing a string

```text
#include <stdio.h>
#include <string.h>

void reverse(char*, int, int);

int main()
{
    char a[100];
    gets(a);
    reverse(a, 0, strlen(a)-1);
    printf("%s\n", a);
    return 0;
}

void reverse(char *x, int begin, int end)
{
    char c;
    if (begin >= end)
        return;
    c          = *(x+begin);
    *(x+begin) = *(x+end);
    *(x+end)   = c;
    reverse(x, ++begin, --end);
}
```



## Analyzing strings

![](../../.gitbook/assets/246452%20%282%29.png)

example:

```text
 #include <stdio.h>
#include <ctype.h>

int main(){

    char a[100] = "hello world";

    for (int i = 0; a[i] != '\0'; i++ ){
        if(islower(a[i]))
            printf("lower: %c\n",a[i]);
    }


    return 0;
}
```

##  Finding mixed character strings \(without string.h\)

```text
 #include <stdio.h>
#include <stdbool.h>

int main(){

    char str[] = "Hello World";
        int   i;
    char  found_lower = false, found_upper = false;

    for (int i = 0; str[i] != '\0'; i++) {
        found_lower = found_lower || (str[i] >= 'a' && str[i] <= 'z');
        found_upper = found_upper || (str[i] >= 'A' && str[i] <= 'Z');

        if (found_lower && found_upper) break;
    }

    printf("%d",found_lower && found_upper);

    return 0;
}
```































