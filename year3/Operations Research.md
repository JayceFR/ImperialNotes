# Intro
Good solutions for complex decision problems. 

## Mathematical Programming 
OR solves mathematical programming models 
![[Pasted image 20261015180916.png]]
- So over here we have decision variables, like when we are splitting up some money over a set of stocks, which would be a vector `[x1, x2, x3]` Where this vector is our decision variable. 
- Objective function is what we want to minimise 
- Feasible set is used to represent the possible values the decision variable can take, so like when we have a constraint we would try to express it through the feasible set. 

## Linear Programming 
A Linear Program (LP) is a mathematical program that 
- Optimises a **linear** objective function 
- Over a feasible set described by **linear queaility and/or inequality constraints.** 
Examples 
- ![[Pasted image 20261015185801.png]] this is a linear rogram. 
- ![[Pasted image 20261015185823.png]]while this isn't a linear program. 
- ![[Pasted image 20261015190420.png]]this as well is **not** a linear program, but it can be massaged (because x1 = 3). But if the question asks if this is linear or not, the answer is **no**

Lets do a worded problem 
![[Pasted image 20261015192031.png]]

you can set it up 
![[Pasted image 20261015195734.png]]
*Note:* We could have set totally different decision variables as well 
![[Pasted image 20261015195848.png]]
And make the actual stuff 
![[Pasted image 20261015200011.png]]
*Note:* 
- If we dont include the line, its called a **half line**
- The **half space** contains all points on one side of the line, so the yellow shading in the above example. 
	- Open half space $H = \{x\in R^n : a^T x<b\}$
	- Closed half space $H = \{x\in R^n : a^T x<=b\}$
Formulate those lines as constraints and draw lines 
Finally intersect all the lines to get the overall feasible set. 
![[Pasted image 20261015200132.png]]
Some *important* terminologies to note here
- The feasible set in the above example is **convex** because no matter how we take 2 points in the set, the line will entirely live in the set. 
	So for example, 
	![[Pasted image 20261015200538.png]]
	This is not convex, as if we take the right points, drawing a line would be present int he sector. 
- Second, it is not a generic convex set, its a **polytope**, whats a polytope, its a set that is convex but whose boundary is obtained only by intersection of straight lines. 
	So a triangle is a polytope, pentagon is a polytope, rectangle. bla bla. 
	*Important* There are 2 ways of reprenting polytopes, 
	- Intersection of half spaces (like shading yellow)
	- Vertices
- Another terminology mentioned is **level sets**, its basically a collection of points where a function has the same value. 
  ![[Pasted image 20261015202127.png]]
Finally 
![[Pasted image 20261015202324.png]]
We can move the level set of the objective function to maximise and we would find Q is the **Optimal solution** and `8` is the **Optimal value**
- **Optimal Value** The value that the objective function takes at an optimal solution. So in our case the objectvie function was `x1 + x2` so for point Q (3, 5) we would get `8`. Optimal value is *unique* by defintion, it doesnt matter if there is more than one optimal solution.
- **Optimal Solution**: Q is the optimal solution. There could be more than one, but in our case it is only `1`
Some more *important* facts from above to *note*
- Almost every single time a solution would be the vertex. Why almost, because there could be some *pathalogical* cases where it is unbounded or there is no feasible set. 
- Ok, so now assuming there is no pathalogical cases we can bascially iterate through every single vertex and find which one is resulting in the maximum or minimum. 
	Lets think about the number of vertices 
	- It would grow exponentially in the dimension of program. 
	- It might work in 2,3, 4 dimensions, but not in any above that. 
	Here is where **Simplex** comes in. 
	- If we remove the patholocial case, it can traverse the graph in a clear way. We want to progress from one vertex to another in some efficient way. 
	- Simplex is **worst case exponential\*** . Note the *asterisk.*  Someone years ago constructed a bad instance to prove simplex is exponential by making it need to visit every other vertex before visiting the most optimal one. 
	- BUT in practice, this worst case is patholocial. 
	- There is a different algorithm (ellipsoid) method that can solve the problem in worst case polynomial time. But this works **poorly** in practice 
	- Someone then finally proved if you take all the bad instances of linear programs that give you exponential runtime and you **perturb** thenby a very little. No matter how you construct the original instance, the perturbed version is alwasy **linearly** solvable by simplex. This is called **Smooth complexity**
# Linear Programs In Standard Form
We are going to represent linear programs in a way that would make it simplex algorithm friendly 

*Definition* An LP is in **standard form** if 
- THe aim is to **minimse** a **linear objective function** 
- All constraints are **linear equality constraints**
- All constraint **right hand** sides are **non negative** 
- All **decision variables** are **non negative** 
*Note* it says non negative meaning `>= 0`, so if it is `0` then it is totally fine. 

So in order to make it more concrete, say we have this 
![[Pasted image 20261016104501.png]]
Where we are 
- minimising $z=c^T x$ 
- And we have linear equality to get $Ax=b$ 
- Now we say, $x >= 0$ and $b>=0$ 
- Then we have a standard linear program
If you expand we would get this 
![[Pasted image 20261016104737.png]]

![[Pasted image 20261016104836.png]]
And *Note* The fact of `x >= 0 ` meaninng its for all `xi >= 0`

Lets do some fun practice questions 
- Is this LP in standard form? 
  ![[Pasted image 20261016105655.png]]
  **No**! Because there is no $x_3 >= 0$ 
- ![[Pasted image 20261016110107.png]]
  **No** again, Because the right hand side is negative 
  *Note* We can make it into standard form. But as it currently stands it is not in standard form. 
- ![[Pasted image 20261016110352.png]]
  No, because of the inequality. 
Anoter thing to verify, but the lecturer says is if we have the decision variables, say $x_1 >= 1$ then it would NOT count as standard form.
## Standardising General LPs
Can i make a general LP which can contain 
- Maximisation 
- Inequaliies 
- Equality constraints with negative on right hand side 
- Have free (instead of non negative) decision variables. 
THese general LPs can be transformed to standard LPs in a **systematic way** 

Lets look through each of the above one by one.
### Maximisation -> Minimsation 
![[Pasted image 20261016111814.png]]
We take the negative 
But super *important* Note, inverting **preserves** the **optimal solution $x^*$**
But in order to go back to the **optimal value** which is unique (remember from before) we would need to take the **negative** again. 
Look at the graph above. 
### Inequalities -> Equalities  for constraints
Say we have this 
![[Pasted image 20261016112142.png]]

We can add a **slack** variable say $s_1$ to the lhs to make it equal to the rhs. Easy! Mr Mehta on top. 
![[Pasted image 20261016112240.png]]
*Note* We would need to say below $s_1 >= 0 , s_2 >= 0, ...$ 

With matrix notation, we can write this 
![[Pasted image 20261016112404.png]]

For greater than case, we would subtract 
![[Pasted image 20261016112509.png]]

The slides then give a matrix notation for this by saying $Ax - s = b$.
But doesn't cover the case when there is a mixture of `>=` and `<=` 
GPT does 
![[Pasted image 20261016120612.png]]
![[Pasted image 20261016120624.png]]
### Negative RHS 
Well, just multiply by `-1`
### Free Variables 
- Suppose there is no constraint on $x_j$ aka its free and can be positive or negative.
- Now in order to enforce >= `0` There are `2` approaches 
	Approach `1`
	- We replace $x_j$ with $x_j ^ +  - x_j ^ -$
	- Note, they are just different variables, like you can say `u`, `v`
	- Where $x_j ^ + >= 0$ and $x_j ^ - >= 0$
	- So I actually dont know why does he have the `-` sign on top lol. 
	Approach 2 
	- Not recommended by professor 
	- But what it essentially is, say $x_1$ is free, we would solve for $x_1$ using the other constraints and replace the value for $x_1$ 
	- So in other words, completely remove $x_1$ 
	- ![[Pasted image 20261016114258.png]]

# Fundamental Theorem of Linear Programming 
Say we have this right 
![[Pasted image 20261016123253.png]]
Its fine if it is not in standard form, we can make it to standard form. 

We first have some assumptions to make before we can continue. 
*Note:* This is the first time we are making any assumptions 
**Assumptions** 
- Number of variables (**n**) must be  **>=** number of equations (**m**) 
  So in a hindsight this means we must have more columns than rows. 
- **Rows of A** are **linearly independent** (otherwise the constraints are redundant). *Note* We are **not** talking about the columns of A (thats the job of the **index se**t, to give us linearly independent columns). 
- **rank(A) = m** 
### Index Sets 
Consider the system of linear equations in problem LP. 
$$Ax=b$$
Where $A \in R^{mn}$
Let $A=[a_1, ..., a_n]$ where $a_i \in R^m$ is the `i th` column vector of A. 

We know from the above **assumptions**
- the m rows are linearly independent.
- n >= m 
- m = rank(A)

![[Pasted image 20261016124613.png]]

Now we are going to select **m** columns from A that are linearly independent. And from our assumptions, this is always possible. 

So the size of the index set would be **m**. This index set is a subset of `{1,..,n}`

The matrix B is just the collection fo those columns present in the index set. And B is a **square matrix**. It is called te **basis** corresponding to the index set I. 

For example 
![[Pasted image 20261016124921.png]]

Nice 
### Basic Solutions
*Note* it is not Basis but it is Basic. Like Q Basic. lol

**Basic Solution (BS)**: A solution x to $Ax = b$ with $x_i = 0$ for all $i \notin I$ is a basis solution to $Ax=b$ wit respect to the index set $I$
Try to expand an example to see how it works. 
But effectively it is $B(I)\hat{x}$. Where the $\hat x$ needs to only contain $x_j$  where  $j \in I$. Cool

**Feasible solution (FS):** A solution x satisfying both $Ax=b$ and $x>=0$ is a feasible solution 

**Basic Feasible Solution (BFS)** A feasible solution which is also a basic solution is a basic feasible solution. 
If you look down, you would see effectively `BFS` is a vertex 

Basic solutions continued 
![[Pasted image 20261016153043.png]]
So we had a matrix A, we got and index set which here is all the numebrs from `1` to `m`. 
And then we extract them to matrix B and we solve $Bx_B = b$ 
And we would set the remaining aka `m .. n` as `0` in the final `x` vector. 
Now B has some good properties which would make our lives a lot easier computing $Bx_B=b$ where we would get a **unique** solution. 
![[Pasted image 20261016153319.png]]
So you can see how we are constructing the final `x` which is a unique basic solution to `Ax = b` with respect to `I`. 

So a quick summart before we proceed. 
- We are in this very big high dimensional space. We have this `x` vector whic we need to figure out. And the kind of way we are going about it is by saying, okay, some parts I'm going to be exactly setting to `0`. How many, well `n - m`. Whatever is left I can solve to get a unique solution. And we get a point 
- Do we like the point? If it is **feasible** aka from the above definition if the `x` vector we find is `>= 0` then we know its feasible for this problem here. 

Lets look at a geometric intepretation 
- For a standard form LP, BFSs correspond exactly to vertices of the feasible region. 
- Basis solutions are just the intersection of n hyperplanes in n dimensions 
  So in 2 dimensions, its the intersection of lines 
  ![[Pasted image 20261016193818.png]]
- BFSs are the ones in the feasible region 
  ![[Pasted image 20261016193838.png]]

### Fundamental Theorem of LP 
![[Pasted image 20261016194244.png]]

Just think of BFS as a vertex. It would make sense. 
Nice!

### Basic Variables 
Say, we have an index set `I` of size `m` and `B(I)` which is invertible. 
Now we would obviously split the `x` into `2` components
**Basic Variables (BV)**: The varaibles $\{ x_i \}_{i \in I}$ are called basic variables 
**Non Basic Variables (NBV)**: $\{ x_i \}_{i \notin I}$ are called non basic variables. 
*Note* by construction, NBVs are always `0`. The BVs can be zero or non zero 
And now we say 
$$x_{B} = [x_i | i \in I]\\
and \\
x_{NB} = [x_i | i \notin I]$$
*Note* both $x_B$ and $x_{NB}$ are column vectors 

You might be good to question here wouldn't the $x_{NB}$ just be a `0` vector, you are right, the professor pleads to trust the process. 

### Basic representation 
*Definition* The basic representation corresponding to I is the reformulation of the system $z=c^Tx, Ax=b$ which **expresses the objective function value`z` and each BV as a linear function of the NBV's .** 
![[Pasted image 20261020175528.png]]
Quite confusing, but we want to effectively be able to represent `z` and `BV` as a function of `NBVs`

We can start off by splitting the $Ax=b$ 
**Intuition**
Try expanding in paper a `3x4` `A` matrix wth `x` and using the index set = {1,2,3} so setting $x_4$ to `0`. You would see it is basically three rows of some combination of  $x_1 + x_2 + x_3$ . And the final row element being `0`
Which is effectively just $Bx_B$ simple. 
So now with that intution we can split up $Ax=b$ to 
![[Pasted image 20261020180525.png]]
to $$Bx_B + Nx_N = b$$
You might have the same question again that wouldn't the $x_N$ just be  `0`, you are right we would set them to `0` later but we would need it in this form because these **varaibles are precisely those which we would want to turn on, before moving from one vertex to another.**

And we can even decompose our cost function
$$z=c^Tx \\ z={c_B}^Tx_B + {c_N}^Tx_N$$

And then using them and doing some fancy rearranging we get 
![[Pasted image 20261020181753.png]]
So now we have `z` in form of NBVs only. 

![[Pasted image 20261020192828.png]]
So we have these `2` equations and *Note* how we can get the basis solution, we can just get it by setting $x_N$ to `0` 
And we have another important quantity 
**reduced cost vector** The vector characterises the sensitivity of the objective funciton value `z` with respect ot the non basis variables $x_N$

**Simple example** 
![[Pasted image 20261020200414.png]]
![[Pasted image 20261020200421.png]]
![[Pasted image 20261020200427.png]]
![[Pasted image 20261020200435.png]]
*Note* The $*$ in the above represents this equation here 
![[Pasted image 20261021115458.png]]
Which is present in the above slides screenshot. 

Quick **summary**
![[Pasted image 20261020200919.png]]

*Important*
![[Pasted image 20261020200941.png]]
So if `r >= 0` then no other feasible solution can have a lower objective value than the current BFS.  Because recall in the formula we are adding $r^T x_N$ so if `r >= 0` then we would only make the cost bigger. While we are trying to minimise the cost 

# Simplex
Now we are going to use the above facts in order to set up simplex 
We had this formula for **basic representation** before
![[Pasted image 20261021115524.png]]
## Simplex Tableau 

*Note* We would need to **start** from **basic representation**. If the current $LP$ is not in basic representation, i.e the $z$ and $x_B$ are not written in terms of $x_N$, then we would need to follow the above formula to first make it into a basic representation. 

Now we have a basic representation
We are going to rearrange the above **basic representation** to this form 
![[Pasted image 20261021115817.png]]

For example 
Consider this $LP$ 
![[Pasted image 20261021115844.png]]
This system of $LP$ is in **basic representation**, as it follows the above definition. And is written in the tableau format. More concretely it would look like this 
![[Pasted image 20261021120104.png]]
I would recommend to keep the **BV** column in the left.
So we would have something like this 
![[Pasted image 20261021120146.png]]

*Important* Super important to note that the columns of the basic variables and `z` (Note, column and not row) would have `1` only in the column that associates with it. For example $T_{x_1, x_1} = 1$. And the remaining values in the column are `0`.  ^01d56c
### Properties of Simplex Tableau 
These are very important, make sure you are 1000% thorough with these points 
![[Pasted image 20261021120717.png]]
Description for them 
- This is true, because in order to find the objective value of the current BS we would basically turn of all the NBVs. Which would get us the value `8` in the above example 
- Follows from the above point, we would get the values by settign the values of the NBVs to `0` and because of this fact [[#^01d56c]]  ^d50624
- This is due to 
  Rearranging this eq
  ![[Pasted image 20261021121312.png]]
  to 
  ![[Pasted image 20261021121325.png]]
  And *important* Note from above when we were talking about **reduced cost vector** which is basically how much of each $x_N$ can i turn on, we had this property written, 
  ![[Pasted image 20261021121546.png]]
  Now we have the **negative** of that, becuase of our **rearranging**. 
  So take this representation 
  ![[Pasted image 20261021121716.png]]
  We have reached our local optimum iff all the $\beta_i \leq 0$   because if we rearrange back to $z = ...+r^Tx_N$ then turning anything on would only increase our cost function. 
- This follows from the definition of the **FS** and from point 2 [[#^d50624]]
## Pivoting
So now we have our simplex tableau set up. 
![[Pasted image 20261021122328.png]]
*Note* Leaving the basis, is basically the BV in the BV column going out. and the entering the basis is the one in the row coming in. 
**For example** 
Take this tableau
![[Pasted image 20261021122810.png]]
Say we are going $x_4$ is entering the basis and $x_1$ is going to leave the basis. 
We would essentially need to make the $x_4$ column all `0`s but keep `1` int the row of $x_4$ which would be the $x_1$ now as that is leaving 
![[Pasted image 20261021123150.png]]
We dont need to do anything for this row, as there is already a `1` in $T_{x_4 x_4}$ 
Now we just need to take some linear combination of this row and subtract from the other rows 
![[Pasted image 20261021123305.png]]
In order to get this. Baby maths 
Now $x_4$ is a basis 
### Pivot selection
The real rule is how are we goign to select the pivots. 
There are 2 things to look at, the one entering the basis and the one leaving the basis 
**Variable entering the basis**
We look at the objective row, and choose the one that is positive, because we can technically move one unit in that direction to reduce the cost. 
![[Pasted image 20261021123854.png]]

**Variables leaving the basis**
Consider this Tableau notation again 
![[Pasted image 20261021161308.png]]
Now we are going to set $\bar{x}_{iq}$
![[Pasted image 20261021163318.png]]
*Note* We are guaranteed that $y_{i0} \geq 0$ as its a value of the feasible region and we know by definition, for a feasible point the $y_{i0} \geq 0$
![[Pasted image 20261021163528.png]]
*Note* If the denominator is negative, we are just going to say $\infty$ 
Now we have two cases if the $min \bar x_{iq}$ is either $\infty$ or some value less than infinity. 
![[Pasted image 20261021163717.png]]
So if all the ratio test values  are infintiy then the problem is unbounded 

Otherwise 
![[Pasted image 20261021163801.png]]
*Note* The fact that if there are several same value indexes, then we should choose the one with the smallest index. 
Easy. 

Full steps of Simplex
![[Pasted image 20261021163934.png]]



