## Day6

### 四数相加

> `(n1 + n2) + (n3 + n4) = 0`那么 `n1 + n2 = -(n3 + n4)`

```python
class Solution:
    def fourSumCount(self, nums1: List[int], nums2: List[int], nums3: List[int], nums4: List[int]) -> int:
        hashmap = dict()
        for n1 in nums1:
            for n2 in nums2:
                if n1 + n2 in hashmap:
                    hashmap[n1 + n2] += 1
                else:
                    hashmap[n1 + n2] = 1
        count = 0
        for n3 in nums3:
            for n4 in nums4:
                key = - n3 - n4
                if key in hashmap:
                    count += hashmap[key]
        return count
```

### 赎金信

**数组**

`all`所有元素满足条件返回`True`否则返回`False`注意`ord(c) - ord('a')`要减去`a`不然会越界`return all(ransom_count[i] <= magazine_count[i] for i in range(26))`检查`magazine_count`中每种字母数量是否都够拼出`ransom_count`

```python
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        ransom_count = [0] * 26
        magazine_count = [0] * 26
        for c in ransomNote:
            ransom_count[ord(c) - ord('a')] += 1
        for c in magazine:
            magazine_count[ord(c) - ord('a')] += 1
        return all(ransom_count[i] <= magazine_count[i] for i in range(26))
```

**字典**

`if i not in counts or counts[i] == 0:`判断这个字母有没有在字典里有的话如果这个字母数量不够用，也拼不出赎金信，返回`False`

```python
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        counts = {}
        for i in magazine:
            counts[i] = counts.get(i, 0) + 1
        for i in ransomNote:
            if i not in counts or counts[i] == 0:
                return False
            counts[i] -= 1
        return True
```

**count**

原理差不多，但是代码更简洁，使用`count`来统计字母出现次数有没有超过，最关键的是用了`set`来去重，简化了多次重复`count`来提高性能~完美

```python
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        return all(ransomNote.count(c) <= magazine.count(c) for c in set(ransomNote))
```

### 三数之和

**双指针**

先排序`nums.sort()`再`if nums[i] > 0:`如果最小的数都大于0，那这三个数肯定大于0，直接返回`result`进行一个剪枝的操作，然后`if i > 0 and nums[i] == nums[i-1]:`从第二个数开始判断是否和上个数一样，进行一个去重操作，不然会`append`一个重复的三元组`while right > left and nums[left] == nums[left + 1]:`找到等于0的三元组后再进行一个去重操作，去重完后再进行`left += 1right -= 1`双指针的移动，一定要在找到时才进行双指针的移动

```python
class Solution:
    def threeSum(self, nums: list[int]) -> list[list[int]]:
        result = []
        nums.sort()
        for i in range(len(nums)):
            if nums[i] > 0:
                return result
            if i > 0 and nums[i] == nums[i-1]:
                continue
            left = i + 1
            right = len(nums) - 1
            while right > left:
                sum_ = nums[i] + nums[left] + nums[right]
                if sum_ > 0:
                    right -= 1
                elif sum_ < 0:
                    left += 1
                else:
                    result.append([nums[i], nums[left], nums[right]])
                    while right > left and nums[left] == nums[left + 1]:
                        left += 1
                    while right > left and nums[right] == nums[right - 1]:
                        right -= 1
                    left += 1
                    right -= 1
        return result
```

### 四数之和

`nums[i] > 0`不能直接判断然后直接返回了，因为`target`是任意数而不是0这样确定的数，有可能会是负数，虽然不能返回，但是还可以剪枝，但要满足条件`if nums[i] > target and nums[i] > 0 and target > 0:`要注意`if nums[j] + nums[i] > target and target > 0:`第二个循环条件是`nums[j] + nums[i]`这两个数加起来要是大于`target`就直接剪枝，和三数之和同理，最小的两个数加起来都大于`target`了那就不可能等于`target`重点来了在第二个循环去重的时候`if j > i + 1 and nums[j] == nums[j - 1]:`注意这次不是`>0`而是`>i + 1`因为`j`是从`i + 1`开始的第一个下标，再后面的话就和三数之和一样啦

**双指针**

```python
class Solution:
    def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
        result = []
        nums.sort()
        for i in range(len(nums)):
            if nums[i] > target and nums[i] > 0 and target > 0:
                break
            if i > 0 and nums[i] == nums[i - 1]:
                continue
            for j in range(i + 1, len(nums)):
                if nums[j] + nums[i] > target and target > 0:
                    break
                if j > i + 1 and nums[j] == nums[j - 1]:
                    continue
                left, right = j + 1, len(nums) - 1
                while right > left:
                    sum = nums[i] + nums[j] + nums[left] + nums[right]
                    if sum > target:
                        right -= 1
                    elif sum < target:
                        left += 1
                    else:
                        result.append([nums[i], nums[j], nums[left], nums[right]])
                        while right > left and nums[left] == nums[left + 1]:
                            left += 1
                        while right > left and nums[right] == nums[right - 1]:
                            right -= 1
                        left += 1
                        right -= 1
        return result               
```

