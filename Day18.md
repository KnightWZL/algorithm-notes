## Day18

### 修剪二叉搜索树

**递归法**

如果val < low小于左区间，那么返回剪掉左子树的节点，返回右子树节点；如果大于右区间，剪掉右子树，返回左子树节点，如果这个值刚好在范围内，不用修剪，对他的左右子树节点进行递归，最后指向的是修剪后的左右子树

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def trimBST(self, root: Optional[TreeNode], low: int, high: int) -> Optional[TreeNode]:
        if not root:
            return root
        if root.val < low:
            return self.trimBST(root.right, low, high)
        if root.val > high:
            return self.trimBST(root.left, low, high)        
        root.left = self.trimBST(root.left, low, high)
        root.right = self.trimBST(root.right, low, high)
        return root
```

### 将有序数组转换为二叉搜索树

**递归法**

遇到空数组返回None，整除2找中间节点`mid = len(nums) // 2`，构造根节点`root = TreeNode(nums[mid])`左右节点指向`nums[:mid] 和 nums[mid + 1:]`

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None
        mid = len(nums) // 2
        root = TreeNode(nums[mid])
        root.left = self.sortedArrayToBST(nums[:mid])
        root.right = self.sortedArrayToBST(nums[mid + 1:])
        return root
```

### 把二叉搜索树转换为累加树

**递归法**

我的思路是用一个全局遍历来存储递归的累加值`self.num = 0`,遇到空节点直接返回None，然后先从右子树开始递归，因为题目要求是每个节点值都变为原本值加上原本二叉搜索树中所有比该节点值大的节点值的总和，所以得从右子树开始，然后处理中间节点，全局变量加上当前节点的值，然后赋值给当前节点，进行递归右子树，最后返回root

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def __init__(self):
        self.num = 0
    def convertBST(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        self.convertBST(root.right)
        self.num += root.val
        root.val = self.num
        self.convertBST(root.left)
        return root
```

