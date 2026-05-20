## Day1

### 数组理论基础

数组是存放在连续内存空间上的相同类型数据的集合。数组的下标都是从0开始访问的。Python的数组也就是```[]```列表```list```操作会比较简洁，容易忽略底层原理。

### 二分查找

**注意边界条件两种写法的边界条件**

`right_index + (left_index - right_index) // 2`等价`(left + right_index) // 2`但是用前者可以防止``int`精度溢出，虽然在python里不用担心精度问题~另外要注意使用整除`//`向下取整，如果用`/`就变成浮点型

左闭右闭

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left_index, right_index = 0, len(nums) - 1
        find_index = -1
        while(left_index <= right_index):
            mid_index = right_index + (left_index - right_index) // 2
            if(nums[mid_index] > target):
                right_index = mid_index - 1
            elif(nums[mid_index] < target):
                left_index = mid_index + 1
            else:
                find_index = mid_index
                break
        return find_index
```

左闭右开

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left_index, right_index = 0, len(nums)
        find_index = -1
        while(left_index < right_index):
            mid_index = right_index + (left_index - right_index) // 2
            if(nums[mid_index] > target):
                right_index = mid_index
            elif(nums[mid_index] < target):
                left_index = mid_index + 1
            else:
                find_index = mid_index
                break
        return find_index
```

### 移除元素

双指针法(快慢指针法)

快指针：寻找新数组的元素，新数组就是不含目标元素的数组

慢指针：指向更新 新数组下标的位置

```fast <= l```会越界```show```用来收集不等于```val```的值，如果```fast```对应的值不等于```val```则把它与```show```替换

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        fast, slow, l = 0, 0, len(nums)
        while fast < l:
            if nums[fast] != val:
                nums[slow] = nums[fast]
                slow += 1
            fast += 1
        return slow
```

暴力法

```for j in range(i + 1, l):```找到元素则将后面元素向前平移，```i -= 1```再次检测平移后的这个元素，防止漏掉

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        i, l = 0, len(nums)
        while(i < l):
            if nums[i] == val:
                for j in range(i + 1, l):
                    nums[j - 1] = nums[j]
                i -= 1
                l -= 1
            i += 1
        return l 
```

### 有序数组的平方

双指针法

提前定义新列表存放结果

```python
class Solution:
    def sortedSquares(self, nums: List[int]) -> List[int]:
        l, r, i = 0, len(nums) - 1, len(nums) - 1
        res = [float('inf')] * len(nums)
        while l <= r:
            if nums[l] ** 2 < nums[r] ** 2:
                res[i] = nums[r] ** 2
                r -= 1
            else:
                res[i] = nums[l] ** 2
                l += 1
            i -= 1
        return res
```

暴力排序法

```python
class Solution:
    def sortedSquares(self, nums: List[int]) -> List[int]:
        for i in range(len(nums)):
            nums[i] *= nums[i] 
        nums.sort()
        return nums
```

------

**心得笔记**

左闭右闭和左闭右开的循环条件要注意，不然容易导致死循环或者越界，```/```和```//```的细节要注意，`left + (right - left) // 2` 的防溢出写法还有双指针思想也好牛，还学会了```res = [float('inf')] * len(nums)```这个方法来定义新列表