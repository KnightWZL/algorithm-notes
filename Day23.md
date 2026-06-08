## Day23

### [分发饼干](https://leetcode.cn/problems/assign-cookies/)

直接AC，过会去看看题解~我先说下我的思路，总的就是排序后先把`g[g_right]`也就是最大块的饼干喂给`s[s_right]`胃口最大的那个孩子~结束。

| 执行用时分布 | 消耗内存分布 |
| ------------ | ------------ |
| 23ms         | 21.87MB      |
| 击败56.07%   | 击败21.19%   |

```python
class Solution:
    def findContentChildren(self, g: List[int], s: List[int]) -> int:
        total = 0
        g.sort()
        s.sort()
        g_right, s_right = len(g) - 1, len(s) - 1
        while g_right >= 0 and s_right >= 0:
            if s[s_right] >= g[g_right]:
                total += 1
                g_right -= 1
                s_right -= 1
            else:
                g_right -= 1
        return total
```

思路和我一样~排序后大饼干优先喂给胃口最大的孩子`index >= 0`防止越界~

| 执行用时分布 | 消耗内存分布 |
| ------------ | ------------ |
| 27ms         | 21.80MB      |
| 击败32.37%   | 击败38.68%   |

```python
class Solution:
    def findContentChildren(self, g: List[int], s: List[int]) -> int:
        res = 0
        g.sort()
        s.sort()
        index = len(s) - 1
        for i in range(len(g) - 1, -1, -1):
            if index >= 0 and s[index] >= g[i]:
                res += 1
                index -= 1
        return res
```

从最小胃口孩子开始满足，满足一个孩子index就+1，不满足那么i就继续遍历，越往后饼干越大，`index < len(g)`防止越界~

| 执行用时分布 | 消耗内存分布 |
| ------------ | ------------ |
| 23ms         | 21.86MB      |
| 击败56.07%   | 击败22.88%   |

```python
class Solution:
    def findContentChildren(self, g: List[int], s: List[int]) -> int:
        g.sort()
        s.sort()
        index = 0
        for i in range(len(s)):
            if index < len(g) and s[i] >= g[index]:
                index += 1
        return index
```

### [摆动序列](https://leetcode.cn/problems/wiggle-subsequence/)

AC了，但是过程可能优点不优雅，因为边界处理问题在提交后的几个测试用例里没AC，接下来讲下我的思路，过会再去看题解~题目有提到仅有一个元素或者含两个不等元素的序列也视作摆动序列，对len(nums)为1的情况做下处理。接下来我的while是为了将right指针移动到两个数不同的时候，因为他们一直是相同的话，`if nums[right] - nums[right - 1] > 0`无法正确根据题目第一个差（如果存在的话）可能是正数或负数。将flag分配为True或False来控制正负数差的判断，移动完后要注意`if right == len(nums): return 1`进行一个判断操作，这就是我前面提到的提交后踩到的边界坑，测试用例nums = [0, 0, 0]这种情况，我在while的时候我`right < len(nums)`做了个边界处理，但是忘记了，上面那种全是相等数的情况，所以在while结束后加上一个判断，right指针==len(nums)的话返回1~后面的for就没什么要说明的了，根据题目意思判断摆动序列，因为已经判断出第一个差是正数还是负数了，后面就`if flag:`感觉正数差或者负数差来进行处理就好，处理完后对flag进行一个反转，另外是要和相邻的两个数进行对比这点要注意

| 执行用时分布 | 消耗内存分布 |
| ------------ | ------------ |
| 0ms          | 18.99MB      |
| 击败100.00%  | 击败90.62%   |

```python
class Solution:
    def wiggleMaxLength(self, nums: List[int]) -> int:
        if len(nums) == 1:
            return 1
        res = 0
        flag = True
        right = 1
        while right < len(nums)  and nums[right - 1] == nums[right]:
            right = right + 1
        if right == len(nums): return 1
        if nums[right] - nums[right - 1] > 0:
            flag = True
        else:
            flag = False
        for i in range(right, len(nums)):
            if flag:
                if (nums[i] - nums[i - 1]) > 0:
                    res += 1
                    flag = False 
            else:
                if (nums[i] - nums[i - 1]) < 0:
                    res += 1
                    flag = True 
        return res + 1
```

使用pre记录前一对元素的差值来判断当前元素差值是要大于0还是小于0也就是有摆动的时候增加峰值的个数，太优雅了~

| 执行用时分布 | 消耗内存分布 |
| ------------ | ------------ |
| 0ms          | 19.01MB      |
| 击败100.00%  | 击败86.44%   |

```python
class Solution:
    def wiggleMaxLength(self, nums: List[int]) -> int:
        if len(nums) <= 1:
            return 1
        prenum, curnum, res = 0, 0, 1
        for i in range(len(nums) - 1):
            curnum = nums[i + 1] - nums[i]
            if (prenum >= 0 and curnum < 0) or (prenum <= 0 and curnum > 0):
                res += 1
                prenum = curnum
        return res
```

### [最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

**暴力**

首先想到的是两层循环直接暴力->超时...->尝试剪枝优化->超出时间限制202 / 210 个通过的测试用例->放弃暴力，woc~有些测试用例里的nums超级大

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        maxnum = float('-inf')
        for i in range(0, len(nums)):
            total = 0
            for j in range(i, len(nums)):
                total += nums[j]
                maxnum = max(maxnum, total)
        return maxnum
```

**贪心**

核心在于`if total <= 0:`如果累加的值<=0，那么就应该从nums[i+1]开始进行累加，因为负数的total只会拖累总和，比如遇到[-1, -2, -3, 1]的时候total每次加完负数就为0，不断调整最大子序和区间的起始位置，到最后一位时直接将1加进去就可以了，一次循环结束，就不用两次循环~

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        maxnum = float('-inf')
        total = 0
        for i in range(0, len(nums)):
            total += nums[i]
            if maxnum < total:
                maxnum = total
            if total <= 0:
                total = 0
        return maxnum
```

