## Refrences
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
void SetValue(cosnt int &value){
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
