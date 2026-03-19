# Basics Gotchas

1. *Imm* translates to *Mem[Imm]*
so for example, we want to say move the value stored at memory address 0x4A to rax then we do 
```armasm
movq 0x4, %rax # means to move the value stored at memory address 0x4.
```
*Note:* In the above case we didn't use the $ sign, using the $ sign means we are moving the actual number 0x4 to memory. 
```armasm
movl $0x4, %eax # moves the number 4 to eax
```

2. Imm(r1, r2, s)
It means 
```armasm 
Imm(r1, r2, s) -> Mem[R[r1] + s * R[r2] + Imm]
```
where *s should be either 1 2 4 8*
Usiage: 
```armasm
movl 0x5(%rax, %rdx, 4), %eax 
```
*Note* There is no $ sign before 0x5

# Pointers ooF!

basically we define a type of pointer using * , so
```c
int test = 4;
int *p = &test 
p
```
and the & sign is used to get the memory address 

we again use * to dereference the pointer
```c
int test = 4;
int* p = &test;
printf("%d", *p); // prints 4
*p = 5;
printf("%d", *p); // prints 5
```
And *most importatntly* even 
```c
printf("%d", test) // prints 5
```
would print 5, as p never exists, it is just holding a reference to a memory location test. DAMN. 

Examples: 
```c
int test = 4; 
int *p = &test;
```
converting to assembly we get
saved for future Jayce, lol :) 

Present Jayce : I believe we save the register into the stack and pass in that memory location. 
Future Jayce : Yes, I can confirm that 
so I believe we get, assuming test is in rdx register and p is in rcx register 
```armasm
movl $4, %edx
subq $8, %rsp
movl %edx, (%rsp)
movq %rsp, %rcx 
```
But on chatgpting, we need to even save the pointer on the stack as well 
so we get
```armasm
subq $16, %rsp           # allocate stack space
movl $4, (%rsp)          # store 'test' (4 bytes)
movq %rsp, %rax          # get address of 'test'
movq %rax, 8(%rsp)       # store pointer to 'p'
```
# Compare and jump order
text bk 
```armasm
cmpl b, a  # it computes a - b 
```

what you as a programmer should know is 
```armasm
cmpl b, a 
jl target # would jump iff a < b 
```
*Note* the order, it is important. last comes first (a comes before b).
Think of it like the same way of ordering stuff in assembly. it's always been 
SRC, DEST. 
so read Dest = Dest - Src or 
Dest = Dest + Src... you get the idea.

# CMOV - fun function
```armasm 
say 
%rdi has x 
%rsi has y 
cmpq %rsi, %rdi
cmovle %rdx, %rax # if x <= y then perform the move.
```
*Note* the *le*, you can replace it with anything of your choice.

Something random 
	*Note* we always need the q l or b after cmp and test as well. so cmpq, testq, so on...

# While translation 

```c
while(test)
	Body 
```
```c
goto test 
loop:
	Body
test:
	if (Test)
		goto loop
done:
```

# Indirect Jump

```armasm
jmp *.L4(,%rdi,8)
```
*Note:* the * for indirect jump.

# Control Flow; Stack Discipline

![[Pasted image 20250504155151.png]]

rsp hold the *address* of top of the stack.
rip holds the memory address of the *next* instruction. 

We use the stack for the following reasons 
1. Not enough registers
2. Address operator & is used 
3. Arrays or structures are used as local variables. (prob becuase we ass in the pointer to the array or the structure when using it)
4. A procedure allocate space on the stack by decremneting the stack pointer. 

# Note about Imm(r1, r2, scale)
the Imm(r1, r2, scale) is always a memory instruction, except for lea.
can be even used for addq.

# Motivation for aligning data
For 32 bit system data is accessed by chunks of 4 bytes. 
For 64 bit system data is accessed by chunks of 8 bytes. 
so aligning would mean we wouldn't need to fetch two different chunks for part of the data which is in half of one and half in another. 
It speeds it up. 

# Cache oooooffff
### Block / Line
block is just a collection of bytes. so we can have a block with 4 bytes or 2 bytes. 
*Important :* Even the cache would have a block stroed for each index. 
so if we have 2 bytes per block, then at a particular index in the cache we would have 2 possible bytes stored / can store. *Note* we don't need a tag to differnetiate between the elements in the block, as we can just retrieve the entire block and use the offset to get the particular data. 

### Cache is organised into (S, E, B)

2^s, sets 
each set has 2^e lines per set 
each line has 2^b bytes of data. 

2 way set associative means, each set has 2 blocks
n way set associative means, each set ha n blocks. 





