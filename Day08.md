## Day8

### 翻转字符串中的单词

反转整个字符串`split`切割成列表还能自动忽略多余的空格，最后用`' '.join`用空格拼接列表里的每个数

``` python
class Solution:
    def reverseWords(self, s: str) -> str:
        s = s[::-1]
        return ' '.join(text[::-1] for text in s.split())
```

双指针思想没得说，太好用了

```python
class Solution:
    def reverseWords(self, s: str) -> str:
        res_s = s.split()
        left,right = 0, len(res_s) - 1
        while right > left:
            res_s[left], res_s[right] = res_s[right], res_s[left]
            left += 1
            right -= 1
        return ' '.join(res_s)
```

### 右旋转字符串

**解法一**

```python
index = int(input())
s = input()
n = len(s)
res = s[n - index:] + s[:n - index]
print(res)
```

**解法二**

```python
index = int(input())
s = input()
n = len(s)
res = s[-index:] + s[:-index]
print(res)
```

### KMP算法

**前缀表**

前缀表是用来回退的，记录了模式串与主串不匹配的时候，模式串应该从哪里开始重新匹配

```python
class Solution:
    def getNext(self, next, s):
        j = -1
        next[0] = j
        for i in range(1, len(s) - 1):
            while j >= 0 and s[i] != s[j + 1]:
                j = next[j]
            if s[i] == s[j + 1]:
                j += 1
            next[i] = j

    def strStr(self, haystack: str, needle: str) -> int:
        if not needle:
            return -1
        next = [0] * len(needle)
        self.getNext(next, needle)
        j = -1
        for i in range(len(haystack)):
            while j >= 0 and haystack[i] != needle[j + 1]:
                j = next[j]
            if haystack[i] == needle[j + 1]:
                j += 1
            if j == len(needle) - 1:
                return i - len(needle) + 1
        return -1
```

**前缀表不减1**

``` python
class Solution:
    def getNext(self, next, s):
        j = 0
        next[0] = j
        for i in range(1, len(s) - 1):
            while j > 0 and s[i] != s[j]:
                j = next[j - 1]
            if s[i] == s[j]:
                j += 1
            next[i] = j

    def strStr(self, haystack: str, needle: str) -> int:
        if not needle:
            return -1
        next = [0] * len(needle)
        self.getNext(next, needle)
        j = 0
        for i in range(len(haystack)):
            while j > 0 and haystack[i] != needle[j]:
                j = next[j - 1]
            if haystack[i] == needle[j]:
                j += 1
            if j == len(needle):
                return i - len(needle) + 1
        return -1
```

**暴力法**

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        m, n = len(haystack), len(needle)
        for i in range(m):
            if haystack[i:i + n] == needle:
                return i
        return -1
```

**index**

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        try:
            return haystack.index(needle)
        except ValueError:
            return -1
```

**find**

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        return haystack.find(needle)
```

### 重复子字符串

**前缀表减一**

`next[-1]`如果为`-1`就像`abc`没有任何相等的前后缀`(next[-1] + 1)`是最长相等前后缀的长度`len(s) % (len(s) - (next[-1] + 1)) == 0`总长度%(总长度-最长相等前后缀长度)要是等于0也就是重复子字符串，比如`ababab`总长度6%(6-4得出最小周期长度=2)

```python
class Solution:
    def getNext(self, next, s):
        j = -1
        next[0] = j
        for i in range(1, len(s)):
            while j >= 0 and s[i] != s[j + 1]:
                j = next[j]
            if s[i] == s[j + 1]:
                j += 1
            next[i] = j

    def repeatedSubstringPattern(self, s: str) -> bool:
        if not s:
            return False
        next = [0] * len(s)
        j = -1
        self.getNext(next, s)
        if next[-1] != -1 and len(s) % (len(s) - (next[-1] + 1)) == 0:
            return True
        return False
```

**find**

看了卡哥视频，这思路太天才了，`abcabc`+`abcabc`里面,如果能找到s右一半`abc`+第二个s左一半`abc`，那这个字符串肯定由重复的子字符串组成，另外两个s加起来后一定要掐头去尾，不然会直接和前一半匹配到了，也就是原字符串，而我们要找的是两个s中间的那部分子字符串，所以得掐头去尾一下`bcabcabcab`然后再来`find`

```python
class Solution:
    def repeatedSubstringPattern(self, s: str) -> bool:
        if len(s) < 1: 
            return False
        ss = s[1:] + s[:-1]
        return ss.find(s) != -1
```

