## Decorators 
Decorators let you add extra behavior to a function, without changing the function's code.
A decorator is a function that takes another function as input and returns a new function.
```python
def changecase(func):
  def myinner():
    return func().upper()
  return myinner

@changecase
def myfunction():
  return "Hello Sally"

print(myfunction())
```

We can even let the decorator take in parameters as well. It uses a closure concept, which is defined below 
```python 
def changecase(option):
    def change(func):
        def inner():
            if option == 1:
                return func().upper()
            return func().lower()
        return inner
    return change 

@changecase(0)
def myfunction():
    return "Hello Linus"    

print(myfunction())
```
## Generators 
Generaotrs are functions that can pause and resume their execution. 
A generator function returns a geneator which is an iterator. 
```python
def fibonacci():
    a,b = 0,1 
    while True:
        yield a 
        a,b = b, a+b

gen = fibonacci()
for x in range(100):
    print(next(gen))
```
**Generator expressions**
```python
# List comprehension - creates a list
list_comp = [x * x for x in range(5)]
print(list_comp)

# Generator expression - creates a generator
gen_exp = (x * x for x in range(5))
print(gen_exp)
print(list(gen_exp))
```
Can even use generator expressions with function like `sum`
```python
# Calculate sum of squares without creating a list
total = sum(x * x for x in range(10))
print(total)
```

**Generator Methods**
Generators have special methods for advanced control. 
- send -> We can assign something to `yield`. 
- close 
**send** 
```python
def echo_generator():
  while True:
    received = yield
    print("Received:", received)

gen = echo_generator()
next(gen) # Prime the generator
gen.send("Hello")
gen.send("World")
```
*Note:*  The `priming` in the above code. So when we call the function which has a `yield` keyword, it doesn't execute anything and just stops. Then we need to call `next` function on our generator, in  order for the function to run and stop in the first `yield` keyword 

**close** 
```python
def my_gen():
  try:
    yield 1
    yield 2
    yield 3
  finally:
    print("Generator closed")

gen = my_gen()
print(next(gen))
gen.close()
```

## Closures
```python
def pre(p):
    # Outer function stores prefix
    def add(t):
        # Inner function uses stored prefix
        return p + " " + t
    return add  # Return closure

# Create a closure that always prefixes with "Hello"
h = pre("Hello")
print(h("World"))
print(h("Python"))
```
 **How Closures Work Internally?**
When Python creates a closure:
- It stores outer function’s variables in a special attribute called ****__closure__****.
- The inner function keeps a reference, not a copy, to these variables.

## Context Managers
Context managers allow you to allocate and release resources precisely when you want to. The most widely used example of context managrs is the `with` statement 
Suppose you have 2 related operations which you'd like to execute as a pair, with a block of code in between. Context managers allow you to do specifically that. 
```python
with open("some_file", "w") as opened_file:
	opened_file.write("Hola!")
```
The above file opens the file, writes some data to it and then closes it. 
Two ways of implementing
- Implementing a context manager as a class 
- Implementing a context manager as a generator! 
**Class Implementation**
We override the `__enter__` and `__exit__` method. 
```python
class File(object):
    def __init__(self, file_name, method):
        self.file_obj = open(file_name, method)
    def __enter__(self):
        return self.file_obj
    def __exit__(self, type, value, traceback):
        self.file_obj.close()
```
*Note:* The type signature of the `__exit__` method, it takes in 3 params. 
Now we can do 
```python
with File('demo.txt', 'w') as opened_file:
    opened_file.write('Hola!')
```
Steps 
1. The `with` statement stores the `__exit__` method of the `File` class.
2. It calls the `__enter__` method of the `File` class.
3. The `__enter__` method opens the file and returns it.
4. The opened file handle is passed to `opened_file`.
5. We write to the file using `.write()`.
6. The `with` statement calls the stored `__exit__` method.
7. The `__exit__` method closes the file

*Exceptions*
1. It passes the type, value and traceback of the error to the `__exit__` method.
2. It allows the `__exit__` method to handle the exception.
3. If `__exit__` returns `True` then the exception was gracefully handled.
4. If anything other than `True` is returned by the `__exit__` method then the exception is raised by the `with` statement.
Returning `True` in the `__exit__` would mean it is handled and python doesn't throw an exception. Otherwise it would raise an exception. 

**Generator implmentation**
```python
from contextlib import contextmanager

@contextmanager
def open_file(name):
    f = open(name, 'w')
    try:
        yield f
    finally:
        f.close()
```
So now we can do 
```python 
with open_file('some_file') as f:
    f.write('hola!')
```
Here are the steps involved 
1. Python encounters the `yield` keyword. Due to this it creates a generator instead of a normal function.
2. Due to the decoration, contextmanager is called with the function name (`open_file`) as its argument.
3. The `contextmanager` decorator returns the generator wrapped by the `GeneratorContextManager` object.
4. The `GeneratorContextManager` is assigned to the `open_file` function. Therefore, when we later call the `open_file` function, we are actually calling the `GeneratorContextManager` object.

## New Features 
Python 3.14 is the latest version. 
- It supports free GIL build python. 
	Gil was present in the past in order to make the underlying CPython code much more reasonable to reason about. It made reference counting simple and much more. More of a history thing. 
- T strings, close to f strings, but called template strings. It makes serialisation to json much more efficient. 
- Multiple interpreters 
## Garbage collection 
