## Day30

### [01背包理论基础之一](https://kamacoder.com/problempage.php?pid=1046)

> 有n件物品和一个最多能背重量为w的背包，第i件物品的重量是weight[i]，得到的价值是value[i]。每件物品只能用一次，求解将哪件物品装入背包里的物品价值总和最大。

核心是每一件物品只有两个状态，**取或者不取**，所以可以用回溯法来暴力搜索出所有情况。但是暴力法是指数级的时间复杂度，进而才需要动态规划的解法来优化。

|       | 重量 | 价值 |
| ----- | ---- | ---- |
| 物品0 | 1    | 15   |
| 物品1 | 3    | 20   |
| 物品2 | 4    | 30   |

动态规划五部曲

1. 确定数组下标

   这题需要用二位数组，因为有两个维度需要分别表示：物品和背包容量

2. 确定递推公式

   参考5推导dp数组，来求`dp[1][4]`，两种情况，分别是放物品1和不放物品1，我们要取最大值也就是`dp[1][4] = max(dp[0][4], dp[0][1] + 物品1 的价值)`

   - 不放物品：背包容量为j，里面不放物品i的最大价值是`dp[i - 1][j]`
   - 放物品：背包空出物品i的容量后，背包容量为`j - weight[i]`, `dp[i - 1][j - weight[i] + value[i](物品i的价值)]`，就是背包放物品i的最大价值

   递归公式： `dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])`

3. dp数组初始化

   如果背包容量j为0，那么`dp[i][0]`无论取哪些物品，背包价值总和一定为0，在看其他情况，状态转移方程 `dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])` 可以看出 i 是由 i-1 推导出来，那么 i 为 0 的时候就一定要初始化。`dp[0][j]`也就是i为0的时候各个容量背包所能存放的最大价值，那么当`j < weight[0]`时，`dp[0][j]`应该是0，因为背包容量比编号0的物品重量还小，当`j >= weight[0]`时，`dp[0][j]`应该是`value[0]`，因为背包容量足够放编号0的物品。**当`j < weight[i]`的时候，`dp[i][j]`装不下这个物品，那么就继承`dp[i - 1][j]`的值**。

   ```python
   dp = [[0] * (bagweight + 1) for _ in range(n)]
   for j in range(weight[0], bagweight + 1):
       dp[0][j] = value[0]
   ```

4. 确定遍历顺序

   先遍历物品再遍历背包重量比较好理解，反过来也是可以的，只不过先遍历物品更好理解

5. 举例推导dp数组

   | 下标  | 0    | 1    | 2    | 3    | 4    |
   | ----- | ---- | ---- | ---- | ---- | ---- |
   | 物品0 | 0    | 15   | 15   | 15   | 15   |
   | 物品1 | 0    | 15   | 15   | 20   | 35   |
   | 物品2 | 0    | 15   | 15   | 20   | 35   |

```python
n, bagweight = map(int, input().split())
weight = list(map(int, input().split()))
value = list(map(int, input().split()))
dp = [[0] * (bagweight + 1) for _ in range(n)]
for j in range(weight[0], bagweight + 1):
    dp[0][j] = value[0]
for i in range(1, n):
    for j in range(bagweight + 1):
        if j < weight[i]:
            dp[i][j] = dp[i - 1][j]
        else:
            dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])
print(dp[n - 1][bagweight])
```

### [01背包理论基础之二](https://kamacoder.com/problempage.php?pid=1046)

二维dp数组的递推公式为： `dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])`一维dp数组其实就是上一层dp[i - 1]这一层拷贝的dp[i]所以在上面递推公式的基础上去掉i这个维度就好~递推公式为`dp[j] = max(dp[j], dp[j - weight[i]] + value[i])`dp[j]为容量j的背包所背最大价值，dp[j]可以通过dp[j - weight[i]]推导出来，dp[j - weight[i]]表示容量为j - weight[i]的背包所背的最大价值，`dp[j - weight[i]] + value[i]`表示容量为[j - 物品容量]的背包加上物品i的价值。也就是容量为j的背包放入物品i了之后的价值即：dp[j]。**遍历顺序**和二维dp写法里的顺序不一样，**一维是倒序遍历，为了保证i只被放入一次**，如果是正序遍历，那么物品0就会被重复加入多次，举个例子：物品0的重量weight[0] = 1，价值value[0] = 15

- 正序遍历

  dp[1] = dp[1 - weight[0]] + value[0] = 15

  dp[2] = dp[2 - weight[0]] + value[0] = 30

  > 此时dp[2]就已经是30的，意味着物品0被放入了两次，所以不能正序遍历

- 倒序遍历

  dp[2] = dp[2 - weight[0]] + value[0] = 15 （dp数组已经都初始化为0）

  dp[1] = dp[1 - weight[0]] + value[0] = 15

  从后往前循环，每次取得状态不会和之前取得状态重合，这样每次物品就只取一次了

推导dp数组

| 用物品0遍历背包 | 0    | 15   | 15   | 15   | 15   |
| --------------- | ---- | ---- | ---- | ---- | ---- |
| 用物品1遍历背包 | 0    | 15   | 15   | 20   | 35   |
| 用物品2遍历背包 | 0    | 15   | 15   | 20   | 35   |

`for j in range(bagweight, weight[i] - 1, -1):`注意这里倒序循环遍历到`weight[i]`，后面我去看了二维解法，有个关键的判断：`if j < weight[i]:`**当`j < weight[i]`的时候，`dp[i][j]`装不下这个物品，那么就继承`dp[i - 1][j]`的值**

```python
n, bagweight = map(int, input().split())
weight = list(map(int, input().split()))
value = list(map(int, input().split()))
dp = [0] * (bagweight + 1)
dp[0] = 0
for i in range(n):
    for j in range(bagweight, weight[i] - 1, -1):
        dp[j] = max(dp[j], dp[j - weight[i]] + value[i])
print(dp[bagweight])
```

### [分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

> 本题要求集合里是否出现总和为 sum / 2 的子集，既有一个只能装重量为 sum / 2 的背包，商品为数字，这些数字能不能把这个背包装满，那每一件商品是数字的话，对应的重量和价值是多少呢，一个数字只有一个维度，既重量等于价值。当数字可以装满承载重量为 sum / 2 的背包时，这个背包价值也是 sum / 2，那么价值最大的时候是多少呢，如果最大价值是 sum / 2，说明商品被装满了，因为商品是数字，重量和对应的价值是相同的。

1. 确定dp数组以及下标的含义

   dp[j]表示容量（所能装的重量）为j的背包，所背的物品价值最大可以为 dp[i]，如果背包所载重量为 target，dp[target] 就是装满背包之后的总价值，因为本题中每一个元素的数值即是重量，也是价值，所以当 dp[target] == target 的时候，背包就装满了，拿输入数组 [1, 5, 11, 5] 举例，dp[7] 的时候只能等于6，因为背包这个时候只能放进1 和 5，dp[6] 就可以等于6，放进 1 和 5，那么 dp[6] == 6，说明背包装满了。

2. 确定递推公式

   01背包递推公式为`dp[j] = max(dp[j], dp[j - weight[i]] + value[i])`本题相当于背包里放入数值，那么物品 i 的重量是 nums[i]，其价值也是 nums[i] 所以递推公式：`dp[j] = max(dp[j], dp[j - nums[i]] + nums[i])`

3. dp数组初始化

   一维 dp 如何初始化详细在01背包理论基础之二里。从 dp[j] 的定义来看首先 dp[0] 一定是0。如果题目给的价值**都是正整数**那么非0下标都**初始化为0**就可以了，如果题目给的价值**有负数**，那么非0下标就要初始化为**负无穷**。**这样才能让dp数组在递推的过程中取得最大的价值，而不是被初始值覆盖了**

4. 确认遍历顺序

   一维dp，倒序遍历，详细原理也在01背包理论基础之二里。

5. 举例推导dp数组

   输入[1, 5, 11, 5]，target = sum(nums) / 2 = 11

   | 下标 | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   |
   | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
   | 0    | 1    | 1    | 1    | 1    | 1    | 5    | 6    | 6    | 6    | 6    | 10   | 11   |

`dp = [0] * 10001`是因为nums[i]的值 <=100 ，nums.lenght长度 <=200 ,总和 100 * 200 = 2000 不会大于 2000 ，背包最大只需要其中一半 sum / 2 就好，所以 10001 大小就可以了，如果 sum 整除 2 为 1 的话直接返回 False。内层循环倒序遍历的时候，终点依旧在num也就是（weight[i])，我重复下前两题里提到过的一个关键的判断：`if j < weight[i]:`**当`j < weight[i]`的时候，`dp[i][j]`装不下这个物品，那么就继承`dp[i - 1][j]`的值**

```python
class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        _sum = 0
        dp = [0] * 10001
        for num in nums:
            _sum += num
        if _sum % 2 == 1:
            return False
        target = _sum // 2
        for num in nums:
            for j in range(target, num - 1, -1):
                dp[j] = max(dp[j], dp[j - num] + num)
        if dp[target] == target:
            return True
        return False
```

