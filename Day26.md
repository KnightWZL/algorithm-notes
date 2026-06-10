## Day26

### [用最少数量的箭引爆气球](https://leetcode.cn/problems/minimum-number-of-arrows-to-burst-balloons/)

按气球的左区间排序`points.sort(key = lambda x : x[0])`，当前气球左区间>上个气球右区间说明这两个气球没有重叠，total+1，有重叠的话，将当前气球和上个气球的最小右区间赋值给当前气球的右区间进行下次循环判断

```python
class Solution:
    def findMinArrowShots(self, points: List[List[int]]) -> int:
        if len(points) == 0: return 0
        points.sort(key = lambda x : x[0])
        total = 1
        for i in range(1, len(points)):
            if points[i][0] > points[i - 1][1]:
                total += 1
            else:
                points[i][1] = min(points[i][1], points[i - 1][1])
        return total
```

### [无重叠区间](https://leetcode.cn/problems/non-overlapping-intervals/)

和上题不一样的点在于，上题是1,2 2,3是没问题的选2就能一箭射爆，这题1,2 2,3区间就是重叠区间，需要移除一个区间， 所以当`intervals[i][0] < intervals[i - 1][1]:`当前左区间小于上一个区间的右区间，说明他们是重叠区间，total加1，更新当前区间的右区间为当前区间和上个区间的最小右区间进行下次循环判断

```python
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        if len(intervals) == 0: return 0
        intervals.sort(key = lambda x : x[0])
        total = 0
        for i in range(1, len(intervals)):
            if intervals[i][0] < intervals[i - 1][1]:
                total += 1
                intervals[i][1] = min(intervals[i][1], intervals[i - 1][1])
        return total
```

### [划分字母区间](https://leetcode.cn/problems/partition-labels/)

`last_occurrence = {}`存储每个字符最后出现的位置，`for i, ch in enumerate(s):`循环遍历s`enumerate`将 s 拆分成 i 下标和 ch 值，`end = max(end, last_occurrence[ch])`找到当前字符出现的最远距离，`end = max(end, last_occurrence[ch])`更新 end，当`if end == i:`如果当前位置是最远距离说明可以分割出一个区间，将这个区间的数添加进res，`start = i + 1`更新 start 最后返回res

```python
class Solution:
    def partitionLabels(self, s: str) -> List[int]:
        last_occurrence = {}
        for i, ch in enumerate(s):
            last_occurrence[ch] = i
        start, end, res = 0, 0, []
        for i, ch in enumerate(s):
            end = max(end, last_occurrence[ch])
            if end == i:
                res.append(end - start + 1)
                start = i + 1
        return res
```

