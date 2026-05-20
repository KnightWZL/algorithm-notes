## Day10

### 逆波兰表达式求值

没看题解自己写出来的，嘻嘻~要注意的有两点，一点是`pop_2 - pop_1`和`pop_2 / pop_1`，栈是先进后出，pop_2要放在前面，第二点是除法要进行一个`int(pop_2 / pop_1)`向下取整的操作

```python
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:
        res = []
        fu = ["+", "-", "*", "/"]
        for i in tokens:
            if i in fu:
                pop_1 = res.pop()
                pop_2 = res.pop()
                if i == "+":a
                    res.append(pop_1 + pop_2)
                elif i == "-":
                    res.append(pop_2 - pop_1)
                elif i == "*":
                    res.append(pop_1 * pop_2)
                elif i == "/":
                    res.append(int(pop_2 / pop_1))
                       
            else:
                res.append(int(i))
        return res[0]
```

### 滑动窗口最大值

看到这题我直接就是暴力起手最后也是超出时间限制寄掉了

38 / 52 个通过的测试用例

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        maxs = []
        for i in range(len(nums) - k + 1):
            left, right = i, i + k
            max_num = -10 ** 4 - 1
            for j in range(left, right):
                max_num = max(nums[j], max_num)
            maxs.append(max_num)

        return maxs
```

**单调队列**

`while res and nums[i] > res[-1]: res.pop() res.append(nums[i])`所有小于新元素的都要被移除`if i >= k and nums[i - k] == res[0]:`是滑动窗口的核心，比如[5,1,2,3,]当i=3k=3的时候满足判断条件，用`res.popleft()`把5从`res`最里面最左边也就是最大值移出去来实现窗口的滑动，`if i >= k - 1:`当k=3i>=2的时候，每次循环就可以往`maxs`添加最大值`res[0]`

```python
from collections import deque
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        maxs = []
        res = deque()
        for i in range(len(nums)):
            while res and nums[i] > res[-1]:
                res.pop()
            res.append(nums[i])
            if i >= k and nums[i - k] == res[0]:
                res.popleft()
            if i >= k - 1:
                maxs.append(res[0])
        return maxs
```

### 前 K 个高频元素

我也想到了字典解决这道题的思路，实现过程看了题解真的太精妙了~`index_dict[count_dict[key]].append(key)`将key和value反转，或者说交替存入`index_dict`里之后进行排序，将排序后从后面把最大的先`+=`进列表中`res +=  index_dict[key[-1]]`这里不用`append`是因为最大次数可能一样，比如1和2次数一样`append`添加的就是[1,2]，最后结果可能就是[3,[1,2]]，而`+=`的结果就是[3,1,2]他是展开拼接的，`append`就像是整体加进去的，然后`cnt < k`题解里是`cnt != k`我想着假设k=1，只要同时出现两个或者两个以上的最大次数，比如两个那加完后cnt=2，然后cnt!=k导致循环在遍历完`index_dict`前无法正确结束循环，所以我改成了`cnt < k`,`cnt += len(index_dict[key[-1]])`也是同理，正常情况+1，同时出现两个最大次数那就+2，最后要记得`key.pop()`弹出最多的那个次数

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        count_dict = defaultdict(int)
        for num in nums:
            count_dict[num] += 1
        index_dict = defaultdict(list)
        for key in count_dict.keys():
            index_dict[count_dict[key]].append(key)
        key = list(index_dict.keys())
        key.sort()
        res = []
        cnt = 0
        while index_dict and cnt < k:
            res +=  index_dict[key[-1]]
            cnt += len(index_dict[key[-1]])
            key.pop()
        return res[:k]
```

