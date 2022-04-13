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
