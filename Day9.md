## Day9

### 用栈实现队列

`self.stack_in = []`输入栈`self.stack_out = []`输出栈`pop`函数里`if self.stack_out:`如果输出栈里有元素，直接输出输出栈里的元素，没有的话就把输入栈里的元素倒入输出栈，这样顺序就反过来了，然后`pop()`把列表最后那个数删除并返回

```python
class MyQueue:

    def __init__(self):
        self.stack_in = []
        self.stack_out = []

    def push(self, x: int) -> None:
        self.stack_in.append(x)

    def pop(self) -> int:
        if self.empty():
            return None
            
        if self.stack_out:
            return self.stack_out.pop()
        else:
            for i in range(len(self.stack_in)):
                self.stack_out.append(self.stack_in.pop())
            return self.stack_out.pop()

    def peek(self) -> int:
        ans = self.pop()
        self.stack_out.append(ans)
        return ans

    def empty(self) -> bool:
        return not (self.stack_in or self.stack_out)


# Your MyQueue object will be instantiated and called as such:
# obj = MyQueue()
# obj.push(x)
# param_2 = obj.pop()
# param_3 = obj.peek()
# param_4 = obj.empty()
```

### 用队列实现栈

**双向队列实现**

[]in:[1, 2, 3] []out:[]`for i in range(len(self.deque_in) - 1): self.deque_out.append(self.deque_in.popleft())`输入栈里只剩下3[]in:[3] []out:[1,2]之后交换`self.deque_in, self.deque_out = self.deque_out, self.deque_in`[]in:[1,2] []out:[3]交换后把输出栈的3pop()出去，并且因为交换了输出和输入栈，最后输入栈的元素也从最开始的[1,2,3]变成了[1,2]完美实现栈的先进后出

```python
from collections import deque
class MyStack:

    def __init__(self):
        self.deque_in = deque()
        self.deque_out = deque()

    def push(self, x: int) -> None:
        self.deque_in.append(x)

    def pop(self) -> int:
        if self.empty():
            return None
        for i in range(len(self.deque_in) - 1):
            self.deque_out.append(self.deque_in.popleft())
        self.deque_in, self.deque_out = self.deque_out, self.deque_in
        return self.deque_out.pop()

    def top(self) -> int:
        if self.empty():
            return None
        for i in range(len(self.deque_in) - 1):
            self.deque_out.append(self.deque_in.popleft())
        self.deque_in, self.deque_out = self.deque_out, self.deque_in
        ans = self.deque_out.pop()
        self.deque_in.append(ans)
        return ans
        

    def empty(self) -> bool:
        return len(self.deque_in) == 0
             

# Your MyStack object will be instantiated and called as such:
# obj = MyStack()
# obj.push(x)
# param_2 = obj.pop()
# param_3 = obj.top()
# param_4 = obj.empty()
```

**优化，使用一个队列实现**

```python
from collections import deque
class MyStack:

    def __init__(self):
        self.que = deque()

    def push(self, x: int) -> None:
        self.que.append(x)

    def pop(self) -> int:
        if self.empty():
            return None
        for i in range(len(self.que) - 1):
            self.que.append(self.que.popleft())
        return self.que.popleft()

    def top(self) -> int:
        if self.empty():
            return None
        for i in range(len(self.que) - 1):
            self.que.append(self.que.popleft())
        ans = self.que.popleft()
        self.que.append(ans)
        return ans
        

    def empty(self) -> bool:
        return len(self.que) == 0
             

# Your MyStack object will be instantiated and called as such:
# obj = MyStack()
# obj.push(x)
# param_2 = obj.pop()
# param_3 = obj.top()
# param_4 = obj.empty()
```

### 有效的括号

`not stack or stack[-1] != item:`栈已经空了却来了个右符号，那说明没有对应的左符号or栈顶不是当前这个右符号，这两种情况返回`False`循环结束后`True if not stack else False`如果为空说明全部符号匹配成功

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        for item in s:
            if item == "(":
                stack.append(")")
            elif item == "[":
                stack.append("]")
            elif item == "{":
                stack.append("}")
            elif not stack or stack[-1] != item:
                return False
            else:
                stack.pop()
        return True if not stack else False
```

**使用字典**

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        mapping = {
            "(": ")",
            "[": "]",
            "{": "}"
        }
        for item in s:
            if item in mapping.keys():
                stack.append(mapping[item])
            elif not stack or stack[-1] != item:
                return False
            else:
                stack.pop()
        return True if not stack else False
```

### 删除字符串中的所有相邻重复项

**使用栈**

```
class Solution:
    def removeDuplicates(self, s: str) -> str:
        res = []
        for i in s:
            if res and res[-1] == i:
                res.pop()
            else:
                res.append(i)
        return "".join(res)
```

**双指针**

如果不让用栈就可以使用这种方法`if slow > 0 and res[slow] == res[slow - 1]`当slow大于0且下标为slow和slow-1的数相等，那slow就-1，相当于相邻那就消掉，不相邻就保留slow+=1`res[slow] = res[fast]`将快指针指向的字符写到满指针指向的下标，相当于消掉原本相邻的存在，将新的数写入慢指针指向的位置，循环到最后切割`res[:slow]`

```python
class Solution:
    def removeDuplicates(self, s: str) -> str:
        res = list(s)
        slow = fast = 0
        length = len(s)
        while fast < length:
            res[slow] = res[fast]
            if slow > 0 and res[slow] == res[slow - 1]:
                slow -= 1
            else:
                slow += 1
            fast += 1
        return "".join(res[:slow]) 
```

