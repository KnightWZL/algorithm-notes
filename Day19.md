## Day19

### 组号

**未剪枝优化**

回溯返回条件为`len(path) == k`当满足path里的数量==k的时候，将path加入res里并返回，**要注意append的是`path[:]`而不是`path`**因为如果是path，那么path改变后，所有的path里面的值都会改变，因为加进去的是path的地址，而path[:]相当于拷贝了一份全新的列表添加进去，是独立的新列表。`for i in range(startIndex, n + 1):`这里`startIndex`非常的精妙，我最开始用1写死了，两次for循环全部暴力枚举，重复的已经有过的数据也再次全部遍历，而`startIndex`每次调用的时候传入`i + 1 `则可以跳过本次已经添加过的数，存入的path不会重复，可以避免重复组合，而核心就是`path.pop()`了，回溯的关键，比如当[1, 2]添加完，遇到len(path) == k:添加进res里然后return，之后就是path.pop()了把2弹出去，剩下[1]然后for循环i=3，path=[1, 3]再次遇到len==k，非常精妙~

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        res = []
        self.backtracking(n, k, 1, [], res)
        return res

    def backtracking(self, n, k, startIndex, path, res):
        if len(path) == k:
            res.append(path[:])
            return
        for i in range(startIndex, n + 1):
            path.append(i)
            self.backtracking(n, k, i + 1, path, res)
            path.pop() 
```

**剪枝优化**

`n - (k - len(path)) + 2`是优化的地方，len(path)是已经选择的元素个数，k - len(path)是在看还需要选多少个元素，`n - (k - len(path)) + 1`在集合n中至多要从该起始位置开始遍历，比如n = 4 k = 4，目前已经选取的元素为0(path = 0),n - (k - len(path)) + 1也就是4 - (4 - 0) + 1 = 1，那么只需要遍历一次，有一种答案就够了，[1, 2, 3, 4]，后面从2开始一直到4都是没有意义的，因为后续的len(path)元素数量到不了k=4，就像n=4 k=2的时候，i=4，path=[4]，之后的数只剩下0，是凑不够k=2个的条件的，代入公式的话，path=[0]我们代入公式，4 - （2 - 0）+ 1 = 3，循环条件i只要遍历到3就可以了，4剪枝掉，完美，最后Python的range循环是[ )不包含右边的，需要再+1，所以最后是` n - (k - len(path)) + 2`

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        res = []
        self.backtracking(n, k, 1, [], res)
        return res

    def backtracking(self, n, k, startIndex, path, res):
        if len(path) == k:
            res.append(path[:])
            return
        for i in range(startIndex, n - (k - len(path)) + 2):
            path.append(i)
            self.backtracking(n, k, i + 1, path, res)
            path.pop() 
```

### 组合II

我的思路和组合I一样，注意看题目里只使用数字1-9，所以范围是`for i in range(startIndex, 10):`到10，n是和为这个数的时候就可以添加进res里，所以回溯的返回条件就很清晰了，len(path)==k的时候，sum(path)==n，也就是这个列表里的合加起来==n，那么就满足条件，sum不等于就不做处理，但是他len到了k的范围，还是要进行return，return后跟组合I一样，使用回溯path.pop()弹出，继续循环，递归遍历

```python
class Solution:
    def combinationSum3(self, k: int, n: int) -> List[List[int]]:
        res = []
        self.backtracking(k, n, 1, [], res)
        return res
    def backtracking(self, k, n, startIndex, path, res):
        if len(path) == k:
            if sum(path) == n:
                res.append(path[:])
            return
        for i in range(startIndex, 10):
            path.append(i)
            self.backtracking(k, n, i + 1, path, res)
            path.pop()
```

**剪枝**

太妙了，当curSum > 目标sum后，再往后遍历就没有意义了，直接剪枝，for循环范围的剪枝也和刷题一样直接`9 - (k - len(path)) + 2`

```python
class Solution:
    def combinationSum3(self, k: int, n: int) -> List[List[int]]:
        res = []
        self.backtracking(k, n, 0, 1, [], res)
        return res
    def backtracking(self, k, n, curSum, startIndex, path, res):
        if curSum > n:
            return
        if len(path) == k:
            if curSum == n:
                res.append(path[:])
            return
        for i in range(startIndex, 9 - (k - len(path)) + 2):
            curSum += i
            path.append(i)
            self.backtracking(k, n, curSum, i + 1, path, res)
            path.pop()
            curSum -= i
```

### 电话号码的字母组合

**回溯**

回溯条件`if index == len(digits):`当index=数字输入的位数时，将s添加进res列表里然后return，`digit = int(digits[index])`来获取当前位置的数字，用获取到的数字`letter = self.digitList[digit]`来获取对应数字下的字母，开始递归回溯，`for i in range(len(letter)):`遍历每一个字母，`self.backtracking(digits, index + 1)`开始处理下一个数字，`self.s = self.s[:-1]`删除字符串最后一位也是最关键的一步，回溯

```python
class Solution:
    def __init__(self):
        self.digitList = ["", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"]
        self.s = ""
        self.res = []
    def backtracking(self, digits, index):
        if index == len(digits):
            self.res.append(self.s)
            return
        digit = int(digits[index])
        letter = self.digitList[digit]
        for i in range(len(letter)):
            self.s += letter[i]
            self.backtracking(digits, index + 1)
            self.s = self.s[:-1] 
    def letterCombinations(self, digits: str) -> List[str]:
        if len(digits) == 0:
            return self.res
        self.backtracking(digits, 0)
        return self.res         
```

**回溯**精简

```python
class Solution:
    def __init__(self):
        self.digitList = ["", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"]
    def backtracking(self, digits, index, s, res):
        if index == len(digits):
            res.append(s)
            return
        digit = int(digits[index])
        letters = self.digitList[digit]
        for letter in letters:
            self.backtracking(digits, index + 1, s + letter, res)
    def letterCombinations(self, digits: str) -> List[str]:
        res = []
        if len(digits) == 0:
            return res
        self.backtracking(digits, 0, "", res)
        return res         
```

