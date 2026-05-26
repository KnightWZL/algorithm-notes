## Day15

### 最大二叉树

这题没看题解自己做出来了~嘻嘻，我的思路来自Day14从中序与后序遍历序列构造二叉树这题的解法~通过max找出数组最大值，然后切割左右，max构造root节点，root左右节点进行递归~

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def constructMaximumBinaryTree(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None
        num = max(nums)
        maxindex = nums.index(num)
        root = TreeNode(num)
        root.left = self.constructMaximumBinaryTree(nums[:maxindex])
        root.right = self.constructMaximumBinaryTree(nums[maxindex + 1:])
        return root
```

### 合并二叉树

这题也没看题解自己做出来啦~思路是如果两边节点都为空返回None，有一边为空返回另一边的值，两边都有就对值进行相加，原本想额外写第三个二叉树，但是在原有的二叉树上更改肯定更节省空间，所以就直接对root1的值进行一个root1和root2值的相加，之后和其他题一样直接对左右节点进行递归~

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1 and not root2:
            return None
        if not root1 and root2:
            return root2
        if root1 and not root2:
            return root1
        if root1 and root2:
            root1.val = root1.val + root2.val
        root1.left = self.mergeTrees(root1.left, root2.left)
        root1.right = self.mergeTrees(root1.right, root2.right) 
        return root1
```

**递归法**

| 执行用时    | 消耗内存 |
| ----------- | -------- |
| 30 ms（我） | 19.3 MB  |
| 11 ms       | 19.2 MB  |

还得是卡哥，两个判断抵我四个判断，`if not root1 and not root2`可以省掉是因为，到叶子节点的时候not root1的话，返回root2，可root2也是空的呀~not root2也是同理，所以可以直接省掉，然后if root1 and root2:更是可以直接省掉，因为已经排除了两个节点有空节点的情况，剩下的就是两个节点至少会有一个非空

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1:
            return root2
        if not root2:
            return root1
        root1.val = root1.val + root2.val
        root1.left = self.mergeTrees(root1.left, root2.left)
        root1.right = self.mergeTrees(root1.right, root2.right) 
        return root1
```

### 二叉搜索树中的搜索

**递归法**

找到目标节点或者当前节点是空节点就返回，如果不加return就是遍历整棵树了。另外，二叉搜索树非空左子树的所有键值小于其根节点的键值；非空右子树的所有键值大于其根节点的键值；左右子树都是二叉搜索树。根据二叉搜索树的满足条件，来解决这题可以更快查询到目标节点

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def searchBST(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        if not root or root.val == val:
            return root
        if root.val > val:
            return self.searchBST(root.left, val)
        if root.val < val:
            return self.searchBST(root.right, val)
```

**迭代法**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def searchBST(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        while root:
            if root.val > val:
                root = root.left
            elif root.val < val:
                root = root.right
            else:
                return root
        return None
```

**栈遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def searchBST(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        stack = [root]
        while stack:
            node = stack.pop()
            if node.val == val:
                return node
            if node.left:
                stack.append(node.left)
            if node.right:
                stack.append(node.right)
        return None
```

### 验证二叉搜索树

**递归 判断是不是正序数组**

二叉搜索树左子树的值是比中间的值小的，所以先`self.traversal(root.left)`递归把左边的值放进列表，然后再放中间节点的值，最后递归放右子树的将整个树的值存入列表，之后遍历列表看看里面的值是否是正序，一旦出现前面大于后面的情况返回Flase，注意也不能等于，因为二叉搜索树里不能有相同元素的值，所以判断语句是`if self.vec[i - 1] >= self.vec[i]:`

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def __init__(self):
        self.vec = []
    def traversal(self, root):
        if not root:
            return 
        self.traversal(root.left)
        self.vec.append(root.val)
        self.traversal(root.right)
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        self.vec = []
        self.traversal(root)
        for i in range(1, len(self.vec)):
            if self.vec[i - 1] >= self.vec[i]:
                return False
        return True
```

**递归**

`self.maxval = float('-inf')`定义一个极小值，因为后台测试数据里有int最小值，之后依旧是验证遍历的元素是不是从小到大

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def __init__(self):
        self.maxval = float('-inf')
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        if not root:
            return True
        left = self.isValidBST(root.left)
        if root.val > self.maxval:
            self.maxval = root.val
        else:
            return False
        right = self.isValidBST(root.right)
        return left and right
```

**迭代法**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        stack = []
        pre = None
        cur = root
        while cur or len(stack) > 0:
            if cur:
                stack.append(cur)
                cur = cur.left
            else:
                cur = stack.pop()
                if pre and pre.val >= cur.val:
                    return False
                pre = cur
                cur = cur.right
        return True
```

