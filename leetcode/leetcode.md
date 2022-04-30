2022 04 29

**Medium** 

[evaluate-division](https://leetcode.com/problems/evaluate-division/) 

```python
class Solution:
    def calcEquation(self, equations: List[List[str]], values: List[float], queries: List[List[str]]) -> List[float]:
        def dfs(start, end): 
            stack = [(start, 1)]
            visited = set() 
            while stack!=[]: 
                x, val = stack.pop()
                if x == end: 
                    return val 
                visited.add(x)
                for y in nei[x]: 
                    if y not in visited: 
                        stack.append((y, val * edges[(x,y)]))
            return False 
            
        vertices = set() 
        edges = {}
        nei = {} 
        for i in range(len(equations)):
            edges[equations[i][0], equations[i][1]] = values[i]
            edges[equations[i][1], equations[i][0]] = 1/values[i]
            vertices.add(equations[i][0])
            vertices.add(equations[i][1])
        
        for v in vertices: 
            nei[v] = [] 
        for e in equations: 
            nei[e[0]].append(e[1])
            nei[e[1]].append(e[0])
        
        ans = [] 
        for q in queries: 
            if q[0] not in vertices or q[1] not in vertices: 
                ans.append(-1)
            else: 
                res = dfs(q[0],q[1])
                if not res: 
                    ans.append(-1)
                else: 
                    ans.append(res)
        return ans
```

2022 04 27

**Medium** 

[path-with-minimum-effort](https://leetcode.com/problems/path-with-minimum-effort) 

Tried with uniderctional UCS search, got time limit exceeded. Bi-directional search passed.
```python
import heapq 

class Solution:
    def minimumEffortPath(self, heights: List[List[int]]) -> int:
        rows = len(heights)
        cols = len(heights[0]) 
        
        def get_actions(pos): 
            actions = [] 
            x, y = pos 
            # left 
            # x, y + 1 
            if y + 1 < cols: 
                actions.append ((x, y+1))
            # right 
            # x, y - 1
            if y - 1 >= 0: 
                actions.append((x, y-1))
            # up 
            # x - 1, y
            if x - 1 >= 0: 
                actions.append((x-1, y))
            # bottom
            # x + 1, y
            if x + 1 < rows: 
                actions.append((x+1, y))
            return actions         
            
            
        L1 = [(0,0,0)] 
        L2 = [(0, rows-1, cols-1)]
        heapq.heapify(L1)
        heapq.heapify(L2)
        visited1 = {} 
        visited2 = {}
        while L1 != [] or L2 != [] : 
            pos1 = heapq.heappop(L1)
            pos2 = heapq.heappop(L2)
            
            # have cost first so the heap gives the least cost 
            cost1, x1, y1 = pos1
            cost2, x2, y2 = pos2  
            
            visited1[(x1, y1)] = cost1
            visited2[(x2, y2)] = cost2
            
            if (x1, y1) in visited2: 
                return max(visited1[(x1,y1)], visited2[(x1,y1)])
            if (x2, y2) in visited1:
                return max(visited1[(x2,y2)], visited2[(x2,y2)])
                
            for a in get_actions((x1,y1)): 
                x_, y_ = a
                if not (x_, y_) in visited1:
                    cost_ = abs(heights[x_][y_] - heights[x1][y1])
                    heapq.heappush(L1, ( max(cost_, cost1), x_, y_ ) )
                    
            for a in get_actions((x2,y2)): 
                x_, y_ = a
                if not (x_, y_) in visited2:
                    cost_ = abs(heights[x_][y_] - heights[x2][y2])
                    heapq.heappush(L2, ( max(cost_, cost2), x_, y_ ) )
```

2022 04 26

**Medium**

[smallest-string-with-swaps](https://leetcode.com/problems/smallest-string-with-swaps) 

```python
class Solution:
    def smallestStringWithSwaps(self, s: str, pairs: List[List[int]]) -> str:
        # build the set of disjoint sets 
        # for each disjoint set, sort the letters 
        def root(x): 
            while id[x] != x: 
                id[x] = id[id[x]] 
                x = id[x] 
            return x 
        
        def union(x, y): 
            p = root(x) 
            q = root(y)
            id[p] = id[q]
            
        l = list(s)
        indices = set() 
        for (x, y) in pairs: 
            indices.add(x) 
            indices.add(y) 
        indices = list(indices)
        
        id = {} 
        for i in indices: 
            id[i] = i 
        
        for (i, j) in pairs: 
            if root(i) != root(j): 
                union(i,j)
        
        disj = {} 
        for i in indices:
            r = root(i)
            if not r in disj:
                disj[r] = [i] 
            else: 
                disj[r].append(i)
                
        for d in disj: 
            sub = [ s[i] for i in disj[d] ] 
            sub.sort() 
            j = 0 
            for i in disj[d]: 
                l[i] = sub[j]
                j+=1
            
        return "".join(l) 
```

2022 04 25

**Medium**

[min-cost-to-connect-all-points](https://leetcode.com/problems/min-cost-to-connect-all-points) 

Minimum Spanning Tree (MST) on a complete graph. Can be solved using Prim's or Kruskal's algorithms. 

Here is our Kruskal's solution: 

```python
class Solution:
    def minCostConnectPoints(self, points: List[List[int]]) -> int:
        if len(points) == 1: 
            return 0
        
        def root(x): 
            while id[x] != x: 
                id[x] = id[id[x]] 
                x = id[x] 
            return x 
        
        def union(x, y): 
            p = root(x) 
            q = root(y)
            id[p] = id[q]
            
        id = {} 
        for i in range(len(points)): 
            id[i] = i 
        
        distances = []
        for i in range (len(points)): 
            for j in range(i+1, len(points)):
                distances.append((i, j, \
                abs(points[i][0] - points[j][0]) + abs(points[i][1] - points[j][1]))) 

        distances.sort(key=lambda d: d[2])
        cost = 0 
        k = 0 
        for distance in distances:
            i, j, d = distance
            if root(i) != root(j): 
                union(i,j)
                cost += d
                k += 1
                if k == len(points) - 1: 
                    break
        return cost 
```

2022 04 24

**Medium**

[peeking-iterator](https://leetcode.com/problems/peeking-iterator/)

```python
# Below is the interface for Iterator, which is already defined for you.
#
# class Iterator:
#     def __init__(self, nums):
#         """
#         Initializes an iterator object to the beginning of a list.
#         :type nums: List[int]
#         """
#
#     def hasNext(self):
#         """
#         Returns true if the iteration has more elements.
#         :rtype: bool
#         """
#
#     def next(self):
#         """
#         Returns the next element in the iteration.
#         :rtype: int
#         """

class PeekingIterator:
    def __init__(self, iterator):
        """
        Initialize your data structure here.
        :type iterator: Iterator
        """
        self.iterator = iterator 
        self.x = 0 

    def peek(self):
        """
        Returns the next element in the iteration without advancing the iterator.
        :rtype: int
        """
        if self.x == 0:
            self.x = self.iterator.next()
        
        return self.x
        
    def next(self):
        """
        :rtype: int
        """
        if self.x == 0: 
            return self.iterator.next()
        else: 
            y = self.x
            self.x = 0
            return y

    def hasNext(self):
        """
        :rtype: bool
        """
        return self.x != 0 or self.iterator.hasNext()
        

# Your PeekingIterator object will be instantiated and called as such:
# iter = PeekingIterator(Iterator(nums))
# while iter.hasNext():
#     val = iter.peek()   # Get the next element but not advance the iterator.
#     iter.next()         # Should return the same value as [val].
``` 

2022 04 23 

**Medium** 

[design-underground-system](https://leetcode.com/problems/design-underground-system/)

```python
class UndergroundSystem:

    def __init__(self):
        self.avg = {} 
        self.trips = {}

    def checkIn(self, id: int, stationName: str, t: int) -> None:
        if not stationName in self.avg: 
            self.avg[stationName] = {} 
        self.trips[id] = stationName, t 

    def checkOut(self, id: int, stationName: str, t: int) -> None:
        start, t0 = self.trips[id]
        d = t - t0
        if not stationName in self.avg[start]:
            self.avg[start][stationName] = d, 1
        else: 
            d_, c = self.avg[start][stationName]
            self.avg[start][stationName] = d_ + d, c + 1

    def getAverageTime(self, startStation: str, endStation: str) -> float:
        d, c = self.avg[startStation][endStation]
        return d / c
        


# Your UndergroundSystem object will be instantiated and called as such:
# obj = UndergroundSystem()
# obj.checkIn(id,stationName,t)
# obj.checkOut(id,stationName,t)
# param_3 = obj.getAverageTime(startStation,endStation)
```

2022 04 22

**Medium** 

[encode-and-decode-tinyurl](https://leetcode.com/problems/encode-and-decode-tinyurl/) 

```python
import random 

class Codec:
    def __init__(self): 
        self.d = {} 
        self.L = list ( range (65, 91) ) + list ( range (48, 58) ) \
        + list ( range(97, 123) ) 
        
    def encode(self, longUrl: str) -> str:
        """Encodes a URL to a shortened URL.
        """
        start = True
        hash = "" 
        while hash in self.d or start: 
            start = False 
            
            hash = "".join(
                [ 
                    chr ( random.choice(self.L) ) for i in range (5) 
                ]
            )
        
        self.d[hash] = longUrl
        
        return "http://tinyurl.com/" + hash

    def decode(self, shortUrl: str) -> str:
        """Decodes a shortened URL to its original URL.
        """
        hash = shortUrl.split("/")[-1]
        print (hash)
        return self.d[hash] 
        
        

# Your Codec object will be instantiated and called as such:
# codec = Codec()
# codec.decode(codec.encode(url))
```

2022 04 21  

**Easy** 

[design-hashmap](https://leetcode.com/problems/design-hashmap/) 

```python
class MyHashMap:

    def __init__(self):
        self.p = 2069
        self.L = [[] for i in range(self.p)]

    def put(self, key: int, value: int) -> None:
        k = key % self.p
        for i in range(len(self.L[k])): 
            if self.L[k][i][0] == key: 
                self.L[k][i] = (key, value)
                return 
        self.L[k].append((key,value))

    def get(self, key: int) -> int:
        k = key % self.p
        for i in range(len(self.L[k])):
            if (self.L[k][i][0]== key):
                return self.L[k][i][1]
        return -1
        

    def remove(self, key: int) -> None:
        i = 0 
        k = key % self.p
        while i < len(self.L[k]):
            if (self.L[k][i][0] == key):
                break
            i += 1 
        self.L[k] = self.L[k][:i] + self.L[k][i+1:]


# Your MyHashMap object will be instantiated and called as such:
# obj = MyHashMap()
# obj.put(key,value)
# param_2 = obj.get(key)
# obj.remove(key)
```

2022 04 20

**Easy**

[design-hashset](https://leetcode.com/problems/design-hashset/)

```python
class MyHashSet:

    def __init__(self):
        self.L = [[] for i in range(1000)]         

    def add(self, key: int) -> None:
        if not self.contains(key): 
            self.L[key%1000].append(key)
        
    def remove(self, key: int) -> None:
        i = 0 
        while i < len(self.L[key%1000]): 
            if self.L[key%1000][i] == key:
                break
            i += 1
        self.L[key%1000] = self.L[key%1000][:i]+self.L[key%1000][i+1:]

    def contains(self, key: int) -> bool:
        return key in self.L[key%1000]
        


# Your MyHashSet object will be instantiated and called as such:
# obj = MyHashSet()
# obj.add(key)
# obj.remove(key)
# param_3 = obj.contains(key)
```

2022 04 19

**Medium**

[binary-search-tree-iterator](https://leetcode.com/problems/binary-search-tree-iterator)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class BSTIterator:
    # ptr = TreeNode(-1)
    
    def __init__(self, root: Optional[TreeNode]):
        self.stack = [] 
        ptr = root 
        while ptr != None:
            self.stack.append(ptr)
            ptr = ptr.left 
        

    def next(self) -> int:
        ptr2 =  self.stack.pop()
        if ptr2.right != None: 
            ptr = ptr2.right
            while ptr != None: 
                self.stack.append(ptr)
                ptr = ptr.left
        return ptr2.val

    def hasNext(self) -> bool:
        return self.stack != [] 
        


# Your BSTIterator object will be instantiated and called as such:
# obj = BSTIterator(root)
# param_1 = obj.next()
# param_2 = obj.hasNext()
```

2022 04 18

**Medium** 

[recover-binary-search-tree](https://leetcode.com/problems/recover-binary-search-tree) 

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def findOutofOrder(self, root, min_, max_, L):
        if root == None: 
            return 
        if root.val < min_.val: 
            L.append(root)
            L.append(min_)
        if root.val > max_.val: 
            L.append(max_)
            L.append(root)
        self.findOutofOrder(root.right, root, max_, L)
        self.findOutofOrder(root.left, min_, root, L)


    def recoverTree(self, root: Optional[TreeNode]) -> None:
        """
        Do not return anything, modify root in-place instead.
        """
        min_ = TreeNode(-2 ** 31 - 1)
        max_ = TreeNode(2 ** 31)
        L = []
        self.findOutofOrder(root, min_, max_, L)
        L.sort(key=lambda node: node.val)
        L[0].val, L[-1].val = L[-1].val, L[0].val
```

2022 04 17 

**Medium** 

[kth-smallest-element-in-a-bst](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) 

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
       
    def size(self, root, k, count, ans):
        if root == None or ans[0] != -1: 
            return 0
  
        s1 = self.size(root.left, k, count, ans)
        count += s1 
        
        count += 1 
        if count == k and ans[0] == -1: 
            ans[0] = root.val 
        
        s2 = self.size (root.right, k, count, ans)
        count += s2   
        return s1 + s2 + 1 

    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        # recursively compute the size of the tree by scanning from smallest to largest 
        # keep a count of the size scanned so far 
        # return when the count == k
        count = 0 
        ans = [-1] 
        self.size(root, k, count, ans) 
        return ans[0]
```


2022 04 16 

**Easy** 

[Increasing Order Search Tree](https://leetcode.com/problems/increasing-order-search-tree/) 

Solution 1: Memory Intensive 

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:

    def scan(self, root, L): 
        if root.left != None: 
            self.scan (root.left, L)
        L.append(root)
        if root.right != None: 
            self.scan (root.right, L)
        
    def increasingBST(self, root: TreeNode) -> TreeNode:
        L = []
        self.scan (root, L)
        for i in range(len(L)-1): 
            L[i].right = L[i+1]
            L[i].left = None
        L[-1].left = None 
        L[-1].right = None
        return L[0]
```

Solution 2: Memory Efficient

```python 
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def increasingBST(self, root: TreeNode) -> TreeNode:
        if root == None: 
            return None 
        root.right = self.increasingBST(root.right)
        tmp = self.increasingBST(root.left)
        if tmp != None:
            tmp1 = tmp
            while tmp1.right != None: 
                tmp1 = tmp1.right
            tmp1.right = root
            root.left = None
            return tmp
        else: 
            return root 
 ```

2022 04 15

**Medium**

[Convert BST to Greater Tree](https://leetcode.com/problems/convert-bst-to-greater-tree/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def scanBST (self, root, L): 
        if root == None: 
            return 
        self.scanBST(root.left, L)
        L.append(root)
        self.scanBST(root.right, L) 
        
    def convertBST(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        L = [] 
        self.scanBST(root, L)
        for i in range(len(L)-2, -1, -1): 
            L[i].val += L[i+1].val
        return root
```

2022 04 14

**Medium**

[Trim a Binary Search Tree](https://leetcode.com/problems/trim-a-binary-search-tree/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def trimBST(self, root: Optional[TreeNode], low: int, high: int) -> Optional[TreeNode]:
        if root == None: 
            return None
        if root.val >= low and root.val <= high: 
            root.right = self.trimBST(root.right, low, high)
            root.left = self.trimBST(root.left, low, high)
            return root
        elif root.val < low: 
            return self.trimBST(root.right, low, high)
        else: 
            return self.trimBST(root.left, low, high)
```

2022 04 13

**Easy**

[ Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/) 

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def searchBST(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        while root != None: 
            if root.val == val: 
                return root
            elif root.val > val:
                if root.left != None: 
                    root = root.left 
                else: 
                    return None 
            else: 
                if root.right != None: 
                    root = root.right
                else: 
                    return None 
        return None
```

2022 04 12 

**Medium**

[Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/) 
```python
class Solution:
    def generateMatrix(self, n: int) -> List[List[int]]:
        L = [ [0 for j in range(n)] for i in range (n) ]
        x = 1
        i = 0 
        j = 0 
        a = 0 
        b = n
        while a < b: 
            for j in range(a, b): 
                L[i][j] = x 
                x += 1
            for i in range(a + 1, b):
                L[i][j] = x
                x += 1 
            for j in range(b - 2, -1 + a, -1): 
                L[i][j] = x 
                x += 1
            for i in range(b - 2, a, -1): 
                L[i][j] = x 
                x += 1
            a += 1
            b -= 1
        return L 
```
