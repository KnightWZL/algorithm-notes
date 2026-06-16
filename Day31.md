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

