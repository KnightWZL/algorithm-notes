## Day31

### [1049. 最后一块石头的重量 II](https://leetcode.cn/problems/last-stone-weight-ii/)

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

```python
class Solution:
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
        total_sum = sum(nums)  
        if abs(target) > total_sum:
            return 0  
        if (target + total_sum) % 2 == 1:
            return 0  
        target_sum = (target + total_sum)
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

