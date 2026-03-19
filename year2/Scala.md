# OOP Class, Abstract Classes & Traits
```scala
class Point(x : Int, y: Int) {
	println("creating a point")
	val value : Int = x * y
}
```

### Abstract classes
```scala
abstract class Shape(final val name:String) { 
	def area: Double 
} 

class Square(side:Double) extends Shape("Square") { 
	override def area:Double = side * side 
}
```

### Special Methods 
`f.apply(args)` can be written as `f(args)`. 
`c.update(k, v)` can be written as `c(k)=v`.

### Equals method 
```scala
final class Vec(val x : Double, val y : Double) {
    def magnitude: Double = math.sqrt(x * x + y * y)
    def +(v : Vec) = Vec(x + v.x, y + v.y)
    def show: String = s"($x, $y)"
    override def equals(that: Any): Boolean = that match
        case v: Vec => v.x == x && v.y == y
}

println(Vec(5,6) == Vec(5,6)) // results to true 
```

*Note:* You should never ever override equals instead we should use [[#^56a6eb | Case Class]] (basically kotlin data class) thingy
### Abstract class example 
```scala
abstract class Stack[A]{
    def push(x : A) : Stack[A]
    def pop : (A, Stack[A])
    def peek : A = this.pop._1
} 

class ListStack[A](xs : List[A]) extends Stack[A]{
    override def push(x: A): Stack[A] = ListStack(x :: xs)

    override def pop: (A, Stack[A]) = {
        val (x :: xs2) = xs :@unchecked  
        (x, ListStack(xs2))
    }
}
```

The line in pop is basically dereferencing the array into its head and xss. i.e 
val (x : xs2) = xs 
The :@unchecked is basically added to just supress compiler warnings for the scenario where the list is empty.

### Companion objects 
Continuing with the previous example we can make the object creation a bit easier.

```scala
class ListStack[A] private (xs : List[A]) extends Stack[A]{
    override def push(x: A): Stack[A] = ListStack(x :: xs)

    override def pop: (A, Stack[A]) = {
        val (x :: xs2) = xs :@unchecked  
        (x, ListStack(xs2))
    }
}
object ListStack{
	def empty[A] : ListStack[A] = ListStack[A](Nil)
}
```

So we made the primary constructor private basically saying we can never use it. 
So now to create a ListStack we can just call ListStack.empty 

This object which has the same name of the class and is in the same file is called a companion object. 

Companion objects come with some ambiguity tho.

```scala
class Colour private (r : Int, g : Int, b : Int)
object Colour{
    val red : Colour = Colour(255, 0, 0)
    val blue : Colour = Colour(0, 0, 255)
    val green : Colour = Colour(0, 255, 0)
  
    def apply(r : Int, g : Int, b : Int) : Colour = (r,g,b) match {
        case (255, 0, 0) => red
        case (0, 255, 0) => green
        case (0, 0, 255) => blue
        case _                => new Colour(r, g, b)
    }
}
```

The ambiguity arises in the apply method in the companion object. We just can't right `Colour(r,g,b)` in the last case as this would basically point to the apply function, so we would need to use the `new` keyword to solve this. 

A real world example would be with using Array. 
```scala
Array(1,2,4) // Returns an array with 1,2,4 in it
Array(5) // Returns a singleton array with 5 in it 
new Array(5) // Returns an array of size 5.
```

These companion objects can also be used to implement the factory pattern. These objects along with the apply method can prevent creation of objects which wouldn't be possible with just using plain constructors. 

### #define in scala?? (final val)
```scala
object BadErrors{
	final val TypeError = "Bad Types"
	final val SyntaxError = "Bad Syntax"
}
```

`final val` allows the compiler to inline these constants. 
*Note:*  So basically whenever we have a magic number or a string, make sure to use final val. 

### Traits 
They are just Scala interfaces lol.

We would use traits instead of abstract classes when we would need to extend mutliple different traits. 

```scala
trait Expr
trait LValue
trait RValue

class Id(val v : String) extends Expr, LValue, RValue
class Foo extends LValue, RValue

def foo(x : LValue & RValue): Unit = ()
```

# ADTs

Simple small ADT to start with 

```scala
sealed abstract class Nat
case object Zero extends Nat
case class Succ(n : Nat) extends Nat
```

Oooo what are case classes you might ask???
### Case Class
^56a6eb
Basically scala version of kotlin's data class
```scala
case class Succ(n : Nat) extends Nat
```
provides its own equals function and makes the n public with no need for val in the constructor. 
Further even provides a toString function. 

*Note:* A case class can be pattern matched on 

### 2nd Version
We can rewrite the above Nat ADT using enum classes as well 
```scala
enum Nat{
    case Zero 
    case Succ(n : Nat)
}
import Nat.* 
```

So whats the difference between the 2 versions. 
Technically speaking in the enum class version, Zero isn't its own type. 
So in the first version we could do Something like `val x : Zero.type = Zero` *but it looks like i can do this even in the other version*

*Note:* Use the first version to represent complex relationships. 
Example:
Say we wanted to represent the following 
```scala
val z : Zero.type = pred(Succ(Zero))
```

We can do this via the following code 
```scala
sealed abstract class Nat 
case object Zero extends Nat 
case class Succ[N <: Nat](n : N) extends Nat

def pred[N <: Nat](n : Succ[N]): N = n.n 
```

Where `<:` is the subtype notation. LINK HERE.
*But NO THIS IS STILL POSSIBLE USING ENUMS*

The real limitation (found by hours of chatgpting)

Can't represent this using enums 
```scala
sealed abstract class Expr[A]
case class IntLit(n: Int) extends Expr[Int]
case class Add(x: Expr[Int], y: Expr[Int]) extends Expr[Int]
```

We might think this may work 
```scala
enum Expr[A]{
    case IntLit(n : Int)
    case Add(x : Expr[Int], y: Expr[Int])
}
```
BUT it woud fail for this example 
```scala
val e : Expr[String] = IntLit(42)
```
The above line works with enums which is rubbish. But fails with the separate class definition. 

But this is the highlight from ChatGPTing for hours 
![[Pasted image 20260110144819.png]]
It dissed Jamie lol.

### Access Modifier 
```scala
sealed abstract class Nat 
case object Zero extends Nat 
final case class Succ(n: Nat) extends Nat
```
We need to make sure the case class is made final whenever we have it extending a saled abstract class Nat.

### Covariance 

Writing something like this would fail
```scala
enum Option[A]{
	case Some(x : A)
	case None // this faults 
}
```

So we need to add the `+`
```scala 
enum Option[+A]{
	case Some(x : A)
	case None 
}
```
The `+` basically says if we have `A <: B` when `F[+A]` then we get `F[A] <: F[B]`

So basically i can say 
```scala
val x : Option[Int] = Option.None
val y : Option[String] = Option.None
println(x eq y) // Returns try 
```

*Note* They are referentially equal!

# Functions 

### Lambdas
```scala
val succ = (x : Int) => x + 1 
val add = (x : Int, y : Int) => x + y 
```

### By Name Parameters 


# Project Management

### Scalatest 
basic test syntax
```scala
import org.scalatest.flatspec.AnyFlatSpec

class TrieTests extends AnyFlatSpec{
	behaviour of "An empty Trie"
	
	it should "have size 0" in pending 
}
```

`pending` means the test still hasn't been implemented. So Initial setup. 

### Exporting 
BootStrapped: basically contains a shell script along with the packaged jar file which would download all the packages and the right scala version on the host computer whoever runs the file
`scala --power package . --force -o word-searcher-bootstrapped`

FatJob: Contains everything 
`scala --power package . --force --assembly -o word-searcher`


# Implicits

```scala
class L[+A](val xs: List[A]){
	export xs.{flatten => _, sum => _, sorted => _, *}
}

val xs = L(List(1,2,3))

```

Now `xs` would have access to all the methods avaialbae to list excepty for sum, flatten and sorted. 
*Note:* The reason we can't define those methods is because of the type, like `sum` would require us to be handling with Ints not A. Same goes with flatten.

Solution to the problem in Scala 2 is **Evidence**

### Evidence (Scala 2)

```scala
class L[+A](val xs: List[A]){
	export xs.{flatten => _, sum => _, sorted => _, *}
	def sum(ev : A <:< Int): Int = xs.foldLeft(0)((n,x) => n + ev(x))
}

val xs = L(List(1,2,3))
println(xs.sum(<:<.refl))

```

### Implicits (Scala 2)

```scala
class L[+A](val xs: List[A]){
	export xs.{flatten => _, sum => _, sorted => _, *}
	def sum(using ev : A <:< Int): Int = xs.foldLeft(0)((n,x) => n + x)
}

val xs = L(List(1,2,3))
println(xs.sum)
```

So basically by having the using keyword the compiler would ba able to do the work for us implicitly without us explicitly providing refl. 

### Extension (Scala 3)
So in scala 3 we have extensions which solves the same issue a bit better 
```scala
extension (xs : L[Int]){
	def sum: Int = xs.foldLeft(0)(_ + _)
}
```

### Ad hoc Polymorphism

```scala
extension [A] (l : L[A]) (using ord : Ordering[A]){
    def sorted : List[A] = {
        def insert(x : A, ys : List[A]) : List[A] = ys match{
            case Nil => List(x)
            case y :: ys if ord.lt(x,y) => x :: y :: ys 
            case y :: ys => y :: insert(x, ys)
        }
        l.xs.foldRight(Nil)(insert)
    }
}
```

So now over here where we want to have some sort of Ordering involved in A we need to use the above syntax. 
We could make it look a bit nicer by a fancy import 

```scala
import Ordering.Implicits.*

extension [A] (l : L[A]) (using Ordering[A]){
    def sorted : List[A] = {
        def insert(x : A, ys : List[A]) : List[A] = ys match{
            case Nil => List(x)
            case y :: ys if x < y => x :: y :: ys 
            case y :: ys => y :: insert(x, ys)
        }
        l.xs.foldRight(Nil)(insert)
    }
}
```

So now we get the `<` operator and we dont require the `ord`

We could do something fun with this 
```scala
val xs = L(List(1,4,3,2))
println(xs.sorted) // returns 1,2,3,4
locally{
	given Ordering[Int] = Ordering.Int.reverse 
	println(xs.sorted) // returns 4,3,2,1
}
```

Fun way to reverse stuff
*Note:* `given` is the producer of implicits and `using` is the consumer of implicits.

# Devops 

Shared runners are the same as instance runners 


# Property Based Testing or randomised testing 

Generate Random data and then use it to test properties of your code. 
## Library 
```scala
using dep org.scalatest::scalacheck-1-18::3.2.19.0 


using testFramework org.scalatest.tools.Framework 
```

Remember we need test.dep while using them in wacc. 
## Generators
Genereators are ways to generate random data. 
Lets try building our own generator 

```scala
def genList[A](gx : Gen[A]): Gen[List[A]] = ???
```

So we are taking a generator of type A, cause we would get stuck otherwise. 

So now here is a simple genList function for lists

```scala
def genList[A](gx : Gen[A]): Gen[List[A]] = Gen.oneOf(
	Gen.const(Nil),
	for x <- gx; xs <- genList(gx) yield x :: xs 
)
```

The above has 50% chance of creating one.

We can control these parameters using frequency 
```scala
def genList[A](gx : Gen[A]): Gen[List[A]] = Gen.frequency(
	1 -> Gen.const(Nil),
	4 -> for x <- gx; xs <- genList(gx) yield x :: xs 
)
```


Now there is a better way of doing this as well
```scala
def genList[A](gx: Gen[A]): Gen[List[A]] = Gen.sized {
	case 0 => Gen.const(Nil)
	case n => (gx, Gen.resize(n-1, genList(gx))).mapN(_, _)
}
```
The map syntax comes from cats and is basically like syntatic sugar.. yum!
So what the above example does is it always generates size of 100 elementes in the list! 

