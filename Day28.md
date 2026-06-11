## Day28

动态规划中每一个状态一定是由上一个状态推导出来的，贪心是没有状态推导直接从局部选最优的。动态规划五部曲
1. 确定dp数组（dp table）以及下标含义
2. 确定递推公式
3. dp数组如何初始化
4. 确定遍历顺序
5. 举例推导dp数组

### [斐波那契数](https://leetcode.cn/problems/fibonacci-number/)

创建 dp talbe `dp = [0] * (n + 1)`初始化 dp 数组`dp[0] = 0 dp[1] = 1`从前向后遍历`for i in range(2, len(dp)):`确定递归公式、状态转移公式`dp[i] = dp[i - 1] + dp[i - 2]`

```python
class Solution:
    def fib(self, n: int) -> int:
        if n == 0:
            return 0
        dp = [0] * (n + 1)
        dp[0] = 0
        dp[1] = 1
        for i in range(2, len(dp)):
            dp[i] = dp[i - 1] + dp[i - 2]
        return dp[-1]
```

优化：只想要维护两个数值就可以了，不需要记录整个序列

```python
class Solution:
    def fib(self, n: int) -> int:
        if n == 0:
            return 0
        dp = [0, 1]
        for i in range(2, n + 1):
            sumN = dp[0] + dp[1]
            dp[0] = dp[1]
            dp[1] = sumN
        return dp[1]
```

### [爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

有了上题 dp 的经验~这题直接AC，原理和上题差不多~

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        if n <= 1: return n
        dp = [1, 1]
        for i in range(2, n + 1):
            sumN = dp[0] + dp[1]
            dp[0] = dp[1]
            dp[1] = sumN
        return dp[1]
```

### [使用最小花费爬楼梯](https://leetcode.cn/problems/min-cost-climbing-stairs/)

创建 dp 数组 `dp = [0] * (len(cost) + 1)` dp[i]的定义：到达第i台阶所花费的最少体力为dp[i]，有两个途径可以得到dp[i]，因为每次爬楼梯是可以选择爬一楼还是爬两楼的，所以两个途径分别是 dp[i - 1] 跳到 dp[i] 需要花费 dp[i - 1] + cost[i - 1] 另一个是 dp[i - 2] 跳到 dp[i] 需要花费 dp[i - 2] + cost[i - 2] 那么要选哪个跳呢，一定要选最小的~`dp[0] = 0 dp[1] = 0` dp数组初始化，因为题目有说可以从下标为1或者下标为0的台阶开始爬楼梯，所以刚开始是不花费的，直接给这两个台阶初始化 0 就好，递归顺序是从前往后遍历 cost 数组的，推导dp数组

| cost | 1    | 100  | 1    | 1    | 1    | 100  | 1    | 1    | 100  | 1    | 楼顶 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 下标 | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| dp   | 0    | 0    | 1    | 2    | 2    | 3    | 3    | 4    | 4    | 5    | 6    |

```python
class Solution:
    def minCostClimbingStairs(self, cost: List[int]) -> int:
        dp = [0] * (len(cost) + 1)
        dp[0] = 0
        dp[1] = 0
        for i in range(2, len(cost) + 1):
            dp[i] = min(cost[i - 1] + dp[i - 1], cost[i - 2] + dp[i - 2])
        return dp[-1]
```

