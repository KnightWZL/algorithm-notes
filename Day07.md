## Day7

### 反转字符串

双指针真的好用啊~

```python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        left, right = 0, len(s) - 1
        while left < right:
            s[left], s[right] = s[right], s[left]
            left += 1
            right -= 1
```

**range**

```python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        n = len(s)
        for i in range(n // 2):
            s[i], s[n - i - 1] = s[n - i - 1], s[i]
```

**reverse**

```python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        s.reverse()
```

### 反转字符串II

算法和Python的神奇，我自己尝试用双指针做了下，一堆逻辑代码，看了下代码随想录里的写法~真的nb，这写法很Python，又让我惊艳到了

```python
class Solution:
    def reverseStr(self, s: str, k: int) -> str:
        p = 0
        while p < len(s):
            p2 = p + k
            s = s[:p] + s[p:p2][::-1] + s[p2:]
            p = p + 2 * k
        return s
```

### 替换数字

在看题解前自己试了下~

```python
class Solution(object):
    def subsitute_numbers(self, s):
        s_res = ""
        for i in s:
            if i.isdigit():
                s_res += "number"
            else:
                s_res += i
        return s_res
if __name__ == "__main__":
    solution = Solution()

    while True:
        try:
            s = input()
            result = solution.subsitute_numbers(s)
            print(result)
        except EOFError:
            break
```

**题解**

`n = count * 5 + len(s)`计算扩充后字符串的大小，每有一个数字增加五个长度，之后下标从末端开始，避免每次都申请新数组变成O(n^2)的算法，还避免了每次添加元素后还要把所有元素向后移动的问题，非常的精妙~

```python
class Solution(object):
    def subsitute_numbers(self, s):
        count = sum(1 for char in s if char.isdigit())
        n = count * 5 + len(s)
        s_res = [''] * n
        new_index = n - 1
        old_index = len(s) - 1

        while old_index >= 0:
            if s[old_index].isdigit():
                s_res[new_index-5:new_index+1] = "number"
                new_index -= 6
            else:
                s_res[new_index] = s[old_index]
                new_index -= 1
            old_index -= 1
        return "".join(s_res) 

if __name__ == "__main__":
    solution = Solution()

    while True:
        try:
            s = input()
            result = solution.subsitute_numbers(s)
            print(result)
        except EOFError:
            break
```

