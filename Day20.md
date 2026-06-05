## Day20

### [组合总和](https://leetcode.cn/problems/combination-sum/)

我的思路和解决day19那些题差不多，回溯条件，当 sum(path) > target 的时候return，以及sum(path) == target 的时候将path[:]添加进res里，使用[:]是拷贝一个新的列表进去，不然最后输出的列表都是[]这样的空列表，添加进去的就是地址了，而不是独立的列表，一个pop就都是空的了，回溯到最后自然全部都是空列表。for i in range(start, len(candidates)):原本是直接in candidates,但是结果会有[2, 3, 5]，[3, 2, 5], [5, 2, 3]这样的重复结果出现，加了start让他不会从头开始，就像2,3,5遍历完，到3开始的话，他就是3,5，不会出现回头从2开始添加的情况

```python
class Solution:
    def backtracking(self, candidates, target, start, path, res):
        if sum(path) > target:
            return
        if sum(path) == target:
            res.append(path[:])
            return
        for i in range(start, len(candidates)):
            path.append(candidates[i])
            self.backtracking(candidates, target, i, path, res)
            path.pop()
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        res = []
        path = []
        if len(candidates) == 0:
            return res
        self.backtracking(candidates, target, 0, path, res)
        return res
```

**回溯剪枝**

加入total优化，原本的sum每次都需要重新累加，而用total来存储累加值，不需要每次递归回溯都调用sum可以提升性能，而剪枝核心在于`if total + candidates[i] > target:`当累加值 + 这次candidates[i]的值如果大于 target 那么直接 break，后续的就不用再递归了，关键就是在开始递归前要将 `candidates.sort()`排序成有序数组，好处是比如数据是 [3, 2, 5]，target = 8 我循环到 3 + 3 + 3 > 8，这时候不能break，因为后面 3 + 3 + 2 是 == 8的，但是如果数据都是有序的列表就可以进行剪枝，比如排序后[2, 3, 5]2有total=8，candidates[i] = 3, 3 + 3 + 3>8那么就可以直接剪枝，5也是同理， [2, 3, 5, 8] 这种情况，也会循环遍历到 candidates[i] = 8,total = 8，append 后 8 + 8 > 8 直接剪枝，完美

```python
class Solution:
    def backtracking(self, candidates, target, total, start, path, res):
        if total > target:
            return
        if total == target:
            res.append(path[:])
            return
        for i in range(start, len(candidates)):
            if total + candidates[i] > target:
                break
            total += candidates[i]
            path.append(candidates[i])
            self.backtracking(candidates, target, total, i, path, res)
            total -= candidates[i]
            path.pop()
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        res = []
        path = []
        candidates.sort()
        self.backtracking(candidates, target, 0, 0, path, res)
        return res
```

### [组合总和 II](https://leetcode.cn/problems/combination-sum-ii/)

解题思路和上一题差不多，只不过上一题一个的元素可以一直重复使用，比如2222，这题一个元素只能使用一次，这题我用i + 1来回溯，跳过自己，就只能使用一次~不能回头再使用自己，实现不可重复，`if i > index and candidates[i] == candidates[i-1]:`是为了做去重操作，i > index也就是从第二个数开始，毕竟不是从0开始而是从index，那么就可以开始判断是否和上一个元素一样，一样的话就跳过，当然用python的集合和字典来解决也可以，自动去重，然后和上题题解的回溯剪枝思路一样，这次我就直接剪枝了~提前排序，然后也是判断`if total + candidates[i] > target:`累加值和当前值如果大于target就进行剪枝

```python
class Solution:
    def backtracking(self, candidates, target, total, index, path, res):
        if total == target:
            res.append(path[:])
            return
        for i in range(index, len(candidates)):
            if i > index and candidates[i] == candidates[i-1]:
                continue
            if total + candidates[i] > target:
                break
            total += candidates[i]
            path.append(candidates[i])
            self.backtracking(candidates, target, total, i + 1, path, res)
            total -= candidates[i]
            path.pop()
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        path = []
        res = []
        candidates.sort()
        self.backtracking(candidates, target, 0, 0, path, res)
        return res
```

### 分割回文串

回文串，切割后的每一子串都是回文,path里的[aa,b]都是回文的情况才能添加进res里。回溯退出条件：`if startIndex == len(s):`当startIndex=s的长度时，添加进res里再返回，注意添加的时候用path[:]来拷贝一个新的列表。`is_palindrome`判断是否为回文的函数，如果`self.is_palindrome(s, startIndex, i)`这一段是回文的话，开始切割进path里，`s[startIndex:i + 1]`注意列表切割是左闭右开的，这里s[0:2]切出来的是[“aa”]然后开始递归，startIndex从i+1开始，跳过本次，最后path回溯

```python
class Solution:
    def partition(self, s: str) -> List[List[str]]:
        res = []
        path = []
        self.backtracking(s, 0, path, res)
        return res
    def backtracking(self, s, startIndex, path, res):
        if startIndex == len(s):
           res.append(path[:])
           return
        for i in range(startIndex, len(s)):
            if self.is_palindrome(s, startIndex, i):
                path.append(s[startIndex:i + 1])
                self.backtracking(s, i + 1, path, res)
                path.pop()
    def is_palindrome(self, s, start, end):
        l = start
        r = end
        while l < r:
            if s[l] != s[r]:
                return False
            l += 1
            r -= 1
        return True
```

**使用[::-1]**

虽然方便，但是性能比上面的写法差好多~

```python
class Solution:
    def partition(self, s: str) -> List[List[str]]:
        res = []
        path = []
        self.backtracking(s, 0, path, res)
        return res
    def backtracking(self, s, startIndex, path, res):
        if startIndex == len(s):
           res.append(path[:])
           return
        for i in range(startIndex, len(s)):
            if s[startIndex:i + 1] == s[startIndex:i + 1][::-1]:
                path.append(s[startIndex:i + 1])
                self.backtracking(s, i + 1, path, res)
                path.pop()
```

