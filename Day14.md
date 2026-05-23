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

