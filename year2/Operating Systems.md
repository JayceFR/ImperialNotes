Notes are taken from `Modern Operating Systems - Tanenbaum, Andrew S., 1944 | 5th edition`

# General

## OS as a Resource Manager

Two types of multiplexing (sharing)
1. Time multiplexing : Where each process gets a fixed time to use to entire CPU. 
2. Space mutliplexing : Where the CPU is divided and each process gets one part.

## History
Charles Babbage created the first computer 
Ada Lovelace was the world's first programmer W. 

## Some bookkeeping 
**Mechanism** are low level methods or protocols that implement a needed piece of functionality. ^4e05f6

**Policies** are algorithms for making some kind of decision within the OS. For example, given a number of possible programs to run on a CPU, which program should the OS run?
# Introduction 
Basically OS provides clean abstraction interface to applications and hides interface of different hardware devices. 

Features include 
## Managing Resources
OS must make efficient use of limited available resources in terms of time, space and money. 
OS must share resources among multiple users, so to provide a fair allocation and prevent interference between users. 
OS manages resources like 
1. Processors, e.g CPU cores, multi socket CPU, SMT/hyper threading 
2. Memory, e.g caches, RAM 
3. I/O devices (GPU, NIC)
4. Internal devices (clocks, timers, interrupt controllers)
5. Persistent storage (disks, SSDs, DVD)

## Provide clean interfaces 
OS hides complexity of lower levels from higher levels. 
OS keeps details of hardware hidden from programs, only OS can allow access to hardware resources. User requests for resources should be abstract. 
![[Pasted image 20260406125610.png]]
So the API that is wrapped around the bare machine OS is stable. 

## OS Characterstics 
### Sharing 
OS must share data, programs and hardware. Os uses time multilexing and space multiplexing. 
OS must support simultaneous access to resources. (Disks, RAM, network, GPU).
OS must offer mutual exclusion for some resources. OS must guarantee that risky operations are protected. 
OS must protect agianst accidental or malicious corruption. 
### Concurrency 
OS must support several simultaneous parallel activities, multiple users and programs must run in parallel. 
OS must offer primitives to synchronise actions. 
OS must protect users/process interference. 
### Non determinism
When a computer is running, results from events occur in an unpredictable order. 
Like timer interrupts, user input, program error, network packet loss, disk erorrs may happen at any time. 
### Storing data
Access control. Backups. 

# Kernel 
OS Kernel is a privileged program that implements OS functionality. 
3 different types of kernels 
1. Monolithic OS kernel (Linux, BSD, Solaris) - Single black box with all functionality. 
2. Microkernels (L4, Mach) - Little functionality in kernel as possible. 
3. Hybrid Kernels (Windows NT, Mac OS X) - Combines both. 

## Monolithic kernels 
Kernel is single executable with its own address space. 
Structure implied through pushing parameters to stack and trapping to execute system call. **What this means is that when in user mode, we have a system call which pushes system call onto stack and then it goes to a privelleged kernel mode which executes the system call. So the same thread like in pintos goes from user mode to kernel mode.**

![[Pasted image 20260406140702.png]]

*Advantages* 
- Efficient calls within kernel 
- Easier to write kernel components due to shared memory. 
*Disadvantages*
- Complex design with lots of interactions 
- No protection between kernel components. **So if some device driver fails then entire kernel comes crashing down**
## Microkernels 
Minimal kernel with funcitonality in user level servers. 
Kernel does *Inter Process Communication (IPC)*. IPC is between processes that allows them to communicate. 
![[Pasted image 20260406141441.png]]
*Advantages*
- Kernel itself not complex, so its less error prone 
- Servers have clean interfaces. 
- Servers can creash and restart without bringing down the entire kernel.
*Disadvantages*
- Overhead of IPC within kernel is high. **As obviosuly communicating between different processes / servers would be slower than everything implemented in the same kernel. Thats why monolithic kernel is preferred compared to micro kernels.**

## Hybrid Kernels 
Combines features of both monolithic and microkernels. 
![[Pasted image 20260406141800.png]]
*Advantages*
- More structured design 
*Disadvantages*
- Performance penalty for user level servers. 

# Processes
Process is the oldest abstraction in computing. 
**Process is an instance of a program being executed, a running program.** 
Allows a single processor to run multiple programs simultaneously. 
- Processes turn a single CPU into multiple virtual CPUs 
- Each process runs on a virtual CPU 

Reasons to have processes. 
- Provides the illusion of concurrency. 
	 One example technique used is **time slicing**. OS switches applications running on physical CPU every 50ms. Easy!
- Provide isolation between programs. Each process has its own address space. 
- Simplicity of programming. 
- Allow better utilisation of resources. Different processes require different resources at certain times. 
### Types of Concurrency 
*Pseudo Concurrency* (FAKE) Single Physical processor is switched between processes by **interleaving**.  Gives the illusion of concurrent execution. 
*Real Concurrency* (Parallelism)  Utilises multiple physical processors or CPU cores. 
### Context Switches
On a context switch, the processor switches from executing process A to executing process B. 
OS may take periodic scheduling decisions and may switch processes in response to events / interrupts (e.g. I/O completion)

Process A may need to restart later, therefore all information concerning the process, needs to be stored somewhere (process struct in Pintos!). Thats the PCB (Process Control Block). 

**PCB** has the following 
- Its own virtual CPU
- Its own address space (stack, heap, text, data)
- Open file descriptors
![[Pasted image 20260408235333.png]]

Context switches are expensive. 

### Process creation and termination 
Processes are created during 
- System initialisation 
- User request 
- System call by a running process 

Processes created can either be 
- **Forground**, interact with users 
- **Background**, handle incoming mail, printing requests. (daemons)

Process can terminate under the following conditions 
- Normal completion
- System call 
	 exit() in Unix  
	 Exit Process in Windows 
- Abnormal exit: Process has run into an error or an unhandled exception. 
- Aborted: Killed 
- Never: Like email server or printer server. 

## UNIX process commands 
### fork
```C
int fork(void)
```
Creates new child process by making exact copy of parent process image. 
**fork()** returns **twice!**
- In parent process: fork() returns process id of child 
- in child process: fork() returns `0` 
Why? Because fork is a function that outputs two processes. And even with the return type we can know whether we are the child process or the parent process when coding. 
On error, no child is created and `-1` is returned to parent. **fork can fail when global process limit is exceeded** or not enough memory / swap space.
![[Pasted image 20260406182211.png]]

Now obviously just duplicating a process is boring. We can do better 
### execve 
```C
int execve(const char *path, char *const argv[], char *const envp[])
where 
	path = full pathname of program to run 
	argv = arugments passed to main 
	envp  = environment variables. 
```
Execve just copies over the program to the current running process. **IT DOESN'T CALL FORK INTERNALLY**. So it just replaces the current process. If we want to start a child running another program then you would 
```c
if (fork() == 0){
	// We are the child 
	execve(programPath, NULL, NULL)
}
```
Easy!
###  waitpid 
```c
int waitpid(int pid, int *status, int options)
```
Suspends execution of calling process until process with pid terminates normally or a signal is received. The *status* is the status with which the child exited. 

The pid field offers us some functionalities. 
- `pid = -1` wait for any child 
- `pid = 0` wait for any child in the same process group as caller 
- `pid = -gid` wait for any child with process group gid. 
Returns 
- pid of terminated child process. 
- `0` if `WNOHANG` is set in options (indicating call should not block) and tehre are not terminated children. 
- `-1` on error, with errno set to indicate the error. 

So with all these three we could write a simple bash script 
![[Pasted image 20260406183728.png]]
Nice!
### exit 
```C
void exit(int status)
```
Terminates a process with the given status code.
Remember this status code is returned back to the **waitpid**
Exit never returns 

### kill
```C
int kill(int pid, int sig)
```
Send signal sig to process pid. 
Signals are just a way a process can communicate. 

## Process communication
How can processes communicate?
- Files
- Signals 
- Events
- Pipes
- Message Queues
- Mailslots 
- Sockets
- Shared memry 
- Semaphores
### Unix Signals 
Unix signal is an **IPC** (Inter Process Communication)[[#^4e05f6| mechanism]]. 
Signal delivery is similar to delivery of hardware interrupts and is used to notify processes when event occurs. 
![[Pasted image 20260406190519.png]]

Signals can be handled by the following ways 
- Ignore it 
- Handle it by installing a signal handler 
**TWO signals that can't be ignored/handled are SIGKILL and SIGSTOP**
![[Pasted image 20260406190736.png]]

### Unix Pipes
A pipe is a method of connecting the standard output of one process to the standard input of another. 
Allows for one way communication between processes. 
There are two types of pipes
- Unnamed 
- Named 
**Unnamed Pipes**
```c
int pipe(int fd[2])
```
Returns two file descriptors in fd 
- `fd[0]` The read end of the pipe 
- `fd[1]` The write end of the pipe
Sender closes the read end and the receiver should close the write end as they wouldn't be using it and we can save memory. 
You can think of pipe doing some **synchronisation** behind the scenes! 
So when we do a pipe both of them execute but the second one would block until some data has been written at the other end. 

So 
- If receiver reads from empty pipe, it blocks until data is written at the other end 
- If sender attempts to write to full pipe, it blocks until data is read at the other end. 

![[Pasted image 20260406205036.png]]

Note the fork copies, so closing `fd[0]` in the parent process doesn't mean the child process doesn't have access to it. 

**Named Pipes**
They are persistent and outlive process which created them. 
Stored on file system 
And any process can open it like regular file. 
Why would we use named pipes rather than files? Cause disk is slower. 

**Important nuances about them.**
You could use unnamed pipe to make something like a load balancer with one parent and multiple child! As they all would have the same read file descriptor and they children would compete to get the job done. 

When to use unnamed vs named?
Use an unnamed pipe when:
- Processes are **related** (e.g. parent ↔ child)
- You create them using something like `fork()`
- Communication is **short-lived**
- You don’t need a name in the filesystem

Use a named pipe when:
- Processes are **unrelated** (no common parent)
- You want communication via the **filesystem**
- You need something more **persistent or reusable**
# Threads 
A process contains multiple threads. 
The threads in the same process share the same state like address space, open files, signals. 
![[Pasted image 20260406224817.png]]

**Why Threads?**
Many applications (process) contain multiple activities which execute in parallel, access and process the same data and some of which may block. 
Processes are too heavyweight 
- Difficult to communicate between different address spaces 
- Acitivty that blocks may switch out the entire application 
- Expensive to context switch between activities 
- Expensive to create/ destroy activities. 

*Note:* Threads are not good at preventing failures of one part of the program from crashing another as they share the same memory. 
### Problem with threads
Shared address space. Memory corruption and concurrency bugs. 

Forking a process which is multi threaded creates a new process with a single thread. 

We dont really know which thread would handle a signal when it arrives. 

## PThreads
![[Pasted image 20260406231915.png]]

So pthreads_create is simple, remember it just returns if it was successfully created or not. We can even pass the variable to store the newly created thread, pass the function that the newly created thread would run. 

![[Pasted image 20260406232216.png]]

**The value you passed to pthread_exit is what can be waited on by pthread_join.**
And remember about the pthread_exit called in main. If we dont call it and the main thread dies then implicitly all the other treads would just die. 

![[Pasted image 20260407001126.png]]

![[Pasted image 20260407001210.png]]

## Thread Implementation
Two ways of implementing threads 
- User level thread
    Os kernel is not aware of threads 
    Each process manages its own threads 
- Kernel level thread 
    Managed by OS kernel 

*User Level Threads*

![[Pasted image 20260407001633.png]]

**Advantages** 
- Better performance 
- Thread creation and termination are fast 
- Thread switching is fast. (NO syscall or anything)
- Thread synchronisation is fast 
- All these operations don't require any kernel involvement. 
- Each application can have its own scheduling algorithm. 

**Disadvantages**
- If one user thread would want to do a syscall then all threads in the process would stop. 
- Non blocking IO can be used, but its harder to use and understand. 
- During page fault, OS blocks whole process. 

*Kernel level threads*
![[Pasted image 20260407002200.png]]

**Advantages**
- If one thread calls a blocking system call or causes a page fault, the kernel can schedule a runnable thread from the same process 

**Disadvantages**
- Thread creation and termination are more expensive. (Requires system call)
- Thread synchronisation is more expensive. 
- Thread switching is more expensive. (Requires system call)
- No application specific schedulers. 

*Hybrid Approach*
![[Pasted image 20260407002601.png]]

# Scheduling 
Process's states 
- New: The process is being created 
- Ready: runnable and waiting for processor 
- Running: executing on a processor 
- Waiting / Blocked: waiting for an event 
- Terminated: Process is being deleted 

If every process is ready to run, we need a way to choose which process to run next. 
## Goals of scheduling algorithms 
- Ensure fairness 
- Avoid indefinite postponement 
    No process should starve 
- Enforce policy (priorities)
- Maximize resouce utlisation
    CPU, I/O devidces 
- Minimize overhead 
    From context switches, scheduling decisions 

**User applications are divided as well**
- Batch Systems 
    *Important definitions*
    *Throughput: Jobs per unit of time*
    *Turnaround time: Time between job submission and completion*
- Interactive systems 
    *Response time* crucial: Time between request issued and first response (a movement of mouse). High when using SJF
- Real time systems -> We want the process to get and computed by a certain deadline. I don't care of how it happens. 
    Two types of meeting deadlines: 
    - Soft deadlines: Like maintaining 60fps in streaming videos or a game. Not meeting so wouldn't lead to death. 
    - Hard deadlines: Plane operations. Failing to do so might lead to a plane crash. Boom!

## Types of scheduling
- Non Preemptive 
    Let process run until it blocks or voluntarily releases CPU. 
    For example. FCFS (First Come First Served)
- Preemptive 
    Let process run for a mazimum amount of fixed time 
    Requires clock interrupt 

## CPU Bound vs I/O Bound Processes 
We differentiate processes based on where do they spend most of their time waiting. 
- CPU bound processes  
    Spend most of their time using the CPU 
- I/O Bound processes 
    Spend most of their time waiting for I/O 
    Tend to only use thew CPU briefly before issuing I/O request. 

## First Come First Served 
Runnable process are added to the end of ready queue 
Non preemptive 
**Advantages**
- No indefinite postponement 
    All processes are eventually scheduled 
- Really easy to implement 
**Disadvantages**
- FCFS suffers from **head of line blocking (HOL)**
![[Pasted image 20260407163439.png]]
*Important*
The throughput = `4/3603`
Average turnaround time = 
```
Turnaround time for 1st process = 3600
Turnaround time for 2nd process = 3601 
Turnaround time for 3rd process = 3602
Turnaround time for 4th process = 3603 
Therefore average is somewhere around 3601.5 
```
Now, if we flip it around 
![[Pasted image 20260407163747.png]]
Througput remains the same 
```
Turnaround time for 1st process = 1
Turnaround time for 2nd process = 2 
Turnaround time for 3rd process = 3
Turnaround time for 4th process = 3603 
Therefore average is somewhere around 902.25
```
MUCH LOWER!

## Round Robin Scheduling 
Process runs until it blocks or time quenatum exceeded. (Preemptive according to google.) 

**Features**
- Fairness 
    Ready jobs get equal share of the CPU 
- Response time 
    Good for small number of jobs 
- Average turnaround time  
    Low when run times differ
    Poor for similar run times 

**RR Quantum**
RR overhead: 
- `4ms` quantum, `1ms` context switch, we have an overhead time of 20%
    *Overhead time* = `context switch / quantum`
- `1s` quantum, `1ms` context switch time: `0.1%` time for overhead 

*Large quantum*
So large quantum time = smaller overhead. 
large quantum time = worse response time. 
If `quantum is inifinity` then we have FCFS

*Small quantum*
Small quantum = large overhead 
Small quantum = better response time. 

Therefore *REMEMBER* `quantum is directly proportional to response time and inversely proportional to overhead`

Heuristic for choosinga quantum value 
- Should be much larger than context switch cost 
- But provides decent response time 

EASY!

## Shortest Job First (SJF)
Non Preemptive scheduling with run times known in advance. Pick the shortest job first. 
![[Pasted image 20260407172352.png]]
```
FCFS turnaround time = 8 + 12 + 16 + 20 = 56
SJF turnaround time = 4 + 8 + 12 + 20 = 44 
```
Provably otimal. 
*Important:* Response time is high for SJF. 
![[Pasted image 20260408235312.png]]


## Shortest Remaining Time (SRT)
Preemptive  version of shortest job first. Again runtimes have to be known in advance. 
**Choose process whose remaining time is shortest**
- When new process arrives with execution time less than the remaiing time for the running process, run it. 
![[Pasted image 20260407172717.png]]

![[Pasted image 20260407172727.png]]

## Fair share scheduling 
![[Pasted image 20260407173153.png]]
A fair share RR scheduler would have for `user 1` each process would get `50% / 4`
and for `user 2` each process would get `25%`

## Priority scheduling 
Jobs are run based on their priority. Always run the job with the highest prioirty. 
Priorities can be externally defined, by the user or based on some process specific metrics. 
Priorities can be static or dynamic. 

## General purpose scheduling 
**Favour short and I/O bound jobs**
- Get good resource utilisation 
- And short respone times 
**Quickly determine the nature of job and adapt to changes**
- Processes have periods when they are I/O bound and periods when they are CPU - bound. 

## Multi level Feedback Queues 
A form of priority scheduling. 
One queue for each prioirty level 
- Run job on highest non empty prioirt queue 
- Each queue can use different scheduling algorithm usually round robin. 
![[Pasted image 20260407174314.png]]
![[Pasted image 20260407174400.png]]

![[Pasted image 20260407174412.png]]

## Lottery Scheduling 
Job receive lottery tickets for various resources. 
At each scheduling decision, one ticket is chosen at random and the job holding that ticket wins. 
100 lottery tickests for CPU time, P1 has 20 tickets. Chance of P1 running during the next CPU quantum is 20%
![[Pasted image 20260407174703.png]]

# Synchronisation 
Some definitions 
**Critical section** Section of code in which processes access a shared resource
**Mutual exclusion** ensures that if a process is executing its critical section, no other process can be xecuting it. Processes must request permission to enter ciritcal ections. 
![[Pasted image 20260407200143.png]]

## Disabling interrupts 
![[Pasted image 20260407200318.png]]

## Strict Alteration 
![[Pasted image 20260407201912.png]]
Strict Alteration uses busy waiting. Busy waiting wastes CPU time and should only be used when the wait is expected to be short. 

## Peterson's solution 
![[Pasted image 20260407202226.png]]

## Lock Variables
![[Pasted image 20260407202821.png]]
Well this wouldn't work as in the lock function and we are right after the while loop if we get an interrupt and we context switch, then we are cooked!!! 

**Test and Set Lock**
![[Pasted image 20260407212832.png]]
Spin locks waste CPU and should only be used when the wait is expected to be short. We may run into priority inversion problem. 
So what is prioirty inversion problem first?
Lets say we have two processes, H with high prioirty and L with low priority. H should always be scheduled if runnable. 
Lets assume the following scenario. 
- H is waiting for I/O 
- L acquires lock A and enters critical section
- I/O arrives and H is scheduled 
- H tires to acquire lock A that L is holding 

## Lock Overhead and Lock Contention 
**Lock Overhead** Measure of cost associated with using locks like the memory space, initialisation and time required to acquire and release locks. 
**Lock Contention** Measure of number of processes waiting for lock. More Contention, less parallelism. 

So coarse grained locking would have less overhead but more contention and less complexity. 
Fine grained would have more overhead, less contention and more complexity. 

## Read/Write Locks
![[Pasted image 20260407214239.png]]

## Memory Models 
In this course we assume **sequential consistency**
**Sequential consistency** means, the operations of each thread appear in program order. and the operations of all threads are executed in some sequential order atomically. 
![[Pasted image 20260407214716.png]]

## Semaphores
![[Pasted image 20260407222232.png]]
*Note:* The above `3` functions are atomic. 
So basically if we down on `0` it is just going to yield and wait till its upped. 

![[Pasted image 20260407222803.png]]

![[Pasted image 20260407222815.png]]

**Producer / Consumer**
• Producer constraints: – Items can only be deposited in buffer if there is space – Items can only be deposited in buffer if mutual exclusion is ensured 
• Consumer constraints:– Items can only be fetched from buffer if it is not empty– Items can only be fetched from buffer if mutual exclusion is ensured 
• Buffer constraints:– Buffer can hold between 0 and N items
![[Pasted image 20260407224150.png]]

## Monitors
![[Pasted image 20260407224604.png]]
![[Pasted image 20260407224615.png]]

## Summary
![[Pasted image 20260407225127.png]]
I dont really know what a mutex is? Apparantly gpt says a mutex enforces the compulsory ownership. So only the thread that locks can unlock. 

# Deadlocks
Set of processes is deadlocked if each process is waiting for an event that only antoher processc an cause. 
4 conditions for deadlock 
- Mutual exclusion: each resource is either availabe or assigned to exactly one process. 
- Hold and Wait: process can request resources while it holds other resources earlier. 
- No preemption: resources given to a procss can't be forcibly revoked. 
- Circular wait: a set of processes in a circular chain, each waiting for a resource held by the next process. 
If all thesse 4 conditions hold, then we have a deadlock. 

Another way to find a deadlock is by **resource allocation graph**
## Resource Allocation Graph 
Directed graph models resource allocation
- Direceted edge from resource to processss means that the process is currently owning that resource. 
- Directed edge from process to resource means that the process is currently blocked waiting for that resource. 
And if there is a **cycle** then we have a **deadlock**.

## Stratergies for dealing with deadlock
**Detection and recovery**
![[Pasted image 20260407231502.png]]
So we just build a resource allocation graph and check if there are any cycles. 
![[Pasted image 20260407231848.png]]

**Dynamic avoidance**
System grants resources when it knows that it is safe to do so. 

**Prevention**
Focus on removing any 1 of the 4 conditions for a deadlock. 
- Removing mutual exclusion condition 
    Share the resource. 
- Removing the hold and wait condtion 
    Require all processes to request resources before start 
    If not available then wait. 
    Issue with this is we need to know what we need in advance 
- Removing the no preemption condition 
    Force processs to give up printer half way 
- Removing the circular wait conditon 
    Force single resource per process? - Optimality issues 
    Number resources, processes must ask for resources in **order** - Issue: Large number of resources can be difficult to organise. 
    ![[Pasted image 20260407232747.png]]

## LiveLocks 
Livelocks are a variants of deadlocks but where threads are not blocked, but they or the system as a whole is not making progress. 

## Starvation
We need to make sure even the thread with the lowest priority would certainly run. Its not that it would never get its turn. 

# Memory 
## Logical vs Physical address space
Memory management binds logical address space to physical address space. 
**Logical Address**
- Generated by the CPU 
- Address sapce seen by process 
**Physical address**
- Address seen by the memory unit 
- Refers to physical system memory
*Note:* We as a programmer would only be able to see the logical address and never the physical address. 
#### MMU  
MMU is a hardware device for mapping logical to physcial addresses. 
A simple one could be to just add some offset to the logical address. 
## Continuous Memory Allocation 
Main memory is split into kernel and user space. 
**Base** register contains value of smallest *physical address*. 
**Limit** register contains the range of logical address. *Note:* Logical addresses would go from `0` to `limit - 1` 
And in order to make it into physical address we would do `base + virtual address` And before this we can check if the virtual address is legal by checking with the `limit`. 
This is an **old convention** aka before paging (new one). It assumes everything can be fit inside the RAM. 
![[Pasted image 20260408143527.png]]
So each process goes and occupies into the RAM. 
![[Pasted image 20260408143615.png]]
A simple workflow. 
Comes with more problems than good. 
We have holes, when we remove a process and then add a new one. 
![[Pasted image 20260408144543.png]]

And in order to satisfy this, we have to use dynamic storage allocation 
- First Fit: Allocate first hole that is bigh enough 
- Best fit: Allocate smallest hole that is big enough 
- Worst Fit: Allocate largest hole 

**Fragmentation**
- **External Fragmentation** When there is total memory to satisfy the process but they are not contiguous
- **Internal Fragmentation** Allocated memory is larger than requested memory. 

**Compaction**
We can reduce external fragmentation by compaction. Where we shuffle memory contents to place all free memory together in one large blcok. Leads to I/O bottlenecks. 

**Swapping**
Solves the problem of having a number of processes limited by amount of avaialble memory. But only running processes need to be in memory. 
Swap processes temporarily out of memory to disk. It requires swap space. 

## Virtual Memory with paging 
![[Pasted image 20260408151046.png]]

Each process now can assume infinite memory (obviously would be bounded by the number of bits in the computer. So a 64 bit would be `2^64 - 1` as MAX)
![[Pasted image 20260408151144.png]]
So now each process can arrange its memory and say ohh my logical address `0` is going to start with my code and my logical address `Max` is where the stack should start and bla bla bla. 
But *remember* we dont really neeed to map all these into physcial. We would only map the used ones into pages. Easy!

We would require to have a somewhat of contstraint into the memory map table. If the memory map table grows linearly with the number allocated pages, then we have a problem. We will look into how we solve this in the future. 

Virtual memory can be implemented via Paging or segmentation. Segmentation is like paging but the pages can grow in size. It is complex and is legacy now. Paging is what stays.  
### Paging 
**Frames**
- Fixes size blocks of *physical* memory 
- Kepp track of all free frames
**Pages**
- Block of same size of logical memory 

So in order to run a program of size n pges 
- Find n free frames and load program 
- Set up page table to translate logical to physical addresses

![[Pasted image 20260408152632.png]]
So now we don't need to have them continuous in the phyiscal memory. But the logical memory is continous. Nice! 
Now the *Page table* is the important structure that holds the mapping from page number to frame number. It looks like an array. With the index being the page number and the value is the frame number. 

Q. I still don't really understand how this would work with multiple processes. 
A. Idk if this is completely right. But from my Pintos knowledge a page table is per process. So when context switching you would use the page table of the other process. This way we would still know the frame number. Im pretty sure this is how it works. Future Jayce can confirm. Future Jayce: I confirm this is right. Look at[[#^39da50 | this]]


**Small page size vs Large page size**
Lets say we want to malloc `1` byte. But the least amount of memory we can provide with virtual memory and paging is `1` page. So we are wasting memory if we are using a large page size. 
But now lets say we have a large program that requires `40` pages. Then if we are using a small page size we are doing a lot of update to the page table. Therefore slowing down our system. 
There is a tradeoff. 
So with small page size there is **less internal fragmentation**
![[Pasted image 20260408235620.png]]
The reduced page transfer time is a fact. REMBER IT. 
![[Pasted image 20260408235628.png]]
This was a shame. :( 
But *Note:* Larger page size doesn't result in less external fragmentation. I believe it would be more. Cause there is much more higher chance of having holes and not being able to fit into any one of them. 

*Important:* **Context switch in virtual memory**
Page table is per process. 
The operating system must locate the page table for the process that is to start running. It must set the base register in the MMU so that it points to the page table in memory. Finally, it must clear any now invalid cached address translations from the [[#^fac0a1|TLB]] (translation lookaside buffer).  ^39da50

#### Address Translation
![[Pasted image 20260408155506.png]]
So *VERY IMPORTANT* we need to write the page size in terms of `2^n` bytes. NOTE THE BYTES.

![[Pasted image 20260408155524.png]]
Now here is the functionality of the MMU. It now has the base pointer pointing at the page table of the process. (from [[#^39da50 |above]])
And then we access the page table from p so (base + p) and find the frame number f. The d is just copied over. Easy!

### Memory Protection
We have a valid bit in the page table, saying whether the page table entry is valid or not. So prevents processes from being able to access a memory it never allocated. 

### Page table Implementation 
Every page table is kept per process, in the process's memory.
Each page table has 
- Page table base register (PTBR) which points to page table.
- Page table length register (PTLR) which indicates size. 

The problem is it is very inefficient. 
- Every data/instruction access requires two memory access, one for page table and one for data/instruction. 
Solution is to use a special fast lookup cache as associative memory. 
**Associative Memory** 
Supports Parallel search. And helps in address translation (p, d).  ^4c5634
- If p in associative register, get frame number out 
- Otherwise get frame number from page table in memory. 

### TLBs (Translation Look aside Buffers)

^fac0a1

^97cbb8
TLBs are a [[#^4c5634|associative memory]], so supports *parallel search*. 
So implemented by the hardware to offer us fast lookup. 
So if we get a TLB hit, we would be able to get fast results. 
![[Pasted image 20260408164209.png]]

There are still a bunch of subtleties here. 
Now when we are doing a context switch we would need to flush the TLB. So this is a problem when we are starting a new process. You would initially get a lot of TLB misses and then you would get a neater performance (Like Roblox dieing at the start!). 

Even with system calls, we would need to do TLB flushes, which would impact the peformance a lot. As after coming back, we would get a lot of TLB misses and then the performance would gradually increase only to again to another system call. There is a neat solution to this apparently. We would look into it later. Future Jayce can tag here. 

Some other TLBs are not process specific but more generic. 
![[Pasted image 20260408164544.png]]

We can configure the kernel to what we would want. 

### Performance : Effective Access time 
Time it takes to access memory. 
![[Pasted image 20260408165223.png]]
*Note:* The associative terms are just TLBs (from above)
```
Let 
TLB lookup time = epsilon

TLB Hit Percentage = alpha 

So two cases to consider 

EAT = TLB hit or TLB Miss
    = TLB hit + TLB Miss
    = (epsilon + 1)alpha + ... 

The 1 is just an assumption for the time it takes to get access memory. If we miss we would need to access the page table and the memory as well, so 2. 
```
Easy!

### Page Table Problems 
![[Pasted image 20260408165604.png]]
Going back to this picture. We know we would require the page table to be an array so we can do fast lookups, therefore continuous. And we know we would need to even have large page table for every amount of page. This makes our page table large. 
Obviously in the question with the `2^16` address space (it can be found [[#^460561|here]]), the page table was tiny so it was fine. But obviously in the real world this would be enormous. 
We need to change the way of representing these page tables. 
- Hierarchical page table 
- Hashed page table 
- Inverted page table
Lets look through these 1 by 1 
### Hierarchical Page Table 
We could have a `2 level page table` 
We are breaking up our virtual address to do an index in the outer page table. And then we are going to the inner page table and then finally the memory. 
![[Pasted image 20260408170132.png]]
But wait, how does this help for the problem we introduced above?
Answer. The outer page table needs to be completely allocated. BUT we only need to allocate the inner page table if any one of those page tables are allocated. 
The problem again is the performance, we would need to do 2 lookups compared to one. 
![[Pasted image 20260408170321.png]]
*Note* The d would remain the same, as it is governed by the page size. Easy!

But now why not instead of storing entry per page but store per frame. 

### Hashed page table 
![[Pasted image 20260408170821.png]]

### Inverted page table 
![[Pasted image 20260408170900.png]]
So now we have a structure where it isn't proportional to number of pages but proportional to the number of frames. 
`# frames << # pages. `
But now the lookups are inefficient. It is O(n) as we are now finding the index rather than the value. 
So the most popular way of handling page table is Hierarchial page table and we would have a good TLB to get better results. 
Nice! Too easy!
## Shared Memory 
Basically two pages map to the same frame. 
There are some common commands to request for shared memory. 
![[Pasted image 20260408222934.png]]

Question: What is better? shared memory or pipes?
![[Pasted image 20260408223145.png]]

## Segmentation
![[Pasted image 20260408223751.png]]

**Paging vs Segmentation**
Why did segmentation not take off?
- Complex. Segmentation is complex in nature to implement in the hardware wise.
- Exposes itself to the programmer. Say we have code segment, data segment, heap segment and stuff. Now whenever we need to access any one of these segments, like when our program needs to access any one of these segments, then it would need to do segment switching. Adds more complexity. Done by the compiler, but makes the compiler more complex and ties it directly to the hardware. So in a nutshell segmentation exposes how it works to the programming model, which is bad. Instead paging is completely abstracted and is better. 
## Demand Paging 
Only bring page into memory when needed. 
- Lower I/O load
- Less memory needed
- Faster response time 
- Support for more users
Page needed -> reference it 
- invalid reference -> abort 
- not in memory -> bring to memory. 
Basically what we implemented in Pintos. Implemented using the valid bit and page faults. 
![[Pasted image 20260409003149.png]]
Now we would need to change the page fault handler to distinguish between page faults. 
![[Pasted image 20260409003311.png]]



# Important question to Practice
^460561

![[Pasted image 20260407180618.png]]
Remember to count properly here. Last time i messed up for counting for B as 25 (included C, D, E). 

![[Pasted image 20260408163608.png]]
Here is the answer
![[Pasted image 20260408163630.png]]

![[Pasted image 20260408200709.png]]
Just for fun. The above question is easy! 

![[Pasted image 20260408201003.png]]

![[Pasted image 20260408223151.png]]

![[Pasted image 20260408235418.png]]
