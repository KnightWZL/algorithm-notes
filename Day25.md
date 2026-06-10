## Day25

### [加油站](https://leetcode.cn/problems/gas-station/)

**自己尝试解**

最开始想以贪心思路解题的，输入 gas =[1,2,3,4,5] 和  gas =[2,3,4]，我最开始解题看到了这两个测试用例，加油站的油量是从小到大的，就想着局部最优解这题，那我就从最后一个加油站开始循环~结果超出时间限制38 / 40 个通过的测试用例，`gas = [0,2,0,0,0,0,0,0,0,0,0,0,0,0...]`有非常非常多元素的一个数组，但是我的思路是想从最后开始遍历，导致直接寄了，明白不能从后往前，我就当彻彻底底的暴力法，过会再去看贪心题解来改`for i in range(n)`的时候，又超出时间限制了``gas = [0,0,0,0,0,0,0,0,0,0,0,0,0,0...,2]`这次他把有效的那个值放到数组最后了，麻了麻了，我去看下题解

```
class Solution:
    def canCompleteCircuit(self, gas: List[int], cost: List[int]) -> int:
        startIndex = 0
        n = len(gas)
        for i in range(len(gas)):   
            total = 0
            startIndex = i
            num = 0
            for i in range(n - 1, -1, -1):
                idx = (i + num) % n
                total += gas[idx]
                if total - cost[idx] < 0:
                    total = 0
                    break
                total -= cost[idx]
                num += 1
            else:
                return startIndex
        return -1
```

**暴力法**

算是找回一点自信了~因为题解里的暴力法也超时了，`total = gas[i] - cost[i]`记录剩余油量，`idx = (i + 1) % n`下一个加油站的索引，我先说下这个%，我的暴力法里也有用到%len(gas)，比如len(gas)=5，里面有5个数，容易出现下标越界的报错，如果用%，4%5=4 5%5=0 6%5=1，这个样子，他是怎样都不会越界的，结果控制在len(gas)的元素个数里，非常的优雅，其实我用%之前也用了个不太优雅的方法来控制行驶一圈的下标，定义一个num=0然后`total += gas[i + num]	if i + num == len(gas):	num -= len(gas)`这个样子~好了进行讲。`while total > 0 and i != idx:`模拟i起点行驶一圈，真的很优雅~`if rest >= 0 and index == i:`当循环行驶一圈index==i后，total还是>=0的情况，说明找到了正确的起始加油站，直接返回起始位置。外层for循环全部遍历结束也没返回正确起始加油站的话直接返回-1

```python
class Solution:
    def canCompleteCircuit(self, gas: List[int], cost: List[int]) -> int:
        n = len(gas)
        for i in range(n):
            total = gas[i] - cost[i]
            idx = (i + 1) % n
            while total > 0 and i != idx:
                total += gas[idx] - cost[idx]
                idx = (idx + 1) % n
            if total >= 0 and idx == i:
                return i
        return -1 
```

**贪心法**

`curSum`记录当前累计的剩余油量，`total`总剩余油量，`startIndex`起始位置

核心在于`if curSum < 0:`一旦当前累计的剩余油量<0说明前面的加油站都不能当做起点，起点直接换成i + 1，一次遍历找到答案。**局部最优：当前累加rest[i]的和curSum一旦小于0，起始位置至少要是i+1，因为从i之前开始一定不行。全局最优：找到可以跑一圈的起始位置**。

```python
class Solution:
    def canCompleteCircuit(self, gas: List[int], cost: List[int]) -> int:
        curSum, total, startIndex = 0, 0, 0
        for i in range(0, len(gas)):
            total += gas[i] - cost[i]
            curSum += gas[i] - cost[i]
            if curSum < 0:
                curSum = 0
                startIndex = i + 1
        if total < 0:
            return -1
        return startIndex
```

### [分发糖果](https://leetcode.cn/problems/candy/)

**贪心**

**局部最优：只要右边评分比左边大，右边的孩子就多一个糖果，全局最优：相邻的孩子中，评分高的右孩子获得比左边孩子更多的糖果**

定义一个新的数组来存放每个该拿到的糖果数量，每人至少1颗，第一次循环从左到右遍历，`if ratings[i] > ratings[i - 1]:`如果当前孩子的分数比上个孩子大，那么`candies[i] = candies[i - 1] + 1`糖果数量也比上个孩子多1，第二次循环从右往左遍历`for i in range(n - 2, -1, -1):`-2是因为，在原本不越界的-1上再-1可以对倒数第一的下标和倒数第二的下标进行比较，`if ratings[i] > ratings[i + 1]:`如果倒数第二的值比倒数第一的值大，那么这个孩子的糖果数量会在当前糖果和右边那个孩子的糖果数量+1中，选最大的那个，首先当前孩子目前的糖果数量是比左边孩子多的因为他分数比左边孩子高，而分数也比右边孩子那么就得比右边孩子糖果多，不能比现在手里的更少这个样子

```python
class Solution:
    def candy(self, ratings: List[int]) -> int:
        n = len(ratings)
        candies = [1] * n
        for i in range(1, n):
            if ratings[i] > ratings[i - 1]:
                candies[i] = candies[i - 1] + 1
        for i in range(n - 2, -1, -1):
            if ratings[i] > ratings[i + 1]:
                candies[i] = max(candies[i], candies[i + 1] + 1)
        return sum(candies)
```

### [柠檬水找零](https://leetcode.cn/problems/lemonade-change/)

用字典存储 5 10 20 后面循环的时候处理各种找零的逻辑判断写完就AC了~

```python
class Solution:
    def lemonadeChange(self, bills: List[int]) -> bool:
        money = {
            5 : 0,
            10 : 0,
            20 : 0
        }
        for bill in bills:
            if bill == 5:
                pass
            elif bill == 10:
                if money[5] < 1:
                    return False
                money[5] -= 1
            else:
                if money[10] >= 1:
                    money[10] -= 1
                    if money[5] < 1:
                        return False
                    money[5] -= 1
                else:
                    if money[5] < 3:
                        return False
                    money[5] -= 3
            money[bill] += 1
        return True
```

**局部最优：遇到账单20，优先消耗美元10，完成本次找零。全局最优：完成全部账单的找零。**

```python
class Solution:
    def lemonadeChange(self, bills: List[int]) -> bool:
        wu, shi = 0, 0
        for bill in bills:
            if bill == 5:
                wu += 1
            elif bill == 10:
                if wu < 1:
                    return False
                wu -= 1
                shi += 1
            else:
                if shi >= 1 and wu >= 1:
                    shi -= 1
                    wu -= 1
                elif wu >= 3:
                    wu -= 3
                else:
                    return False
        return True
```

### [根据身高重建队列](https://leetcode.cn/problems/queue-reconstruction-by-height/)

**局部最优：优先按身高高的people的k来插入。插入操作过后的people满足队列属性。全局最优：最后都做完插入操作，整个队列满足题目队列属性**

`people.sort(key = lambda x:(-x[0], x[1]))`排序条件为-x[0]倒序排序，让身高people[0]从高到矮排序，如果身高遇到一样的情况下，比较x[1]，按从小到大排序比如测试用例`[[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]`里的`[7, 0], [7, 1]`，身高相同的情况下比较0和1，按从小到大排序`[7, 0], [7, 1]`就很合理，[7, 1]前面只有一个身高更高或者排在他前面的人[7, 0]，而[7, 0]前面没有比他高的人了~排序好后遍历people列表，按身高高的i[1]给出的k来插入，插入i

```python
class Solution:
    def reconstructQueue(self, people: List[List[int]]) -> List[List[int]]:
        people.sort(key = lambda x:(-x[0], x[1]))
        que = []
        for i in people:
            que.insert(i[1], i)
        return que
```

