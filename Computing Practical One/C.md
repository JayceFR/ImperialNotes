# C 
## Float stuff

*Note :* use fabs from <math.h> header file. 
### Math.h 
*Note:* when using <math.h>, add -lm to link ccommand, by setting 
```c
LDLIBS = -lm 
```
in your .cbuild or makefile.

## Enums
```c
#include <stdio.h>

enum day {Mon, Tue, Wed, Thu, Fri, Sat, Sun}

int main(void){
	enum day d;
	d = Mon; 
	d++;
	// Now d is Tue
}
```

enums start with 0, and increment by 1.
*Note :* The new type created is 'enum day' and not 'day'!!!

Enumerations in enums 
```c
for (enum day d = Mon; d <= Sun; d++){
	...
}
```
and 
```c
switch(d){
	case Mon: printf("Monday"); break;
	case Tue: printf("Tuesday"); break; 
	case Wed; printf("Wednesday"); break; 
}
```

Typedef

To prevent writing 'enum day'
```c
typedef enum {Mon, Tue, Wed, Thu, Fri} day;
```

Now we can just say 'day d'

*Note:* the difference between 
```c
unsigned int x;
and 
typedef unsigned int x;
```
in the first line we are declaring a variable x of type unsigned int.
in the second one we are declaring the type unsigned int into x. so x is now a type. 

## Array

Array Gotchas
1. When passing an array to a function, the function has no clue how large the array is. So in practice you would send a pointer to the first element and then the size of the list. 

### Macro for .length :)
```c
#define NELEMS(arr) (size_of(arr)/size_of(arr[0]))
int list[] = {0,1,2,3,4};
int length = NELEMS(list);
```

## Strings
*NOTE!!!! :* REMEMBER about the `\0` at the end of the string.... 
so the size of intializing 
```c
char stringVar[] = "Hello"; 
```
would be 1 + length(stringVar) so 1 + 5 = 6. 
so sizeof(stringVar) = 6

### Functions from <string.h>
1. strcpy(stringvar, "Hi!")
	1. It modifies the stringvar to hold the new string. The remaining bits remain the same as the old string but the terminator `\0` prevents it from happening. And dont overrite the terminator. Cause if you do, you would basically combine both the strings. 
	2. Say you have stringvar = "Hello!". Now after doing the strcpy in memory you would have `Hi!\0o!\0`. Now if we replace the first terminator with H, by saying stringVar[3] = 'H'. We would get Hi!Ho!. 
	3. Ensure space is enough. 
2. strcat(stringVar, " there")
	1. appends `there` to the end of the stringVar. 
	2. Ensure there is enough space in stringVar buffer for `there`.
3. strlen(stringVar)
	1. Gets the length of the string. 
```c
	char stringVar[100] = "Hello";
	printf("%ld", strlen(stringVar)); // 5
```
would give 5. *Important:* It doesn't count the terminator. 
Think of it just like the .length. 
4. sprintf()
	1. basically like the printf, but stores the output into the string instead. 
	2. sprintf(stringVar, "debug : x = %d, y = %d", 5, 6); would store `debug : x = 5,y=6` into stringVar. 

## Pointers oooffffff
[[Assembly AT&T x86-64#Pointers ooF!|Pointers from architecture]]
```c
int arr[] = {1,2,3};
int *ip = &arr[0];
int *ip = arr
```
Last two lines are equivalent. 
When we don't add in square brackets to arr, it returns a pointer to the first element. This is called *basal decay*

Pointer code for summing up an array 
```c
int arr[] = {....};
int *beyond = arr + NELEMS(arr); // Going to point to the mem location after the final element in arr
int total = 0;
for (int *p = arr; p < beyond; p++){
	total += *p;
}
printf("%d", total);
```

Post increment 
```c
char msg[] = "Hi!";
char *p = msg;
char ch = *p++;   // ch = 'H'
char ch2 = *p++;  // ch = 'i'
```
So `*p++` parses as `*(p++)`. Called post increment which delivers the current value of `*p` and then increments the value of p as a side effect. 

there are even 
```c
*p--
*++p
*--p 
```

## Functions 

### Ordering of functions with main

*Note* All functions that are called from main ust be declared before main. 
All functions that are called by a function X, must be defined before X is defined.
YUCK!!!!
Saviour : Prototype.

### Prototype (extern)
```c
extern int wibble(double a, double b);
extern int wibble(double, double);
int wibble(double a, double b, double c);
int wibble(double, double);
```
all of the above are queivalent. Pick One!!!

so now we can have the following order of the square function 
```c
extern int squared(int);
int main(void){
	printf("%d", squared(5));
}
int squared(int a){
	return a * a;
}
```
Gotcha
```c
extern int wibble()     // takes any number of args
extern int wibble(void) // takes no args 
```
Note the difference. It is different compared to c++, Java. 


## Structures 

*Note :* Passing a structure to a function is done by value. So *copied* 
So if you put an array into a structure and pass it into a function. It would be copied.... WOOOO!!

```c
#include struct point { double x, y; }; 
struct point flipx( struct point p ) { p.x =-p.x; return p; } 
int main( void ) { 
	struct point p = { 5.0, 5.0 }; 
	printf( "(%g, %g)\n", p.x, p.y ); 
	struct point flipped = flipx( p ); 
	printf( "(%g, %g)\n", flipped.x, flipped.y );
}
```

we can modify the above function to pass by reference as well 

```c
void flipx( struct point *pp ) {
	pp->x = -pp->x; 
}
```

*Note the ->* it is a shorthand for `(*pp).x`

Shorthand type declaration 
```c
typedef struct { double x, y; } point;
```
Now we can just say point, rather than struct point. 

```c
typedef struct {
	int myNum;
	char myLetter;
} myStruct; 
```
*Note:* The `;` between the fields in the struct

## Unions
At one time only one thing would be alive :)
```c
typedef union { 
	char c; int i; 
} myunion;
myunion u;
```

we can even define  a struct inside a union as well 

```c
typedef union{
	struct MyStruct{
		int i;
		char j;
	} myStruct;
	struct YourStruct{
		float i;
		char[] *j;
	} yourStruct;
} myunion; 
```

*Note:* You can't use typedefs...

## Global variables 

```c
int counter = 1;
int main(){
	...
}
```

*Note:* counter is the global variable. 

Local scope, Global lifetime 

```c
void count(void){
	static int counter = 1;
	counter++;
}
int main(){
	count();
	count();
	return 0;
}
```
The aboive program would output 1,2. 

## Assertions
```c
#include <assert.h>

int main(){
	int a = 5; 
	assert(a != 0);
}
```
Located in the <assert.h> module. 

## NULL pointer
NULL is part of <stdlib.h>


## ADTs 

```c
struct dynarray{
	int c;
	char hellowold[25];
};
typedef struct dynarray *dynarray;
```
Basically C's way of dealing with objects, where objects are just references. 

### Generics in C
Well we use void*
TODO : Add more stuff here ig, but it is relatively simple.

### Gotcha in ADTs

suppose we want to define an ADT for linked list where we have nodes. 

```c
struct intlist;
typedef struct intlist *intlist;
struct intlist{
	int head;
	intlist tail;
}
```
*Note:* How we are defining a dummy before and then using it later. 



# GDB ooffff
Need to add *-g* to the CFLAGS in the make file to allow gdb to debug. 

## TUI mode
Text user interface... The amazing split up mode. 
to use this type 
```
gdb -tui ./hello
```
*Note:* we can only debug the produced executable. 

## Commands 

1. whatis __
	1. prints the type of __
	2. so for example if we define q as `char q[5];` then `whatis q` would print `char q[5]` 
 2. p __
	 1. prints the value of the __
3. where 
	1. prints out the stack frames 
4. frame __
	1. jumps to the line in the stack frame. 
5. watch __
	1. wathces the variable

# Valgrind oooffff
Used to check memory leaks
just remember the command valgrind ./program
boom we are done 

# Profiler

Finds hot spots, and we can look at those hot spots and try optimising them to make them less hot, therefore making it faster. 
Add `-pg` to CFLAGS and LDLIBS in the makefile.
Run `make clean all`
Run `make profile` 
this would produce a binary file called `gmon.out`
Now we need to use *gprof* to analyze the executable `gmon.out` which produces a report showing the top 10 functions (across all their calls) sorted by percentage of total runtime. 




