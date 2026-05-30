## Day17

### 二叉搜索树的最近公共祖先

**递归法**

`if not root:`递归返回条件，`if root.val > p.val and root.val > q.val:`如果root.val大于p和q的值，那么p和q都在左子树，因为这是二叉搜索树~利用二叉搜索树的原理来找最近公共祖先效率会更高，如果root,val小于p和q的值则同理，最后return是其他情况，比如不同时大于或等于root.val，那么说明root就是左右子树的最近公共祖先，直接返回root

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if not root:
            return root

        if root.val > p.val and root.val > q.val:
            left = self.lowestCommonAncestor(root.left, p, q)
            if left:
                return left
        if root.val < p.val and root.val < q.val:
            right = self.lowestCommonAncestor(root.right, p, q)
            if right:
                return right
        return root
```

**递归法**精简

只要`if root.val > p.val and root.val > q.val:`q和p都在左子树，那么一定能找到公共子节点，所以不用判断能不能找到，是不是为空，直接return就好，right同理。而终止条件的话，题目中有说p和q为不同节点且均存在与给定的二叉搜索树中，也就是说一定会找到公共祖先，所以不存在遇到空的情况，删掉终止条件也没事~

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if root.val > p.val and root.val > q.val:
            return self.lowestCommonAncestor(root.left, p, q)            
        if root.val < p.val and root.val < q.val:
            return self.lowestCommonAncestor(root.right, p, q)
        return root
```

### 二叉搜索树中的插入操作

我的解题思路是`if not root:`如果这个二叉树是空的那么直接构造一个新节点作为这个二叉搜索树的头节点返回，`if val < root.val:`当val值小于root.val，那我就在val左边进行处理，如果有左子树，那么就继续递归，如果没有，那么root的左节点指向我新构造的节点~另一边同理。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def insertIntoBST(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        if not root:
            return TreeNode(val)
        if val < root.val:
            if root.left:
                self.insertIntoBST(root.left, val)
            else:
                root.left = TreeNode(val)
        elif val > root.val:
            if root.right:
                self.insertIntoBST(root.right, val)
            else:
                root.right = TreeNode(val)
        return root
```

### 删除二叉搜索树中的节点

**递归法**

1判断当前节点root是否为空，2.判断两边是否都为空，3.判断左节点是否为空，4.判断右节点是否为空，5.如果两边都有子节点的情况，`cur = root.right`将右节点赋值给cur，循环遍历cur来到cur的最底层最左边的子节点，然后将这个节点的左节点指向最开始的root的左节点，然后返回root的右节点

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def deleteNode(self, root: Optional[TreeNode], key: int) -> Optional[TreeNode]:
        if not root:
            return root
        if root.val == key:
            if not root.left and not root.right:
                return None
            elif not root.left:
                return root.right
            elif not root.right:
                return root.left
            else:
                cur = root.right
                while cur.left:
                    cur = cur.left
                cur.left = root.left
                return root.right
        if root.val > key:
            root.left = self.deleteNode(root.left, key)
        elif root.val < key:
            root.right = self.deleteNode(root.right, key)
        return root
```

**递归法**精简

`cur.val, root.val = root.val, cur.val`核心在于找到要删除的节点和右子树的最左节点值后两个节点的值相交换，交换完后下次递归的时候，就可以在右子树中递归删除目标节点，因为key那个值被交换到叶子节点，`if not root.right:`key的右子树没有值，直接返回`return root.left`，而root.left也没有值，所以返回的是None就删掉了~

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def deleteNode(self, root: Optional[TreeNode], key: int) -> Optional[TreeNode]:
        if not root:
            return root
        if root.val == key:
            if not root.right:
                return root.left
            cur = root.right
            while cur.left:
                cur = cur.left
            cur.val, root.val = root.val, cur.val
        root.left = self.deleteNode(root.left, key) 
        root.right = self.deleteNode(root.right, key) 
        return root
```

