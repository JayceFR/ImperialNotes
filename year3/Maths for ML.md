# Machine Learning Problem Settings & Linear Models 

*Definition*: Machine learning is the study of **alogirthms** which automatically perform a **task** by processing an **example dataset**

![[Pasted image 20261016201146.png]]

Different ways to model the coordinates. 
- Set Theory 
- Probabilistic 
	![[Pasted image 20261016201804.png]]

- Linear Algebraic 
	This is how we would first be denoting our datasets. 
	![[Pasted image 20261016201953.png]]

## Linear Algebra Description
So lets look into the Linear Algebraic way in more detail, as this is what we would use for the first 5 lecutres. 
Instead of storing them each individually we would group them to a matrix called the **design matrix**
![[Pasted image 20261016202426.png]]
*Note* The transpose of each coordinate is a row. So in a hindsight it is basically collection of `[x,y]` coords. 

Now we can use linear algebra way of analysing dataset 
- If the column space of X is not full rank, then we know they are linearly dependent so we would know the dataset is encoding redundant information. 

## Supervised Learning 
In this course we are looking at single class multi class classification. 
We can again represent it using 3 different ways 
![[Pasted image 20261016223431.png]]
We have the desing matrix again, along with that we even have a vector capturing which label is the current cooridnate classifying to. 

Say we have this task where we need to suggest which speed limit is there for this particular road
![[Pasted image 20261016224148.png]]
Both classification and regression are wrong, we would use something else over here which we will say later in the course. 
But effectively if you think of classification, and say we are classifying into 3 classes, dog, elephant and horse. 
If we get a dog and we classify it to elephant we are wrong. 
But say we are finding a number. 
If the number is 15 and we predict 45, we are more wrong than we predict 30. 

## Unsupervised Learning 
![[Pasted image 20261016224937.png]]

Like clustering. Where we dont know the classes up front. 
### Unsupervised - Generative 
![[Pasted image 20261016225122.png]]
Where we model the original data, over here we are modelling it as 2 Guassian distributions and then we can generate new datasets from that. 

## Hybrid Settings 
![[Pasted image 20261016225239.png]]
Some dont fall cleanly into supervised or unsupervised
For example, in training llms, We are only interested in generating the next token from the previous token. After which we would then run a fine tuning. We could use supervised fine tuning or reinforcement learning. 

## Linear Model
Machine learning is the study of algorithms which automatically perform a task by processign an example dataset. 

*Note* **Algorithms** are **not** the same as **models**

**Models** is something we assume, it gives structure to what we are going to do  
and 
**Algorithms** return learned models, the best model. 

Before we look at models, we can first look at alogirthms 

Linear model is the most simplest model and the most analytically tractable 

The most simplest one 
![[Pasted image 20261016225957.png]]
So we have only one point, and we want to find the line that crosses through that point. 
We can do this with a slider lol. And we get a predicted y hat.
### Building up some Math 
Lets first build up some math before moving on
**Tensors**
![[Pasted image 20261016231821.png]]
So this 3d cube is called a tensor. 
We can even do multiplication of these tensors, which is called **contraction**

Before we look at contraction, this is the **summation notation** for our basic matrix multiplication. 
![[Pasted image 20261016232034.png]]
Which is trivial if we take an example and expand. lol 

Now we can generalise the above to **tensor contraction**
![[Pasted image 20261016232713.png]]
So over here we are multiplying a tensor with a matrix.
Where we are basically **contracting** the dimension `l`. Which you can see in the summation notation. 
*Trick* for writing summation notation. 
- Look at both the above examples, we are basically iterating over the last dimension, like `l` here and `m` above. And then we are simply replacing that `l` with `k`. Nice and Easy. We are keeping the remaining as it is. 
- So in the tensor contraction, our final tensor $C \in R^{n*m*o}$ So we would simply substitute i, j to n, m of A and u for o of B. 
This method is proposed by the GOD Jayce, so don't know if that approach works everywhere. 

Another way of writing notations are using the Einstein notation 
![[Pasted image 20261016233300.png]]
Which is fine to use if you want to use. 

Well we can simply translate this to code using cool pytorch feature as well 
![[Pasted image 20261016233446.png]]
So the syntax for writing it would be from right to left in a way. 
Like if you look at the last example 
`ijk,ko->ijo, A , B` 
We are saying $A_{i,j,k} B_{k,o}$ 
It basically looks like a function lol. 

Lets go back to our linear model now 
### Modelling Decisions and Implications 
Forget Design Matrix for now, and think of one data point. 
Like take an example 
Suppose we want to predict a house's price using three features:
- $x_1$ The number of bedrooms 
- $x_2$ Floor area 
- $x_3$ Distance from the city centre 
Our input data point would be a colum vector 
$$\begin{bmatrix}
x_1 \\ 
x_2 \\
x_3 
\end{bmatrix}
$$ The model would then have a corrsponding weight vector 
$$ \begin{bmatrix}
w_1 \\ 
w_2 \\
w_3 
\end{bmatrix}
$$
Then finally to compute the prediction we would do 
$$ \hat{y} = w^Tx \\ \hat{y} = w_1x_1 + w_2x_2 + w_3x_3$$ Which makes sense. 
Obviously for a collection of points we would use the desing matrix.
![[Pasted image 20261017001413.png]]
****
So what makes a linear model, linear? 
Its quite unintuitive, but its because of the **Linear Transformation**
![[Pasted image 20261017001547.png]]
We know this from comp tech? or calculus or linear algebra 
So what linear model does is, it links the input x to output y via linear transformation. 
BUT there is a **limitation** in the linear transformation definition, which is in the 2nd point in the above defintion. 
- What happens when alpha is `0` 
- Out model is T, which would satisfy the second point, which is saying `T(0) = 0` 
- Meaning our line would always need to pass through the origin which is quite restrictive. 
**The fix**
The fix we can do is to **normalaise the points**. 
![[Pasted image 20261017002240.png]]
Move everythng to `0` 
The way that is done is by subtracting off the **emprical average**
![[Pasted image 20261017002352.png]]
****
### Function View 
Idk if this is meant to go under linear model, but prob a general maths intro ig. 
![[Pasted image 20261017002611.png]]
Some quick points to know 
- Domain is the input and CoDomain is the output 
- The theta is just denoting any parameters we need for our ML model 
- Finally because of the notation of bold is a column vector the $wx$ doesn't typecheck. So we would need to take the Transpose of $w$

With the design matrix, we can predict on a whole batch of data at once. 
![[Pasted image 20261017003340.png]]
This follows from above $\hat{y}=w^T *x$ 
But now we have a collection of points and we want to predict eveything at the same time. 
The above would result in a column vector of N rows and `1` column

A nice neat summary of what we have seen so far 
![[Pasted image 20261017003508.png]]
- We start off with the index notation, where we are indexing $Xw$
- Then we go the summation noation 
- To the dot product 
  Important to note the **superscript** notation, read the above thing properly. He was able to move the subscript from the row indexing to the top. So then we can identify the dot product nature to simplify it. 
  ![[Pasted image 20261017121835.png]]
- Finally to the most generalised function view. 
  *Note* The function `f` could be anything, but it our case we have seen only one model so far, so we define it like $$f^w(x^{(i)}) = w^T . x^{(i)}$$
  You can see the parameter `w` is stuck in the top, saying the function would be receiving parameter w or has access to it. 
  *Interesting question to ask*: Why is the parameter (w) **not** included in the **domain** of the function? Like why not write $f(w, x^{(i)})$
  *Answer:* Essentially when we are thinking about learning, we're thinking about finding or changing the w or the parameter. Thats what we would start with.  We are not thinking about changing the data. 
- ![[Pasted image 20261017123159.png]]

****
That question leads us to the **first learning paradigm** we will investigate which is the **ERM** stands for **Empirical Risk Management**. 
It will take more lectures to flesh out the ideas, so look down it should hopefully be there. 
But today we will look at the *minimization* part. 

We would pose **`learning`** as a minimisation, namely what `w` (parameter) minimizes our errors?

We need to look at 2 things from the above definition
- errors 
- minimisation 
Lets start with 
**Errors**
![[Pasted image 20261017004820.png]]
The red is called the **residual**, which is the difference. 
We would need to define which residual is better than the other. 
We would look back to our friend **norms**
![[Pasted image 20261017005004.png]]
The `0` norm is just going to count the number of non zero entries in our vector. 
Here is a graphical representation of the norms 
![[Pasted image 20261017005245.png]]
The diamond is called the **manhattan** distance which is the `l1` norm. Just substitue `1` instead of `p` in the p norm case. 
We would use these norms to measure error 
![[Pasted image 20261017005508.png]]
*Note:* That fancy L is used to denote loss function. Here it is defined as the **mean squared error** which is the `L2` norm squared . 
But we can choose any norm. But choosing a different norm would change the answer we get. We would have a deep dive later to see which norm would we choose for a particular scenario. 
Another change in notation 
![[Pasted image 20261017010056.png]]
We dont really care about the data poiints, we care about changing the parameters, so we would rewrite $\hat{y}$ as $Xw$ but instead of w use the more accepted term, theta. And yh thats it. 
I have a question here, why is theta in the loss function a domain and not a prameter? 
![[Pasted image 20261017010429.png]]
We really dont care about the min of the loss function, we care about the argument aka theta that would give us the minimum. 
![[Pasted image 20261017010511.png]]Like we care about the `0.04` and not the red thing. Note w is theta (the parameter)
**Loss Landscape**
![[Pasted image 20261017010622.png]]
Loss landscape is basically the sketch of the loss function. Gets more important in the deep learning content. 
Practice question 
![[Pasted image 20261017011531.png]]
Solution:
![[Pasted image 20261017011546.png]]
Well if we want the Mean squared error we should do
![[Pasted image 20261017011727.png]]
But does this matter? 
No, because we dont want the minimum of the loss function, instead we would want the minimum argument passed to the loss function. 

# Ordinary Least Squares & Generalised Linear Models 
Now we are going to look at algorithms 
For the first case we are going to look at how can we find the **argmin** of the loss function, without using a slider. lol 
![[Pasted image 20261017110409.png]]
Simply by taking the derivative. Which leads to our first **learning algorithm**
## Direct Solution: `1st` Learning Algorithm 
We are still in the **ERM** to be super clear 
![[Pasted image 20261017110511.png]]

We can **expand** the loss function 
![[Pasted image 20261017111150.png]]
Using index notation, it was a practice question last class 

We can further expand it to this
![[Pasted image 20261017111215.png]]
*Note*: The 2 middle terms are scalars which equates its transpose, resulting in grouping them

Now we take the **derivative**
![[Pasted image 20261017113208.png]]
But before we look at that lets revise on vector calculus
****
**Vector Calculus revision**
In this course we are going to be using the **numerator layout**
So say we have a function $$f: R^n \to R^m \\ \nabla f \in R^{m*n}$$
*Note* how the codomain becomes the number of rows and domain becomes the number of columns
Basically it is writing the differentation in a **row vector form**. which is different from the column vector form we used in calculus. 

Example 
![[Pasted image 20261017114429.png]]
![[Pasted image 20261017114446.png]]
Nice! 
Its just important to note that the dimension flips. 
![[Pasted image 20261017114519.png]]
Easy!
I beleive it would help for mulitplication. As it would typecheck. 
**Rules to learn for 70%** of the course
![[Pasted image 20261017114608.png]]
*Note:* This is only to **inner product**, not outer product, which will be covered later. 

As a simple task lets try to prove this 
![[Pasted image 20261017115156.png]]

Lets walk through the proof 
![[Pasted image 20261017115209.png]]
- First step is to identify the dimensions. 
  We are differentiating with **respect to theta so theta is our input**
  $$f: R^n \to R \\ \nabla_\theta \in R^{1*n} $$
- Ok now next we write it in summation notation
- Then we are going to take the derivative from some $\theta_j$ 
  Which if we apply on the summation would result in just $c_j$. 
  Important you see this. If you dont write the first step in paper and then take the dervative in both sides. 
- Finally, we can generalise for any `j` but *important* the answer is not $c$ because of the resulting dimenstions. Which would give us $c^T$
Nice!

An important tool for making our life a lot easier when dealing with stupid differentiation in summation is to make use of the **Kronecker delta**
![[Pasted image 20261017133435.png]]
So in the above differentiation, we can do 
![[Pasted image 20261017133721.png]]
Taught by GPT god. 

Next question 
![[Pasted image 20261017130815.png]]
Breaking it down is fun
![[Pasted image 20261017133822.png]]
Now Krnoecker makes our life a lot more easier. 
![[Pasted image 20261017134302.png]]
Ok now we need to go back. 
![[Pasted image 20261017184753.png]]
Nice! 
But we would get mogged by the professor;s solution 
![[Pasted image 20261017184825.png]]
Bruh
****
Now lets go back to taking the derivative of that. 
![[Pasted image 20261017185626.png]]
Which uses the above 2 rules which we just proved. 

Now we set the computed gradient to `0` and we are chilling. 
![[Pasted image 20261017190251.png]]
Thats about it. 
Now we got hte least squares result lol. Yet another way of getting to the least squares result. 
Its called the **Ordinary Least Squares (OLS) Estimate**
![[Pasted image 20261017190517.png]]

This is super powerful, but we have a **problem** 
![[Pasted image 20261017192441.png]]
What happens if we scale our dataset by a bit. 
Holy cooked. 
So we would need to add the bias somehow. 
![[Pasted image 20261017201401.png]]
But if we just add it we are violating the definition of linear transformation. As `f(0) = b` and not `0`
But it is a linear transofrmation in the **extended basis**
### Basis expansion
Essentially we are going to write down a function that takes our inputs deterministically to another space. 
We are going to look at **affine basis expansion** first 
So we define phi as 
$$\phi(x): R \to R^2 \\ \phi(x) = 
\begin{bmatrix}
 x \\ 
 1 \\   
\end{bmatrix}$$

So this would essentially take an input say 5, and return `[5.1]^T`
Now in order to work `theta` also would gain a dimension 
$$\hat{y} = \phi(x)^T * \theta$$
Basically what we looked at in the first class. Except one of the dimensions is the basis expanded dimension, its not an actual data point. 
And now if we just do the summation expansion 
![[Pasted image 20261017202810.png]]
We indeed get the `mx+b` term and its a linear transformation as note the structure of the formula written it doesn't have any `+ b` in them. 
**Solution** So now we get what we want with the **OSM**
![[Pasted image 20261017203002.png]]
So our design matrix has the extra `1` column, because of the phi function. Remember each row of the design matrix is a point $x_i$. And we have a collection of points in order to get the 
Lets look at it gemoetrically if this makes sense. 
![[Pasted image 20261017204817.png]]
Basically we are plotting in a higher dimenstion and there the plane intersetcsts `0` so we are chilling, it is linear. 
This is called **affine basis transformation** because of the b in ax + b. 

**Polynomial basis expansion**
Instead of having our phi to just return `[x,1]^T` we can make it a fucntion of type $R \to R^4$ return $[x^3, x^2, x, 1]^T$
And now expanding it out we would get
![[Pasted image 20261017210339.png]]
We get a cubic with our parameters, $\theta$ representing the coeffs of the polynomial. 
So how can we learn what those best coeffs are, well we already know how to because we have a linear relationship between our basis expanded features and labels which means the answer is **OSM** 
![[Pasted image 20261017210746.png]]
*Important* Didnt see this at the start, The blue is our input which we can change and then we have the ones in purple which is our basis expanded contribuition. 
So *Note* we are finding different functions to map the dataset $x \in R$
And we are using our phi function which is going from R to `R^4` to find a linear looking funciton in that 5 (4 + label, which is 1 ) dimensional plane. 
Makes sense. Super cool. 
**My doubt**: Can we define a `phi` which can go from `R^2 -> R^4`? cause we might have more than one feature right. 
**Answer** Yup
![[Pasted image 20261018143131.png]]

Why stop at dimension 4 we dont, we can keep going
![[Pasted image 20261017211325.png]]

### Regularization 
Regularization is a technique used to prevent overfitting by constraining the complexty of the model during training. 
A prominent way of doing it is to penalise the magnitude of the parameters, with a strength $\lambda$ which is our hyperparameter 
![[Pasted image 20261018141052.png]]

****
**Hyperparameters vs Parameters**
Hyperparameters are the things that we choose. They define the model class 
Parameters are the things that I learn, the output of my learning algorithm. 
****

Coming back to regularization

In our case, `k` from our $\phi_k$ in polynomial basis expansion and the above $\lambda$ are the **hyperparameters** and the $\theta$ is the **parameter**. 

**Graphical view**
![[Pasted image 20261018141701.png]]
What essentially is happening is that we are adding that **penalty** which is the $l_2$ norm of $\theta$ to the function and moving the total mininum closer to `0`
Before 
![[Pasted image 20261018141835.png]]
with `0` regularization. 
And after, with some regualrisation
![[Pasted image 20261018141902.png]]You can see the function becomes more smoother. 

*Note* We can no longer use the **OLS**. Because we have changed the loss function. 
## Limits of Basis Expansion
![[Pasted image 20261018142352.png]]
Basis expansion is powerful but the functiosn which we saw are very chaotic. And sometimes that chaos can destroy what we want to learn. 
### Poisson Distribution
In the above examples we want things to be always positive. 
This isn't something that linear regression can guarantee or any other polynomial regression. 
![[Pasted image 20261018143346.png]]
Here it is predicitng negative values. 

Easy way to solve this is to introduce some non linearity. 
![[Pasted image 20261018143416.png]]
Now if we pass our model through this, we wouldn't get a linear regression but we would get a **poisson regression.** Reasons we will say later. 
Lets now reuse our method of setting the derivative off the loss funciton to `0` to get the learning algorithm 
**Expand**
![[Pasted image 20261018213906.png]]*Note* The new notation `i,:` under X, its just a pythonic way of doing its just the slice operator and it means its going to take every j. Over here it just means give me the entire row. 
*Note* It would return a row vector. So for X of the dimension `mn` $X_{i,:}$ would be of dimension `1 * n`
**Differnentiate and set to `0`**
![[Pasted image 20261018214057.png]]We are going to use chain rule here to help us differentiate.  
**Setting to `0`**
We reach a dead end. 
![[Pasted image 20261018214546.png]]
We would need some other learning algorithm here. Which we will look into later 

Its not just this case where our 1st learning method fails 
### Logistic regression
![[Pasted image 20261018215309.png]]
We only want `0` or `1`
![[Pasted image 20261018215335.png]]
But our linear regression predicts anything in the real line. 
![[Pasted image 20261018215407.png]]
We again find a logistic function, there is a **reason** for this particular funciton, which we will look into later. 
This above logistic funciton isn't just positive but tails out at `1` and `0` 
## GLM (Generalized Linear Model)
![[Pasted image 20261018215909.png]]
We can unite the above **logistic** regression and the **poisson** regression. Which is basically or linear regression which we had before but we would wrap it with a $g^{-1}$ which is a non linear function called the **link function**. 
We can't set it to `0` and solve. 
![[Pasted image 20261018220424.png]]

# Exam Practice Qs
## Week 1
![[Pasted image 20261018232708.png]]
