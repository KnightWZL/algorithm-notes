## Day2

### 长度最小的子数组

滑动窗口法

```while sum_val >= target```不断比较子序列是否符合条件```sum_val -= nums[l] l += 1```体现出滑动窗口的精妙，不断变更l（子序列的起始位置）

```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        l, r, len_val, sum_val, min_val = 0, 0, len(nums), 0, float('inf')
        while r < len_val:
            sum_val += nums[r]
            while sum_val >= target:
                res = r - l + 1
                min_val = res if res < min_val else min_val
                sum_val -= nums[l]
                l += 1
            r += 1
        return min_val if min_val != float('inf') else 0
```

### 螺旋矩阵II

```startx, starty```每一圈的起始位置```loop```控制循环几次，如果```n```为奇数矩阵最中间的值需要单独处理```offset```控制每一条边的长度，每次循环往内收缩一位。非常精妙，后面四次循环全部按左闭右开，依次模拟从左到右、从上到下、从右到左、从下到上

```python
class Solution:
    def generateMatrix(self, n: int) -> List[List[int]]:
        nums = [[0] * n for _ in range(n)]
        startx, starty = 0, 0
        loop, mid = n // 2,n // 2
        count = 1
        for offset in range(1, loop + 1):
            for i in range(starty, n - offset):
                nums[starty][i] = count
                count += 1
            for i in range(startx, n - offset):
                nums[i][n - offset] = count
                count += 1
            for i in range(n - offset, starty, -1):
                nums[n - offset][i] = count
                count += 1
            for i in range(n - offset, startx, -1):
                nums[i][startx] = count
                count += 1
            startx += 1
            starty += 1
        if n % 2 != 0:
            nums[mid][mid] = count
        return nums
```

### 区间和

看了卡哥画的图后对前缀和思路一下就清晰了，```p[b] - p[a] - 1```非常的精妙，还学会了```input = sys.stdin.read```来读取输入的全部内容返回字符串后```split()```切割成列表，要注意使用这个列表里的值时记得用```int```转成整形

```python
import sys
input = sys.stdin.read

def main():
    data = input().split()
    index = 0
    n = int(data[index])
    index += 1

    vec = []
    for i in range(n):
        vec.append(int(data[i + index]))
    index += n

    p = [0] * n
    tempsum = 0
    for i in range(n):
        tempsum += vec[i]
        p[i] = tempsum

    results = []
    while index < len(data):
        a = int(data[index])
        b = int(data[index + 1])
        index += 2

        if a == 0:
            results.append(p[b])
        else:
            results.append(p[b] - p[a - 1])

    for i in results:
        print(i)



if __name__ == "__main__":
    main()
```

### 开发商购买土地

```heng = [0] * n```统计横向土地价值之和```zong = [0] * m```统计纵向土地价值之和，然后进行横向切分，之后通过```hengcount += heng[i] result = min(result,abs(sum - 2 * hengcount))```来将切好后的每一行进行土地总价值之间的最小差距对比，来找出最小差值。最妙的还得是```abs(sum - 2 * hengcount)```等价```abs(hengcount - (sum - hengcount))```其中```sum```是前面累加出来的土地总价值```hengcount```是我们切好后的其中一块土地总价值，那另一块土地价值就是```(sum - hengcount)```了~太妙了,最后在使用```data```里的数据时记得用```int```，还有切完很横向后切纵向时要新建个变量```zongcount```

```python
import sys
input = sys.stdin.read
def main():
    data = input().split()
    index = 0
    n = int(data[index])
    index += 1
    m = int(data[index])
    index += 1
    sum = 0
    vec = []

    for i in range(n):
        row = []
        for j in range(m):
            sum += int(data[index])
            row.append(int(data[index]))
            index += 1
        vec.append(row)
    
    heng = [0] * n
    for i in range(n):
        for j in range(m):
            heng[i] += vec[i][j]

    zong = [0] * m
    for j in range(m):
        for i in range(n):
            zong[j] += vec[i][j]

    result = float('inf')
    hengcount = 0
    for i in range(n):
        hengcount += heng[i]
        result = min(result,abs(sum - 2 * hengcount))

    zongcount = 0
    for j in range(m):
        zongcount += zong[j]
        result = min(result,abs(sum - 2 * zongcount))

    print(result)

if __name__ == "__main__":
    main()
```

