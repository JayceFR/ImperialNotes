
## pure 
pure does nothing but just returns whatever is passed to it 
```scala
pure(7).parse("") -> Success(7)
pure(7).parse("a") -> Success(7)
```

## digit 
digit is defined in `parsley.character` It basically returns a `Parsley[Char]`

## Some 
This combinator repeatedly parses a given parser 0 or more times collecting the results into a list. 
```scala
scala> some(digit).parse("123")
val res1: parsley.Result[String, List[Char]] = Success(List(1, 2, 3))

scala> some(digit).parse("12 3")
val res2: parsley.Result[String, List[Char]] = Success(List(1, 2))
```

Nice! 

## Many 
This combinator is bascially the same as `some` but instead it runs it 1 or more times. 


## stringOfSome

Basically the same as `some` but makes it a string at the end. So you must give it a char type parser. 
```scala
scala> stringOfSome(digit).parse("12345")
val res3: parsley.Result[String, String] = Success(12345)
```

## map 
![[Pasted image 20260123213311.png]]
Example 
```scala
scala> stringOfSome(digit).map(_.toInt).parse("12345")
val res4: parsley.Result[String, Int] = Success(12345)
```

Not a good way of reading numbers!

## as 
![[Pasted image 20260123213842.png]]

Example 
```scala

scala> char('a').as(1).parse("a")
val res10: parsley.Result[String, Int] = Success(1)

scala> many(char('a').as(0)).parse("aaaa")
val res8: parsley.Result[String, List[Int]] = Success(List(0, 0, 0, 0))

scala> many(char('a').as(0)).parse("aaaa")
val res9: parsley.Result[String, List[Int]] = Success(List(0, 0, 0, 0))
```

# Stiching combinators together
## Zip <~>
![[Pasted image 20260123214221.png]]

```scala
scala> (letter <~> digit).parse("a9")
val res11: parsley.Result[String, (Char, Char)] = Success((a,9))
```

## Then ~>
So it parses the left one but it cares only about the second one. 
![[Pasted image 20260123214440.png]]

```scala
scala> (letter ~> digit).parse("a9")
val res12: parsley.Result[String, Char] = Success(9)
```

## <~
Same as above but returns the first thing instead

So with combining everything we get 
```scala
scala> (letter <~> digit).map(_._2).parse("a9")
val res13: parsley.Result[String, Char] = Success(9)
```

## eof 
Every parser we write, we need an EOF to just say that we have consumed everything. 

```scala
scala> (letter <~> digit).parse("b4c")
val res18: parsley.Result[String, (Char, Char)] = Success((b,4))

scala> (letter <~> digit <~ eof).parse("b4c")
val res19: parsley.Result[String, (Char, Char)] = Failure((line 1, column 3):
  unexpected "c"
  expected end of input
  >b4c
     ^)

scala> (letter <~> digit <~ eof).parse("b4")
val res20: parsley.Result[String, (Char, Char)] = Success((b,4))
```

Its mostly abstraced in the lexer. 

## Not followed by 
Basically inverses the combinator.

```scala
notFollowedBy(char('a')).parse('a')
Failure
```

`notFollowedBy(item)` == `eof`

## stringLift, charLift
present in the `parsley.syntax.character._`
it basically helps in implicitly using `"strings"`  as Parsers
```scala
scala> "abc".parse("abcd")
val res0: parsley.Result[String, String] = Success(abc)
```

## Zipped
present in `parsley.syntax.zipped`
```scala
(digit, letter, digit).zipped.parse("4c6")
(Char, Char, Char)
```
Compared to zip, which we can do using 
```scala
digit.zip(letter.zip(digit)).parse("4c6")
(Char (Char, Char))
```
So zipped avoids the nested tuples. Yay!

## The Or Ambiguity 
*Really Important!!!*
```scala
scala> ("hello" | "hi").parse("hi")
val res2: parsley.Result[String, String] = Failure((line 1, column 1):
  unexpected "hi"
  expected "hello"
  >hi
   ^^)
```
Why does the above fail?? Surely it should succeed. 

We can try to debug this 
```scala
scala> ("hello".debug("p1") | "hi".debug("p2")).debug("expr").parse("hi")
>expr> (1, 1): hi•
               ^
  >p1> (1, 1): hi•
               ^
  <p1< (1, 2): hi• Fail
                ^
<expr< (1, 2): hi• Fail
                ^
val res3: parsley.Result[String, String] = Failure((line 1, column 1):
  unexpected "hi"
  expected "hello"
  >hi
   ^^)
```
So the problem is the first combinator (hello) consumes the h and then the or is trying to parse i with hi. Which would fail!!!

So to fix it we need atomic 
**Atomic**
Atomic combinator says all or nothing. 

```scala
scala> (atomic("hello".debug("p1")) | "hi".debug("p2")).debug("expr").parse("hi")
>expr> (1, 1): hi•
               ^
  >p1> (1, 1): hi•
               ^
  <p1< (1, 2): hi• Fail
                ^
  >p2> (1, 1): hi•
               ^
  <p2< (1, 3): hi• Good
                 ^
<expr< (1, 3): hi• Good
                 ^
val res4: parsley.Result[String, String] = Success(hi)
```
So now we see it rolled back after p1. 
Nice!!
```scala
scala> (atomic("hello") | "hi").parse("hi")
val res5: parsley.Result[String, String] = Success(hi)
```
*Note:* Atomic is not great for backtracking but it does make our parser a bit more inefficient. But our literals are indeed atoms.


# Precedence 
```scala
    private lazy val expr: Parsley[BigInt] = 
      precedence(integer | "(" ~> expr <~ ")")(
        Ops(InfixL)("+" as (_ + _), "-" as (_ - _))
      )
```

# Parser vs Lexer 
![[Pasted image 20260124130737.png]]

