## Day27

### [合并区间](https://leetcode.cn/problems/merge-intervals/)

解题思路和Day26里的几题差不多~先按左区间的值从小到大排序，之后遍历列表，如果当前区间的左区间大于等于上一个区间的右区间，那么将这两个区间的最小左区间赋值给当前左区间，最大右区间赋值给当前右区间，否则res添加上一个区间，循环遍历结束后再将最后一个区间添加进去，然后就AC了~我去看看题解

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        intervals.sort(key = lambda x : x[0])
        res = []
        for i in range(1, len(intervals)):
            if intervals[i][0] <= intervals[i - 1][1]:
                intervals[i][0] = min(intervals[i][0], intervals[i - 1][0])
                intervals[i][1] = max(intervals[i][1], intervals[i - 1][1])
            else:
                res.append([intervals[i - 1][0], intervals[i - 1][1]])
        res.append([intervals[len(intervals) - 1][0], intervals[len(intervals) - 1][1]])        
        return res        
```

题解里的思路更加优雅，最开始我也想到类似的pre思路来解~`res.append(intervals[0])`先将第一个区间添加进res。开始循环遍历，如果当前区间的左区间小于等于res里最后一个区间的右区间，说明他们是重叠区间，更新res最后一个区间的右区间为，当前区间和res最后区间的最大右区间。else不是重叠区间的情况下，将当前区间添加进res里

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        if len(intervals) == 0: return []
        intervals.sort(key = lambda x : x[0])
        res = []
        res.append(intervals[0])
        for i in range(1, len(intervals)):
            if intervals[i][0] <= res[-1][1]:
                res[-1][1] = max(res[-1][1], intervals[i][1])
            else:
                res.append([intervals[i][0], intervals[i][1]])      
        return res        
```

### [单调递增的数字](https://leetcode.cn/problems/monotone-increasing-digits/)

从后往前循环遍历，`if strNum[i - 1] > strNum[i]:`当前一个数大于当前这个数时，前一个数-1，然后从当前下标i开始循环遍历将后面的数全部设置为9，`strNum[j] = "9"`

```python
class Solution:
    def monotoneIncreasingDigits(self, n: int) -> int:
        strNum = list(str(n))
        for i in range(len(strNum) - 1, 0, -1):
            if strNum[i - 1] > strNum[i]:
                strNum[i - 1] = str(int(strNum[i - 1]) - 1)
                for j in range(i, len(strNum)):
                    strNum[j] = "9"
        return int("".join(strNum))
```

### [监控二叉树](https://leetcode.cn/problems/binary-tree-cameras/)

从下往上安装摄像头：跳过叶子节点（leaves）这样安装数量最少，局部最优 -> 全局最优。先给叶子节点的父节点安装，然后每隔两层节点安装一个摄像头，直到 Head

- 0： 该节点未覆盖
- 1： 该节点有摄像头
- 2： 该节点有覆盖

退出递归条件为 not cur 返回 2 **该节点有覆盖**，如果左右两边节点都是 2 都有覆盖的情况返回 0 **该节点未覆盖**，当有一边为 0 时可以安装摄像头，res[0] 摄像头数量 + 1，返回 1 **该节点有摄像头**，如果左右两边节点有一边为 1 时返回 2 **该节点有覆盖**。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def minCameraCover(self, root: Optional[TreeNode]) -> int:
        res = [0]
        if self.traversal(root, res) == 0:
            res[0] += 1
        return res[0]
    def traversal(self, cur, res):
        if not cur:
            return 2
        left = self.traversal(cur.left, res)
        right = self.traversal(cur.right, res)
        if left == 2 and right == 2:
            return 0
        if left == 0 or right == 0:
            res[0] += 1
            return 1
        if left == 1 or right == 1:
            return 2
```

elif优化

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def minCameraCover(self, root: Optional[TreeNode]) -> int:
        res = [0]
        if self.traversal(root, res) == 0:
            res[0] += 1
        return res[0]
    def traversal(self, cur, res):
        if not cur:
            return 2
        left = self.traversal(cur.left, res)
        right = self.traversal(cur.right, res)
        if left == 2 and right == 2:
            return 0
        elif left == 0 or right == 0:
            res[0] += 1
            return 1
        else:
            return 2
```

