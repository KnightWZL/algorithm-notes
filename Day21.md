## Day21

### [复原 IP 地址](https://leetcode.cn/problems/restore-ip-addresses/)

好消息~成功AC，坏消息，性能挺差的，过会看下题解，我先说下我的思路：穷举，暴力，循环，差不多这个样子~直接回溯，确认回溯条件：startIndex=s的长度开始判断path里面是否有四个元素，满足ip地址有四个整数组成，然后用join拼接添加进res里，开始循环遍历，`temp = s[startIndex:i + 1]`切割，当temp的长度>1且第一位=“0”的时候执行continue，不然会出现02 023这样的地址，`if int(temp) < 256:`int后的整数要<256才是有效ip~然后条件满足，开始path.append,然后递归，回溯，AC。

```python
class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        res = []
        path = []
        self.backtracking(s, 0, path, res)
        return res
    def backtracking(self, s, startIndex, path, res):
        if startIndex == len(s):
            if len(path) == 4:
                res.append(".".join(path[:]))
            return
        for i in range(startIndex, len(s)):
            temp = s[startIndex:i + 1]
            if len(temp) > 1 and temp[0] == "0":
                continue
            if int(temp) < 256:
                path.append(temp)
                self.backtracking(s, i + 1, path, res)
                path.pop() 
```

**看了题解优化了下**

核心：使用`if len(path) > 4:`剪枝，毕竟要是path里的元素数量>4了，那肯定不符合ip地址规范了，直接剪枝，`if startIndex == len(s) and len(path) == 4:`回溯条件合并~看起来更简洁了，`i > startIndex`这个判断写得比我前面的`len(temp) > 1`更专业更标准~

```python
class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        res = []
        path = []
        self.backtracking(s, 0, path, res)
        return res
    def backtracking(self, s, startIndex, path, res):
        if startIndex == len(s) and len(path) == 4:
            res.append(".".join(path[:]))
            return
        if len(path) > 4:
            return
        for i in range(startIndex, len(s)):
            temp = s[startIndex:i + 1]
            if i > startIndex and temp[0] == "0":
                continue
            if int(temp) < 256:
                path.append(temp)
                self.backtracking(s, i + 1, path, res)
                path.pop() 
```

### [子集](https://leetcode.cn/problems/subsets/)

这算是自己最快AC的回溯题了~ 执行用时分布 0ms 击败100.00%~

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        res = []
        path = []
        self.backtracking(nums, 0, path, res)
        return res
    def backtracking(self, nums, startIndex, path, res):
        res.append(path[:])
        if startIndex == len(nums):
            return
        for i in range(startIndex, len(nums)):
            path.append(nums[i])
            self.backtracking(nums, i + 1, path, res)
            path.pop()
```

### [子集 II](https://leetcode.cn/problems/subsets-ii/)

这题和上题的区别在于上把nums里没有重复的元素，这题的题目有说nums里可能会包含会包含重复的元素，重复的情况下，题解题目也有要求不能包含重复的子集，那么我们先排序一下再做个去重操作~原理和上题一样直接AC。

```python
class Solution:
    def subsetsWithDup(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        path = []
        res = []
        self.backtracking(nums, 0, path, res)
        return res
    def backtracking(self, nums, startIndex, path, res):
        res.append(path[:])
        if startIndex == len(nums):
            return
        for i in range(startIndex, len(nums)):
            if i > startIndex and nums[i] == nums[i - 1]:
                continue
            path.append(nums[i])
            self.backtracking(nums, i + 1, path, res)
            path.pop()
```

