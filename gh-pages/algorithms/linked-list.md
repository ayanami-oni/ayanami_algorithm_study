# 链表算法

## 基础结构

```javascript
// 单链表节点
class ListNode {
  constructor(val = 0, next = null) {
    this.val = val;
    this.next = next;
  }
}
```

## 常用技巧

### 1. 双指针法

**示例：判断链表是否有环**

```javascript
function hasCycle(head) {
  let slow = head;
  let fast = head;
  
  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;
    if (slow === fast) return true;
  }
  
  return false;
}
```

**示例：找到环的入口节点**

```javascript
function detectCycle(head) {
  let slow = head;
  let fast = head;
  
  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;
    if (slow === fast) {
      slow = head;
      while (slow !== fast) {
        slow = slow.next;
        fast = fast.next;
      }
      return slow;
    }
  }
  
  return null;
}
```

### 2. 反转链表

**迭代法**

```javascript
function reverseList(head) {
  let prev = null;
  let curr = head;
  
  while (curr) {
    const next = curr.next;
    curr.next = prev;
    prev = curr;
    curr = next;
  }
  
  return prev;
}
```

**递归法**

```javascript
function reverseListRecursive(head) {
  if (!head || !head.next) return head;
  
  const newHead = reverseListRecursive(head.next);
  head.next.next = head;
  head.next = null;
  
  return newHead;
}
```

### 3. 合并两个有序链表

```javascript
function mergeTwoLists(l1, l2) {
  const dummy = new ListNode(-1);
  let curr = dummy;
  
  while (l1 && l2) {
    if (l1.val <= l2.val) {
      curr.next = l1;
      l1 = l1.next;
    } else {
      curr.next = l2;
      l2 = l2.next;
    }
    curr = curr.next;
  }
  
  curr.next = l1 || l2;
  return dummy.next;
}
```

### 4. 删除链表节点

**删除倒数第 N 个节点**

```javascript
function removeNthFromEnd(head, n) {
  const dummy = new ListNode(0, head);
  let slow = dummy;
  let fast = head;
  
  // fast 先走 n 步
  for (let i = 0; i < n; i++) {
    fast = fast.next;
  }
  
  // 同时移动直到 fast 到达末尾
  while (fast) {
    slow = slow.next;
    fast = fast.next;
  }
  
  slow.next = slow.next.next;
  return dummy.next;
}
```

**删除重复节点**

```javascript
function deleteDuplicates(head) {
  let curr = head;
  
  while (curr && curr.next) {
    if (curr.val === curr.next.val) {
      curr.next = curr.next.next;
    } else {
      curr = curr.next;
    }
  }
  
  return head;
}
```

### 5. 相交链表

```javascript
function getIntersectionNode(headA, headB) {
  let pA = headA;
  let pB = headB;
  
  while (pA !== pB) {
    pA = pA ? pA.next : headB;
    pB = pB ? pB.next : headA;
  }
  
  return pA;
}
```

### 6. 链表中点

```javascript
function middleNode(head) {
  let slow = head;
  let fast = head;
  
  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;
  }
  
  return slow;
}
```

### 7. 回文链表

```javascript
function isPalindrome(head) {
  // 找到中点
  let slow = head;
  let fast = head;
  
  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;
  }
  
  // 反转后半部分
  let prev = null;
  let curr = slow;
  
  while (curr) {
    const next = curr.next;
    curr.next = prev;
    prev = curr;
    curr = next;
  }
  
  // 比较前半和后半
  let p1 = head;
  let p2 = prev;
  
  while (p2) {
    if (p1.val !== p2.val) return false;
    p1 = p1.next;
    p2 = p2.next;
  }
  
  return true;
}
```

## 复杂度分析

| 操作 | 时间复杂度 | 空间复杂度 |
|------|-----------|-----------|
| 访问 | O(n) | O(1) |
| 插入 | O(1)* | O(1) |
| 删除 | O(1)* | O(1) |
| 搜索 | O(n) | O(1) |

*指已知节点位置的情况

## 常见题型

1. **快慢指针**：找中点、判环、找环入口
2. ** dummy 节点**：简化边界处理
3. **先反转再操作**：回文判断、两两交换
4. **双指针配合**：合并有序链表、相交节点
