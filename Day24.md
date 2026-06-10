## Day24

### [买卖股票的最佳时机 II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

将最终利润分解成每天为单位，而不是从第0天到第3天这样整体去思考，比如股票，下面表格第一行是股票价格，第二行是每天理论，贪心，只收集每天的正利润： 4 + 5 + 3 = 12 **局部最优：收集每天的正利润 全局最优：求得最大利润**

| 7    | 1    | 5    | 10   | 3    | 6    | 4    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|      | -6   | 4    | 5    | -7   | 3    | -2   |

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        res = 0
        for i in range(1, len(prices)):
            res += max(prices[i] - prices[i - 1], 0)
        return res
```

### [跳跃游戏](https://leetcode.cn/problems/jump-game/)

我最开始做这题还一直在纠结该挑几步，一步两步还是三步，跳几步才是最优解，那岂不是又要用到双重for来暴力然后超时，看了题解才明白，跳几步无所谓，关键在于可跳的覆盖范围，将这个问题转化为跳跃覆盖范围究竟可不可以覆盖到终点。

**局部最优：每次取最大跳跃步数（取最大覆盖范围），全局最优：最后得到的最大覆盖范围看能不能到达终点。**

带入数据nums = [3, 2, 1, 0, 4]。i = 0, 0 + 3 = 3 cover＝3,i = 1 1 +2 = 3, 2 + 1, 3 + 0...cover始终到不了4，最终返回False，带入数据nums = [2, 3, 1, 1, 4]。i = 0, 0 + 2 = 2 cover = 2, i = 1, 1 + 3 = 4, cover = 4,满足条件`if cover >= len(nums) - 1:`返回True。

```python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        cover = 0
        if len(nums) == 1: return True
        i = 0
        while i <= cover:
            cover = max(i + nums[i], cover)
            if cover >= len(nums) - 1: return True
            i += 1
        return False
```

### [跳跃游戏 II](https://leetcode.cn/problems/jump-game-ii/)

这题需要返回的是最小跳跃次数，如果数组里只有一个元素不需要跳跃，步数为0，i是记录当前位置的下标，count是记录步数，cover是当前能够覆盖的最远举例， `while i <= cover:`是当前位置小于等于当前能够覆盖的最远距离时循环，`for i in range(i, cover+1):`遍历从当前位置到当前能够覆盖的最远距离之间的所有位置，`cover = max(nums[j] + j, cover)`更新能够覆盖的最远举例，`if cover >= len(nums) - 1:`如果当前能够覆盖的最远举例达到或者超过数组里的最后一个位置，那么直接返回count + 1

```python
class Solution:
    def jump(self, nums: List[int]) -> int:
        if len(nums) == 1: return 0
        i, count, cover = 0, 0, 0
        while i <= cover:
            for j in range(i, cover + 1):
                cover = max(nums[j] + j, cover)
                if cover >= len(nums) - 1:
                    return count + 1
            count += 1
```

和上一个解法一样如果nums里只有一个元素就不用跳了，直接返回0，`curcover`是记录这一步能跳到的最远位置，而`nextcover`则是记录下一步能跳到的最远位置，`count`是记录步数,`nextcover = max(nums[i] + i, nextcover)`算出下一步能跳到的最远位置后，如果`i`和`curcover`相等也就是走到了当前这一步能跳到的最远点，说明必须跳一步了~步数加1，`cur_distance = next_distance`然后将下一个能跳到的位置赋值给当前跳到的最远位置，`if nextcover >= len(nums) - 1:`如果下一个能跳到的最远位置已经能到终点了直接结束最后返回步数

```python
class Solution:
    def jump(self, nums: List[int]) -> int:
        if len(nums) == 1: return 0
        curcover, count, nextcover = 0, 0, 0
        for i in range(len(nums)):
            nextcover = max(nums[i] + i, nextcover)
            if i == curcover:
                count += 1
                curcover = nextcover
                if nextcover >= len(nums) - 1:
                    break
        return count
```

### [K 次取反后最大化的数组和](https://leetcode.cn/problems/maximize-sum-of-array-after-k-negations/)

这题写麻了，可能是这会太困了思路有点不清晰。直接讲下思路吧~遍历nums数组，排序这个数组后，nums[i]==0的时候k直接=0，因为0是当前的最小数，我可以一直翻转他，不管反转多少次都是0而我找到0的时候说明前面小于0的存在全部被反转了，而k>=0所以直接k=0，`if nums[i] < 0 and k > 0:`当当前数小于0且k>0的时候翻转当前的数，然后k减1，全部遍历完后再次排序，毕竟原本最小负数翻转后他的正数是很大的，重新排序是为了处理我提交挂掉最多的问题，当k也就是还有需要反转的数而k为奇数的时候，将第一个数也就是最小的那个数翻转，翻转最小数是为了保证最后的总数保持最大，最小的那个比如那个数是 2 反转后也就是-2，对结果影响最小，最后的数组总和也能是最大的，如果是偶数那么就不做处理，直接sum累加，好了我去看题解了~

```
class Solution:
    def largestSumAfterKNegations(self, nums: List[int], k: int) -> int:
        if len(nums) == 0: return 0
        if k == 0: return sum(nums)
        nums.sort()
        for i in range(len(nums)):
            if nums[i] == 0:
                k = 0
            if nums[i] < 0 and k > 0:
                nums[i] *= -1
                k -= 1
        nums.sort()
        if k > 0 and k % 2 != 0:
            nums[0] *= -1
        return sum(nums)
```

**贪心**

- 局部最优：只找数值最小的正整数进行反转，当前数值和可以达到最大
- 全局最优：整个数组和达到最大

**解题步骤**

1. 将数组按照**绝对值**从大到小排序
2. 从前向后遍历，遇到负数将其变为正数，同时k--
3. 如果k还大于0，那么反复转变值最小的元素，将k用完
4. 求和

真优雅啊~

```python
class Solution:
    def largestSumAfterKNegations(self, nums: List[int], k: int) -> int:
        nums.sort(key = lambda x : abs(x), reverse = True)
        for i in range(len(nums)):
            if nums[i] < 0 and k > 0:
                nums[i] *= -1
                k -= 1
        if k % 2 != 0:
            nums[-1] *= -1
        return sum(nums)
```

