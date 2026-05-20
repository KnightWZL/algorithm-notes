## Day12

### 翻转二叉树

**递归法前序遍历**

`root.left, root.right = root.right, root.left`交换当前左右节点`self.invertTree(root.left)`递归处理左节点然后再处理右节点，最后返回根节点`return root`如果根节点为空要返回`None`而不是空列表了这里要注意返回类型

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        root.left, root.right = root.right, root.left
        self.invertTree(root.left)
        self.invertTree(root.right)
        return root
```

**迭代法前序遍历**

核心操作和递归法一样	`cur.left, cur.right = cur.right, cur.left`交换当前左右节点

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        stack = [root]
        while stack:
            cur = stack.pop()
            cur.left, cur.right = cur.right, cur.left
            if cur.right:
                stack.append(cur.right)
            if cur.left:
                stack.append(cur.left)
        return root
```

**递归法中序遍历**

交换左右节点放在中间后，`self.invertTree(root.left)`处理的还是左节点，毕竟左右节点已经交换了

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        self.invertTree(root.left)
        root.left, root.right = root.right, root.left
        self.invertTree(root.left)
        return root
```

**迭代法中序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        stack = [root]
        while stack:
            cur = stack.pop()
            if cur.right:
                stack.append(cur.right)
            cur.left, cur.right = cur.right, cur.left
            if cur.right:
                stack.append(cur.right)
        return root
```

**递归法后序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        self.invertTree(root.left)
        self.invertTree(root.right)
        root.left, root.right = root.right, root.left
        return root
```

**迭代法后序遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        stack = [root]
        while stack:
            cur = stack.pop()
            if cur.right:
                stack.append(cur.right)
            if cur.left:
                stack.append(cur.left)
            cur.left, cur.right = cur.right, cur.left
        return root
```

### 对称二叉树

**递归法**

`compare`函数来判断，先判断left和right为None的情况，然后再排除值不同的情况`elif left.val != right.val: return False`最后再做递归，做下一层的判断

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isSymmetric(self, root: Optional[TreeNode]) -> bool:
        if not root:
            return None
        return self.compare(root.left, root.right)

    def compare(self, left, right):
        if left == None and right != None: return False
        elif left != None and right == None: return False
        elif left == None and right == None: return True
        elif left.val != right.val: return False

        lefts = self.compare(left.left, right.right)
        rights = self.compare(left.right, right.left)
        return lefts and rights
```

**使用栈**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isSymmetric(self, root: Optional[TreeNode]) -> bool:
        if not root:
            return None
        st = []
        st.append(root.left)
        st.append(root.right)
        while st:
            rightNone = st.pop()
            leftNone = st.pop()
            if not rightNone and not leftNone: continue
            if not rightNone or not leftNone or leftNone.val != rightNone.val: return False
            st.append(leftNone.left)
            st.append(rightNone.right)
            st.append(leftNone.right)
            st.append(rightNone.left)
        return True
```

### 二叉树的最大深度

**递归法**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        return self.getDepth(root)
    
    def getDepth(self, node):
        if not node:
            return 0
        leftnode = self.getDepth(node.left)
        rightnode = self.getDepth(node.right)
        index = 1 + max(leftnode,rightnode)

```

**递归法**：**精简代码**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

**层序遍历迭代法**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        depth = 0
        queue = collections.deque([root])
        while queue:
            depth += 1
            for _ in range(len(queue)):
                cur = queue.popleft()
                if cur.left:
                    queue.append(cur.left)
                if cur.right:
                    queue.append(cur.right)
        return depth
```

### n叉树的最大深度

**递归法**

如果根节点为空记得返回0，核心就是遍历`for child in root.children:`n个子节点然后进行递归`depth = max(depth, self.maxDepth(child) + 1)`求出最大深度

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val: Optional[int] = None, children: Optional[List['Node']] = None):
        self.val = val
        self.children = children
"""

class Solution:
    def maxDepth(self, root: 'Node') -> int:
        if not root:
            return 0
        depth = 1
        for child in root.children:
            depth = max(depth, self.maxDepth(child) + 1)
        return depth
```

**迭代法**

第一重循环判断队列是否为空`while queue`第二重循环次数为`for _ in range(len(queue)):`这一层的所有节点，之后最左边的节点出队，第三重循环把当前节点的所有子节点入队，全部循环结束`depth`就是最大深度

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val: Optional[int] = None, children: Optional[List['Node']] = None):
        self.val = val
        self.children = children
"""

class Solution:
    def maxDepth(self, root: 'Node') -> int:
        if not root:
            return 0
        queue = collections.deque([root])
        depth = 0
        while queue:
            depth += 1
            for _ in range(len(queue)):
                node = queue.popleft()
                for child in node.children:
                    queue.append(child)
        return depth
```

**使用栈**

每层循环`max_Depth`都会加一，循环结束后，`max_Depth`就是最大深度

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val: Optional[int] = None, children: Optional[List['Node']] = None):
        self.val = val
        self.children = children
"""

class Solution:
    def maxDepth(self, root: 'Node') -> int:
        if not root:
            return 0
        max_Depth = 0
        stack = [(root, 1)]
        while stack:
            node, depth = stack.pop()
            max_Depth = max(max_Depth, depth)
            for child in node.children:
                stack.append((child, depth + 1))
        return max_Depth
```

### 二叉树的最小深度

最小深度是从根节点到最近叶子节点的最短路径上的节点数量， 叶子节点左右孩子都为空的节点才是叶子节点`leftdepth = self.getDepth(node.left)`递归求左子树深度，右子树同理用递归求，`if not leftdepth and rightdepth:`当左子树为空，右子树不为空：不能取空的左子树，右侧还能走，这时不是最低点`res = 1 + min(leftdepth, rightdepth)`当左右都存在，，取左右最小深度 然后当前节点+1层

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def getDepth(self, node):
        if not node:
            return 0        
        leftdepth = self.getDepth(node.left)
        rightdepth = self.getDepth(node.right)
        if not leftdepth and rightdepth:
            return 1 + rightdepth
        if leftdepth and not rightdepth:
            return 1 + leftdepth
        res = 1 + min(leftdepth, rightdepth)
        return res
    def minDepth(self, root: Optional[TreeNode]) -> int:
        return self.getDepth(root)
```

**递归法精简版**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        if not root.left and root.right:
            return 1 + self.minDepth(root.right)
        if root.left and not root.right:
            return 1 + self.minDepth(root.left)
        return 1 + min(self.minDepth(root.left), self.minDepth(root.right))
```

