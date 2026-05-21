## 平衡二叉树

**二叉树节点的深度**：指的是从根节点到该节点的最长简单路径边的条数，就是从根往下到这个节点的距离

**二叉树节点的高度**：指的是该节点到叶子节点的最长简单路径边的条数，就是从这个节点往下到叶子节点的最远距离

**递归法**

`:=`又学了个写法，这个写法配合`if`相当于**赋值**加**判断**一行搞定~非常方便，`if (left_height := self.getheight(root.left)) == -1:`先算左子树高度，途中发现不平衡就返回-1，右子树同理，之后`if abs(left_height - right_height) > 1:`当前节点的左右子树相减后的绝对值如果大于1那就是不平衡的二叉树返回-1，否则返回当前节点的真实高度，记得加上本层的1

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        if self.getheight(root) != -1:
            return True
        else:
            return False

    def getheight(self, root: [TreeNode]) -> int:
        if not root:
            return 0
        if (left_height := self.getheight(root.left)) == -1:
            return -1
        if (right_height := self.getheight(root.right)) == -1:
            return -1
        if abs(left_height - right_height) > 1:
            return -1
        else:
            return 1 + max(left_height, right_height)
```

**递归法精简**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        return True if self.getheight(root) != -1 else False

    def getheight(self, root: [TreeNode]) -> int:
        if not root:
            return 0
        left = self.getheight(root.left)
        right = self.getheight(root.right)
        if left == -1 or right == -1 or abs(left - right) > 1:
            return -1
        else:
            return 1 + max(left, right)
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
    def getDepth(self, cur):
        st = []
        if cur is not None:
            st.append(cur)
        res = 0
        depth = 0
        while st:
            node = st[-1]
            if node is not None:
                st.pop()
                st.append(node)
                st.append(None)
                depth += 1
                if node.right:
                    st.append(node.right)
                if node.left:
                    st.append(node.left)
            else:
                node = st.pop()
                st.pop()
                depth -= 1
            res = max(res, depth)
        return res
        
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        st = []
        if root is None:
            return True
        st.append(root)
        while st:
            node = st.pop()
            if abs(self.getDepth(node.left) - self.getDepth(node.right)) > 1:
                return False
            if node.right:
                st.append(node.right)
            if node.left:
                st.append(node.left)
        return True
```


