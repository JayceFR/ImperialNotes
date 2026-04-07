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
    Response time crucial: Time between request issued and first response (a movement of mouse)
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
