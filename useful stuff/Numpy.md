# Array Creation 

```python
x = np.array([[1, 2, 3], [4, 5, 6]])
```

Creates a numpy array. 
So the above array is obviously 2 dimensional. 
Axis 0 has 2 elements 
Axis 1 has 3 elements. 

*Note* that is the shape of the array. (2,3). The number of elements in the first axis and then followed by number of elements in the second axis. 

Some other useful functions we can use while creating numpy arrays 
![[Pasted image 20260120145446.png]]

# Array Operations 

### Vectorisation

Using normal python lists, this is what we can achieve. 
```python
numbers = [i * 2-3 for i in range(10)]
print(numbers)
```

But using numpy We can do better.
```python
x = np.arange(10) # Like a for loop. So generates [0..10)
numbers = x * 2 - 3  # This carries out the operation on all the elements. 
print(numbers)
```

Another example 
```python
x = np.ones(2)
y = x * 2 
print(y) # outputs -> [2,2]
```

So this process of operating on all the elements is called *Vectorization*

If we dont want integers (arange) we can use linspace instead
```python
>>> x = np.linspace(0, 10, num=5) 
>>> print(x)
[ 0.   2.5  5.   7.5 10. ]
>>> y = x**2
>>> print(y)
[  0.     6.25  25.    56.25 100.  ]
```

More examples of vectorisation 
```python
>>> x = np.array([1, 2, 3])
>>> y = np.array([4, 5, 6])
>>> z = x + y
>>> print(z)  
[5 7 9]
```

So `+` is an implicit for zip. Damnnn

**Matrix Multiplication**
Lets say we want to carry out this operation 
![[Pasted image 20260120151152.png]]
This is pairwise multiplication. Something we never do in maths 
This can be simply achieved by 
```python
x = np.array([[1, 2], [3, 4]])
y = np.array([[2, 3], [4, 5]])
z = x * y
```

Now lets say we want the proper mulitplication 
![[Pasted image 20260120151336.png]]
This can be achieved by 
```python
x = np.array([[1, 2], [3, 4]])
y = np.array([[2, 3], [4, 5]])
z = x @ y 
```

**Gotchas**

Broadcasting 
yuck
![[Pasted image 20260120151629.png]]



### Indexing Arrays 

Similar to pythony way of doing things. *Except* with indexing higher dimensional lists (n > 1). 

```python
>>> y = np.array([[1, 2, 3, 5], [-1, 4, 7, 9]]) 
>>> print(y)
[[ 1  2  3  5]
 [-1  4  7  9]]
>>> print(y[0, 1])  # (row 0, col 1)
2 
>>> print(y[-1, -2]) # (last row, second-to-last col)
7  
>>> print(y[0:2, 2:4]) # slicing out a subset of the array
[[3 5]
 [7 9]]
>>> print(y[1:3, :-1]) # Try figuring this out yourself! It is a bit confusing!
[[-1  4  7]]
>>> print(y[:, :]) # What does this do?
[[ 1 2 3 5]
 [-1 4 7 9]]
>>> print(y[0]) # And this one?
[1 2 3 5]
```

We can still use `[][]` syntax. But it is *inefficient* 

Another weird case of indexing 
```python
>>> y = np.array([[1, 2, 3, 5], [-1, 4, 7, 9]]) 
>>> print(y)
[[ 1  2  3  5]
 [-1  4  7  9]]
>>> print(y[[1, 0, 1], [3, 2, 0]])
[9 3 -1]
```
Think of the above example like a *zip*!

**Boolean Indexing**
```python
>>> y = np.array([[1, 2, 3, 5], [-1, 4, 7, 9]]) 
>>> print(y)
[[ 1  2  3  5]
 [-1  4  7  9]]
>>> print(y < 4)
[[ True  True  True False]
 [ True False False False]]
>>> print(y[y < 4]) # Keep only elements that are <4. The output is a 1D array
[1 2 3 -1]
>>> print(y[(y < 4) & (y > 1)]) # Keep elements that are <4 and >1 
[2 3]
>>> print(y[np.logical_and(y < 4, y > 1)]) # Different way to achieve the above
[2 3]
```

*Note :* The way they used the single `&`. 

**Good Example**
```python
data = np.array([
       [5.46, 3.06],
       [2.08, 1.57],
       [7.28, 3.06],
       [3.35, 4.74],
       [2.51, 2.43],
       [3.91, 4.55],
       [2.72, 3.11],
       [6.05, 2.72],
       [0.84, 3.57],
       [1.14, 6.31]])

label = np.array([1, 2, 1, 2, 2, 2, 1, 1, 2, 2])


data_1 = data[(label == 1)].T 
data_2 = data[(label == 2)].T 

print(data_1)

assert np.all(data_1 == np.array([[5.46, 7.28, 2.72, 6.05],
                           [3.06, 3.06, 3.11, 2.72]]))

assert np.all(data_2 == np.array([[2.08, 3.35, 2.51, 3.91, 0.84, 1.14],
                           [1.57, 4.74, 2.43, 4.55, 3.57, 6.31]]))
```

# Array Reshaping 

### Flattening 

Two methods available to flatten a numpy array, `flatten` and `ravel`

```python
x = np.array([[1, 2, 3], [4, 5, 6]])
flattened_x = x.flatten()
flattened_x[0] = 10
print(flattened_x)
print(x)

x = np.array([[1, 2, 3], [4, 5, 6]])
flattened_x = x.ravel()
flattened_x[0] = 10
print(flattened_x)
print(x)
```

Difference is `ravel` returns a reference back to us, so making any changes to the flatten list would result in the original array `x` changing. 

### Reshaping 

Two methods `x.reshape` or `x.resize`
`x.resize` mutates `x` itself. 

```python
reshaped_x = x.reshape((6, 2))

# This also works
reshaped_x = x.reshape(6, 2)

# You can make one of the dimensions -1. 
# The dimension will be inferred automatically
reshaped_x = x.reshape((-1, 2))
reshaped_x = x.reshape((6, -1))

# There is also a function version if you are not into OOP.
# This only takes a tuple as the second parameter.
reshaped_x = np.reshape(x, (6, 2))

```


*Note the differnece*
```python
x = np.array([[0, 1, 2, 3],
              [4, 5, 6, 7],
              [-6, -5, -4, -3]
             ])
x = np.array([[0, 1, 2, 3],
              [4, 5, 6, 7],
              [-6, -5, -4, -3]
             ])
​
reshaped_x = x.reshape((2,6))
reshaped_x 
array([[ 0,  1,  2,  3,  4,  5],
       [ 6,  7, -6, -5, -4, -3]])
reshaped_y
y = np.array([[0, 1, 2, 3],
              [4, 5, 6, 7],
              [-6, -5, -4, -3]
             ])
​
reshaped_y = y.reshape((2,6), order="F")
reshaped_y
array([[ 0, -6,  5,  2, -4,  7])
```
So the F denotes column based reordering. So it iterates trhough the columns and then 

### Squeezing an array 
Use `x.squeeze` to remove a dimension of 1 within an array. 
So for example 
```python
>>> x = np.array([[[1, 2]], [[3, 4]], [[5, 6]]])
>>> print(x.shape)
(3, 1, 2)
>>> print(x) # Note all those extra brackets!
[[[1 2]]

 [[3 4]]

 [[5 6]]]
>>> simpler_x = x.squeeze()
>>> assert np.all(simpler_x == np.array([[1, 2],
...                               [3, 4],
...                               [5, 6]])))
>>> assert simpler_x.shape == (3, 2)
```

### Adding a new axis 
Use `np.newaxis` which is an alias to None 
```python
>>> expanded_y = y[:, np.newaxis, :]
>>> print(expanded_y.shape)
(10, 1, 5)
>>> z = x + expanded_y

>>> expanded_y = y[:, None, :]
>>> print(expanded_y.shape)
(10, 1, 5)
>>> z = x + expanded_y
```

# Array Manipulation
### Array Stacking
Use `np.stack` to merge two vectors. 
```python
>>> x = np.array([1, 2, 3])
>>> y = np.array([4, 5, 6])
>>> z = np.stack((x, y), axis=0)  # axis=0 is the default
>>> print(z) 
[[1 2 3]
 [4 5 6]]
>>> z = np.stack((x, y), axis=1) 
>>> print(z) 
[[1 4]
 [2 5]
 [3 6]]
```

### Array Concatenation
Use `np.concatenate` to merge two arrays 
```python
>>> x = np.array([[1, 2], [3, 4]])
>>> y = np.array([[5, 6]])
>>> z = np.concatenate((x, y), axis=0)  # axis=0 is the default
[[1 2]
 [3 4]
 [5 6]]
>>> z = np.concatenate((x, y.transpose()), axis=1)
[[1 2 5]
 [3 4 6]]
```

### Array Split 
Use `np.split`
```python
>>> x = np.arange(1, 19).reshape((2, 9))
>>> print(x)
[[ 1  2  3  4  5  6  7  8  9]
 [10 11 12 13 14 15 16 17 18]]
>>> y = np.split(x, 3, axis=1) # split on axis 1 into 3 evenly-sized sub-arrays
>>> print(y[0])
[[ 1  2  3]
 [10 11 12]]
>>> print(y[1])
[[ 4  5  6]
 [13 14 15]]
>>> print(y[2])
[[ 7  8  9]
 [16 17 18]]
>>> y = np.split(x, 2, axis=0) # split on axis 0 into 2 evenly-sized sub-arrays
>>> print(y[0])
[[1 2 3 4 5 6 7 8 9]]
>>> print(y[1])
[[10 11 12 13 14 15 16 17 18]]
>>> y = np.split(x, 4, axis=1) # attempt to split on axis 1 into 4 evenly-sized sub-arrays
ValueError: array split does not result in an equal division
```

We can even specify where to split 
```python
>>> y = np.split(x, [3, 5], axis=1) # split at columns 3 and 5. 
>>> print(y[0])
[[ 1  2  3]
 [10 11 12]]
>>> print(y[1])
[[ 4  5]
 [13 14]]
>>> print(y[2])
[[ 6  7  8  9]
 [15 16 17 18]]
```

Special functions that split at particular axis
- `np.vsplit(arr, section)`: same as `np.split(arr, section, axis=0)` [Vertical split]
- `np.hsplit(arr, section)`: same as `np.split(arr, section, axis=1)` [Horizontal split]
- `np.dsplit(arr, section)`: same as `np.split(arr, section, axis=2)` [Depth split]

### Repeat Array 

Use `np.repeat` or `np.tile` for two different ways of repeating. *There is a difference*

```python
>>> print(np.repeat(0, 2))
[0 0]
>>> x = np.array([[1, 2],[3, 4]])
>>> print(x)
[[1 2]
 [3 4]]
>>> print(np.repeat(x, 2)) # Use flattened array if you do not specify the axis
[1 1 2 2 3 3 4 4]
>>> print(np.repeat(x, 2, axis=0))
[[1 2]
 [1 2]
 [3 4]
 [3 4]]
>>> print(np.repeat(x, 2, axis=1))
[[1 1 2 2]
 [3 3 4 4]]
```

So now using `np.tile` we get 

```python
>>> x = np.array([[1, 2],[3, 4]])
>>> print(x)
[[1 2]
 [3 4]]
>>> print(np.tile(x, 2))
[[1 2 1 2]
 [3 4 3 4]]
>>> print(np.tile(x, (3, 2)))
[[1 2 1 2]
 [3 4 3 4]
 [1 2 1 2]
 [3 4 3 4]
 [1 2 1 2]
 [3 4 3 4]]
```

