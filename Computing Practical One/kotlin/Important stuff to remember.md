 Functions 
To pass functions as parameters or something 
we use :: 
for example 

```kotlin
fun sayHello(Request request) : Response{
	...
}

pair : Pair<Int, (Request) -> Response> = Pair(5, ::sayHello)
```

While referencing functions into varaibles we have a different way of assinging the data type 

```kotlin
val square : (Int) -> Int = {x -> x * x}
```

The above line can be expanded to the following 

```kotlin
fun sqaury(x : Int) : Int = x * x 
val square : (Int) -> Int = ::squary
```

This is quite an interesting example of use case 

```kotlin
fun composion(f : (Int) -> Int, g : (Int) -> Int) : (Int) -> Int = 
	{x -> f(g(x))}
```

So now to use the above function, we can do it in the following ways 

```kotlin
val power4 = composition(square, square)
println(power4(5))
```

OR 

```kotlin 
println(composition(square, square)(5))
```

Private set 

```kotlin
class ClassIg(){
	var size: Int = 0 
	private set 
}
```

the above code says that size is *mutable* but can only be changed within the class ClassIg. something like private setter. But getter is public 

Count string function 

```kotlin
val someString : String = "Hello World"
someString.count {it == 'a'}
```

## In Scope functions 

Let 

Basically null checks and runs only if the object is not null. Good for mulitrocessing. 

```kotlin 
val hello : Int? = null 
hello?.let{
	it : Int 
	val hello2 = it + 1 
	hello2 
}
```

Also

```kotlin 
fun getSquared() = (i * i).also{i++}
```

Basically same as let but returns the object it is called on
so in the above example it returns i square, then it updates the value of i also 

Apply 

```kotlin 
val intent = Intent().apply{
	putExtra("",""),
	putExtra("",""),
	action = ""
}
```

returns the object it is called on and does some stuff on the object. 

Run 

same as apply but returns the last line and not the object it is called on. 

# Arrays 

```kotlin
val myArray = Array<Int>(size_required) {default value}
val array : Array<Int> = Array(3) {-1}
```

```kotlin
Array (capacity, {index : Int -> do something})
```

copyOf returns the original array with nullables in the desired size 
```kotlin 
val newArray : Array<Int?> = array.copyOf(8)
```
Note the *?*

# OOPs
Secondary Constructors 
```kotlin
class blabla(name : String){
	constructor() : this("hello")
}
```

Some random thingy 

```kotlin
class ResizingArray(name : String){  
    constructor() : this("hello"){  
        println("Initialised ig")  
    }    init {  
        println("in the init block with $name")  
    }}  
  
fun main(){  
    val obj2 = ResizingArray()  
}
```
the output for the above code is 

```
in the init block with hello 
Initialised ig 
```

NO *public* in kotlin. Nothing means Public. Public is redundant 

## Interfaces 

```kotlin
interface ImperialMutableList<T>{
	val size : Int 
	fun add (element : T) 
}
```

The val property above says *provide atleast read access*. You could provide a var property. so private set. 

Interfaces can provide default implementations. 

We always use override. 

#### Set and Get 

```kotlin
interface Rectangle {
	var width : Int
	var height : Int 
	var area : Int 
		get() = width * height 
		set(value) = {
			width = value / height 
		}
}
```

## Sealed Classes vs Enums 

```kotlin 
sealed class Http(val code : Int){
	data class Unauthorised(val reason : String) : Http(401)
	object NotFound(404)
}
```

Basically gives more flexibility than enum. 

## Iterators 

basically an interface with hasNext() and next() method to overrite. 

## Anonymous Objects

```kotlin
class Foo{
	override fun iterator() : Iterator<T> = object : Iterator<T>{
		private var index = 0 
		... 
	}
}
```

Basically inner class without a name. 

## Inheritance 

```kotlin 
open class Lamp(private var isOn : Boolean){
	...
}

class DimmingLamp(isOn : Boolean) : Lamp(isOn) {
	...
}
```

Note the *open* keyword 

We need open in the class definition to say it can be inherited from. 

***To allow functions to be overriden, use *open*  

A sub class can not directly access private properties and methods of subclass. 

## Down casts 

```kotlin 
class SmartHome{
	private var lamp : Lamp = Lamp()
	fun lowerBrightness(notches : Int){
		if (lamp is DimmingLamp){
			for (i in 1..notches){
				(lamp as DimmingLamp).down()
			}
		}
	}	
}
```

## Read Only 

```kotlin 
class User(val name : String){
	private val _friends : MutableSet<User> = mutableSetOf()
	val friends : Set<User>
		get() = _friends 
	fun considerFriendsRequest(otherUser : User){
		...
	}
}
```

On the flip side we could do it like this 

```kotlin
class User(val name : String){
	private val _friends : MutableSet<User> = mutableSetOf()
	val friends : Set<User>
		get() = Collections.unmodifiableSet(_friends)
	fun considerFriendsRequest(otherUser : User){
		...
	}
}
```

The collections wrapper has access to the Mutable Set interface, but it throws an exception instead. 

# Concurrency 

## Concurreny vs Parallelism 
Concurrency (logical parallelism) is composition of independently executing units. Could be non deterministic while 
Parallelism (physical parallelism) is efficient execution of multiple tasks on multiple processing units. Usually deterministic. 

## Process vs Threads 
***Process***
A process is an independent unit of execution (basically a single program).
Characterised by Identifier, Memory space and *one or more threads*

***Threads***
Each thread has a 
	ID
	Program Counter 
	Local Memory (separate for each thread)
	Global Memory (shared between threads)

```kotlin
package lectures  
  
class MyFriend : Runnable{  
    override fun run() {  
        println("Hello world")  
    }}  
  
fun main(){  
    val friend : Thread = Thread(MyFriend())  
    friend.start()  
    friend.join()
}
```

start -> starts the thread 
join   -> blocks the main or aka main functon waits for the thread to complete and then continues. 


# Nitpicking 

```kotlin 
class Visibilty(private val p : Int){
	fun addProperties(other : Visibility) : Int{
		return this.p + other.p 
	}
}
```

Note, the p is visibile of the other class in the current class. 
The p is class private and not object private. 

## By keyword

```kotlin
class LoggingImperialMutableList<T>(
	val list : ImperialMutableList
) : ImperialMutableList by list 
```

The by keyword just specifies that the object provides all the implementations. 

## Sealed Interfaces 

Basically enum but for interfaces that say it has a bound / seal. 

```kotlin
selaed Interface Expr {
	class Literal(val value : Int) : Expr 
	class Mul(val lhs : Int, val rhs : Int) : Expr 
}
```

$$ \vec b+2=\int \lambda ^2 dx $$
Dont know what I just wrote 
whatever 
coming back

## Advanced Generics

Annoying part of kotlin 
$$ u \subseteq v\ \lnot\to Array<c> \subseteq Array<v>  $$
*Note* the  *not*. 
But 
$$ u\subseteq v\to List<c>\ \subseteq \ List<v> $$
we'll understand why later on .... Suspense oooooo [[#Speciality of List|Click me for a Suprise]]
```kotlin
fun replaceFirst(objects : Array<Any>, replacement : Any){
	...
}
```
The above function is *not* valid 
Instead we need to write 
```kotlin
fun <T> replaceFirst(objects : Array<T>, replacement : T){
	...
}
```

Say we have 
$$ Circle\subseteq Shapes$$
and we have a function 
```kotlin
fun largestArray(shapes :Array<Shapes>) : Double 
	= shapes.map(Shape::area).max()
```
and we have a main function
```kotlin
fun main(){
	val myShapes : Array<Shapes> = arrayOf(Circle(...), Circle(...))
	println(largestArray(myShapes))
}
```
the above code *doesn't* compile 
Instead we modify the largest Array function to 
```kotlin
fun <T : Shapes> largestArray(shapes : Array<T>) : Double 
	= shapes.map(Shape::area).max()
```

#### Speciality of List 

Well it is because of the *in* and *out*
*out* key word is would make 

```
if b is a substype of a then c<b> is a subtype of c<a>
```


and *in* keyword would make 
```
if b is a subtype of a then c<a> is a subtype of c<b>
```

The list interface has the out keyword on T therefore allowing us to pass fun things.
## Things to revise 

1. Copying a map, set or list in java and kotlin. 
2. Remember about sealed interfaces for the interpreter practice test

# Some brilliant functions 

### generateSequence(head) {it.next}.toList()

basically used to traverse a linked list easily 

### " ".repeat(n) -> repeats it n times 



