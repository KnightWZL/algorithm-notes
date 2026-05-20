## Day5

### 有效的字母异位词

**写法一**

`ord`将字符转换成对应的ASCII`chr`可以转换回来，如果`record`数组中所有元素都是0说明这两个字符串是异位词

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        record = [0] * 26
        for i in s:
            record[ord(i) - ord('a')] += 1  
        for i in t:
            record[ord(i) - ord('a')] -= 1  
        for i in record:
            if i != 0:
                return False
        return True
```

**写法二**

`Counter`是 python 专门用来计数的内置库，写法一是底层原理要掌握~可以用`Counter`快速解决问题

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        from collections import Counter
        countA = Counter(s)
        countB = Counter(t)
        return countA == countB
```

### 两个数组的交集

**字典和集合**

`table.get(i, 0) + 1`若`i`不在`table`中时返回`0 + 1`然后`for i in nums2: if i in table:`循环第二个列表，如果第二个列表中的元素有在第一个列表中出现过则加入集合中，同时删除`table`中的元素防止重复加入（每个元素只出现一次）

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        table = {}
        for i in nums1:
            table[i] = table.get(i, 0) + 1
        res = set()
        for i in nums2:
            if i in table:
                res.add(i)
                del table[i]
        return list(res)
```

**数组**

`count1[i] * count2[i] > 0:`大于0的时候代表两个列表里都出现过这个数，也就是这两个列表的交集，添加到第三个列表中最后返回第三个列表

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        count1 = [0] * 1001
        count2 = [0] * 1001
        for i in range(len(nums1)):
            count1[nums1[i]] += 1
        for i in range((len(nums2))):
            count2[nums2[i]] += 1
        res = []
        for i in range(1001):
            if count1[i] * count2[i] > 0:
                res.append(i)
        return res
```

**集合**

`set()`转集合去重然后`&`求交集

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        return list(set(nums1) & set(nums2))
```

### 快乐数

**集合**

判断`n`有没有在集合里，如果有在集合里那说明值重复了，陷入无线循环，它就是快乐数，如果没在集合里就继续循环

```python
class Solution:
    def isHappy(self, n: int) -> bool:
        record = set()
        while n not in record:
            record.add(n)
            n_str = str(n)
            n_sum = 0
            for i in n_str:
                n_sum += int(i) ** 2
            if n_sum == 1:
                return True
            else:
                n = n_sum
        return False
```

**集合精简**

```python
class Solution:
    def isHappy(self, n: int) -> bool:
        record = set()
        while n != 1:
            n = sum(int(i) ** 2 for i in str(n))
            if n in record: return False
            record.add(n)
        return True
```

**数组精简**

```python
class Solution:
    def isHappy(self, n: int) -> bool:
        record = []
        while n != 1:
            n = sum(int(i) ** 2 for i in str(n))
            if n in record: return False
            record.append(n)
        return True
            
```

### 两数之和

**字典**

`enumerate`通过内置函数同时获取`nums`的下标和值~`if target - value in records:`遍历当前元素在`records`寻找是否有匹配的`key`没有找到匹配对象就`records[value] = index`把这值和下标都加入进去

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        records = dict()
        for index ,value in enumerate(nums):
            if target - value in records:
                return [records[target - value], index]
            records[value] = index
        return []
```

**集合**

思路和字典差不多但是要注意字典是通过键值对里的`key`来查找下标的，集合的话`nums.index(target - value)`通过`index`来找传进来的参数`nums`里对应的下标，太妙了

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        records = set()
        for index, value in enumerate(nums):
            if target - value in records:
                return [nums.index(target - value), index]
            records.add(value)
```

**双指针**

先排序，再用双指针思想解题，要注意找下标时`left_index = nums.index(nums_sort[left])`是找原数组的下标而不是找排序后数组的下标，而当`if left_index == right_index:`时要处理，不然通不过[3, 3]这样的数据，因为`index`只会返回第一次遇到这个值的下标，返回的值也就变成了`[0, 0]`处理时要跳过第一个`nums[left_index + 1:]`所以要这样进行切片，然后对剩下的数据来用`index`查找，最后`+ left_index + 1`来还原原数组的下标

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        nums_sort = sorted(nums)
        left = 0
        right = len(nums) - 1
        while left < right:
            if nums_sort[left] + nums_sort[right] == target:
                left_index = nums.index(nums_sort[left])
                right_index = nums.index(nums_sort[right])
                if left_index == right_index:
                    right_index = nums[left_index + 1:].index(nums_sort[right]) + left_index + 1
                return [left_index, right_index]
            elif nums_sort[left] + nums_sort[right] > target:
                    right -= 1
            else:
                    left += 1
```

**暴力**

要注意需要返回的是下标，不是值，所以需要循环的时候需要用`len()`而不是直接`for i in nums`

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        for i in range(len(nums)):
            for j in range(i + 1, len(nums)):
                if nums[i] + nums[j] == target:
                    return [i, j]
```

