2002 04 18

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
