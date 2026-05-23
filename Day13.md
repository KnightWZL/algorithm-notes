## Day13

### 平衡二叉树

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

`if root is None:`空树一定是平衡二叉树，核心是这个`if abs(self.getDepth(node.left) - self.getDepth(node.right)) > 1:`计算左右子数的最大高度，如果高度差 > 1那就不是平衡二叉树返回False，而在`getDepth`用到了之前的空指针法，如果遇到的不是None，那就把这个数先弹出来再压回去跟上空指针做标记，然后压入右子树和左子树，同时高度加一比如`root= [3,9,20,null,null,15,7]`传20进去那么这个过程执行完后就是`st = [20, None, 15, 7]`再来一次循环就是`st = [20, None, 7, 15, None]`如果遇到的是空指针那就进行回溯执行完`node = st.pop() st.pop`后就是`st = [20, None, 15]`也就是遇到None把 7 和 None 都弹出去，同时高度 -1 ，此时的高度就是1，每次判断结束后用`max()`来判断res和depth哪个大，大的返回给res，全部循环结束后的res就是最大高度

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
        depth = 0
        res = 0
        while st:
            node = st[-1]
            if node:
                st.pop()
                st.append(node)
                st.append(None)
                if node.right:
                    st.append(node.right)
                if node.left:
                    st.append(node.left)
                depth += 1
            else:
                node = st.pop()
                st.pop()
                depth -= 1
            res = max(res, depth)
        return res
        
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        if root is None:
            return True
        st = [root]
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

**迭代法精简版**

这里和上面迭代法不一样的地方在于`node = stack.pop()`不是`node = stack[-1]`，所以下面else里面不用再把None给弹出去，不为None的时候也不用弹出再压回去，`left, right = height_map.get(node.left, 0), height_map.get(node.right, 0)`是核心，get字典里左右子树的高度，没有返回0，然后`height_map[node] = 1 + max(left, right)`比最大值然后记入该节点的字典里，记得加上本层的1

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        if not root:
            return True
        stack = [root]
        height_map = {}
        while stack:
            node = stack.pop()
            if node:
                stack.append(node)
                stack.append(None)
                if node.right:
                    stack.append(node.right)
                if node.left:
                    stack.append(node.left)
            else:
                node = stack.pop()
                left, right = height_map.get(node.left, 0), height_map.get(node.right, 0)
                if abs(left - right) > 1:
                    return False
                height_map[node] = 1 + max(left, right)
        return True
```

### 二叉树的所有路径

```
输入：root = [1,2,3,null,5]
输出：["1->2->5","1->3"]
```

`path.append(cur.val)`把当前节点加入到路径中path=[1],`if not cur.left and not cur.right:`当子右子树都没有节点就是叶子节点`"->".join(map(str, path))`map就是对里面所有数字变成字符串,然后通过join拼接成[1, 2, 5]然后添加进res里。还没到叶子节点的时候就`self.traversal(cur.left, path, res)`看哪边还有左右子树走哪边，走完后使用`path.pop()`回溯。比如添加完[1, 2, 5]路径后开始return，然后开始`path.pop()`变成[1, 2]接着判断当前节点右子树，这样依次递归遍历左子树右子树，探索分支路径，核心就是`path.pop()`完美

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def traversal(self, cur, path, res):
        path.append(cur.val)
        if not cur.left and not cur.right:
            res.append("->".join(map(str, path)))
        if cur.left:
            self.traversal(cur.left, path, res)
            path.pop()
        if cur.right:
            self.traversal(cur.right, path, res)
            path.pop()           

    def binaryTreePaths(self, root: Optional[TreeNode]) -> List[str]:
        res = []
        path = []
        if not root:
            return res
        self.traversal(root, path, res)
        return res
```

### 左叶子之和

参考了上一题二叉树所有路径的递归法，res为每个左叶子节点的值，当左子节点有树而且还满足叶子节点（左右子树都没有的情况下）就`res += cur.left.val`然后递归遍历每个节点，不是左叶子节点的情况下都不进行res的相加，也不进行其他的判断，最后到了`not cur`也就是遍历的路径到了尽头没有节点的时候递归就开始一层一层回溯，回到最初递归的res就是遍历了所有节点后，有左叶子节点的值之和

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def test(self, cur):
        res = 0
        if not cur:
            return 0
        if cur.left and not cur.left.left and not cur.left.right:
            res += cur.left.val
        res += self.test(cur.left)        
        res += self.test(cur.right)
        return res        
    def sumOfLeftLeaves(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0    
        return self.test(root)
```

### 完全二叉树的节点个数

自己写的：递归法遍历全部节点~每遍历到一个就用全局变量把res+=1算出节点个数

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def getDepth(self, cur):
        if not cur:
            return 
        self.res += 1
        if cur.left:
            self.getDepth(cur.left)
        if cur.right:
            self.getDepth(cur.right)

    def countNodes(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        self.res = 0
        self.getDepth(root)
        return self.res
```

**递归法**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def getDepth(self, cur):
        if not cur:
            return 0
        leftNum = self.getDepth(cur.left)
        rightNum = self.getDepth(cur.right)
        return leftNum + rightNum + 1

    def countNodes(self, root: Optional[TreeNode]) -> int:
        return self.getDepth(root)
```

**递归精简**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def countNodes(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        return self.countNodes(root.left) + self.countNodes(root.right) + 1
```

