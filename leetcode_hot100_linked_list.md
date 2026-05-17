# LeetCode Hot 100 链表知识点整理（Java）

## 核心题型

Hot 100 里的链表题大致可以分成这些类：

1. 基础指针操作
   - 206. 反转链表
   - 21. 合并两个有序链表
   - 19. 删除链表倒数第 N 个节点
   - 24. 两两交换链表中的节点

2. 快慢指针
   - 141. 环形链表
   - 142. 环形链表 II
   - 234. 回文链表
   - 876. 链表的中间结点（补基础很有用）

3. 链表反转进阶
   - 25. K 个一组翻转链表
   - 92. 反转链表 II（补充题）

4. 多链表 / 复杂结构
   - 160. 相交链表
   - 23. 合并 K 个升序链表
   - 138. 随机链表的复制

5. 链表 + 数据结构
   - 146. LRU 缓存
   - 148. 排序链表

---

## Java 链表基础模板

```java
public class ListNode {
    int val;
    ListNode next;

    ListNode() {}

    ListNode(int val) {
        this.val = val;
    }

    ListNode(int val, ListNode next) {
        this.val = val;
        this.next = next;
    }
}
```

链表题的关键不是“写循环”，而是维护好几个引用变量之间的关系。  
在 Java 里，`ListNode` 是引用类型，修改 `node.next` 会真实改变链表结构。

---

## 最重要的技巧

### 1. 虚拟头节点 dummy

凡是可能修改头节点的题，优先考虑 `dummy`。

典型场景：

- 删除节点
- 合并链表
- 两两交换
- K 个一组翻转
- 分区、重排类问题

```java
ListNode dummy = new ListNode(0);
dummy.next = head;
ListNode cur = dummy;

// 最后返回 dummy.next
return dummy.next;
```

好处是避免单独处理 `head` 被删除、被交换、被反转的情况。

### 2. 反转链表三指针

这是链表模块最核心的基本功。

```java
public ListNode reverseList(ListNode head) {
    ListNode prev = null;
    ListNode cur = head;

    while (cur != null) {
        ListNode next = cur.next;
        cur.next = prev;
        prev = cur;
        cur = next;
    }

    return prev;
}
```

注意点：

- 一定要先保存 `next`
- 再改 `cur.next`
- 最后移动 `prev` 和 `cur`

很多链表 bug 都是因为改指针前没有保存后继节点。

### 3. 快慢指针

常见用途：

- 判断是否有环
- 找环入口
- 找中点
- 找倒数第 N 个节点
- 判断回文链表

判断环：

```java
public boolean hasCycle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;

    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;

        if (slow == fast) {
            return true;
        }
    }

    return false;
}
```

找倒数第 N 个节点，一般用 `dummy + fast 先走 n 步`：

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;

    ListNode fast = dummy;
    ListNode slow = dummy;

    for (int i = 0; i < n; i++) {
        fast = fast.next;
    }

    while (fast.next != null) {
        fast = fast.next;
        slow = slow.next;
    }

    slow.next = slow.next.next;
    return dummy.next;
}
```

### 4. 链表相交题的双指针

160. 相交链表的经典写法：

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    ListNode pA = headA;
    ListNode pB = headB;

    while (pA != pB) {
        pA = pA == null ? headB : pA.next;
        pB = pB == null ? headA : pB.next;
    }

    return pA;
}
```

核心思想：两个人走完自己的路后，换到对方的路上。  
若有交点，会在同一节点相遇；若没有，最后都变成 `null`。

注意：这里比较的是节点引用，即 `pA == pB`，不是值相等。

### 5. 合并链表

21. 合并两个有序链表：

```java
public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
    ListNode dummy = new ListNode(0);
    ListNode cur = dummy;

    while (list1 != null && list2 != null) {
        if (list1.val <= list2.val) {
            cur.next = list1;
            list1 = list1.next;
        } else {
            cur.next = list2;
            list2 = list2.next;
        }
        cur = cur.next;
    }

    cur.next = list1 != null ? list1 : list2;
    return dummy.next;
}
```

23. 合并 K 个链表通常用：

- 优先队列，小根堆
- 分治合并

Java 小根堆写法：

```java
PriorityQueue<ListNode> pq = new PriorityQueue<>((a, b) -> Integer.compare(a.val, b.val));
```

### 6. K 个一组反转

25. K 个一组翻转链表是链表模块难点。

要点：

- 先判断剩余节点是否够 `k` 个
- 每组内部反转
- 反转后接回前后链表
- 使用 `dummy` 降低头节点处理难度

核心思路不是死背代码，而是记住四个指针：

```text
prevGroup -> groupStart ... groupEnd -> nextGroup
```

反转后变成：

```text
prevGroup -> groupEnd ... groupStart -> nextGroup
```

这题非常考验指针保存顺序。

### 7. 回文链表

234. 回文链表常见做法：

1. 快慢指针找中点
2. 反转后半部分
3. 前后两段逐个比较

复杂度：

- 时间 `O(n)`
- 空间 `O(1)`

注意奇偶长度：

- 奇数长度时，中间节点不用比较
- 实际代码里只要以后半段长度为准比较即可

### 8. 随机链表复制

138. 随机链表的复制有两种常见解法：

第一种：哈希表，最直观。

```java
Map<Node, Node> map = new HashMap<>();
```

第一遍复制节点，第二遍连接 `next` 和 `random`。

第二种：原地穿插节点，空间 `O(1)`，但指针操作更复杂。

复习时建议先掌握哈希表版本，再看原地版本。

### 9. 排序链表

148. 排序链表一般用归并排序。

原因：

- 链表不适合随机访问
- 快排在链表上不好写
- 归并排序天然适合链表

步骤：

1. 快慢指针找中点
2. 断开链表
3. 递归排序左右两段
4. 合并两个有序链表

关键是找中点后要断链：

```java
ListNode mid = slow.next;
slow.next = null;
```

否则递归可能死循环。

### 10. LRU 缓存

146. LRU 缓存本质是：

```text
HashMap + 双向链表
```

要求：

- `get` O(1)
- `put` O(1)

哈希表负责根据 key 找节点。  
双向链表负责维护最近使用顺序。

常见设计：

```java
class Node {
    int key;
    int value;
    Node prev;
    Node next;
}
```

使用两个哨兵节点：

```java
Node head;
Node tail;
```

链表顺序通常设计成：

```text
head <-> 最近使用 ... 最久未使用 <-> tail
```

---

## Java 链表题常见坑

1. 忘记保存 `next`

```java
ListNode next = cur.next;
cur.next = prev;
```

如果先改 `cur.next`，后面的链表可能丢失。

2. 返回错节点

使用 `dummy` 时，大多数情况返回：

```java
return dummy.next;
```

反转链表时通常返回：

```java
return prev;
```

3. 空指针判断顺序错

快慢指针循环条件一般是：

```java
while (fast != null && fast.next != null)
```

不能写反。

4. 节点比较不能用 val

相交链表、环入口等题，比较的是节点本身：

```java
if (p1 == p2)
```

不是：

```java
if (p1.val == p2.val)
```

5. 链表断开不彻底

排序链表、分割链表、重排链表时，常需要手动断开：

```java
node.next = null;
```

否则可能形成环或尾巴残留。

---

## 建议刷题顺序

1. 206. 反转链表
2. 21. 合并两个有序链表
3. 141. 环形链表
4. 160. 相交链表
5. 19. 删除链表倒数第 N 个节点
6. 234. 回文链表
7. 24. 两两交换链表中的节点
8. 2. 两数相加
9. 138. 随机链表的复制
10. 148. 排序链表
11. 23. 合并 K 个升序链表
12. 25. K 个一组翻转链表
13. 146. LRU 缓存

链表模块最该练的是“指针关系图”。每道题写代码前，先在纸上画出 `prev / cur / next / dummy` 的移动过程，会比直接硬写稳定很多。
