# Mock Objects 

![[Pasted image 20251015123032.png]]

The main reason why there exists mock objects is to be able to write tests for one object without caring about the other objects it talks with. 

We use a module for this called JUnitMockery 

Imagine we are writing tests for a HeadChef class, whose main role is to communicate and delegate tasks with the pastryChef and the waiter. 

We first create a mock object 
```java
public JUnitRuleMockery context = new JUnitRuleMockery();
Chef pastryChef = context.mock(Chef.class)
Waiter waiter = context.mock(Waiter.class)
```
*Note:* The chef.class and the waiter.class are interfaces. 

Now before calling the order function on the headChef object, we set up a checking as follows 

```java
HeadChef headChef = new HeadChf(pastryChef);
context.checking(new Expectation() {{
	exactly(1).of(pastrChef).order(APPLE_TART);
}});
headChef.order(ROAST_CHICKEN, APPLE_TART);
```
So the context checks if the headChef calls the pastryChef order once. If it does then it would pass. 

Now for a different scenario, suppose the mock object needs to return something. You could do it like this with the mock object 

```java 
context.checking(new Expectations(){{
	exactly(1).of(pastryChef).isCooked(APPLE_TART); will(returnValue(true));
	exaclty(1).of(waiter).serve(APPLE_TART);
}})
```
So the will return value will set the return value of the isCooked() function. Nice!

# Patterns 

## Template Method 
Use inheritance to abstract common features. 
![[Pasted image 20251021175622.png]]

***Coupling***
When two classes are coupled. It means modifying A modifies B. 
We want low coupling. 
Afferent Coupling (Ca) : Measure of how many other classes point to the current class. Think of it like *arrive* 
Efferent Coupling (Ce) : Measuer of how many other classes the current class points to. Thinkg of it like *emerge*

## Stratergy Pattern 

Uses composition and interfaces instead. 
So passes in the object which needs to be called based on each case. 

## Factory Methods 
![[Pasted image 20251117221644.png]]
Basically a much more clear way of creating objects. 

![[Pasted image 20251117221712.png]]

Look more deep into this. There is some Factory pattern as well. 
## Builder 
![[Pasted image 20251117222717.png]]

Used to create cool ways of initialising. 
![[Pasted image 20251117222748.png]]

## Singleton 
![[Pasted image 20251117222830.png]]

Ensures only one object can exist at most at any given time. 
Might be useful in pygs Game object. 

### Synchronised 

Synchronised is basically a lock around the thing. 

