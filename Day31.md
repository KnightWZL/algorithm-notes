## Day31

### [最后一块石头的重量II](https://leetcode.cn/problems/last-stone-weight-ii/)

本题其实是尽量让石头分成重量相同的两堆，相撞后剩下的石头就是最小的，一堆石头重量是 sum，那么我们就尽可能的拼成重量为 sum/2 的石头堆，这样剩下的石头堆也是尽可能接近 sum / 2的重量。在计算 target 的时候，target = sum // 2 是向下取整，所以sum - dp[target] 一定是大于等于_ dp[target] 的。那么相撞后剩下的最小石头重量就是`_sum - dp[target] - dp[target]`

```python
class Solution:
    def lastStoneWeightII(self, stones: List[int]) -> int:
        dp = [0] * 3001
        _sum = 0
        for stone in stones:
            _sum += stone
        target = _sum // 2
        for stone in stones:
            for j in range(target, stone - 1, -1):
                dp[j] = max(dp[j], dp[j - stone] + stone)
        return _sum - dp[target] - dp[target]
```

### [目标和](https://leetcode.cn/problems/target-sum/)

分成加法总和和减法总和，假设加法总和为x，那么减法对应的总和就是sum - x。所以我们要求的就是  x - (sum - x) = target 也就是 x = (target + sum) / 2 **此时问题就转化为，用 nums 装满容量为 x 的背包 有几种方法。**这里是 x 是 target_sum, 也就是我们后面要求的背包容量，求 target_sum 也就是 (target + total_sum) // 2 的时候要担心计算过程中向下取整有没有影响，比如 sum 是 5，target 是 2 的话其实就是无解的，`if (target + total_sum) % 2 == 1:`，也就是没有方案返回 0，同时如果 target 的绝对值已经大于sum，那么也是没有方案的`if (target + total_sum) % 2 == 1:`因为每个物品（题目中的1）只能用一次

1. 确定dp数组以及下标含义

   `dp[i][j]`：使用下标为[0, i]的nums[i]能够装满j(包括j)怎么大容量的包，有`dp[i][j]`种方法

2. 确定递推公式

   - 不放物品i：即背包容量为j，里面不放物品i，装满`dp[i - 1][j]`种方法
   - 放物品i：即先空出物品i的容量，背包容量为（j - 物品i容量），放满背包有`dp[i - 1][j -  物品i容量]`种方法

   本题中，物品i容量是nums[i]，价值也是nums[i]

   递推公式：`dp[i][j] = dp[i - 1][j] + dp[i - 1][j - nums[i]]`

   如果`j - nums[i]`小于零，说明背包容量装不下物品i，所以此时装满背包方法等于不放物品i的装满背包方法即`dp[i][j] = dp[i - 1][j]`

3. dp数组初始化

   `dp[2][2]`是由左上方和上方推出，那么二维dp的最上行和最左列一定要初始化。初始化最上行：`dp[0][nums[0]] = 1`其他均为 0 。最左列也要初始化，`dp[i][0]`:背包容量为 0 放物品 0 到物品i，装满有几种方法，都是有一种方法，就是放物品0，即`dp[i][0] = 1`，但也有例外，如果物品数值就是 0 呢，如果有两个物品，物品 0 为0，物品 1 为 0，装满背包容量为0的方法有几种

   - 放 0 件物品
   - 放物品 0
   - 放物品 1
   - 放物品 0 和 物品 1

   此时有四种方法，就是算数组里有 t 个 0，按照组合数量求，即 2 ^ t 次方

   ```python
           numZero = 0
           for i in range(len(nums)):
               if nums[i] == 0:
                   numZero += 1
               dp[i][0] = int(math.pow(2, numZero))
   ```

4. 确定遍历顺序

   在明确递推方向时，当前值是由上方和左上方推出，那么遍历顺序一定是从上到下，从左到右，只有这样才能基于之前的数值做推导

5. 举例推导dp数组

   输入：nums: [1, 1, 1, 1, 1], target: 3

   `target_sum = (target + total_sum) // 2` = ( 3 + 5 ) / 2 = 4

   | 背包容量 | 0    | 1    | 2    | 3    | 4    |
   | -------- | ---- | ---- | ---- | ---- | ---- |
   | 物品0    | 1    | 1    | 0    | 0    | 0    |
   | 物品1    | 1    | 2    | 1    | 0    | 0    |
   | 物品2    | 1    | 3    | 3    | 1    | 0    |
   | 物品3    | 1    | 4    | 6    | 4    | 1    |
   | 物品4    | 1    | 5    | 10   | 10   | 5    |

**二维dp**

```python
class Solution:
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
        total_sum = sum(nums)  
        if abs(target) > total_sum:
            return 0  
        if (target + total_sum) % 2 == 1:
            return 0  
        target_sum = (target + total_sum) // 2
        dp = [[0] * (target_sum + 1) for _ in range(len(nums))]
        dp[0][0] = 1
        if nums[0] <= target_sum:
            dp[0][nums[0]] = 1
        numZero = 0
        for i in range(len(nums)):
            if nums[i] == 0:
                numZero += 1
            dp[i][0] = int(math.pow(2, numZero))
        for i in range(1, len(nums)):
            for j in range(target_sum + 1):
                dp[i][j] = dp[i - 1][j]
                if j >= nums[i - 1]:
                    dp[i][j] += dp[i - 1][j - nums[i]]

        return dp[len(nums)-1][target_sum]
```

**一维dp**

```python
class Solution:
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
        total_sum = sum(nums)
        if (total_sum + target) % 2 == 1:
            return 0
        if abs(target) > total_sum:
            return 0
        target_sum = (total_sum + target) // 2
        dp = [0] * (target_sum + 1)
        dp[0] = 1
        for num in nums:
            for j in range(target_sum, num - 1, -1):
                dp[j] += dp[j - num]
        return dp[target_sum]
```

### [一和零](https://leetcode.cn/problems/ones-and-zeroes/)

```python
class Solution:
    def findMaxForm(self, strs: List[str], m: int, n: int) -> int:
        dp = [[0] * (n + 1) for _ in range(m + 1)]
        for s in strs:
            zoreNum = s.count("0")
            oneNum = len(s) - zoreNum
            for i in range(m, zoreNum - 1, -1):
                for j in range(n, oneNum - 1, -1):
                    dp[i][j] = max(dp[i][j], dp[i - zoreNum][j - oneNum] + 1)
        return dp[m][n]
```

