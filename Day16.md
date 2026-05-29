## Day16

### 二叉搜索树的最小绝对差

**递归法** 中序递增，结合数组

通过递归`traversal`将二叉搜索树转换为有序数组，之后`minval = min(minval, self.ves[i] - self.ves[i - 1])`循环遍历得出有序数组的最小差值

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def __init__(self):
        self.ves = []
    def traversal(self, root):
        if not root:
            return
        self.traversal(root.left)
        self.ves.append(root.val)
        self.traversal(root.right)
    def getMinimumDifference(self, root: Optional[TreeNode]) -> int:
        self.ves = []
        self.traversal(root)
        if len(self.ves) < 2:
            return 0
        minval = float("inf")
        for i in range(1, len(self.ves)):
            minval = min(minval, self.ves[i] - self.ves[i - 1])
        return minval
```

**递归法**利用中序递增找到该树最小值

原理和上面的递归法差不多~核心就是`self.pre = root`记录上一个节点和现在这个节点想减得出差值

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def __init__(self):
        self.res = float("inf")
        self.pre = None
    def traversal(self, root):
        if not root:
            return
        self.traversal(root.left)
        if self.pre:
            self.res = min(self.res, root.val - self.pre.val)
        self.pre = root
        self.traversal(root.right)
    def getMinimumDifference(self, root: Optional[TreeNode]) -> int:
        self.traversal(root)
        return self.res
```

**迭代法**

迭代遍历~将当前节点和上一个节点的差每次遍历到一个节点都进行一个对比，res将存储最小的那个差值

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def getMinimumDifference(self, root: Optional[TreeNode]) -> int:
        stack = []
        pre = None
        cur = root
        res = float("inf")
        while cur or len(stack) > 0:
            if cur:
                stack.append(cur)
                cur = cur.left
            else:
                cur = stack.pop()
                if pre:
                    res = min(res, cur.val - pre.val)
                pre = cur
                cur = cur.right
        return res
```

### 二叉搜索树中的众树

**递归法**集合

二叉树定义里是不会有重复元素的，所以这题的题目描述里有提到假定 BST 满足如下定义，在会出现众数（也就是出现频率最高的元素）的情况下来解决这题，比如[1, 2, 2]返回[2]，[1, 1, 2, 2]返回[1, 2]这个样子。再加上最关键的二叉搜索树按照中序遍历依旧是升序排序的，也就是说不会出现[1, 2, 1]这样的情况，所以我的解题思路是**定义一个pre来存储上一个节点**，然后用**count来统计重复值**出现的次数，**另外初始值要为1**，不然会在[0]这个测试用例卡住，然后众数是出现频率最高的数，所以还要一个maxcount，当上一个节点和当前节点的值相等，则count+=1，当不同时则count=1，之后进行判断，count>maxcount的时候清空集合，并且将这个数add进去并且将count赋值给maxcount，如果相等则直接添加新的众数。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def __init__(self):
        self.res = set()
        self.pre = None
        self.count = 1
        self.maxcount = 1
    def findMode(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
        self.findMode(root.left)
        if self.pre and root.val == self.pre.val:
            self.count += 1
        else:
            self.count = 1
        if self.count > self.maxcount:
            self.res.clear()
            self.res.add(root.val)
            self.maxcount = self.count     
        if self.count == self.maxcount:
            self.res.add(root.val)
        self.pre = root
        self.findMode(root.right)
        if self.count == 0 and not self.pre:
            return root.val
        return list(self.res)
```

集合和字典可以去重，如果我这个代码set换成列表那么会解答错误

Case 1

输入

root =

[1,null,2,2]

输出

[2,2]

预期结果

[2]

测试用例： 1 -right> 2 -left> 2

原因是因为正序遍历的时候，先递归左子节点，进去后清空集合，然后append了2，之后回溯到了上个节点，再添加了一次2，所以我这套解法用list没有ac全部用例

**递归**列表

看了下题解~先`if self.count == self.maxcount:`再`if self.count > self.maxcount:`我前面把这两个先后弄反了，才需要用到集合，先判断==是并列最高的众数一起append进去，后>是发现新的最高众数，清空列表重新添加冠军~

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def __init__(self):
        self.res = []
        self.pre = None
        self.count = 1
        self.maxcount = 1
    def findMode(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
        self.findMode(root.left)
        if self.pre and root.val == self.pre.val:
            self.count += 1
        else:
            self.count = 1
        if self.count == self.maxcount:
            self.res.append(root.val)
        if self.count > self.maxcount:
            self.res = [root.val]
            self.maxcount = self.count     
        self.pre = root
        self.findMode(root.right)
        return self.res
```

**迭代法**

思路和递归法一样，`if not pre:`但如果没有这句的话会遍历到空节点的清空报错：'NoneType' object has no attribute 'val'，需要注意~

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def findMode(self, root: Optional[TreeNode]) -> List[int]:
        res = []
        st = []
        cur = root
        pre = None
        count = 0
        maxcount = 0
        while cur or st:
            if cur:
                st.append(cur)
                cur = cur.left
            else:
                cur = st.pop()
                if not pre:
                    count = 1
                elif cur.val == pre.val:
                    count += 1
                else:
                    count = 1
                if count == maxcount:
                    res.append(cur.val)
                if count > maxcount:
                    res = [cur.val]
                    maxcount = count
                pre = cur
                cur = cur.right
        return res
```

### 二叉树的最近公共祖先

返回条件`if root == p or root == q or not root: return root`除了平时的not root，当root==p或者q的时候也reutrn~做出退出递归的条件。`left = self.lowestCommonAncestor(root.left, p, q)`左子树节点找到后返回给left，rigiht同理，`if left and right:
            return root`说明左子树和右子树都找到了~那么root节点就是最近公共祖先，`if left and not right:
            return left`右子树返回的是None也就是q和p都不在右子树里，那么q和p都在左子树~返回left这个值，另一边情况同理，else判断则是如果左右两边子树都为空返回None

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if root == p or root == q or not root:
            return root
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right:
            return root
        if left and not right:
            return left
        elif not left and right:
            return right
        else:
            return None
```

**递归**精简

如果左右子树都为空，那么返回right也是返回None~所以可以左右精简

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if root == p or root == q or not root:
            return root
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right:
            return root
        if left and not right:
            return left
        return right
```

