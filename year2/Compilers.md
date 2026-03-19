# Front-End

## Terminologies
**tail call :** An optimisation that happens when you have something like this 
```haskell
f x = g (x+1)
```
In this expression f can basically just donate its stack frame to g. 
So usually this is how our stack frame would look like 

## Lexers
Lexers basically generate tokens. 
Most of the time we would skip this stage. It is only useful if we are writing a bottom up parser, so something like Dijkstras (from last year Interim).

## Parsers 
Parsers generate either an AST or a parse tree 
Difference between them is 
![[Pasted image 20260122222304.png]]
AST is more conciese (ie. to the left). And parse tree is all the garbage the parser saw. 

Some languages end here (python). Like it skips the typecheker. This explains why out of scope variables as reported only in the last minute when the program is running. lol :)

## Renamer  (Scope checking )

Its basically renaming the variables into their own unique names. So later on while referencing it, we can just ask the variable and dont need to ask where are you currently located. 
![[Pasted image 20260122223025.png]]


## Typechecker 
Trivial. Checks the types. lol

# Usefull WACC stuff
While creating the *AST* we can try to use Generics to avoid trying to redefine multiple different definintions. 
![[Pasted image 20260122224207.png]]
I know we can use Generics in Scala. But idk how to represent this gibberish lol. 


