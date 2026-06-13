## Day29

### [不同路径](https://leetcode.cn/problems/unique-paths/)

**暴力**dfs——超时

机器人每次只能向下或者向右移动一步，那么机器人走过的路径可以抽象为一颗二叉树，而叶子节点就是终点，当`if i == m and j == n:`到达终点后返回1，相当于找到了叶子节点

```python
class Solution:
    def dfs(self, i, j, m, n):
        if i > m or j > n:
            return 0
        if i == m and j == n:
            return 1
        return self.dfs(i + 1, j, m, n) + self.dfs(i, j + 1, m, n)
    def uniquePaths(self, m: int, n: int) -> int:
        return self.dfs(1, 1, m, n)
```

**暴力**递归——超时

原理和上面的解法差不多，但是代码更加简洁优雅~退出递归的条件是`if m == 1 or n == 1:`当行或者列有一个==1的情况下返回1，因为当他们有一个到达1的时候，只剩下一条路径，没有其他分叉口了，一直往起始点1的方向出发就可以到达终点了，非常妙~只不过和上题一样这样解会导致时间复杂度变成指数级别导致超时

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        if m == 1 or n == 1: return 1
        return self.uniquePaths(m - 1, n) + self.uniquePaths(m, n - 1)
```

**动态规划**

动态五部曲：

1. `dp[i][j]`数组表示从(0, 0)出发到(i, j)有`dp[i][j]`条不同的路径
2. 递推公式：想求`dp[i][j]`只能从两个方向来推导，即`dp[i - 1][j]`和`do[i][j - 1]`，因为从左上角到右上角，肯定是从上往下从左到右的顺序，而不会是从右或者从下这两个方向，此时再回顾一下`dp[i - 1][j]`表示啥，是从(0, 0)的位置到(i - 1, j)有几条路径，`dp[i][j - 1]`同理。那么很自然，递推公式为`dp[i][j] = dp[i - 1][j] + dp[i][j - 1]`
3. 初始化，首先`dp[i][0]`一定都是1，因为从(0, 0)位置到(i, 0)路径只有一条，`dp[0][j]`同理，`dp[i][0]`和`dp[0][j]`初始化初始化为1
4. 这里要看一下递推公式为`dp[i][j] = dp[i - 1][j] + dp[i][j - 1]`,`dp[i][j]`都是从左和上两个方向过来的，那么从左到右一层一层遍历就可以了
5. 举例推导dp数组 m = 3, n = 7, 推导数值如下：

| 1    | 1    | 1    | 1    | 1    | 1    | 1    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1    | 2    | 3    | 4    | 5    | 6    | 7    |
| 1    | 3    | 6    | 10   | 15   | 21   | 28   |

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        dp = [[0] * n for _ in range(m)] 
        for i in range(0, m):
            dp[i][0] = 1
        for i in range(0, n):
            dp[0][i] = 1
        for i in range(1, m):
            for j in range(1, n):
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
        return dp[m - 1][n - 1]
```

**动态规划**版本二

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        dp = [1] * n
        for i in range(1, m):
            for j in range(1, n):
                dp[j] += dp[j - 1]
        return dp[n - 1]
```

### [不同路径 II](https://leetcode.cn/problems/unique-paths-ii/)

和上题不同的是多了障碍物，递归公式依旧是`dp[i][j] = dp[i - 1][j] + dp[i][j - 1]`要考虑的就是有障碍物的情况下如何解题，创建dp数组，当原数组位置是1也就是有障碍物的时候，当前dp数组不赋值1，而是直接break整个循环，后续的值保持0，只有没遇到障碍物的值是1，第一行第一列都赋值完后~开始遍历dp数组，如果原数组ij位置是1也就是有障碍物的话，跳过本次处理，也就是保持dp数组初始化时的值0，没遇到障碍物的话正常处理

```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        m = len(obstacleGrid)
        n = len(obstacleGrid[0])
        if obstacleGrid[m - 1][n - 1] == 1 or obstacleGrid[0][0] == 1:
            return 0
        dp = [[0] * n for _ in range(m)]
        for i in range(m):
            if obstacleGrid[i][0] == 0:
                dp[i][0] = 1
            else:
                break
        for i in range(n):
            if obstacleGrid[0][i] == 0:
                dp[0][i] = 1
            else:
                break
        for i in range(1, m):
            for j in range(1, n):
                if obstacleGrid[i][j] == 1:
                    continue
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
        return dp[m - 1][n - 1]
```

### [整数拆分](https://leetcode.cn/problems/integer-break/)

`dp[i]`分拆数字i，可以得到的最大乘积为`dp[i]`而最大乘积是怎么得到的呢，从1遍历到j，有两种渠道可以得到`dp[i]`一个是`j * (i - j)`直接相乘，一个是`j * dp[i - j]`相当于是拆分`(i - j)`递推公式：`dp[i] = max(dp[i], (i - j) * j, dp[i - j] * j)`初始化`dp[2] = 1`遍历顺序`dp[i]`是依靠`dp[i - j]`的状态，所以遍历一定是i从前往后遍历，先有`dp[i - j]`再有`dp[i]`推导dp数组n = 10。`i // 2 + 1`是因为最优分割一定会出现在 j <= i // 2的前半段，后半段几乎对称，和前面算出来的max完全一样，比如我带入n = 4,i = 3 j = 1 dp[i] = max(0, 2, 2) = 2;i = 4 j = 1 dp[i] = max(0, 3, 2)  = 3;j = 2 dp[i] = max(3, 4, 2) = 4。此时最终dp[n] = 4 但是如果不分割的话，再走下 j = 3 的情况 dp[i] = max(4, 3, 0) 还是 = 4，最大值已经在 i // 2 + 1之前出现过了~分隔后剩余部分不会成为最大值~

| 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1    | 2    | 4    | 6    | 9    | 12   | 18   | 27   | 36   |

```python
class Solution:
    def integerBreak(self, n: int) -> int:
        dp = [0] * (n + 1)
        dp[2] = 1
        for i in range(3, n + 1):
            for j in range(1, i // 2 + 1):
                dp[i] = max(dp[i], (i - j) * j, dp[i - j] * j)
        return dp[n]
```

### [不同的二叉搜索树](https://leetcode.cn/problems/unique-binary-search-trees/)

算法真的精妙了，让我自己来想这题真推导不出这公式，太妙了！！~等这次刷完一遍代码随想录后面准备再重新二刷下，也可能会先刷力扣100，题外话先讲到这。dp数组含义dp[i] ： 1到i为节点组成的二叉搜索树的个数为dp[i]。递推公式：`dp[i] += dp[j - 1] * dp[i - j]`，j-1 为j为头结点左子树节点数量，i-j 为以j为头结点右子树节点数量。dp初始化定义上空节点也是颗二叉树，递归公式上来讲，dp[以j为头结点左子树节点数量] * dp[以j为头结点右子树节点数量] 中以j为头结点左子树节点数量为0，也需要dp[以j为头结点左子树节点数量] = 1，否则乘法结果就都变成0了，所以初始化dp[0] = 1。确定遍历顺序从递归公式：`dp[i] += dp[j - 1] * dp[i - j]可以看出`节点数为i的状态是依靠之前的节点数的状态，那么遍历i里面每一个数作为头节点的状态，用j来遍历。举例推导dp数组 n = 5

| 0    | 1    | 2    | 3    | 4    | 5    |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 1    | 1    | 2    | 5    | 14   | 42   |

```python
class Solution:
    def numTrees(self, n: int) -> int:
        dp = [0] * (n + 1)
        dp[0] = 1
        for i in range(1, n + 1):
            for j in range(1, i + 1):
                dp[i] += dp[j - 1] * dp[i - j]
        return dp[n]
```

