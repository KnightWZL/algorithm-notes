## Day14

### 找树左下角的值

`self.max_depth`记录当前遍历到的最大深度`self.res`保存最终左下角的结果，注意这个左下角，题目要求：给定一个二叉树的 **根节点** `root`，请找出该二叉树的 **最底层 最左边** 节点的值。这个最底层最左边的数哪怕最底层只有一个树，他还是right的树，他也是要找的那个最底层最左边的值。只在叶子节点来判断最大深度来更新值，其他情况就继续递归遍历

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def findBottomLeftValue(self, root: Optional[TreeNode]) -> int:
        self.max_depth = float("-inf")
        self.res = 0
        self.traversal(root, 0)
        return self.res

    def traversal(self, cur, depth):
        if not cur.left and not cur.right:
            if depth > self.max_depth:
                self.max_depth = depth
                self.res = cur.val
            return
        if cur.left:
            self.traversal(cur.left, depth + 1)
        if cur.right:
            self.traversal(cur.right, depth + 1)
```

**迭代法**

`if i == 0:  res = node.val`i = 0的时候说明到达这一层的最左边，res变更val，一直遍历到最后一层的最左边就是需要的值了

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def findBottomLeftValue(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        res = 0
        queue = collections.deque([root])
        while queue:
            n = len(queue)
            for i in range(n):
                node = queue.popleft()
                if i == 0:
                    res = node.val
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
        return res
```

### 路径总和

**递归法**

`count`是目标路径的值，如果这个值到叶子节点的时候=0，返回True，不等于0说明这条路径上的节点值加起来不等于targeSum。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def traversal(self, cur, count):
        if not cur.left and not cur.right and count == 0:
            return True
        if not cur.left and not cur.right:
            return False
        if cur.left:
            count -= cur.left.val
            if self.traversal(cur.left, count):
                return True
            count += cur.left.val
        if cur.right:
            count -= cur.right.val
            if self.traversal(cur.right, count):
                return True
            count += cur.right.val
        return False

    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if not root:
            return False
        return self.traversal(root, targetSum - root.val)
```

**递归精简**

这还能精简我是真没想到，太牛逼了卡哥。True的判断条件从count==0变成了叶子节点的时候targetSum == 叶子节点的那个val，在那之前左右子节点调用递归的时候传入的targetSum也都有减去自身节点的val，那么到这个路径最后的叶子节点时，如果满足True条件说明这条路径上所有节点值相加等于目标

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:

    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if not root:
            return False
        if not root.left and not root.right and targetSum == root.val:
            return True
        return self.hasPathSum(root.left, targetSum - root.val) or self.hasPathSum(root.right, targetSum - root.val)
```

**迭代法**

将每个节点和值存入元组，再讲元组压进栈里，之后while遍历，弹出栈顶第一个元组，判断这个节点是不是叶子节点，同时判断值是不是等于targetSum，如果不等于，那判断有没有左子节点，有的话就把左子节点，还有左子节点的值加上原本这个节点的值加起来的和存入元组再进行压进栈里。如果遍历结束都没有满足条件的return，则返回Flase

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if not root:
            return False
        st = [(root, root.val)]
        while st:
            node, sum_val = st.pop()
            if not node.left and not node.right and sum_val == targetSum:
                return True
            if node.left:
                st.append((node.left, sum_val + node.left.val))
            if node.right:
                st.append((node.right, sum_val + node.right.val))
        return False
```

### 路径总和II