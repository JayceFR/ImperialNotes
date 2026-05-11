# TDD
4 elements of simple desing of J.B Rainsberger. 
![[Pasted image 20260510145231.png]]

![[Pasted image 20260510145734.png]]
So we write a failing test, code to pass the test and then refactor. 
So refactoring means, making change to the structure but not the behaviour. 
We can flip this to even understand that TDD is something like a design activity from which we get a really good test suite. 
![[Pasted image 20260510150012.png]]
Also known as behaviour driven development. 

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
Some more syntax like `ignoring` and `never` can be used as well. 
ASK GPT about them. 
### Test Doubles
Class of test doubles contain the following.
![[Pasted image 20260510222801.png]]
So a **stub** is like a mock object that ignores everything. 
**Fake** is proper implementation that is simple and fakes a task, like we want to interact with a database, but the production database uses postgres and we don't want to spin up an entire postgres database in our computer. 
Instead we would want to run an in memory database, like SQLite or something like that. 
**Spy** is a combination of the real implementation and the mock. So we would really send emails, but we would add on the functionality that if the email has been sent and been caught or not. Dynamically add code into the object so we can check if it has been called. 

# Reuse and Extensibility
## Template Method Pattern 
Basically **inheritance**. Easy

## Coupling 
So we have two types of coupling. 
- **Afferent coupling** (CA) ->Class A's afferent couplng -> how many other classes use A.   Think of it like arriving. 
- **Efferent coupling**  (CE)-> Class A's efferent coupling -> how many other classes are used by A. 
**Stability**: Ratio of Afferent / Efferent Coupling. 
Therefore Template method pattern has a strong coupling. The sub classes mention the name of the super class. So moving the sub class to another file would mean moving the super class as well to the other file. 
## Stratergy Pattern
Uses **composition**.
So we can simply define an interface and then pass the object of that interface into the super class and then now the other classes can simply just implement the interface. Easy!
*Note*: Python is a dynamic language, so if the method singatures match, then we can call either. Same doesn't work for Java, we require an interface to do the same. 

# Designing For Flexibility
- **Rigidity** - where the code is hard to understand and hard to easily change 
- **Fragility** - where when we change one part, other parts break unexpectedly 
- **Immobility** - where it is hard to reuse elements of the code in other applications

**Law of Demeter**: So when we have something like this 
```python 
table.getGrid().getColumnModel().getColumn(index).setPrefferedWidth(newWidth)
```
*Problem* with this is that the class in which this line is present is talking with the internals of the other classes. So if someone later down in the line wants to change how the getColumn works and renames it to getColumnIdx and they think it is only being using in the Column Model, BOOM there is a bug, this class which contains this dodgy line would also need to change. 
*Solution* Just refactor and try moving it to the neighbouring classes. 
So somethign like `grid.updateWidth(...)` 
And keep pushing them to the next class. 

The Law of Demeter is a principle of OO programming that aims to help us follow Tell, Don’t Ask, and can be summarised as:
- Each unit should have only limited knowledge about other units 
- Each unit should only talk to its friends; don't talk to strangers. 
- Only talk to your immediate friend
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

There is even **Factory Objects**
![[Pasted image 20260511113919.png]]
A related pattern is a factory class/object. Here we can use the factory is to defer the decision about the exact type of the object that is to be created until runtime. We can use some property only known or calculated at runtime (such as here the country in which the application is running) to determine which subtype of the required type to create and return for the client to use polymorphically. This gives a lot more flexibility that just using the new operator

We even have **abstract factory pattern**
![[Pasted image 20260511113955.png]]
So factory to initialise another factory
Sometimes we have a family of different (but related) objects to create, and the exact types for each depend of the same condition. For example, if we are creating widgets for a user interface, then we might choose a particular look-and-feel, and that might determine what type of scroll bars, maximise/minimise icons, etc we want. One way to solve this problem is to use an Abstract Factory Pattern, where we create a type hierarchy including different types of factory. Then we instantiate a particular factory, and use that factory everywhere we need to create a widget. Sometimes we might even use a Factory to instantiate the right Factory. Be careful of going too deep down this road or you might end up with class names like AbstractEntityManagerFactoryBean

## Builder 
![[Pasted image 20251117222717.png]]

Used to create cool ways of initialising. 
![[Pasted image 20251117222748.png]]

## Singleton 
![[Pasted image 20251117222830.png]]

Ensures only one object can exist at most at any given time. 
Might be useful in pygs Game object. 

Now instead of initialising the BankAccountStore instance at the start there, we could do it **lazily**
![[Pasted image 20260511120036.png]]
**Synchronised** 
Synchronised is basically a lock around the thing. 
*Note:* Singleton creates dependency as we are using the class name of the singleton directly inside the class. 
![[Pasted image 20260511125048.png]]

# Working with Legacy code
![[Pasted image 20260511155151.png]]
### Seams 
A seam is a place where you can alter the behaviour in your program without editing in that place. 
Every seam would have an **enabling point**, a place where you can make the decision to use one behaviour or another. 
### Sensing 
We break dependencies to sense when we can't access values our code computes. So sensing is basically **mock objects**

# Interactive applications 
### Observer pattern
The model where we do something in response to an event or a notification, is known as the observer pattern. 
We invert the control of the program, so that instead of *calling the widget to ask whether it has been pressed, we register an object to be notified when something of interest happens*
Also known as **register a callback** or **publish subscribe**
### Model View Controller 
![[Pasted image 20260511175416.png]]
- **Model** Holds the main business logic and the data. 
- **View** is reponsible for the dispay. 
- **Controller** is responsible for identifying any changes that has happened in the user side (like click of a button) and then call the appropriate function from the model. 
### PAC -> Presentation Abstraction Control 
Used in React. 
Useful for hierarchical type of display. 
Tree of MVCs 
So each **agent** is a triple and its the M and the V nd the C that just look after a particular part of the user interface. And each agent looks over only a particular part of the user interface. 
![[Pasted image 20260511180809.png]]
So agents can talk with each other, but only the controllers can communicate. 
**Wormhole** One agent can talk directly with another agent without going up and down the tree. But it degrades the verall structure which we have. 

Another structure which we can use instead would be an **event bus**. 
So in an **Event Bus** we have all our agents, but they dont form a tree, instead the communication is done by broadcasting the message into a common **event bus** and then the appropriate agent can subscribe to what event it wants to hear. 
# Concurrency
### Threads 
So we have basic threads, extending the thread class allows us to define the run method. 
![[Pasted image 20260511184154.png]]
*Note:* As we are extending Thread, we have coupling issues. What if we want to reuse the computation in the run method in another contet where we aren't using threads. **Runnables**  solves this problem
### Runnables 
We break the copling issues we get with extending threads class (inheritance) by composing together objects instead following the **stratergy pattern** (Remember it means **composition**). 
So we simply implement an *interface* with the run method. Now we can use the run method simply as well. 
To make it a thread, we would simply wrap the Runnable inside the `Thread()`constructuor 
![[Pasted image 20260511184612.png]]

![[Pasted image 20260511184648.png]]
A Runnable just encapsulates some behaviour to be run at a particular time. We can either run it now, or later - synchronously or asynchronously. We can use a Runnable without a Thread just by calling its run() method directly.

### Callable 
Similar idea to Runnable, but further allows us to return values from the call method. Further allows exceptions to be thrown as well if necessary.
![[Pasted image 20260511184811.png]]
Easy!
### Command Pattern 
Command is like an instance of the stratergy pattern. 
So where in the stratergy pattern, we had a number of different implementations of the same interface that would specify differnt behaviour.  Here we have different behaviour on different data. 

So simply data with behaviour is a command. 
For example: Say we want to make video edits, we would say apply this behaviour at this time frame. 

![[Pasted image 20260511190104.png]]

So we have a command queue 
![[Pasted image 20260511190336.png]]
In this queue, everything is running one after the other. We could make it parallel by using an executor. 
![[Pasted image 20260511190858.png]]
![[Pasted image 20260511190922.png]]

Executors are part of the style called **producer and consumer**
![[Pasted image 20260511190947.png]]
Queue is acting as a **load balancer**, so it is trying to balance the workload to go to the end as fast as possible. 

### Returning results 
So we saw callables above. 
We can feed it to an `ExecutorService` *Note* It is different from the `Executor` which we used above for `Runnables`. `ExecutorService` provides a richer API compared to `Executor`
An `ExecutorService` uses `submit` compared to `execute` and returns a `Future`

**Future** is like a tracker for the task we submitted, So its like a ticket.
Say when we go for drycleaning. Now we have some options, like we could wait right there until your dry cleaning is ready, or otherwise they give you a little ticket that correspons to the items that you've given to them, you can come back later (do some shopping) and then finally wait again. 
![[Pasted image 20260511192145.png]]
- `get` is a blocking call 
- `get timeout` means we are only going to wait here for this much time. 
*Note:* The may interrupt thingy, can be caught as an exception. Look below for example. 

Some other important ExecutorService functions 
- `shutdown()` It says no more jobs can be submitted. 
- After we call shutdown, we can call `awaitTermination(120, TimeUnit.Seconds)` which means that any jobs that are left in the queue are going to be processed and this call is going to **block** until they are completed. We could also specify a timeout for this if we want to wait, but not forever.![[Pasted image 20260511192613.png]]

### Latch
An alternative to observing if all the task in the executor service is done  is using a Latch. 
![[Pasted image 20260511192831.png]]
Which is first initialised to the number of jobs we have so lets say `4`. Then as each job completes we decrement the counter atomically. When the counter reaches `0` we know that for sure tht all the jobs that were submitted are done. 

So here we wait on the latch compared to waiting on the executor service. That means *no need to shutdown the executor service* and no need to build another one for our next batch of work. 
We can just treat each batch separately with a separate latch, but use the same executor service to essentially execute all of our tasks. 
![[Pasted image 20260511193624.png]]
![[Pasted image 20260511193635.png]]
So the latch is shared to all the tasks. And then we can call `latch.countDown()` to decrement the counter atomically. Then in order to wait we can simply do `latch.await`. *Note:* `latch.await` is blocking. 

### Summary
So if we are using `callables` then use an `ExecutorService` which would return a `future`. We would obviously wait for each task, as it wants to return a value, so we would do `future.get()` which would block. And then finally we would call *shutdown* on the executorService.  Example 
![[Pasted image 20260511194058.png]]

Now for `runnables` it is a bit different. We have two different ways of waiting for termination. One of them would be to `shutdown` the `ExecutorService` and then call `awaitTermination` which would then block. Or we could use a `latch`. Problem with the first approach would be that the entire executorService needs to be `shutdown()` therefore we would need to create a totally different executorService in order to submit the next batch. 
Easy!

# Concurrency At Scale
### MapReduce
![[Pasted image 20260511195042.png]]
Collection of average `10 thousand` computers that may fail, but MapReduce infrastructure would take care. 
![[Pasted image 20260511195328.png]]
So in order to use the MapReduce problem, we would need to express it as a series of maps and a series of reduces. 
![[Pasted image 20260511195429.png]]
Google's Map Reduce is a secret and written in C++.
But there is an open source version called Hadoop. Which we can run on a cluster of computers. Nice!
Hadoop is written in Java. 
We need to write two different classes, one is the Map function and the otehr is the reduce function. 
![[Pasted image 20260511200140.png]]
Here is the map function. 
Context is the way we write to the output. 
![[Pasted image 20260511200423.png]]
Here is the reduce function. 

### Queues
Another way of doing distributed programming is to use Queues 
![[Pasted image 20260511201109.png]]
Uses the producer and consumer. 
**One** message can go to only **one** consumer
The queue jobs can be written in XML or JSON. So that would allow the producer to be running Python and then sending XML. And the consumer can use C++ for high performance. 
### Topics
![[Pasted image 20260511201456.png]]
Similar to queues but they are broadacast channels. i.e **One** message ot **multiple** consumers
So whoever subscribes to the message would get the it. But this may overwhelm the consumer. To solve this we would use the **fanout pattern**
### Fanout Pattern
![[Pasted image 20260511201710.png]]
We can get best of both worlds. So send it to a topic. And then have a queue for each one. 
### AWL Lamda vs EC2
![[Pasted image 20260511202610.png]]
EC2 is IaaS (Infrastructure as a Service)
Lamda is serverless. 
Infrastructure as a Service (IaaS) allows us to provision virtual machines and other devices in the cloud, for our own use. We can provision and configure whatever we want. We have quite a lot of work to do, but we get a lot of control. 
On the other side of the coin, taking a “serverless” approach means that we leave a lot more of the infrastructure to the provider. All we provide is our business logic for them to run for us. This can be a lot simpler to manage, but we yield a lot of control to the provider, allowing them to optimise resources holisticall.
![[Pasted image 20260511202721.png]]
In an IaaS model, we can create virtual machines, and install different software on each - for example our producer app, our consumer app, and a message broker can all be deployed to separate VMs
![[Pasted image 20260511202735.png]]
In a serverless model, we would make use of cloud-managed services like SQS to manage our queues, and AWS Lambda to run our consumer functions - but we don’t know (or care) which machines they run on.

![[Pasted image 20260511202753.png]]
EC2 is orange 
Lamda is Blue. 
The cost per usage for lambda is expensive. But doesn't cost us for idle. 
While using in the EC2, we would still need to pay for idle cost as well. 
# Systems Integration 
### Adapter Pattern 
Well we want to use a 3rd party API, which doesn't implement our interface, we just use an adaptor which would help us do the conversion. Easy!

### Decorator 
Wraps around the object to add on some more behaviour. 
![[Pasted image 20260511203626.png]]
### Facade Pattern 
Reduces capabilities of the object. Simplifies the functionality. Just exposes what is really needed. 

### Simplicator 
A variance of the facade pattern. 
 ![[Pasted image 20260511204108.png]]
### Proxy pattern 
Control access to oject providing placeholder object. 
A cache is implemented using a proxy. 
So the cache would hold the map of values to result. And the proxy would simply stay as the middle man and check whether the value that is been searched for is present in the proxy. If it is then we would simply return it. If it isn't then we would do the expensive operation and then update the cache before returning the value. 
![[Pasted image 20260511204546.png]]
Works well with simplicators. 
As simplicator would make the change from the custom proprietary network API protocol into something like HTTP, then we can use an off the shelf HTTP cache and can even use the HTTP headers for cache control and simply get the updated data. Easy!

### Hexagonal Architecture / Ports and Adaptors Architecture
![[Pasted image 20260511204800.png]]
Onion. 
Centre would be our business logic. Outermost circle would be the 3rd party code. The middle layer would be an Adaptor, to convert from the third party to the core layer. 
Code in the middle can be in our standard. 
If we dont do this, we would have our code in the middle polluted by the 3rd party API. 
This would also remove strong coupling. 
**Integration test** Test some code you wrote and some code you didn't. 
**System Test** We are not going to do any mock objects. 

# Distribution and Remoting 


# CD and Agile Methods
### Agile Methods
**SCRUM** 
![[Pasted image 20260511205953.png]]
We have a sprint. 
There was a 30 day sprint. And recently it is reudced to `2` or `1` week sprint.
So at the start of the sprint, they meetup and decide what needs to be done in that sprint. And at the end we have a showcase to the customer. 
There is even daily scrum meeting for planning what is to be done today. Done as standup meetings. 
Some people involved are the 
- **Scrum master**: They might be doing administrative tasks about updating plans and reports. 
- **Product owner** Reponsible for making the decisions about which features should we build. Someone from the customer's team or the business side or even the technical team. They should be able to say should we be building X or Y which one is more valuable. 
Scrum Doesn't require ant technical requirment. 

**XP**
Extreme Programming. 
Hass TDD and stuff. 

**Kanban**
Optimising flow. 
Measure the rate at which different things are produced and where things are produced unnecessarily or are being stacked up and waiting for other processes and where stuff requires waiting. 
The entire procedure is about making surr they organise these tasks such that there is maximal flow. 
Just doing enough for the feature for giving benefit to the customer. 
![[Pasted image 20260511210810.png]]

**Continuos Integration**
Core practice of XP. 
Small changes are made and then merged into master asap. 
In order to support this we need to have automated build. 
CI Tooling. 
![[Pasted image 20260511210933.png]]

We could even do grid of servers, so one can do a build for MAC and another a build for Windows
![[Pasted image 20260511211040.png]]

**Continuos Delivery**
Deployment pipelines 
Basically what we did in WACC. 
![[Pasted image 20260511211254.png]]
UAT is checking if we really solved what we solved 

