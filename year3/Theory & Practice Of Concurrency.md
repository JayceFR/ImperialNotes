Refer [[C++]] first to get a good basic understanding of it. 
# Threading and Locks in Cpp 

**Reentrant locks:**
Can call lock multiple times. 

**Shared locks:**
Allows multiple readers 

## Threads 
Threads can be found in the `thread` module

```cpp
#include <thread>
#include <iostream>

void threadBody1(){
    std::cout << "Hello" << "\n";
}

void threadBody2(){
    std::cout << "World" << "\n";
}

int main(){
    std::thread t1(threadBody1);
    std::thread t2(threadBody2);
    t1.join();
    t2.join();
    return 0;
}
```

So the `std::thread` takes in its constructor a function pointer and its arguments. Simple. We must join the threads otherwise we would get a compiler error. 

Another way would be to use the `std::vector` module to use vectoes 
```cpp
#include <cstddef>
#include <thread>
#include <iostream>
#include <vector>


void threadBody(){
    std::cout << "Hello" << "\n";
}

int main(){
    std::vector<std::thread> threads;
    for (size_t i = 0; i < 12; i++){
        threads.push_back(std::thread(threadBody));
    }

    for (auto& t : threads){
        t.join();
    }

    return 0;
}
```

**Passing a reference to `std::thread`**
Say our threadBody looks something like this 
```cpp
void threadBody(std::string by_balue, std::string& by_reference){
    std::cout << "Hello" 
              << by_balue 
              << " " 
              << by_reference 
              << "\n";
}
```

Doing this would **NOT** work
```cpp
std::thread(threadBody, hello, world)
```
Because of the way thread is coded, which already uses template magic. 
Instead we would need to be explicit and use `std::ref` which is found under `std::functional`
```cpp
#include <functional>

int main(){
	std::thread t1(threadBody, hello, std::ref(world)));
	t1.join()
    return 0;
}
```

**Vector of therads**
Instead of doing the `push_back` and stuff
![[Pasted image 20261011201010.png]]
We can use the `emplace_back` function given by vectors. 
Which uses type inference to determine the type
![[Pasted image 20261011201052.png]]

**This thread**
Inside the function to the thread, we can do something liek `std::this_thread::`
which offers some set of useful functions.
Like getting the `id` of the current thread using `std::this_thread::get_id()`
Here are some other functions avaiable 
![[Pasted image 20261011201328.png]]

*Note:* We can do the following with `std::this_thread`
- `std::this_thread::get_id()`
- `std::this_thread::sleep_for()`
- `std::this_thread::sleep_until()`
- `std::this_thread::yield`
## Mutex 
Found under the `mutex` header, we have access to `std::mutex`
![[Pasted image 20261011201554.png]]

*Important to Note* Locks/Mutex in `Cpp` are **NOT** Reentrant and **NOT** Shared!!!

**Mutex vs Lock in Cpp**
Mutex has the ability to lock and unlock. 
While a Lock is used to manage multiple mutexes. 

**Scoped Locks**
So in a mutex, say we have something like this, 
```cpp
#inlcude <mutex>
class Logger{
public:
    Logger() = default; 
    void Log(const std::string& contents) {
        mutex_.lock();
        if (2 > 1){
		    mutex_.unlock();
	        return;
        }
        contents_ += contents;
        mutex_.unlock();
    }

    std::string GetContents() const {
        return contents_;
    }

private:
    std::string contents_;
    std::mutex mutex_;
};
```
So that above pattern with mutexes, to unlock before returning is quite dumb. Instead we can use a Scoped Lock, which has a definition something like this.
![[Pasted image 20261012130715.png]]

Cpp does give us a ScopedLock, which is present under the `mutex` module. 
And we can do 

```cpp
#inlcude <mutex>
...
    void Log(const std::string& contents) {
	    std::scoped_lock<std::mutex> lock(mutex_)
        if (2 > 1){
	        return;
        }
        contents_ += contents;
    }
...
```
Which looks a lot cleaner. 

This comes under the **RAII** design pattern
which stands for **Resource Acquisition is Initialisation**
Think of it like the destructor cleans up what resources the object holds. 
Like how the `std::unique_ptr` is used 

## Race Conditions 
- Write read race condition 
- Read Write race condition 
- Write Write race condtion 
Race condtions are a key part of concurrent programming. 
![[Pasted image 20261012191531.png]]

## Data Races
A data race occurs when 
- Distinct threads access a memory location 
- At least one of the accesses modifies the location 
- At least one of the accesses is non atomic 
- The accessses are not ordered by synchronisation 
**Undefined behaviour**
The behaviour of C++ program P on input I is undefined if a data race occurs when P executed on I. 
Basically if a data race is present the compiler is free to do anything. And the program can do anything
C++ compiler is written in a way that it assumes there are no data races in the underlying program. 
So if there is a data race you might see random stuff happening when you run the program!

![[Pasted image 20261012191943.png]]

So take for example this case 
```cpp
#include <thread>
void Foo(int &x) {
	x = 1; 
}
void Bar(int &x) {
	while (x == 0) {}
}

int main() {
	int x = 0; 
	std::thread t1(Bar, std::ref(x));
	std::thread t2(Foo, std::ref(x));
	t1.join();
	t2.join();
	return 0; 
}
```
You can see with the above code it basically checks every point of the data race case, so we have an undefined program.
Now the compiler can do the follwing. 
![[Pasted image 20261012192313.png]]
![[Pasted image 20261012192321.png]]

More agressive, it can do the following 
![[Pasted image 20261012192339.png]]
![[Pasted image 20261012192347.png]]

 **ThreadSanitizer (tsan)**
 Automatic dynamic detaction of data races 
![[Pasted image 20261012193153.png]]

## Data races vs Race conditions 
![[Pasted image 20261012193705.png]]
One way to think about it is that race condtions wont affect the correctness of the program.
So example where a data race is not a race condtion. 
![[Pasted image 20261012194007.png]]
Something like this, this is not a race condtions as the correctness of the program is guaranteed no matter what we do. But its a data race under cpp because there is atleast one read, not atomic, not ordered. 
So proram is undefined. 

## `std::unique_lock` vs `std::scoped_lock`
We have seen scoped lock before 
**Scoped lock**
![[Pasted image 20261012194301.png]]

**Unique lock**
`std::unique_lock`
- Constructed with one mutex (this is **not** the reason it is called unique, I beleive its called unique because the copy constructor is set to delete)
- Locks mutex on cosntruction (default), or locking can be deferred. 
- Allows unlocking and relocking 
- Unlocks mutex on destruction if still held 
- Supports transfer of ownership to a distinct unique owner via `std::move` 
It doesn't support management of multiple mutexes. 

So here is a quick table of differences summarised 

|            Scoped Lock            |                                  Unique Lock                                  |
| :-------------------------------: | :---------------------------------------------------------------------------: |
|   Supports one or more mutexes    |                            Only supports one mutex                            |
| Locks mutex only on construction  |        Can lock mutex on construction + allows locking to be deferred         |
| Unlocks mutex only on destruction | Supports unlocking and relocking + unlocks mutex on destruction if still held |
|     Can't transfer ownership      |                Supports transfer of ownership via `std::move`                 |
|          More efficient           |                        Less efficient than scoped Lock                        |
So in short, unique lock supports everything a scoped lock gives + some more benefits but doesn't support mutliple mutexes. 
## Condition Variables 
Present in the `conditon_variable` module. 
We can use `std::condtion_variable` to allow threads to wait on one another. 
![[Pasted image 20261012195608.png]]

To wait on the condition variable we can use the `.wait` function which accepts a mutex, predicate that returns a bool, which would say this to proceed. 
![[Pasted image 20261012200034.png]]

What happens when you call wait. 
- Thread calls the wait function
- Acquires the lock 
- Predicates holds? yes then return 
- No? then release the lock and go to sleep. 
![[Pasted image 20261012200414.png]]

*Important*: Note the fact that if the predicate returns true it doesn't wait. It only waits / spins when the predicate `p` is `false`. 

So basically `p` is like the post condition that is guaranteed to be true after the condition.wait

Another important thing i want to mention is the fact that we are creating these unique_locks over and over, its not like we are creating it as a member variable. Which is because of the deconstruction when it dies.  
```cpp
void Enqueue(T item){
	// while (size == contetns.size) {}
	std::unique_lock<std::mutex> lock(mutex_);
	condition_.wait(lock, [&]()->bool {
		return size_ < contents_.size();
	});
	contents_[head_] = item;
	head_ = (head_ + 1) % contents_.size();
	size_++; 
	condition_.notify_all();
}

T Deq() {
	// while (size_ == 0) {}
	std::unique_lock<std::mutex> lock(mutex_);
	condition_.wait(lock, [&]() -> bool {
		return size_ > 0; 
	});
	T result = contents_[tail_];
	tail_ = (tail_ + 1) % contents_.size();
	size_--; 
	condition_.notify_all();
	return result;
}
```
Now instead of having a `notify_all` if we change it to `notify_one` there is a case where the consumers can just wake up the consumers and never wake up the producer and livelock. 
Because everything would be sleeping. 

# 2.b Atomics 
## Atomics 
Need to load the `atomic` module 
`std::atomic<T>`
![[Pasted image 20261012225404.png]]

Basically we create an object and then pass the constructor to it with what value we want to store. 
**RMW** Read Modify Write Operations 
- Reads the old value of the variable 
- Writes a new value to the variable 
- The new value is either a completely new value or a function of the old value. 
All this is performed in one indivisible step. 
![[Pasted image 20261012225856.png]]
**compare_exchange**
Lets look into more detail of what `bool compare_exchange(T& expected, T desired)` does 
Consider `x.compare_echange(e,d)`
Obvioulsy x is an atomic, the value of `x` and `e` are compared
- if equal: d is stored to x and true is returned 
- if not equal: x is stored to e, x is left unchanged and false is returned 
So you can think of its actual implementation something like this 
```cpp
template <typename T>
class atomic{
public:
	bool compare_exchange(T& e, T d){
		if (x == e){
			x = d; 
			return true; 
		}
		e = x; 
		return false;	
	}
private:
	T x; 
};
```
So it does that entire if condition stuff atomically, which is pretty cool. 
But actually C++ does not provide `compare_exchange` instead it provides, `compare_exchange_strong(expected, desired)` and `compare_exchange_weak(expired, desired)`
- `compare_exchange_strong(expected, desired)` : behaviour is the same as above. 
- `compare_exchange_weak(expected, desired)`: allows to fail, i.e it is allowed to behave as if `expected != x` even when `expected == x` Failure should occur with low probability. 
	Why does this even exist? Because in some architectures this is cheaper and works fine if the compare exchange is in a loop. 
	Like this
	```cpp
	while (!compare_exchange_weak(e,d)) {}
	```
	So if it fails sometimes, its fine, we would just try again. 
	If a `compare_exchange_strong` was used instead, then it would return true in the first instance itself. 

**Operations on `std::atomic<integral_type>`**
More RMW operations 
![[Pasted image 20261013174631.png]]
So 
- `fetch_add`
- `fetch_sub`
- `fetch_and`
- `fetch_or`
- `fetch_xor`
There is operator overloading as well when we intialise `std::atomic`
![[Pasted image 20261013181636.png]]

But be careful, 
![[Pasted image 20261013181914.png]]
The second one would have a race condtion. 
Just use the `.load` functions. 

Back to the data race condtions 
![[Pasted image 20261013182018.png]]
So now, if there are two atomics, then we dont have a data race, and the compiler cant do anything bad with our program. 
## Memory Orderings
More to come from the 2nd half of the course. 
### Sequential Consistency (SC)
- Program's behaviour corresponds to an interleaving of threads. 
- Its the basic way of reasoning about programs. 
- SC can be **expensive**
- Modern mulitocre CPUs are **not SC**: more details on why in the theory part of this course. 
- It is fine when the performance of the atomic operation is not critical. 
- C++ atomics are SC by **default**
So basically `x.store(42)` is a shorthand for `x.store(42, std::memory_order_seq_cst)`
An example could be say we have 2 threads with this body of code 
```cpp 
void threadBody1(int &x, int&y){
	x = 1; 
	print(y);
}

void threadBody2(int &x, int &y){
	y = 1; 
	print(x);
}
```
Under SC we can expect 
- `1, 0`
- `0, 1`
- `1, 1`
But we would never get `0,0` 
As it is never possible with the ordering .

### Relaxed memory order 
`std::memory_order_relaxed`
- Its the weakest memory ordering. 
- Only guarantees sequential consistency per location aka per atomic variable. 
![[Pasted image 20261013194905.png]]
So this could be a possible output under relaxed memory order. Where for each location (aka x and y) we can seee they follow some valid `SC` interleaving of threads. 
But if you look at both the outputs together, that is not even possible. 
- Compiler has freedom to reorder relaxed (and non atomic) operations that work on different locations. 

# Random Useful Cpp stuff for this course 
- `std::numeric_limit`
	We can use this to find the maximum possible element, would be useful for stuff like infinity 
```cpp
	size_t max_elem = std::numeric_limit<size_t>::max();
```
- `std::chrono::milliseconds`
	Can be used along with `std::this_thread::sleep_for()`
	so something like 
	```cpp
	std::this_thread::sleep_for(
		std::chrono::milliseconds(1)
	);
	```
	