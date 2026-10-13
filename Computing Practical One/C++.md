*Note:* Notes are taken from alastair's slides and further research from random websites and youtube videos. 

## Printing to the console 

While using `std::cout` CPP would not immediately write to the screen instead it would want to batch the output to the console, internally it has a bufffer, and if the buffer gets full it then writes to the console. 
OR a call to `std::flush` would empty the buffer and write everything in it to the console. 

**std::endl**
This command adds in a `\n` to the end and a call to `std::flush` which is an OS sys call, whose main job is to say stop batching and immediately write to the console 
*Note:* When writing performant code it is best to avoid using `std::endl`. 


## Strings
Strings in cpp is an object. 
Strings are mutable in CPP!!
![[Pasted image 20261008184104.png]]
Basically you can create an object, the constructor takes in a string to intially create something. 
```cpp
#include <string> 

int main() {
	std::string hello("Hello");
}

```

## References
Just an alieas for an exisiting vairable. Created using the `&` operator but before the new variable name. 
```cpp
string food = "Pizza";
string &meal = food; 

cout << food << endl; // Pizza
cout << meal << endl; // Pizza 
```
we can even update using refrences, 
```cpp
meal = "Burger";

cout << food << endl; // Burger
cout << meal << endl; // Burger
```

Its weird, so now even the `&` is overloaded like the `*`

Now iun functions we can pass by reference using the new refrence mechanism rather than the pointer approach. 
```cpp
void changeValue(int &num){ // instead of the C *
	num = 50;
}

int main(){
	int value = 10;
	changeValue(value) // Note don't need the & here
}
```

Another important difference between references and pointers is references always expects a value. references can't be *NULL*. While pointers can, with that we have the following cheatsheet 
![[Pasted image 20261008183933.png]]

## Function objects 
![[Pasted image 20261009174330.png]]
## Lambda 
```cpp
[capture] (parameters) {code};
```
**Example:** 
```cpp
int main(){
	auto message = []() {
		cout << "This is lambda";
	}
}

message();
return 0;
```
**Lamda with Parameters**
```c++
#include <iostream>
using namespace std;

int main() {
  auto add = [](int a, int b) {
    return a + b;
  };

  cout << add(3, 4);
  return 0;
}
```
**Passing Lambdas to Functions** 
```c++
#include <iostream>
#include <functional> // Needed for std::function
using namespace std;

// A function that takes another function as parameter
void myFunction(function<void()> func) {
  func();
  func();
}

int main() {
  auto message = []() {
    cout << "Hello World!\n";
  };

  myFunction(message);
  return 0;
}
```
if it is going to return a value or soemthing, we can do 
![[Pasted image 20261009174203.png]]


**Capture clauses**
We can use the `[]` brackets to give a lmabda access to variables outside of it. 
```c++
int main() {  
  int x = 10;  
  auto show = [x]() {  
    cout << x;  
  };  
  
  show();  
  return 0;  
}
```
Capture by reference 
```c++
int main() {  
  int x = 10;  
  
  auto show = [&x]() {  
    cout << x;  
  };  
  
  x = 20;  // Change x after the lambda is created  
  
  show();  // prints 20 
  return 0;  
}
```
So if we are using capture by reference then the lambda would store the reference inside the lambda and the compiler does its own magic in order to make it available. In practice the entrie mechanism would 
## OOP
**Constructors**
```cpp
class Car {  
  public:  
    string brand;  
    string model;  
  
    Car() {  
      brand = "Unknown";  
      model = "Unknown";  
    }  
  
    Car(string b, string m) {  
      brand = b;  
      model = m;  
    }  
};  
  
int main() {  
  Car car1;  
  Car car2("BMW", "X5");  
  Car car3("Ford", "Mustang");  
  
  cout << "Car1: " << car1.brand << " " << car1.model << "\n";  
  cout << "Car2: " << car2.brand << " " << car2.model << "\n";  
  cout << "Car3: " << car3.brand << " " << car3.model;  
  return 0;  
}
```

**Member function qualifier**
So we can define qualifiers on the object that calls the function itself. Which is pretty disgusting. 
```cpp 
#include <iostream>

class A{
	public:
		A(int x, int y): x_(x), y_(y) {}

		int GetX() const {
			return x_; 
		}

		void setY(int y) {
			y_ = y;
		}

	private:
		int x_;
		int y_; 
};

int main(int argc, char** argv){
	A non_const_a(2,5); 
	non_const_a.setY(5);
	std::cout << non_const_a.GetX() << "\n";
	
	const A const_a(2,6);
	const_a.setY(5); // Complains
	std::cout << const_a.GetX() << "\n";


	std::cout << "Program is done" << std::endl; 
	return 0;
}

```
So in the above example, we have created two objects, one of them is created with a const keyword and another without it. Now the object which is defined with the const keyword cant access the `setY` function as the function has no modifier saying it allows const objects to call it. 
Super interesting. Looks so weird tho 
There is a pretty neat way of thinking about it tho. 
```cpp 
Lets imagine on what the cpp compiler is doing. 

A a(2, 5);
a.GetX();

CPP is doing something like 
GetX(&a);

so `this` is where the this keyword is coming from. Cause at the end of the day Cpp is just C with ten thousand features that the developer of cpp as well wouldnt know all of the legal ways of doing things. 

And thats where the const is being applied to. 

```

**Friend Function**
A friend function is not a member of the class, but is allowed to access the class's private data. 
```cpp
class Employee {
  private:
    int salary;

  public:
    Employee(int s) {
      salary = s;
    }

    // Declare friend function
    friend void displaySalary(Employee emp);
};

void displaySalary(Employee emp) {
  cout << "Salary: " << emp.salary;
}

int main() {
  Employee myEmp(50000);
  displaySalary(myEmp);
  return 0;
}
```

Sounds useless, but super interesting use case is 
```cpp
class Vector {
private:
    int x, y;

public:
    Vector(int x, int y) : x(x), y(y) {}

    friend Vector operator+(const Vector& a, const Vector& b);
};

Vector operator+(const Vector& a, const Vector& b) {
    return Vector(a.x + b.x, a.y + b.y);
}
```
**Inheritance**
Inheritance with constructor
```cpp
class Animal {
public:
    Animal(int age) {
        cout << "Animal constructor\n";
    }
};

class Dog : public Animal {
public:
    Dog(int age) : Animal(age) {
        cout << "Dog constructor\n";
    }
};
```
*Note:* There is a constructor initialiser list syntax for initialising even variables, so we can do 
```cpp
class Player {
    int health;

public:
    Player(int health) : health(health) {}
};
```
Yup thats total legal, its soo weird, imagine writing a compiler that needs to translate that. 

## Virtual funtions
By default (with**out** virtual), C++ decided which function to call based on the pointer type and not the actual object type. 
So what does that mean? 
Take this example 
```cpp
class Animal {
  public:
    void sound() {
      cout << "Animal sound\n";
    }
};

class Dog : public Animal {
  public:
    void sound() {
      cout << "Dog barks\n";
    }
};

int main() {
  Animal* a;  // Declare a pointer to the base class (Animal)
  Dog d;  // Create an object of the derived class (Dog)
  a = &d;  // Point the base class pointer to the Dog object
  a->sound(); // Call the sound() function using the pointer. Since sound() is not virtual, this calls Animal's version
  return 0;
}
```

So even though `a` points to Dog, it still calls `Animal::sound()` because the function is not Virtual. 
Another example given by Alastair is 
![[Pasted image 20261008171501.png]]
![[Pasted image 20261008171512.png]]
So Alastair is making the same point but he makes it more concrete by talking about apparent vs actual type. Which is another way of thinking about it. 
*Note:* Normal methods are dispatched based on apparent type. 

Now with virtual, 
```cpp
class Animal {
  public:
    virtual void sound() {
      cout << "Animal sound\n";
    }
};

class Dog : public Animal {
  public:
    void sound() override {
      cout << "Dog barks\n";
    }
};

int main() {
  Animal* a;
  Dog d;
  a = &d;
  a->sound(); // Outputs: Dog barks
  return 0;
}
```

*Important* So when to use virtual, use `virtual` when we want a member function in the base class that needs to be overidden in the derived class. Basically the keyword `abstract` in other programming languauges. 

Back to Alastair's example 
![[Pasted image 20261008171909.png]]
So now virtual methods are dispatched based on actual type 
![[Pasted image 20261008172005.png]]


## Abstract classes / interfaces 
**Abstract method**
A virtual method that is declared with `=0` instead of a body 
```cpp
class A {
	public:
		virtual void Foo() = 0;
};
```
So now Foo is an abstract method in the above example. 

**Abstract classes**
A class is abstract if and only if it has atleast one abstract method. 
So the above class `A` is an abstract class, there is no abstract class keyword in cpp. 
Now obviously creating an object of the abstract class is **illegal**
```cpp
int main(){
	A a; // Complains. ERROR
	return 0;
}
```
Common to say interface when talking about a class with only abstract methods 
C++ allows multiple inheritance. 

Look at this link here: [[#^707a5c | Destructors for abstract classes]] which is below in the destructors section .
## Templates
Basically **generics** in other languages. 

Template functions 
```cpp
template <typename T>  
T add(T a, T b) {  
  return a + b;  
}  
  
int main() {  
  cout << add<int>(5, 3) << "\n";  
  cout << add<double>(2.5, 1.5) << "\n";  
  return 0;  
}
```

Template classes 
```cpp
template <typename T>  
class Box {  
  public:  
    T value;  
    Box(T v) {  
      value = v;  
    }  
    void show() {  
      cout << "Value: " << value << "\n";  
    }  
};  
  
int main() {  
  Box<int> intBox(50);  
  Box<string> strBox("Hello");  
  
  intBox.show();  
  strBox.show();  
  return 0;  
}
```

*Important points*
Template classes is compiled separately for every distinct configuration of instantiating types 
Pros 
- Generated code can be really fast 
- Template class may be designed to work for only certain types 
Cons 
- Executables can get large 
- Error messages can be confusing
![[Pasted image 20261008174708.png]]
Which brought up an intersting point about using `A<T>` for extending the class. 
You can resolve it using the `this` keyword 
![[Pasted image 20261008175125.png]]

## Value Categories 
LValue, RValue, LValue Reference and RValue Reference 

LValues are actual memory locations. While RValues are temporary variables. 
So for example 
```cpp
int main(){
	Stirng firstName = "Jayce";
	Stirng lastName = "Robin";
	String fullName = firstName + lastName; 
}
```
Over here `firstName`, `lastName` and `fullName` are LValues, while `"Jayce"`, `"Robin"` and importantly `firstName + lastName` is an RValue. 
So what would happen is the compiler would create another tmp variable to store the `firstName + lastName` and then assign that to the LValue `fullName`. 

LValue References can accept only LValues. 
So for example 
```cpp
void SetValue(int &value){
}

int main(){
	int i = 10; 
	SetValue(i);
	SetValue(10); // Errors, saying invalid reference to non const must be an lvalue. 
}
```

We can fix that by accepting a **const lvalue reference**. 
```cpp
void SetValue(const int &value){
}

int main(){
	int i = 10; 
	SetValue(i);
	SetValue(10); // Doesn't error.
}
```
So like 
```cpp
int &a = 10; // errors 
but 
const int &a = 10; // works!
```
So therefore a const lvalue reference can accept both lvalue and rvalue. 

```cpp
#include <iostream>

void PrintName(std::string& name){
	std::cout << name << std::endl; 
}

int main(){
	Stirng firstName = "Jayce";
	Stirng lastName = "Robin";
	String fullName = firstName + lastName; 
	
	PrintName(fullname); // Compiles and works 
	PrintName(firstName + lastName) // Doesn't work 
}
```

If we change the `PrintName` function definition to this 
```cpp
void PrintName(const std::string& name){
	std::cout << name << std::endl; 
}

int main(){
	...
	PrintName(fullname); // Compiles 
	PrintName(firstName + lastName) // Compiles
}
```

We can even use **RValue reference** as well. 
```cpp
void PrintName(std::string&& name){
	std::cout << name << std::endl; 
}

int main(){
	...
	PrintName(fullname); // Doesn't Compile 
	PrintName(firstName + lastName) // Compiles
}
```

Another fun useful bit cpp gives you is the fact that we can define both rvalue reference and a const lvalue reference acceptance functions. So now passing in a rvalue, technically it can choose either the rvalue reference or the cosnt lvalue reference function overload, but it is guaranteed to choose the rvalue reference. 
So for example:
```cpp
void PrintName(const std::string& name){
	cout << "lvalue" << "\n";
}
void PrintName(std::string&& name){
	cout << "rvalue" << "\n";
}
int main(){
	PrintName(std::string("Hello")); // Would print rvalue!
	return 0;
}
```
Prints rvalue, tho it can go to both functions. 

## Implicit Constructor

## Vectors 
```cpp
#include <iostream>
#include <vector>
#include <string>

int main(){
    std::vector<std::string> ss; 
    std::string hello("Hello");
    std::string world("world");

    ss.push_back(hello);
    ss.push_back(world);

    ss[0].pop_back();

    std::cout << "Hello " << hello << "\n";
    std::cout << "World " << world << "\n";
    std::cout << "ss[0] " << ss[0] << "\n";
    std::cout << "ss[1] " << ss[1] << "\n";

    return 0; 
}
```
*Note:* The above code creates a copy of hello and world when you push it into the vector. 
so the ouput looks like this 
```
Hello Hello
World world
ss[0] Hell
ss[1] world
```

Another important point 
![[Pasted image 20261009130757.png]]
Look at those for loops in particular, so just doing something like this 
```cpp
for (std::string s : ss)
```
is hugely inefficeint as it is copying each string. 

**Important** Cpp alllows value lookups of objects, which is different as python / kotlin would only ever give reference lookups of objects.
For example 
```python
items = [[1, 2], [3, 4]]

for item in items:
    item.append(5)

print(items)
# [[1, 2, 5], [3, 4, 5]]
```
So python has a reference. 
But in cpp 
```cpp
#include <bits/ostream.h>
#include <iostream>
#include <ostream>
#include <vector>

std::ostream& operator<<(std::ostream &out, std::vector<int> is){
    for (auto i: is){
        out << i << " ";
    }
    out << "\n";
    return out;
}

int main(){

    std::vector<std::vector<int>> iss = {{1,2}, {3,4}};
    for (std::vector<int> is : iss){
        is.push_back(5);
    }

    for (auto is: iss){
        std::cout << is; 
    }

    return 0; 
}
```
The output just remains 
```
[1 2]
[3 4]
```
But now say we want the reference, then we can simply pass by reference. 
```cpp
for (std::vector<int>& is : iss){
	is.push_back(5);
}
```
Thats it. So cpp offeres both ways of looking at objects, values as well as references. 

## Destructors 
Destructor of an object is called at the end of the object's lifetime. 
Default destructor: invokes destructors of all object fields 
A custom destructor can be provided to perform additional objects. 
```cpp
class A{
	public:
		A(int x): x_(x) {}
		~A(){
			cout << "Destructor" << "\n"; 
		}
	private:
		int x_;
}
```
![[Pasted image 20261009184547.png]]
![[Pasted image 20261009184606.png]]

**Destructors for abstract classes**
Say we have  ^707a5c
```cpp 
class Animal {
public:
    ~Animal() {
        cout << "Animal destroyed\n";
    }
};

class Dog : public Animal {
public:
    ~Dog() {
        cout << "Dog destroyed\n";
    }
};
```
And now someone does this 
```cpp
Animal* ptr = new Dog();

delete ptr;
```
That should technically call the destructor of dog, but because of the apparent type issue, it would call the destructor of Animal instead. 
Cooked 
So virtual comes again in clutch for the destructor object 
```cpp
class Animal {
public:
    virtual ~Animal() {
        cout << "Animal destroyed\n";
    }
};
```
Now run the same code. 
```cpp
Animal* ptr = new Dog();
delete ptr;
```
It would work! cause not because of virtual it would call the destructor of Dog instead. Simple!
## Smart Pointers 
Like C, C++ relies on manual memory management. 
Old style C++ allocates memory using `new` and frees it using `delete`
Modern style C++ almost never wants you to use `new` and `delete` instead we can smart pointers provided by `std::unique_ptr`

It is present in the `memory` module. So we can use that. 
```cpp
#include <memory>

std::unique_ptr<A> CreateInstance(bool use_subclass){
	return use_subclass
		? std::make_unique<B>("Hello")
		: std::make_unique<A>("World");
}

int main(int argc, char** argv){
	std::unique_ptr<A> owner = CreateInstance(argc > 1);
	return 0; 
}
```
Now `owner` owns the allocated memory. It would get destructed when `owner` gets destructed. 
This is illegal 
![[Pasted image 20261009195648.png]]
Unique ptrs can't be duplicated, here is where the *unique* comes in. 

And the way it does this is by setting the **copy constructor** value to **delete**, [[#Copy Constructor | Click here to scroll down to copy constructors]]


**Transferring ownership** 
We can use move semantics here .
It is present in the `utility` module. 
![[Pasted image 20261009195807.png]]

## Explicit Qualifiers 
Techincally when you write a **single** argument constructor in C, we would have the option of constructing it using 2 ways 
```cpp
class A {
	public:
		A(int x): x_(x) {}
	private:
		int x_; 
};
```
Now we can cosntruct objects in 2 ways
```cpp
int main() {
	A my_a = 1; 
	or 
	A my_a(1); 
}
```

If we want to prevent the first way of construction we can use the **explicit** keyword 

```cpp
class A{
	public:
		explicit A (int x);
}
```
And now, this raises an ERROR
```cpp
int main(){
	A my_a = 1; ERROR
	A my_a(1); PASS 
}
```

## Nullptr
When setting the ptr to NULL, in C we would set it to `0` but in cpp we can use `nullptr` which is better practice 
```cpp
struct Node* head = nullptr;
```
## Assert 
`assert` is a `C` header file, so instead we can use `cassert` to use assert. 
```cpp
void Add(int elem) {
	if (head_ == nullptr){
		assert(tail_ == nullptr && "Head should be null iff tail is null");
	}
}
```
*Note* the trick with the `&&` in the assert function. 

## Copy Constructor 

When you try to create a new variable and you assign it with another variable which has the same type as a variable that you are creating, you are calling a copy instructor. 
```
String string = "Jayce";
String second = string; // Copy cosntructor is called
```

Cpp does provide a deafualt copy constructor. 

The signature of a copy constructor looks like this, say we are writing a string class 
```cpp
String(const String&other)
```
And its default implementation would just be a shallow copy, so something like this. 
```cpp 
class String{
private:
	char *mBuffer_;
	unsigned int mSize_; 
public:
	String(const String &other)
		: mBuffer_(other.mBuffer_), mSize_(other.m_Size)
}
```
Or if you want to be a bit more exctiing, it provides this 
![[Pasted image 20261011222220.png]]
*Note:* It is **not** creating the other, but instead it is constructing itself in a way. It looks like a constructor, IT IS A CONSTRUCTOR, which is taking itself as input. So thats why its called a copy constructor! lol. 

Now say we want to not allow a copy constructor to be callled, like with a unique pointer, we can do the following. 
```cpp
class A{
private:
	int x_; 
public:
	A(int x): x_(x) {}
	A(A& other) = delete; 
};
int main(){
	A a(5);
	This is illegal now 
	A b = a; // Compiler ERROR
}
```

But look at this, this still works 
```cpp
#include <iostream>

class A{
private:
    int x_;
public:
    A(int x): x_(x) {
        std::cout << "Constructing with " << x << "\n";
    }
    A(A& other) = delete;

    friend void fun(A other);
};

void fun(A other){
    std::cout << other.x_ << "\n";
}

int main(){
    A a(5);
    fun(A(6));
}
```
I can pass in an **Rvalue** of A into the function there, which doesn't call the copy constructor but calls the constructor. 
The output looks like this
![[Pasted image 20261011223630.png]]
Which is super interseting. 

## Move Semantics / Move Constructor
Here is a simple naive implementaiton of a string class in cpp
```cpp
#include <cstdint>
#include <cstring>
#include <iostream>

class String{
public:
    String() = default;
    String(const char* string){
        std::cout << "Created" << "\n";
        mSize_ = strlen(string);
        mData_ = new char[mSize_];
        memcpy(mData_, string, mSize_);
    }

    String(const String& other): mSize_(other.mSize_){
        std::cout << "Copied" << "\n";
        mData_ = new char[mSize_];
        memcpy(mData_, other.mData_, mSize_);
    }

    ~String(){
        std::cout << "Deleted" << "\n";
        delete mData_;
    }

    void Print()
    {
        for (uint32_t i = 0; i < mSize_; i++){
            std::cout << mData_[i];
        }
        std::cout << "\n";
    }

private:
    char* mData_;
    uint32_t mSize_; 
};

class Entity{
public:
    Entity(const String& name): name_(name) {}

    void PrintName(){
        name_.Print();
    }
private:
    String name_;
};

int main(){
    Entity entity(String("Jayce"));
    entity.PrintName();
    return 0;
}
```

And the output when you run this program would look like this 
```cpp
Created
Copied
Deleted
Jayce
Deleted
```

The copy in the middle is the annoying bit, where we are allocating whole new heap allocation there, tho the String which we are sending into entity is an **Rvalue**
We can solve this by adding in a special constructor which accepts rvalue reference, this is the **move constructor**
```cpp
    String(String&& other): mSize_(other.mSize_){
        std::cout << "Moved" << "\n";
        mData_ = other.mData_;

        other.mSize_ = 0;
        other.mData_ = nullptr;
    }
```
If you look at this code now, you can see we are just doing a shallow copy of the buffer by moving the pointer. And then the **most** important bit, we are creating a **hollow object**, in order to fix the case of what would happen when other gets destructed, we can't be loosing the access to the underlying memory by that freeing it. 

But if we run the program we would still see the above output. 
Instead we would need to add another constructor to the Entity object as well (Move constructor)
```cpp
Entity(String&& name): name_((String &&)name) {}
```
But instead of doing that explicit cast, we can use the `std::move` function which just does that wrapping around. 
```cpp
Entity(String&& name): name_(std::move(name)) {}
```
