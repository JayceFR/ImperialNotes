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
