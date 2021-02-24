---
title: LeetCode
typora-root-url: ..
---

## Two Sum

  - Brute Force: O(n^2) | O(1)

  ```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        for i in range(len(nums)):
            for j in range(i+1, len(nums)):
                if nums[i] + nums[j] == target:
                    return [i, j]
  ```

  - Two-Pass Hash Table: O(n) | O(n)

    We reduce the look up time from O(n)*O*(*n*) to O(1)*O*(1) by trading space for speed. A hash table is built exactly for this purpose, it supports fast look up in *near* constant time. I say "near" because if a collision occurred, a look up could degenerate to O(n)*O*(*n*) time. But look up in hash table should be amortized O(1)*O*(1) time as long as the hash function was chosen carefully.

    >https://www.baeldung.com/java-hashmap-advanced#:~:text=Collisions%20in%20the%20HashMap,bucket%20location%20or%20array%20index.
    >
    >**Keep in mind that it's the hash value of the key that determines the bucket the object will be stored in. And so, if the hash codes of any two keys collide, their entries will still be stored in the same bucket.**
    >
    >And by default, the implementation uses a linked list as the bucket implementation.
    >
    >**The initially constant time *O(1)* *put* and *get* operations will occur in linear time *O(n)* in the case of a collision.** This is because after finding the bucket location with the final hash value, **each of the keys at this location will be compared with the provided key object using the *equals* API**.

  ```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:

        table = {num: i for i, num in enumerate(nums)}

        for i, num in enumerate(nums):
            try:
                j = table[target-num]
                if j != i:
                    return [i, j]
                except KeyError:
                    pass

class Solution:
	def twoSum(self, nums: List[int], target: int) -> List[int]:

		for i, num in enumerate(nums):
			try:
				j = nums.index(target-num)  # searching? O(n) or using hash table? O(1)
				if j != i:
					return [i, j]
			except ValueError:
				pass
  ```

  - One-Pass Hash Table: O(n) | O(n)

    ```python
    class Solution:
        def twoSum(self, nums: List[int], target: int) -> List[int]:

            table = {}
    
            for i, num in enumerate(nums):
                try:
                    j = table[target-num]
                    if j != i:
                        return [i, j]
                except KeyError:
                    pass
                
                table[num] = i
                
    ```
    
    

## Non-Divisible Subset

https://www.hackerrank.com/challenges/non-divisible-subset/problem

Given a set of distinct integers, print the size of a maximal subset S of where the sum of 2 any numbers in S' is *not* evenly divisible by k.

![image-20210109155142656](/assets/images/image-20210109155142656.png)

### count table of remainders

O(n+k)

```python
def nonDivisibleSubset(k, s):
    """O(n+k)"""
    cnt = [0] * k
    for x in a:
        cnt[x % k] += 1
        
    ans = min(cnt[0], 1)
    for rem in range(1, (k + 1) // 2):
        ans += max(cnt[rem], cnt[k - rem])
    if k % 2 == 0:
        ans += min(cnt[k // 2], 1)
        
    return ans

n = 5
k = 4
s = [1,2,3,4,5,6,7,8]
result = f(k, s)
print(result)
```

### my solution (not working)

```python
#!/bin/python3

import math
import os
import random
import re
import sys
from itertools import combinations
#
# Complete the 'nonDivisibleSubset' function below.
#
# The function is expected to return an INTEGER.
# The function accepts following parameters:
#  1. INTEGER k
#  2. INTEGER_ARRAY s
#

def nonDivisibleSubset(k, s):
    
    # Write your code here
    table = {}
    for i in range(len(s)):
        for j in range(i+1, len(s)):
            table[(s[i], s[j])] = (s[i] + s[j]) % k != 0
    
    def combine(s):
        result = []
        for n in range(1, len(s)+1):
            result.extend(list(combinations(s, n)))
        return result
    a = permute(s)
        
    results = []
    for temp_arr in a:
        result = set()
        for i in range(len(temp_arr)):
            for j in range(i+1, len(temp_arr)):
                if table[(temp_arr[i], temp_arr[j])]:
                    result.add(temp_arr[i])
                    result.add(temp_arr[j])
        results.append(result)
        
    max_ = 0
    for result in results:
        if len(result) > max_:
            max_ = len(result)
            
    return max_
    
    

if __name__ == '__main__':
    fptr = open(os.environ['OUTPUT_PATH'], 'w')

    first_multiple_input = input().rstrip().split()

    n = int(first_multiple_input[0])

    k = int(first_multiple_input[1])

    s = list(map(int, input().rstrip().split()))

    result = nonDivisibleSubset(k, s)

    fptr.write(str(result) + '\n')

    fptr.close()

```



## Permutation/Combination

```python
# itertools.combinations
# https://stackoverflow.com/questions/41694722/algorithm-for-itertools-combinations-in-python
def combinations(iterable, r):
    # combinations('ABCD', 2) --> AB AC AD BC BD CD
    # combinations(range(4), 3) --> 012 013 023 123
    pool = tuple(iterable)
    n = len(pool)
    if r > n:
        return
    
    indices = list(range(r))  # indices to generate the result
    
    yield tuple(pool[i] for i in indices)  # first result
    
    while True:
        
        # decide the main index slot (from the last to the first)
        for i in reversed(range(r)):
            # move the index left-wise if reaching the end, 
            # e.g, for [0, 1, 2, 3, 4, 5, 6, 7], n = 8, r = 3, n - r = 5
		   # (0, 3, 5) => i = 2  (5-2 != n-r)
        	# (3, 6, 7) => i = 0  (6-1 = 7-2 = n-r)
            # (5, 6, 7) => return  (5-0 = 6-1 = 7-2 = n-r)
            if indices[i] != i + n - r:
                break
        else:
            return
        
        # i: the main index slot
        
        # increment the index at the slot
        indices[i] += 1
        
        # reset all the indices that are to the right of i
        for j in range(i+1, r):
            indices[j] = indices[j-1] + 1
            
        # generate the result from indices
        yield tuple(pool[i] for i in indices)
        
print(list(combinations('ABCDE', 2)))
```

## Huffman Decoding

https://www.hackerrank.com/challenges/tree-huffman-decoding/problem

## Loop

```python
import queue as Queue

cntr = 0

class Node:
    def __init__(self, freq, data):
        self.freq = freq
        self.data = data
        self.left = None
        self.right = None
        global cntr
        self._count = cntr
        cntr = cntr + 1
        
    def __lt__(self, other):
        if self.freq != other.freq:
            return self.freq < other.freq
        return self._count < other._count

def huffman_hidden():#builds the tree and returns root
    q = Queue.PriorityQueue()

    
    for key in freq:
        q.put((freq[key], key, Node(freq[key], key) ))
    
    while q.qsize() != 1:
        a = q.get()
        b = q.get()
        obj = Node(a[0] + b[0], '\0' )
        obj.left = a[2]
        obj.right = b[2]
        q.put((obj.freq, obj.data, obj ))
        
    root = q.get()
    root = root[2]#contains root object
    return root

def dfs_hidden(obj, already):
    if(obj == None):
        return
    elif(obj.data != '\0'):
        code_hidden[obj.data] = already
        
    dfs_hidden(obj.right, already + "1")
    dfs_hidden(obj.left, already + "0")

"""class Node:
    def __init__(self, freq,data):
        self.freq= freq
        self.data=data
        self.left = None
        self.right = None
"""        
```
```python
# Enter your code here. Read input from STDIN. Print output to STDOUT
def decodeHuff(root, s):    
    #Enter Your Code Here    
    result = ''
    node = root
    while True:
              
        if not node.left and not node.right:
            result += node.data
            node = root
        
        if len(s) == 0:
            break
            
        node = node.left if s[0] == '0' else node.right
        s = s[1:]
    print(result)
        
ip = input()
```

or simpler

```python
# Enter your code here. Read input from STDIN. Print output to STDOUT
def decodeHuff(root, s):    
	#Enter Your Code Here    
    result = ''
    node = root
    for c in s:
        node = node.left if c == '0' else node.right

        if not node.left and not node.right:
            result += node.data
            node = root
        
    print(result)
```



## Recursion (Mine)

```python
# Enter your code here. Read input from STDIN. Print output to STDOUT
def decodeHuff(root, s):    
    #Enter Your Code Here
    def decodeNode(node, s):
        
        if not node.left and not node.right:
            return node.data + decodeNode(root, s)

        if len(s) == 0: return ''

        return decodeNode(node.left if s[0] == '0' else node.right, s[1:])
    
    result = decodeNode(root, s)
    print(result)
    return result
    

ip = input()
```

or 

```python
# Enter your code here. Read input from STDIN. Print output to STDOUT
def decodeHuff(root, s):    
    #Enter Your Code Here    
    
    def decodeNode(node, s):
        
        if len(s) == 0: return ''
        
        node = node.left if s[0] == '0' else node.right
        
        if not node.left and not node.right:
            return node.data + decodeNode(root, s[1:])
        
        return decodeNode(node, s[1:])
    
    result = decodeNode(root, s)
    print(result)
    return result
```

