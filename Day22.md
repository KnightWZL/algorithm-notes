## Day22

### [非递减子序列](https://leetcode.cn/problems/non-decreasing-subsequences/)

最开始我的思路其实是排序后再解，但是后面发现那样就无法按照题目意思正确解答出结果了，去掉排序后重新更改判断条件，但是无法通过`nums = [1,2,3,4,5,6,7,8,9,10,1,1,1,1,1]`这条测试用例上，那时候我的判断条件是`nums[i] >= nums[i - 1]`，想和相邻的元素比较，但是一旦出现类似上面的用例，就无法AC，会漏掉一些元素，比如`nums = [1, 10, 2]`这种情况我用我最开始的判断最后结果会是[1, 10]但是正确答案里还有个[1, 2]给漏掉了，因为我比较的是上一个相邻元素，2 > 10 False，所以没有成功将正确结果加进去，将条件改成`nums[i] >= path[-1]`最后成功AC。再说下做这题时遇到的细节，去重，没有进行去重操作的话，像这题给出的用例1中`nums = [4,6,7,7]`在我进行去重前的代码，答案会给出很多重复的比如[4, 6, 7], [4, 6, 7]...这种情况，所以需要进行一个去重操作，于是我用到了set()集合，返回最终结果的时候进行一个list()转换回列表，注意在递归时给res添加path的时候，要进行一个tuple()转换成元组的操作，最开始我是直接添加列表报错了，后面才想起来集合只接受哈希也就是不可变类型，比如元组、字符串、数字，于是我将列表转换成`tuple`元组再添加进集合中，解决报错后，说下回溯条件：`if total >= 2:  `需要当path里的元素在两个及两个以上的时候才进行一个添加操作，对了这里path其实也可以直接用set集合，这样就不用进行一个元组转换的操作了...三秒后我回来了：输出结果里会有重复值，比如`[4,6,7,7]`和`[4,7,7]`都是递增子序列，所以path不能用set会去重，另外`if startIndex == len(nums):`是真正的回溯条件，和前面的回溯算法题解法一样，当startIndex==len(nums)的时候开始回溯~判断条件我觉得是这题最关键的地方`if total == 0 or nums[i] >= path[-1]:`后面的那个判断条件在最开始讲了~前面这个判断条件是在`total==0`total是我用来统计path长度用的，不然每次调用len感觉性能上不太行，这里的判断条件也就是path里没有元素的时候将nums里的第一个元素添加进path里，完美。

```python
class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        path = []
        res = set()
        self.backtracking(nums, 0, 0, path, res)
        return list(res)
    def backtracking(self, nums, total, startIndex, path, res):
        if total >= 2:    
            res.add(tuple(path[:]))
        if startIndex == len(nums):    
            return
        for i in range(startIndex, len(nums)):
            if total == 0 or nums[i] >= path[-1]:
                path.append(nums[i])
                total += 1
                self.backtracking(nums, total, i + 1, path, res)
                path.pop()
                total -= 1
```

### [全排列](https://leetcode.cn/problems/permutations/)

这题最关键的核心返回的子列表里的元素不能重复，回溯返回条件为`total == len(nums)`当长度==列表长度的时候进行添加和返回，原本我想用集合的，但是pop()的时候会报错，因为add时添加进的元素会有重复元素，这种情况下会导致pop...不对，集合用的是remove，remove需要指定删除的元素，而add时重复元素又加不进去，被自动去重掉，导致报错，也就无法正常回溯，那么就用列表来解决去重问题，`if nums[i] in path:`判断path是否有当前这个元素，如果有那就跳过本次循环，不添加进path里~完美

执行用时分布0ms击败100.00%

消耗内存分布19.17MB击败92.70%

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        res = []
        path = []
        self.backtracking(nums, 0, 0, path, res)
        return res
    def backtracking(self, nums, total, startIndex, path, res):
        if total == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if nums[i] in path:
                continue
            path.append(nums[i])
            self.backtracking(nums, total + 1, i + 1, path, res)
            path.pop()
```

**题解**

used也是个好写法，又学到了新解法

执行用时分布0ms击败100.00%

消耗内存分布19.21MB击败88.00%

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        res = []
        path = []
        self.backtracking(nums, path, [False] * len(nums), res)
        return res
    def backtracking(self, nums, path, used, res):
        if len(path) == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if used[i]:
                continue
            path.append(nums[i])
            used[i] = True
            self.backtracking(nums, path, used, res)
            path.pop()
            used[i] = False
```

**看完题解后优化前面自己写的回溯**

执行用时分布0ms击败100.00%

消耗内存分布19.12MB击败96.18%

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        res = []
        path = []
        self.backtracking(nums, path, res)
        return res
    def backtracking(self, nums, path, res):
        if len(path) == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if nums[i] in path:
                continue
            path.append(nums[i])
            self.backtracking(nums, path, res)
            path.pop()
```

### [全排列 II](https://leetcode.cn/problems/permutations-ii/)

熟悉的感觉又回来了，自己能AC，但是性能不好，过会看下题解..先说下我的解题思路：这题和上题不一样的地方在于这题给的nums数据里会有重复的元素`nums = [1,1,2]`上题是没有重复的，根据题意在res添加path的地方价加个判断条件`if path not in res:`配合上题刚学到的used方法直接AC~

```python
class Solution:
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        res = []
        self.backtraking(nums, 0, [], [False] * len(nums), res)
        return res
    def backtraking(self, nums, total, path, used, res):
        if total == len(nums):
            if path not in res:
                res.append(path[:])
            return
        for i in range(len(nums)):
            if used[i]:
                continue
            path.append(nums[i])
            used[i] = True
            self.backtraking(nums, total + 1, path, used, res)
            path.pop()
            used[i] = False
```

**题解**

看到排序的时候灵光一闪，自己去试了下，只差`not used[i - 1]`这个条件，真的很妙~原理和我`if path not in res:`操作不同，卡哥是从根源上进行去重，我上面的写法太慢了，not used[i - 1]当上一个重复元素已经添加过了，那我这次就不用再添加了，直接通过本次循环

```python
class Solution:
    def permuteUnique(self, nums):
        nums.sort()
        result = []
        self.backtracking(nums, [], [False] * len(nums), result)
        return result
    def backtracking(self, nums, path, used, result):
        if len(path) == len(nums):
            result.append(path[:])
            return
        for i in range(len(nums)):
            if (i > 0 and nums[i] == nums[i - 1] and not used[i - 1]) or used[i]:
                continue
            used[i] = True
            path.append(nums[i])
            self.backtracking(nums, path, used, result)
            path.pop()
            used[i] = False
```

### [解数独](https://leetcode.cn/problems/sudoku-solver/)

这题真难呀~`row_userd = [set() for _ in range(9)]`先创建`row_userd`列表列表里面有九个集合，`col_userd`和`box_userd`这两个列表也是一样，两层循环遍历`num = board[row][col]`如果num上面已经有数值了~将那个数存进`row_userd[row]`和`col_userd[col]`以及`box_userd[(row // 3) * 3 + col // 3]`里，题目要求：数字 `1-9` 在每一个以粗实线分隔的 `3x3` 宫内只能出现一次。`row // 3`表示把3行分成3行一组，有0 1 2三行0代表第1 2 3行，1代表第4 5 6行，2代表第7 8 9行，确定当前各自在上中下那一行里，`col // 3`同理，将9列分成3列一行，而`(row // 3) * 3`是确定各自的起始编号，比如第0行那一组(这个组来自前面`row // 3`分出来的三行一组) -> 0 * 3 = 0, 第1行那一组 -> 1 * 3 = 3, 第2行那一组 -> 2 * 3 = 6 确定好切割出来的九宫格起始编号后+那按列分组的数最终得出当前格子是属于9个3 * 3小宫格里的第几个。将board里的不是"."的数全部add完后，开始递归，第一个回溯返回条件为`if row == 9:`也就是当行递归到了9的时候返回`True`,`next_row, next_col = (row, col + 1) if col < 8 else (row + 1, 0)`下一个格子的位置，如果列的值 < 8,那么列++，当到8的时候，行的值 + 1,列的值重置回0，非常精妙的写法~学到了。之后开始判断`if board[row][col] != ".":`如果有值就跳过不递归，如果值是"."那么进行递归并返回bool结果`return self.backtraking(next_row, next_col, board, row_userd, col_userd, box_userd)`。接下来开始循环遍历`for num in map(str, range(1, 10)):`尝试填1 - 9，这里map意思是给这个循环里的每个数进行str转型操作，因为`board: List[List[str]]`board里的数都是str字符串型的，所以我们遍历出来的1 - 9也得是str型的，`if num not in row_userd[row] and num not in col_userd[col] and num not in box_userd[(row // 3) * 3 + col // 3]:`判断这个num在`not in row_userd[row]`这行没出现过，这列`not in col_userd[col]`没出现过，这个九宫格里`not in box_userd[(row // 3) * 3 + col // 3]`没有出现过，如果都没问题将这个数写进格子里，另外记录这行这列这个九宫格都用过这个数了，然后递归去尝试填下一个格子~如果一路成功则直接返回~不继续回溯了，因为这题题目又说只有一个唯一解，找到就结束~，则是回溯操作，不成功那就回溯，进入下一个数字继续尝试填写，递归，回溯，所有数都试完全部不行的话返回False，能填满那就是True~

```python
class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        row_userd = [set() for _ in range(9)]
        col_userd = [set() for _ in range(9)]
        box_userd = [set() for _ in range(9)]
        for row in range(9):
            for col in range(9):
                num = board[row][col]
                if num != ".":
                    row_userd[row].add(num) 
                    col_userd[col].add(num) 
                    box_userd[(row // 3) * 3 + col // 3].add(num)
        self.backtraking(0, 0, board, row_userd, col_userd, box_userd)
    def backtraking(self, row, col, board, row_userd, col_userd, box_userd):
        if row == 9:
            return True
        next_row, next_col = (row, col + 1) if col < 8 else (row + 1, 0)
        if board[row][col] != ".":
            return self.backtraking(next_row, next_col, board, row_userd, col_userd, box_userd)
        for num in map(str, range(1, 10)):
            if num not in row_userd[row] and num not in col_userd[col] and num not in box_userd[(row // 3) * 3 + col // 3]:
                board[row][col] = num
                row_userd[row].add(num) 
                col_userd[col].add(num) 
                box_userd[(row // 3) * 3 + col // 3].add(num) 
                if self.backtraking(next_row, next_col, board, row_userd, col_userd, box_userd):
                    return True
                board[row][col] = "."
                row_userd[row].remove(num)
                col_userd[col].remove(num)
                box_userd[(row // 3) * 3 + col // 3].remove(num) 
        return False
```

