## Day11

### 二叉树理论基础

**二叉树的种类**

解题过程中二叉树有两种主要的形式：满二叉树和完全二叉树，满二叉树深度为k，有2^k-1个节点的二叉树。在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置，若最底层为第h层(h从1开始)，该层包含1~2^(h-1)个节点

**存储方式**

二叉树可以链式存储，也可以顺序存储

**遍历方式**

- 深度优先遍历

  前序遍历（递归法，迭代法）5 4 1 2 6 7 8 中左右

  中序遍历（递归法，迭代法）1 4 2 5 7 6 8 左中右

  后序遍历（递归法，迭代法）1 2 4 7 8 6 5 左右中

- 广度优先遍历

  层次遍历（迭代法）

**二叉树节点定义**

```python
class TreeNode:
    def __init__(self, val, left = None, right = None):
        self.val = val
        self.left = left
        self.right = right
```

### 二叉树的递归遍历

**每次写递归都按照这三要素来写，保证可以写出正确的递归算法！**

1. **确定递归函数的参数和返回值**：确定哪些参数是递归的过程中需要处理的，那么就在递归函数里加上这个参数，并且还要明确每次递归的返回值是什么，进而确定递归函数的返回值
2. **确定终止条件**：写完了递归算法，运行的时候，经常会遇到栈溢出的错误，就是没写终止条件或者终止条件写的不对，操作系统也是用一个栈的结构来保存没一层的递归信息，如果递归没有终止，操作系统的内存栈必然就会溢出
3. **确定单层递归的逻辑**：确定每一层递归需要处理的信息。在这里也就会重复调用自己来实现递归的过程。

**二叉树的前序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        res = []
        def dfs(node):
            if node is None:
                return
            res.append(node.val)
            dfs(node.left)
            dfs(node.right)
        dfs(root)
        return res
```

**二叉树的中序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        res = []
        def dfs(node):
            if node is None:
                return
            dfs(node.left)
            res.append(node.val)
            dfs(node.right)
        dfs(root)
        return res
```

**二叉树的后序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def postorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        res = []
        def dfs(node):
            if node is None:
                return 
            dfs(node.left)
            dfs(node.right)
            res.append(node.val)
        dfs(root)
        return res
```

### 二叉树的迭代遍历

**二叉树的前序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
        stack = [root]
        res = []
        while stack:
            node = stack.pop()
            res.append(node.val)
            if node.right:
                stack.append(node.right)
            if node.left:
                stack.append(node.left)
        return res      
```

**二叉树的中序遍历**

中序遍历不能把根root放进`stack`里了，先迭代访问最底层的左子树节点`while cur or stack: cur = cur.left`之后开始处理栈顶节点`res.append(cur.val)`此时的栈顶肯定是最底层最左边的左子树节点，放入`res`后看这个节点右边有没有数，没有数`cur`就继续进入`else`分支里，再次弹回栈顶，此时的栈顶右边有数的话`cur`就可以取栈顶右边的数然后继续循环下去就能实现左中右中序遍历

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
        stack = []
        res = []
        cur = root
        while cur or stack:
            if cur:
                stack.append(cur)
                cur = cur.left
            else:
                cur = stack.pop()
                res.append(cur.val)
                cur = cur.right
        return res
```

**二叉树的后序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def postorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
        stack = [root]
        res = []
        while stack:
            node = stack.pop()
            res.append(node.val)
            if node.left:
                stack.append(node.left)
            if node.right:
                stack.append(node.right)
        return res[::-1]
```

### 二叉树的统一迭代法

None空指针给当前要处理的根节点做标记，所以三种遍历每次都是append完node后再跟个None进去

**二叉树的前序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        res = []
        stack = []
        if root:
            stack.append(root)
        while stack:
            node = stack.pop()
            if node != None:
                if node.right:
                    stack.append(node.right)
                if node.left:
                    stack.append(node.left)
                stack.append(node)
                stack.append(None)
            else:
                node = stack.pop()
                res.append(node.val)
        return res
```

**二叉树的中序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        res = []
        stack = []
        if root:
            stack.append(root)
        while stack:
            node = stack.pop()
            if node != None:
                if node.right:
                    stack.append(node.right)
                stack.append(node)
                stack.append(None)
                if node.left:
                    stack.append(node.left)
            else:
                node = stack.pop()
                res.append(node.val)
        return res
```

**二叉树的后序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def postorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        res = []
        stack = []
        if root:
            stack.append(root)
        while stack:
            node = stack.pop()
            if node != None:
                stack.append(node)
                stack.append(None)
                if node.right:
                    stack.append(node.right)
                if node.left:
                    stack.append(node.left)
            else:
                node = stack.pop()
                res.append(node.val)
        return res
```

### 二叉树的层序遍历

**利用长度法遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return []
        res = []
        queue = collections.deque([root])
        while queue:
            lever = []
            for i in range(len(queue)):
                cur = queue.popleft()
                lever.append(cur.val)
                if cur.left:
                    queue.append(cur.left)
                if cur.right:
                    queue.append(cur.right)
            res.append(lever)
        return res
```

