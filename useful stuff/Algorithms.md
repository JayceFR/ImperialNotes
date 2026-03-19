
# Sliding Window 

Two types of sliding window problems 
**Fixed window size**
**Dynamic window size**

*Fixed Window Size*
Trivial 
```c
// set up the window 
int window = 0;
for (int i = 0; i < k; i++){
	window += nums[i];
}

// slide the window across 
for (int i = 1; i <= numsSize - k; i++){
	// update window 
	window += nums[i + k - 1] - nums[i-1];
	// do calculations 
	// ....	
}
```

So we initialise the window with the sum most of the time. And then slide it by adding the `i + k - 1` term and subtracting `i - 1` term 

Practice problem: Leetcode `643`
