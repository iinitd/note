# 链表

## K个一组翻转链表

```text
class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        dummy = ListNode(0)
        p = dummy
        while True:
            count = k 
            stack = []
            tmp = head
            while count and tmp:
                stack.append(tmp)
                tmp = tmp.next
                count -= 1
            # 注意,目前tmp所在k+1位置
            # 说明剩下的链表不够k个,跳出循环
            if count : 
                p.next = head
                break
            # 翻转操作
            while stack:
                p.next = stack.pop()
                p = p.next
            #与剩下链表连接起来 
            p.next = tmp
            head = tmp

        return dummy.next
```

## 反转链表

```text
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:

        pre = None

        while head:
            next = head.next
            head.next = pre
            pre = head
            head = next

        return pre
```

## 删除倒数第n个节点

```text
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummy = ListNode()
        dummy.next = head
        fast = head
        slow = head
        for _ in range(n):
            fast = fast.next
        if fast:
            fast = fast.next
        else:
            return dummy.next.next
        while fast:
            fast = fast.next
            slow = slow.next
        slow.next = slow.next.next
        return dummy.next
```

## 按第k个旋转链表

```text
class Solution:
    def rotateRight(self, head: ListNode, k: int) -> ListNode:

        if not head: return None
        if k == 0: return head

        tail = head
        count = 1

        while tail.next:
            count += 1
            tail = tail.next

        if k % count == 0:
            return head

        move = count - (k % count) -1

        subtail = head

        while move > 0:
            subtail = subtail.next
            move -= 1

        newhead = subtail.next

        subtail.next = None
        tail.next = head

        return newhead
```

