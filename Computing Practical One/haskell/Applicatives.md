Applicatives are quite fun :(

```haskell
liftA2 :: Applicative t => (a -> b -> c) -> t a -> t b -> t c

fmap :: Functor f => (a -> b) -> f a -> f b 
```

liftA is technically the same as fmap

```haskell
liftA :: Applicate t => (a -> b) -> t a -> t b
--We can implement this using liftA2 
liftA func as = liftA2 (\a b -> a b) (pure func) as 
```

This implementation works but is not that neat is it 

That's where ($) comes in to the play. This is technically id 

```haskell
($) f a = f a 
```

So now we can make the liftA look much better 

```haskell 
liftA func as = liftA2 ($) (pure func) as 
```

Now there is an app *<`*`>*  which has the following definition 

```haskell
<*> :: Applicative t => t (a -> b) -> t a -> t b 
<*> func as = liftA2 ($) func as 
```

We can basically avoid the pure keyword here as there is a difference in the type signature. We had just **(a -> b)** in the liftA type signature but here we have **t (a -> b)**. 

Now we can write fun stuff 

Like to add 3 to a Just can be done in the following ways 

```haskell
1. liftA (+3) (Just 5)
2. fmap (+3) (Just 5)  -- Note both 1 and 2 are both techinaclly the same 
3. pure (+3) <*> Just 5  = Just (+3) <*> Just 5 
```

Oh yeah the pure in applcatives is basically the least most single thingy. Basically it takes an a and wraps it in something and returns it. so has type 

```haskell
pure :: Applicative t => a -> t a 
```

LiftA can be even defined using app 

```haskell
liftA = (<*>) . pure 
```

So apping a pure f with a structure is same as apping.

Now there is another way of writing fmap using symbol notation 

```haskell
(<$>) :: Functor f => (a -> b) -> f a -> f b 
(<$>) = fmap 
```

So just as a recap let's recall the different notations once 
1. LiftA                                     -> Belongs to Applicatives
2. <$> or fmap                               -> Belongs to Functors 
3. MonadA -- Introduced in the monad lecture -> Belongs to Monads

### Excersice
	Try to implement the liftA2 function with combination of app and pure 
	Old Jayce couldn't do it without looking at the solution 


Soln 

```haskell 
liftA2 :: Applicative t => (a -> b -> c) -> t a -> t b -> t c 
liftA2 func as bs = pure func <*> as <*> bs 
```




