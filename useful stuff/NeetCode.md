Problems i couldn't solve efficiently first tryyyyy

# Partition Equal Subset Sum

You are given an array of positive integers `nums`.

Return `true` if you can partition the array into two subsets, `subset1` and `subset2` where `sum(subset1) == sum(subset2)`. Otherwise, return `false`.

**Example 1:**

```java
Input: nums = [1,2,3,4]

Output: true
```

Explanation: The array can be partitioned as `[1, 4]` and `[2, 3]`.

**Ans**
Was a recursion problem you can draw a tree with either chosing one of the value to inlcude for sum or not...
Could use a bottom up approach as well 
Where we store all the possible sums inside a set, so for the above example we would have. Start from back 
{0, 4}
{0, 4, 3, 7}
{0, 4, 3, 7, 2, 6, 5, 9} 
.... 
and search for the target (1 + 2 + 3 + 4) / 2 = 5. When it occurs we know we found a target... Too SMART!!


# Container With Most Water

You are given an integer array `heights` where `heights[i]` represents the height of the ithith bar.

You may choose any two bars to form a container. Return the _maximum_ amount of water a container can store.

**Example 1:**

![](https://imagedelivery.net/CLfkmk9Wzy8_9HRyug4EVA/77f004c6-e773-4e63-7b99-a2309303c700/public)

```java
Input: height = [1,7,2,5,4,7,3,6]

Output: 36
```

**Example 2:**

```java
Input: height = [2,2,2]

Output: 4
```

**Ans**
use 2 pointer with first pointer at the left end and right pointer at the right end 
then just move the pointer which has the lowest height 
loop case
while(lp < rp)
TOO SMART !

# Longest Repeating Character Replacement 
You are given a string `s` consisting of only uppercase english characters and an integer `k`. You can choose up to `k` characters of the string and replace them with any other uppercase English character.

After performing at most `k` replacements, return the length of the longest substring which contains only one distinct character.

**Example 1:**

```java
Input: s = "XYYX", k = 2

Output: 4
```

Explanation: Either replace the 'X's with 'Y's, or replace the 'Y's with 'X's.

**Example 2:**

```java
Input: s = "AAABABB", k = 1

Output: 5
```

**Solution**
First check what condition needs to be met in each substring (window) for the condition to be true. 
it is `length - maxFrequency <= k`
now we just use sliding window to maximise, like keep the fp = 0, rp = 0 and move rp along the string. When the condition fails move the fp till the condiiton becomes true. 

*Note:* Now the problem is calculating this maxFrequency every time, but here is the smart bit, it's ok to overestimate! The question is asking for the longest string obviously so such a string can only occur when the maxFrequency is greater than the maximum Frequency of the entire thing. 

```python
class Solution:
    def characterReplacement(self, s: str, k: int) -> int:
        fp = 0
        rp = 0
        occur = {}
        maxOccur = 0
        maxLen = 0
        while rp < len(s):
            if occur.get(s[rp]) is not None:
                occur[s[rp]] += 1
            else:
                occur[s[rp]] = 1
            if occur[s[rp]] > maxOccur:
                maxOccur = occur[s[rp]]
                maxChar  = s[rp]
            # check if valid
            length = rp - fp + 1
            # move fp till it becomes valid
            while length - maxOccur > k:
                occur[s[fp]] -= 1
                fp += 1
                length = rp - fp + 1
            maxLen = max(maxLen, length)
            rp += 1
        return maxLen
```

# Sliding Window Maximum 
You are given an array of integers `nums` and an integer `k`. There is a sliding window of size `k` that starts at the left edge of the array. The window slides one position to the right until it reaches the right edge of the array.

Return a list that contains the maximum element in the window at each step.

**Example 1:**

```java
Input: nums = [1,2,1,0,4,2,6], k = 3

Output: [2,2,4,4,6]

Explanation: 
Window position            Max
---------------           -----
[1  2  1] 0  4  2  6        2
 1 [2  1  0] 4  2  6        2
 1  2 [1  0  4] 2  6        4
 1  2  1 [0  4  2] 6        4
 1  2  1  0 [4  2  6]       6
```

**Constraints:**

- `1 <= nums.length <= 1000`
- `-1000 <= nums[i] <= 1000`
- `1 <= k <= nums.length`

**Soluton :**
Use a heap, heapq by default gives u a min heap 
```python
import heapq as h
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        heap = []
        result = []
        for i in range(len(nums)):
            h.heappush(heap, (-nums[i], i))
            if i >= k -1:
                while heap[0][1] <= i - k:
                    h.heappop(heap)
                result.append(-heap[0][0])
        h.heappop(heap)
        return result
```

# Daily Temperature 
You are given an array of integers `temperatures` where `temperatures[i]` represents the daily temperatures on the `ith` day.

Return an array `result` where `result[i]` is the number of days after the `ith` day before a warmer temperature appears on a future day. If there is no day in the future where a warmer temperature will appear for the `ith` day, set `result[i]` to `0` instead.

**Example 1:**

```java
Input: temperatures = [30,38,30,36,35,40,28]

Output: [1,4,1,2,1,0,0]
```

**Example 2:**

```java
Input: temperatures = [22,21,20]

Output: [0,0,0]
```

**Constraints:**

- `1 <= temperatures.length <= 1000`.
- `1 <= temperatures[i] <= 100`

**Solution :**

Use a decreasing stack to help u... Its too smart 

```python
def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        result = [0 for x in range(len(temperatures))]
        stack = []
        for pos, t in enumerate(temperatures):
            while stack and t > stack[-1][0]:
                n, i = stack.pop()
                result[i] = pos - i
            stack.append((t,pos))
        return result
```

# Car Fleet
*Note:* I should have solved this, was sooo closseeee. Didn't think about just looking at it from backwards instead of solving it from the front. 

There are `n` cars traveling to the same destination on a one-lane highway.

You are given two arrays of integers `position` and `speed`, both of length `n`.

- `position[i]` is the position of the `ith car` (in miles)
- `speed[i]` is the speed of the `ith` car (in miles per hour)

The **destination** is at position `target` miles.

A car can **not** pass another car ahead of it. It can only catch up to another car and then drive at the same speed as the car ahead of it.

A **car fleet** is a non-empty set of cars driving at the same position and same speed. A single car is also considered a car fleet.

If a car catches up to a car fleet the moment the fleet reaches the destination, then the car is considered to be part of the fleet.

Return the number of **different car fleets** that will arrive at the destination.

**Example 1:**

```java
Input: target = 10, position = [1,4], speed = [3,2]

Output: 1
```

Explanation: The cars starting at 1 (speed 3) and 4 (speed 2) become a fleet, meeting each other at 10, the destination.

**Example 2:**

```java
Input: target = 10, position = [4,1,0,7], speed = [2,2,1,1]

Output: 3
```

Explanation: The cars starting at 4 and 7 become a fleet at position 10. The cars starting at 1 and 0 never catch up to the car ahead of them. Thus, there are 3 car fleets that will arrive at the destination.

**Solution**
If the before car reaches the destination at time <= the next car, we know they were a fleet. Sort in reverse order and iterate forwards. 
```python
def carFleet(self, target: int, position: List[int], speed: List[int]) -> int:
	if len(position) <= 1:
		return len(position)
	# (pos, time)
	cars = []
	for x in range(len(position)):
		cars.append((position[x], speed[x]))
	cars.sort(reverse = True)
	rp = 1
	timeToTarget = (target - cars[0][0]) / cars[0][1]
	while rp != len(cars):
		if (target - cars[rp][0]) / cars[rp][1] <= timeToTarget:
			cars.pop(rp)
		else:
			timeToTarget = (target - cars[rp][0]) / cars[rp][1]
			rp += 1
	return len(cars)
```


# Linked List cycle detection 

Given the beginning of a linked list `head`, return `true` if there is a cycle in the linked list. Otherwise, return `false`.

There is a cycle in a linked list if at least one node in the list can be visited again by following the `next` pointer.

Internally, `index` determines the index of the beginning of the cycle, if it exists. The tail node of the list will set it's `next` pointer to the `index-th` node. If `index = -1`, then the tail node points to `null` and no cycle exists.

**Note:** `index` is **not** given to you as a parameter.

**Example 1:**

![](https://imagedelivery.net/CLfkmk9Wzy8_9HRyug4EVA/3ecdbcfc-70fc-429a-4654-cf4f6a7dbe00/public)

```java
Input: head = [1,2,3,4], index = 1

Output: true
```

Explanation: There is a cycle in the linked list, where the tail connects to the 1st node (0-indexed).

**Example 2:**

![](https://imagedelivery.net/CLfkmk9Wzy8_9HRyug4EVA/89e6716c-9f65-46da-d7b2-f04a93269700/public)

```java
Input: head = [1,2], index = -1

Output: false
```

**Solution** : 

Well obviosly we can use our trustee old seen set 
But we can do it in O(1) 

#### Floyds hare and tortoise

```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        slow, fast = head, head

        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if slow == fast:
                return True
        return False
```

# Reorder Linked List

Solved 

You are given the head of a singly linked-list.

The positions of a linked list of `length = 7` for example, can intially be represented as:

`[0, 1, 2, 3, 4, 5, 6]`

Reorder the nodes of the linked list to be in the following order:

`[0, 6, 1, 5, 2, 4, 3]`

Notice that in the general case for a list of `length = n` the nodes are reordered to be in the following order:

`[0, n-1, 1, n-2, 2, n-3, ...]`

You may not modify the values in the list's nodes, but instead you must reorder the nodes themselves.

**Example 1:**

```java
Input: head = [2,4,6,8]

Output: [2,8,4,6]
```

**Example 2:**

```java
Input: head = [2,4,6,8,10]

Output: [2,10,4,8,6]
```

**Constraints:**

- `1 <= Length of the list <= 1000`.
- `1 <= Node.val <= 1000`

**Solution :**
The solution is a mixture of using fast and slow pointers to find the half of the linked list. Reverse the second half of the linked list. FInally merge both the lists. 

*Note:* The reverse linked list logic. Its too smartt!!!!!

```python
class Solution:
    def reorderList(self, head: Optional[ListNode]) -> None:
        slow, fast = head, head.next
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
        # our second half is from slow.next
        # reverse the second half
        prev, curr = None, slow.next
        while curr is not None:
            temp = curr.next
            curr.next = prev
            prev = curr
            curr = temp
        slow.next = prev
        # merge both the lists
        first, second = head, slow.next
        slow.next = None
        while second:
            tmp1, tmp2 = first.next, second.next
            first.next = second
            second.next = tmp1
            first, second = tmp1, tmp2
```
# Find the Duplicate Number

Solved 

You are given an array of integers `nums` containing `n + 1` integers. Each integer in `nums` is in the range `[1, n]` inclusive.

Every integer appears **exactly once**, except for one integer which appears **two or more times**. Return the integer that appears more than once.

**Example 1:**

```java
Input: nums = [1,2,3,2,2]

Output: 2
```

**Example 2:**

```java
Input: nums = [1,2,3,4,4]

Output: 4
```

Follow-up: Can you solve the problem **without** modifying the array `nums` and using O(1)O(1) extra space?

**Constraints:**

- `1 <= n <= 10000`
- `nums.length == n + 1`
- `1 <= nums[i] <= n`

**Solution :**
FLoyds hare and tortoise thing. Its too smart!!!. Watch a video for it.

```python
class Solution:
    def findDuplicate(self, nums: List[int]) -> int:
        slow, fast = 0, 0
        while True:
            slow = nums[slow]
            fast = nums[nums[fast]]
            if slow == fast:
                break
        slow1, slow2 = 0, slow
        while True:
            slow1 = nums[slow1]
            slow2 = nums[slow2]
            if slow1 == slow2:
                return slow1
```

# Construct Binary Tree from Preorder and Inorder Traversal

Solved 

You are given two integer arrays `preorder` and `inorder`.

- `preorder` is the preorder traversal of a binary tree
- `inorder` is the inorder traversal of the same tree
- Both arrays are of the same size and consist of unique values.

Rebuild the binary tree from the preorder and inorder traversals and return its root.

**Example 1:**

![](https://imagedelivery.net/CLfkmk9Wzy8_9HRyug4EVA/938c14d3-6669-47ab-924b-a1a08640f200/public)

```java
Input: preorder = [1,2,3,4], inorder = [2,1,3,4]

Output: [1,2,3,null,null,null,4]
```

**Example 2:**

```java
Input: preorder = [1], inorder = [1]

Output: [1]
```

**Constraints:**

- `1 <= inorder.length <= 1000`.
- `inorder.length == preorder.length`
- `-1000 <= preorder[i], inorder[i] <= 1000`

**solution:**
```python
class Solution:

    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder or not inorder:
            return None
        root = TreeNode(preorder[0])
        mid = inorder.index(preorder[0])
        root.left  = self.buildTree(preorder[1:mid + 1], inorder[:mid])
        root.right = self.buildTree(preorder[mid+1:], inorder[mid + 1:])
        return root
```

# Find Median From Data Stream

Solved 

The **[median](https://en.wikipedia.org/wiki/Median)** is the middle value in a sorted list of integers. For lists of _even_ length, there is no middle value, so the median is the [mean](https://en.wikipedia.org/wiki/Mean) of the two middle values.

For example:

- For `arr = [1,2,3]`, the median is `2`.
- For `arr = [1,2]`, the median is `(1 + 2) / 2 = 1.5`

Implement the MedianFinder class:

- `MedianFinder()` initializes the `MedianFinder` object.
- `void addNum(int num)` adds the integer `num` from the data stream to the data structure.
- `double findMedian()` returns the median of all elements so far.

**Example 1:**

```java
Input:
["MedianFinder", "addNum", "1", "findMedian", "addNum", "3" "findMedian", "addNum", "2", "findMedian"]

Output:
[null, null, 1.0, null, 2.0, null, 2.0]

Explanation:
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.findMedian(); // return 1.0
medianFinder.addNum(3);    // arr = [1, 3]
medianFinder.findMedian(); // return 2.0
medianFinder.addNum(2);    // arr[1, 2, 3]
medianFinder.findMedian(); // return 2.0
```

**Constraints:**

- `-100,000 <= num <= 100,000`
- `findMedian` will only be called after adding at least one integer to the data structure.

**Solution:** 
	Super smart combination of min heap and max heap 
```python
import heapq as h 
class MedianFinder:

    def __init__(self):
        self.small = []
        self.large = []

    def addNum(self, num: int) -> None:
        if self.large and num > self.large[0]:
            h.heappush(self.large, num)
        else:
            h.heappush(self.small, num * -1)
        
        if len(self.small) > len(self.large) + 1:
            val = h.heappop(self.small) * -1 
            h.heappush(self.large, val)
        if len(self.large) > len(self.small) + 1:
            val = h.heappop(self.large) * -1
            h.heappush(self.small, val)

    def findMedian(self) -> float:
        if len(self.small) > len(self.large):
            return -1 * self.small[0]
        elif len(self.large) > len(self.small):
            return self.large[0]
        else:
            return (-1 * self.small[0] + self.large[0]) / 2.0 
```

# Combination Sum

You are given an array of **distinct** integers `nums` and a target integer `target`. Your task is to return a list of all **unique combinations** of `nums` where the chosen numbers sum to `target`.

The **same** number may be chosen from `nums` an **unlimited number of times**. Two combinations are the same if the frequency of each of the chosen numbers is the same, otherwise they are different.

You may return the combinations in **any order** and the order of the numbers in each combination can be in **any order**.

**Example 1:**

```java
Input: 
nums = [2,5,6,9] 
target = 9

Output: [[2,2,5],[9]]
```

Explanation:  
2 + 2 + 5 = 9. We use 2 twice, and 5 once.  
9 = 9. We use 9 once.

**Example 2:**

```java
Input: 
nums = [3,4,5]
target = 16

Output: [[3,3,3,3,4],[3,3,5,5],[4,4,4,4],[3,4,4,5]]
```

**Example 3:**

```java
Input: 
nums = [3]
target = 5

Output: []
```

**Constraints:**

- All elements of `nums` are **distinct**.
- `1 <= nums.length <= 20`
- `2 <= nums[i] <= 30`
- `2 <= target <= 30`

**Solution** : Make sure you draw a DESCISION TREE which does not contain this duplicates. It is quite challenging !!!
```python
class Solution:

    def combinationSum(self, nums: List[int], target: int) -> List[List[int]]:
        solutions = []

        def dfs(sol, i, total):
            if (i+1 > len(nums)) or total > target:
                return
            if total == target:
                solutions.append(sol.copy())
                return
            sol.append(nums[i])
            dfs(sol, i, total + nums[i])
            sol.pop()
            dfs(sol, i+1, total)
        dfs([], 0, 0)
        return solutions
```

# Redundant Connection

Solved 

You are given a connected **undirected graph** with `n` nodes labeled from `1` to `n`. Initially, it contained no cycles and consisted of `n-1` edges.

We have now added one additional edge to the graph. The edge has two **different** vertices chosen from `1` to `n`, and was not an edge that previously existed in the graph.

The graph is represented as an array `edges` of length `n` where `edges[i] = [ai, bi]` represents an edge between nodes `ai` and `bi` in the graph.

Return an edge that can be removed so that the graph is still a connected non-cyclical graph. If there are multiple answers, return the edge that appears last in the input `edges`.

**Example 1:**

![](https://imagedelivery.net/CLfkmk9Wzy8_9HRyug4EVA/1a966522-e4d9-4215-18a1-4df7d26c3700/public)

```java
Input: edges = [[1,2],[1,3],[3,4],[2,4]]

Output: [2,4]
```

**Example 2:**

![](https://imagedelivery.net/CLfkmk9Wzy8_9HRyug4EVA/5cf17b17-8758-4f0a-8829-99cea143b100/public)

```java
Input: edges = [[1,2],[1,3],[1,4],[3,4],[4,5]]

Output: [3,4]
```

**Constraints:**

- `n == edges.length`
- `3 <= n <= 100`
- `1 <= edges[i][0] < edges[i][1] <= edges.length`
- There are no repeated edges and no self-loops in the input.

**Solution**

### UNION FIND !!!

```python
class Solution:
    def findRedundantConnection(self, edges: List[List[int]]) -> List[int]:
        n = len(edges)
        pars = [x for x in range(n+1)]
        ranks = [1 for x in range(n+1)]

        def find(node):
            if node == pars[node]:
                return node
            pars[node] = pars[pars[node]]
            return find(pars[node])

        def union(n1, n2):
            p1, p2 = find(n1), find(n2)
            if p1 == p2:
                return True

            if ranks[p2] > ranks[p1]:
                pars[p1] = p2
                ranks[p2] += ranks[p1]
            else:
                pars[p2] = p1
                ranks[p1] += ranks[p2]

            return False

        for n1, n2 in edges:
            if union(n1,n2):
                return [n1, n2]
        return []
```

# House Robber II

You are given an integer array `nums` where `nums[i]` represents the amount of money the `i`th house has. The houses are arranged in a circle, i.e. the first house and the last house are neighbors.

You are planning to rob money from the houses, but you cannot rob **two adjacent houses** because the security system will automatically alert the police if two adjacent houses were _both_ broken into.

Return the _maximum_ amount of money you can rob **without** alerting the police.

**Example 1:**

```java
Input: nums = [3,4,3]

Output: 4
```

Explanation: You cannot rob `nums[0] + nums[2] = 6` because `nums[0]` and `nums[2]` are adjacent houses. The maximum you can rob is `nums[1] = 4`.

**Example 2:**

```java
Input: nums = [2,9,8,3,6]

Output: 15
```

Explanation: You cannot rob `nums[0] + nums[2] + nums[4] = 16` because `nums[0]` and `nums[4]` are adjacent houses. The maximum you can rob is `nums[1] + nums[4] = 15`.

**Constraints:**

- `1 <= nums.length <= 100`
- `0 <= nums[i] <= 100`

**Solution**

House Robber 1 but do it two times, soo smart 

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        return max(nums[0], self.helper(nums[1::]), self.helper(nums[:-1]))
        
    def helper(self, nums):
        rob1, rob2 = 0, 0
        for num in nums:
            newRob = max(rob1 + num, rob2)
            rob1 = rob2
            rob2 = newRob
        return rob2
```
# Longest Palindromic Substring

Solved 

Given a string `s`, return the longest substring of `s` that is a _palindrome_.
A **palindrome** is a string that reads the same forward and backward.
If there are multiple palindromic substrings that have the same length, return any one of them.
**Example 1:**
```java
Input: s = "ababd"

Output: "bab"
```

Explanation: Both "aba" and "bab" are valid answers.
**Example 2:**

```java
Input: s = "abbc"

Output: "bb"
```

**Constraints:**

- `1 <= s.length <= 1000`
- `s` contains only digits and English letters.

**Solution**

Just expand and keep checking
Remember for odd length you would expand(i,i)
For even length you would expand (i, i+1) 

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        def expand(i, j):
            left = i
            right = j     

            while left >= 0 and right < len(s) and s[left] == s[right]:
                left -= 1
                right += 1

            return right - left - 1

        ans = [0, 0]

        for i in range(len(s)):
            odd_length = expand(i, i)
            if odd_length > ans[1] - ans[0] + 1:
                dist = odd_length // 2
                ans = [i - dist, i + dist]

            even_length = expand(i, i + 1)
            if even_length > ans[1] - ans[0] + 1:
                dist = (even_length // 2) - 1
                ans = [i - dist, i + 1 + dist]

        i, j = ans
        return s[i : j + 1]    
```

# Minimum Limit of Balls in a Bag

Medium

Topics

![premium lock icon](https://leetcode.com/_next/static/images/lock-a6627e2c7fa0ce8bc117c109fb4e567d.svg)Companies

Hint

You are given an integer array `nums` where the `ith` bag contains `nums[i]` balls. You are also given an integer `maxOperations`.

You can perform the following operation at most `maxOperations` times:

- Take any bag of balls and divide it into two new bags with a **positive** number of balls.
    - For example, a bag of `5` balls can become two new bags of `1` and `4` balls, or two new bags of `2` and `3` balls.

Your penalty is the **maximum** number of balls in a bag. You want to **minimize** your penalty after the operations.

Return _the minimum possible penalty after performing the operations_.

**Example 1:**

**Input:** nums = [9], maxOperations = 2
**Output:** 3
**Explanation:** 
- Divide the bag with 9 balls into two bags of sizes 6 and 3. [**9**] -> [6,3].
- Divide the bag with 6 balls into two bags of sizes 3 and 3. [**6**,3] -> [3,3,3].
The bag with the most number of balls has 3 balls, so your penalty is 3 and you should return 3.

**Example 2:**

**Input:** nums = [2,4,8,2], maxOperations = 4
**Output:** 2
**Explanation:**
- Divide the bag with 8 balls into two bags of sizes 4 and 4. [2,4,**8**,2] -> [2,4,4,4,2].
- Divide the bag with 4 balls into two bags of sizes 2 and 2. [2,**4**,4,4,2] -> [2,2,2,4,4,2].
- Divide the bag with 4 balls into two bags of sizes 2 and 2. [2,2,2,**4**,4,2] -> [2,2,2,2,2,4,2].
- Divide the bag with 4 balls into two bags of sizes 2 and 2. [2,2,2,2,2,**4**,2] -> [2,2,2,2,2,2,2,2].
The bag with the most number of balls has 2 balls, so your penalty is 2, and you should return 2.

**Solution:**
Incredible MATHS !!

```python
class Solution:
    def minimumSize(self, nums: List[int], maxOperations: int) -> int:
        def canDivide(threshold):
            ops = 0
            for n in nums:
                ops += ceil(n / threshold) - 1
                if ops > maxOperations:
                    return False
            return True
        l, r = 1, max(nums)
        ans = 0
        while l <= r:
            m = (l + r) // 2
            if canDivide(m):
                r = m - 1
                ans = m
            else:
                l = m + 1
        return ans
```

# Two City Scheduling

Solved

Medium

Topics

![premium lock icon](https://leetcode.com/_next/static/images/lock-a6627e2c7fa0ce8bc117c109fb4e567d.svg)Companies

A company is planning to interview `2n` people. Given the array `costs` where `costs[i] = [aCosti, bCosti]`, the cost of flying the `ith` person to city `a` is `aCosti`, and the cost of flying the `ith` person to city `b` is `bCosti`.

Return _the minimum cost to fly every person to a city_ such that exactly `n` people arrive in each city.

**Example 1:**

**Input:** costs = [[10,20],[30,200],[400,50],[30,20]]
**Output:** 110
**Explanation:** 
The first person goes to city A for a cost of 10.
The second person goes to city A for a cost of 30.
The third person goes to city B for a cost of 50.
The fourth person goes to city B for a cost of 20.

The total minimum cost is 10 + 30 + 50 + 20 = 110 to have half the people interviewing in each city.

**Example 2:**

**Input:** costs = [[259,770],[448,54],[926,667],[184,139],[840,118],[577,469]]
**Output:** 1859

**Example 3:**

**Input:** costs = [[515,563],[451,713],[537,709],[343,819],[855,779],[457,60],[650,359],[631,42]]
**Output:** 3086

**Solution:**
GREEDY. 
Instead of doing dynamic programming you could just compute how much more expensive is it to go to B rather than A. So just compute B - A. Then just take the first half of them and fly them over to B and the rest to A. 
SO SMART!!

```python
class Solution:
    def twoCitySchedCost(self, costs: List[List[int]]) -> int:
        diffs = []
        for c1, c2 in costs:
            diffs.append([c2-c1, c1, c2])
        diffs.sort()
        res = 0
        for i in range(len(diffs)):
            if i < len(diffs) // 2:
                res += diffs[i][2]
            else:
                res += diffs[i][1]
        return res
```

# Majority Element

EasyTopicsCompany Tags

Given an array `nums` of size `n`, return the **majority element**.

The majority element is the element that appears more than `⌊n / 2⌋` times in the array. You may assume that the majority element always exists in the array.

**Example 1:**

```java
Input: nums = [5,5,1,1,1,5,5]

Output: 5
```

**Example 2:**

```java
Input: nums = [2,2,2]

Output: 2
```

**Constraints:**

- `1 <= nums.length <= 50,000`
- `-1,000,000,000 <= nums[i] <= 1,000,000,000`

**Follow-up:** Could you solve the problem in linear time and in `O(1)` space?

**Solution**:
We keep only a counter and res, **Note** This only works because of the problem statement, where they told that there is always a majority element present in the array. 

# Best Time to Buy and Sell Stock II

MediumTopicsCompany Tags

You are given an integer array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

On each day, you may decide to buy and/or sell the stock. However, you can buy it then immediately sell it on the **same day**. Also, you are allowed to perform any number of transactions but can hold **at most one** share of the stock at any time.

Find and return the **maximum** profit you can achieve.

**Example 1:**

```java
Input: prices = [7,1,5,3,6,4]

Output: 7
```

Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4. Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3. Total profit is 4 + 3 = 7.

**Example 2:**

```java
Input: prices = [1,2,3,4,5]

Output: 4
```

Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4. Total profit is 4.

**Constraints:**

- `1 <= prices.length <= 30,000`
- `0 <= prices[i] <= 10,000`
*Solution*
Its a greedy problem, we can bascially bug and sell for every peak. So buy when low and immediately sell when its high. Stupid problem. 
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        profit = 0 
        for i in range(1, len(prices)):
            if prices[i] > prices[i-1]:
                profit += (prices[i] - prices[i-1])
        return profit 
```
So less lines of code for a seemingly hard problem. 


# **Concept of removing duplicates**
There is 2 ways of removing duplicates in general. 
1. One way is to sort and make sure the previous entry is not the same as the current entry. 
2. The other more interesting way which i encountered for this problem called *Count hashmap*
### Permutations II

MediumTopicsCompany Tags

You are given an array `nums`, that might contain duplicates , return all possible unique permutations in **any order**.

**Example 1:**

```java
Input: nums = [1,1,2]

Output: [
    [1,1,2],
    [1,2,1],
    [2,1,1]
]
```

**Example 2:**

```java
Input: nums= [2,2]

Output: [[2,2]]
```

The way i solved it was using `1`
```python
class Solution:
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        
        nums.sort()
        res = []
        def backtrack(numbers : List[int], perm: List[int]):
            if len(numbers) == 0:
                res.append(perm.copy())
                return 
            
            curr = None
            for i in range(len(numbers)):
                if curr is not None and curr == numbers[i]:
                    # skip, to avoid duplicates
                    continue
                curr = numbers[i]
                perm.append(curr)
                copy = numbers.copy()
                copy.remove(curr)
                backtrack(copy, perm)
                perm.pop()
        backtrack(nums, [])
        return res 
```
But as you can see the copy in the middle is quite annoying. 
Instead using Neetcode's second approach of storing things in a *Count Hashmap*
![[Pasted image 20260911140030.png]]
Now the code is much more simpler 
![[Pasted image 20260911140102.png]]
Ok cool. 

# Matchsticks to Square

MediumTopicsCompany Tags

You are given an integer array `matchsticks` where `matchsticks[i]` is the length of the `ith` matchstick. You need to use all the matchsticks to make one square. You should **not break** any stick, but you can link them up, and each matchstick must be used exactly one time.

Return `true` if you can make this square and `false` otherwise.

**Example 1:**

```java
Input: matchsticks = [1,3,4,2,2,4]

Output: true
```

  

**Example 2:**

```java
Input: matchsticks = [1,5,6,3]

Output: false
```
```python
class Solution:
    def makesquare(self, matchsticks: List[int]) -> bool:
        square = [0, 0, 0, 0] # T R B L 

        permiter = sum(matchsticks)
        if (permiter // 4) * 4 != permiter:
            return False 

        size = permiter // 4 

        matchsticks.sort(reverse=True)

        print("size", size)
        def isValidSq(square):
            for s in square:
                if s != size:
                    return False  
            return True 

        def backtrack(pos: int, square: List[int]) -> bool:
            if pos == len(matchsticks):
                return isValidSq(square)
            m = matchsticks[pos]
            for p in range(len(square)):
                if square[p] + m <= size:
                    # add it to that side
                    square[p] += m 
                    if backtrack(pos + 1, square):
                        return True 
                    square[p] -= m 
            
            return False 

        return backtrack(0, square)
```

# Partition to K Equal Sum Subsets

MediumTopicsCompany Tags

You are given an integer array `nums` and an integer `k`, return `true` if it is possible to divide this array into `k` **non-empty** subsets whose sums are all equal.

**Example 1:**

```java
Input: nums = [2,4,1,3,5], k = 3

Output: true
```

Explanation: Given array can be divided into three subsets [5], [2,3], [4,1].

**Example 2:**

```java
Input: nums = [1,2,3,4], k = 3

Output: false
```

**Solution**
Solution seems easy at the start. Simple backtracking problem, with keeping a list of k lists, and then choosing one at a time and backtracking if we dont reach a solution. 
```python
class Solution:
    def canPartitionKSubsets(self, nums: List[int], k: int) -> bool:
        partitions = [[] for _ in range(k)]

        def validPartition(partitions: List[List[int]]) -> bool:
            for x in range(1, len(partitions)):
                if sum(partitions[x-1]) != sum(partitions[x]):
                    return False 
            return True 

        def backtrack(pos, partitions: List[List[int]]) -> bool:
            if pos == len(nums):
                return validPartition(partitions)
            curr = nums[pos]
            for x in range(k):
                partitions[x].append(curr)
                if backtrack(pos+1, partitions):
                    return True 
                partitions[x].pop()
            return False 

        return backtrack(0, partitions)
```
The problem with this approach is its time complexity its basically going to be a tree, with the height being `n` and each node we do `k` decisions, giving a time complexity of `k^n`. 
![[Pasted image 20260912131629.png]]
And in the constraints sections they give `n,k <= 16`. So 16^16 is huge. 
Instead neetcode suggests another smarter algorithm where at each node we are bounded by only 2 decisions at max. Hence reducing the time complexity to `k*2^n`. Which is significantly smaller than the predecessor. 
Basically the solution is we first calculate the target, so the sum of the elements in nums / k. And then we go through each element and just make the decision of including in the subset or not, to make one perfect subset. And then after finding one, we continue in the same tree but with the items that are not yet considered to make another tree of height n. we do this at most k times, hence the time complexity `k * 2^n`
```python
class Solution:
    def canPartitionKSubsets(self, nums: List[int], k: int) -> bool:
        if sum(nums) % k:
            return False 
        
        nums.sort(reverse=True)
        target = sum(nums) // k 
        used = [False] * len(nums)

        def backtrack(i, k, subsetSum):
            if k == 0:
                return True 
            if subsetSum == target:
                return backtrack(0, k-1, 0)
            
            for j in range(i, len(nums)):
                if used[j] or subsetSum + nums[j] > target:
                    continue
                used[j] = True 
                if backtrack(j, k, subsetSum + nums[j]):
                    return True 
                used[j] = False
            return False
        
        return backtrack(0, k, 0)

```

# Rotate Array

MediumTopicsCompany Tags

You are given an integer array `nums`, rotate the array to the right by `k` steps, where `k` is non-negative.

**Example 1:**

```java
Input: nums = [1,2,3,4,5,6,7,8], k = 4

Output: [5,6,7,8,1,2,3,4]
```

Explanation:  
rotate 1 steps to the right: [8,1,2,3,4,5,6,7]  
rotate 2 steps to the right: [7,8,1,2,3,4,5,6]  
rotate 3 steps to the right: [6,7,8,1,2,3,4,5]  
rotate 4 steps to the right: [5,6,7,8,1,2,3,4]

**Example 2:**

```java
Input: nums = [1000,2,4,-3], k = 2

Output: [4,-3,1000,2]
```

Explanation:  
rotate 1 steps to the right: [-3,1000,2,4]  
rotate 2 steps to the right: [4,-3,1000,2]

**Constraints:**

- `1 <= nums.length <= 100,000`
- `-(2^31) <= nums[i] <= ((2^31)-1)`
- `0 <= k <= 100,000`

**Follow up:** Could you do it in-place with O(1) extra space?

**Solution**
Well we would need to first reverse the entire array, and then reverse the first k elements of the array and then reverse the rest. Stupid by heart the solution type problem. 
![[Pasted image 20260915201850.png]]
Annoying problem. 
```python
class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        
        k = k % len(nums)

        def reverse(start: int, end: int):
            fp = start 
            lp = end 
            while fp < lp:
                tmp = nums[fp]
                nums[fp] = nums[lp]
                nums[lp] = tmp 
                fp += 1 
                lp -= 1 
        
        reverse(0, len(nums)-1)
        reverse(0, k-1)
        reverse(k, len(nums)-1)
```

## Odd Ocurrences in Array 
A non-empty array A consisting of N integers is given. The array contains an odd number of elements, and each element of the array can be paired with another element that has the same value, except for one element that is left unpaired. For example, in array A such that: A[0] = 9 A[1] = 3 A[2] = 9 A[3] = 3 A[4] = 9 A[5] = 7 A[6] = 9 content_copy the elements at indexes 0 and 2 have value 9, the elements at indexes 1 and 3 have value 3, the elements at indexes 4 and 6 have value 9, the element at index 5 has value 7 and is unpaired. Write a function: def solution(A) content_copy that, given an array A consisting of N integers fulfilling the above conditions, returns the value of the unpaired element. For example, given array A such that: A[0] = 9 A[1] = 3 A[2] = 9 A[3] = 3 A[4] = 9 A[5] = 7 A[6] = 9 content_copy the function should return 7, as explained in the example above. Write an efficient algorithm for the following assumptions: N is an odd integer within the range [1..1,000,000]; each element of array A is an integer within the range [1..1,000,000,000]; all but one of the values in A occur an even number of times.

**Solution**
Thanks to bending spoons we know the fact that, 
x XOR x  = `0`
using that fact we can just xor all the elements together to find the result. 
*Important:* You should know this mathematical releationship of XOR. It comes in clutch for interviews. 


[41. First Missing Positive](https://leetcode.com/problems/first-missing-positive/)

Solved

Hard

Topics

![premium lock icon](https://leetcode.com/_next/static/images/lock-a6627e2c7fa0ce8bc117c109fb4e567d.svg)Companies

Hint

Given an unsorted integer array `nums`. Return the _smallest positive integer_ that is _not present_ in `nums`.

You must implement an algorithm that runs in `O(n)` time and uses `O(1)` auxiliary space.

**Example 1:**

**Input:** nums = [1,2,0]
**Output:** 3
**Explanation:** The numbers in the range [1,2] are all in the array.

**Example 2:**

**Input:** nums = [3,4,-1,1]
**Output:** 2
**Explanation:** 1 is in the array but 2 is missing.

**Example 3:**

**Input:** nums = [7,8,9,11,12]
**Output:** 1
**Explanation:** The smallest positive integer 1 is missing.

**Solution**
Its soo bad, we need to use the actual array as a set by using negative numebers and stuff. Its pretty shit. Here is the youtube link if you want to understand. 
[First Missing Positive - Leetcode 41 - Python - YouTube](https://www.youtube.com/watch?v=8g78yfzMlao)
# Ways to make a fair array
You are given an integer array `nums`. You can choose **exactly one** index (**0-indexed**) and remove the element. Notice that the index of the elements may change after the removal.

For example, if `nums = [6,1,7,4,1]`:

- Choosing to remove index `1` results in `nums = [6,7,4,1]`.
- Choosing to remove index `2` results in `nums = [6,1,4,1]`.
- Choosing to remove index `4` results in `nums = [6,1,7,4]`.

An array is **fair** if the sum of the odd-indexed values equals the sum of the even-indexed values.

Return the _**number** of indices that you could choose such that after the removal,_ `nums` _is **fair**._

**Example 1:**

**Input:** nums = [2,1,6,4]
**Output:** 1
**Explanation:**
Remove index 0: [1,6,4] -> Even sum: 1 + 4 = 5. Odd sum: 6. Not fair.
Remove index 1: [2,6,4] -> Even sum: 2 + 4 = 6. Odd sum: 6. Fair.
Remove index 2: [2,1,4] -> Even sum: 2 + 4 = 6. Odd sum: 1. Not fair.
Remove index 3: [2,1,6] -> Even sum: 2 + 6 = 8. Odd sum: 1. Not fair.
There is 1 index that you can remove to make nums fair.

**Example 2:**

**Input:** nums = [1,1,1]
**Output:** 3
**Explanation:** You can remove any index and the remaining array is fair.

**Example 3:**

**Input:** nums = [1,2,3]
**Output:** 0
**Explanation:** You cannot make a fair array after removing any index.

**Solution**
Need to hold prefix sum for even and odd. Need to note that because removing an element, all the items after that would change their parity from odd to even or even to odd. 
```python
class Solution:
    def waysToMakeFair(self, nums: list[int]) -> int:
        pre_even_sums = [0 for x in range(len(nums) + 1)]
        pre_odd_sums  = [0 for x in range(len(nums) + 1)]

        pre_even_sums[0] = 0 
        pre_odd_sums[0] = 0 

        for i, num in enumerate(nums):
            if i % 2 == 0:
                pre_even_sums[i+1] = pre_even_sums[i] + num 
                pre_odd_sums[i+1] = pre_odd_sums[i]
            else:
                pre_odd_sums[i+1] = pre_odd_sums[i] + num 
                pre_even_sums[i+1] = pre_even_sums[i]
        res = 0 
        total_even = pre_even_sums[-1]
        total_odd = pre_odd_sums[-1]

        for x in range(len(nums)):
            prev_even = pre_even_sums[x]
            prev_odd = pre_odd_sums[x]

            next_even = total_even - pre_even_sums[x + 1]
            next_odd = total_odd - pre_odd_sums[x + 1]

            # Everything after x changes parity
            new_even = prev_even + next_odd
            new_odd = prev_odd + next_even

            if new_even == new_odd:
                res += 1
        return res 

```

# Maximum Sum Circular Subarray

MediumTopicsCompany Tags

You are given a circular integer array `nums` of length `n`, return the maximum possible sum of a non-empty **subarray** of `nums`.

A circular array means the end of the array connects to the beginning of the array. Formally, the next element of `nums[i]` is `nums[(i + 1) % n]` and the previous element of `nums[i]` is `nums[(i - 1 + n) % n]`.

A **subarray** may only include each element of the fixed buffer `nums` at most once. Formally, for a subarray `nums[i], nums[i + 1], ..., nums[j]`, there does not exist `i <= k1, k2 <= j` with `k1 % n == k2 % n`.

**Example 1:**

```java
Input: nums = [-2,4,-5,4,-5,9,4]

Output: 15
```

Explanation: Subarray [-2,4,9,4] has maximum sum 15.

**Example 2:**

```java
Input: nums = [2,3,-4]

Output: 5
```
**Solution:**
Initially thought of modifiying Kadane by looking if we used the last element, but that breaks. Look down at the solution. 
Ohh yh Kadane is for finding Maximum Subarray problem. Basically if the currSum is negative then we just start fresh, cause there is a better maximum. 
```python
class Solution:
    def maxSubarraySumCircular(self, nums: List[int]) -> int:
        # maxSum = nums[0]
        # currSum = 0
        # cs, ce = 0, 0 
        # ms, me = 0, 0 
        # for i, val in enumerate(nums):
        #     currSum += val
        #     if currSum > maxSum:
        #         maxSum = currSum
        #         ms, me = cs, i 
        #     if currSum < 0:
        #         currSum = 0 
        #         # reset the current pointers
        #         cs = i + 1 
        #         ce = i + 1
        #     else:
        #         # update the end pointer 
        #         ce = i 

        # print(ms, me, maxSum)
        # if me == len(nums) - 1:
        #     # We have included the last item, must try to grow the sub array upto ms 
        #     tmpSum = maxSum
        #     for i in range(0, ms):
        #         tmpSum += nums[i]
        #         maxSum = max(maxSum, tmpSum)

        # Initially thought of using Kadane, and then if the subarray includes the last element, extend it. 
        # But that breaks for the input [5, -3, 5] were the max is 10, while ours would reutrn 7 
        # intended solution is soo smart!

        globMax, globMin = nums[0], nums[0]

        currMax, currMin = 0, 0
        total = 0 

        for num in nums:
            currMax = max(currMax + num, num)
            currMin = min(currMin + num, num)
            total += num 
            globMax = max(globMax, currMax)
            globMin = min(globMin, currMin)

        return max(globMax, total - globMin) if globMax > 0 else globMax 
```

# Build a Matrix With Conditions

HardTopicsCompany Tags

You are given a positive integer `k`. You are also given:

- a `2D` integer array `rowConditions` of size `n` where `rowConditions[i] = [above[i], below[i]]`, and
- a `2D` integer array `colConditions` of size `m` where `colConditions[i] = [left[i], right[i]]`.

The two arrays contain integers from `1` to `k`.

You have to build a `k x k` matrix that contains each of the numbers from `1` to `k` **exactly once**. The remaining cells should have the value `0`.

The matrix should also satisfy the following conditions:

- The number `above[i]` should appear in a **row** that is strictly **above** the row at which the number `below[i]` appears for all `i` from `0` to `n - 1`.
    
- The number `left[i]` should appear in a **column** that is strictly **left** of the column at which the number `right[i]` appears for all `i` from `0` to `m - 1`.
    

Return **any** matrix that satisfies the conditions. If no answer exists, return an empty matrix.

**Example 1:**

```java
Input: k = 3, rowConditions = [[2,1],[1,3]], colConditions = [[3,1],[2,3]]

Output: [[2,0,0],[0,0,1],[0,3,0]]
```

**Example 2:**

```java
Input: k = 3, rowConditions = [[1,2],[2,3],[3,1],[2,3]], colConditions = [[2,1]]

Output: []
```

**Solution**
We need to figure out that this is a graph problem first. And then you need to figure out that this is a topological sort problem. 
Topological sort visits, all of its previous before visiting the current node. Its basically dfs reversed. 
```python
class Solution:
    def buildMatrix(self, k: int, rowConditions: List[List[int]], colConditions: List[List[int]]) -> List[List[int]]:
        # Topological sort Graph PROBLEM! How can i come up with this, without knowing its a graph problem in 20 mins??? 

        def dfs(src, adj, visited, path, order):
            # dfs to the end and add the last element. 
            # cycle check 
            if src in path:
                return False 
            if src in visited:
                return True 

            visited.add(src)
            path.add(src)

            for nei in adj[src]:
                if not dfs(nei, adj, visited, path, order):
                    return False 

            path.remove(src)

            order.append(src)           

            return True 

        
        def topoSort(edges):
            # Build adjacency graph 

            adj = defaultdict(list)
            for src, dst in edges:
                adj[src].append(dst)

            visited = set()
            path = set()
            order = [] 
            for src in range(1, k+1):
                if src not in visited:
                    if not dfs(src, adj, visited, path, order):
                        return []
            return order[::-1]

        row_order = topoSort(rowConditions)
        if not row_order: return []
        
        col_order = topoSort(colConditions)
        if not col_order: return []

        val_to_row = {num : i for i, num in enumerate(row_order)}
        val_to_col = {col: i for i, col in enumerate(col_order)}

        res = [[0] * k for _ in range(k)]

        for num in range(1, k+1):
            r,c = val_to_row[num], val_to_col[num] 
            res[r][c] = num 
        
        return res 
```

# Last Stone Weight II

MediumTopicsCompany Tags

You are given an array of integers `stones` where `stones[i]` is the weight of the `ith` stone.

We are playing a game with the stones. On each turn, we choose any two stones and smash them together. Suppose the stones have weights `x` and `y` with `x <= y`. The result of this smash is:

- If `x == y`, both stones are destroyed, and
- If `x != y`, the stone of weight `x` is destroyed, and the stone of weight `y` has new weight `y - x`.

At the end of the game, there is **at most one** stone left.

Return the **smallest** possible weight of the left stone. If there are no stones left, return `0`.

**Example 1:**

```java
Input: stones = [2,4,1,5,6,3]

Output: 1
```

**Solution**
We need to identify in order to get minimum we need to make one set close to `target // 2` 
So it becomes a bounded knapsack problem. 
So we can do a dp, with states (i,target)
```python
class Solution:
    def lastStoneWeightII(self, stones: List[int]) -> int:
        # Didn't get this question at the start 
        # But we would basically need to form a target 
        # Which is just a knapsack problem. 
        # I am going to do it with a 2 dp bottom up. 

        # We can space optimise it as well. 
        total = sum(stones)
        target = total // 2 
        
        dp = [False] * (target + 1)
        dp[0] = True 

        for i in range(len(stones)):
            new_dp = [False] * (target + 1)
            new_dp[0] = True 
            for y in range(1, target + 1):
                new_dp[y] = dp[y] or dp[y - stones[i]] if stones[i] <= y else dp[y]
            dp = new_dp
        print(dp)
        # Now get the max one
        for y in range(target, -1, -1):
            if dp[y]:
                return abs(y - (total - y))

        return 0 

```
