## Day4

### 两两交换链表中的节点

`current` 指向要交换的两个节点的前一个位置`temp1`保存第一个节点`temp2`保存第二个节点的下一个节点，交换后 `current` 后移两位继续下一轮，循环条件`while current.next and current.next.next:`还保证了至少有两个节点不能交换，完美

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy_head = ListNode(next = head)
        current = dummy_head
        while current.next and current.next.next:
            temp1 = current.next
            temp2 = current.next.next.next
            current.next = current.next.next
            current.next.next = temp1
            temp1.next = temp2
            current = current.next.next
        return dummy_head.next
```

**递归法**

`cur.next = pre` 反转前两个，`pre.next = self.swapPairs(next)` 递归处理剩下的

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        pre = head
        cur = head.next
        next = head.next.next

        cur.next = pre
        pre.next = self.swapPairs(next)

        return cur
```

### 删除链表的倒数第N个节点

快慢指针`fast` 先走 `n+1` 步，然后 `slow` 和 `fast` 同时走`fast` 到末尾时，`slow` 正好在倒数第 `n` 个节点的前一个，这思路太精妙了，尤其虚拟头节点非常绝，处理删除实际头节点的逻辑时非常方便

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
        dummy_head = ListNode(next = head)
        slow = fast = dummy_head
        for i in range(n + 1):
            fast = fast.next
        while fast:
            fast = fast.next
            slow = slow.next
        slow.next = slow.next.next
        return dummy_head.next
```

### 链表相交

先算两条链表的长度差，让长链表先走差值步，然后同步遍历找相同节点~`-> Optional[ListNode]:`要注意返回的是节点不是值，所以最后返回的是`slow`而不是`slow.val`

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        lenA, lenB = 0, 0
        cur = headA
        while cur:
            cur = cur.next
            lenA += 1
        cur = headB
        while cur:
            cur = cur.next
            lenB += 1
        curA, curB = headA, headB
        if lenA > lenB:
            lenA, lenB = lenB, lenA
            curA, curB = curB, curA
        for _ in range(lenB - lenA):
            curB = curB.next
        while curA:
            if curB == curA:
                return curA
            else:
                curB = curB.next
                curA = curA.next
        return None
```

### 环形链表II

快慢指针找相遇点`if slow == fast:`再从相遇点和头节点出发找环的入口`while slow != fast:`原理卡哥讲得非常详细非常绝~

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow = head
        fast = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if slow == fast:
                slow = head
                while slow != fast:
                    slow = slow.next
                    fast = fast.next
                return slow
        return None
```

