# LeetCode Hot 100 索引与 Java 速查

> 来源：由 `LeetCode知识点.docx` 中的文字和嵌入表格整理，并补充 Java 刷题常用知识点与 Hot 100 专题索引。

## 基础 API 速查

### 数组、字符串、集合长度

| 类型 | 获取长度 |
| --- | --- |
| 数组 `int[] arr` | `arr.length`，属性，无括号 |
| 字符串 `String s` | `s.length()`，方法，有括号 |
| 集合 `List` / `Set` / `Map` | `size()`，方法，有括号 |

### 数组和 List 常用操作

| 操作 | 数组 `int[]` | `List<Integer>` |
| --- | --- | --- |
| 排序 | `Arrays.sort(arr)` | `list.sort(null)` 或 `Collections.sort(list)` |
| 长度 | `arr.length` | `list.size()` |
| 取元素 | `arr[i]` | `list.get(i)` |
| 复制区间 | `Arrays.copyOfRange(arr, i, j)` | `list.subList(i, j)` |

注意：

- `copyOfRange(arr, i, j)` 和 `subList(i, j)` 都是左闭右开区间 `[i, j)`。
- `subList()` 返回的是原 List 的视图，修改它可能影响原 List。刷题时如果要独立副本，用 `new ArrayList<>(list.subList(i, j))`。
- `Arrays.sort(int[])` 默认升序。对象数组或集合可以传比较器。

### 常用工具类

| 工具类 | 常用方法 | LeetCode 用途 |
| --- | --- | --- |
| `Arrays` | `sort()`、`copyOfRange()`、`binarySearch()`、`equals()`、`fill()`、`toString()` | 数组排序、复制、查找、初始化、调试 |
| `Collections` | `sort()`、`reverse()`、`shuffle()`、`max()`、`min()` | List 操作 |
| `Objects` | `equals()`、`hash()` | 安全比较 `null` |
| `Math` | `abs()`、`max()`、`min()`、`pow()`、`sqrt()` | 数学计算 |

### 常用对象类型

| 对象类型 | 常用方法 |
| --- | --- |
| `String` | `charAt()`、`substring()`、`split()`、`toLowerCase()`、`toCharArray()` |
| `StringBuilder` | `append()`、`reverse()`、`delete()`、`deleteCharAt()`、`setCharAt()` |
| `List` | `get()`、`add()`、`remove()`、`size()`、`sort()` |
| `Map` | `put()`、`get()`、`getOrDefault()`、`containsKey()`、`remove()`、`keySet()`、`values()`、`entrySet()` |
| `Set` | `add()`、`remove()`、`contains()`、`size()` |

注意：

- Java 方法区分大小写，`Map.keySet()` 的 `S` 要大写。
- `map.keySet()` 返回 `Set<K>`。
- `map.values()` 返回 `Collection<V>`。
- `map.entrySet()` 返回 `Set<Map.Entry<K, V>>`，适合同时遍历 key 和 value。

---

## Java 刷题常用容器

### HashMap

适合计数、记录位置、缓存映射。

```java
Map<Integer, Integer> map = new HashMap<>();
map.put(x, map.getOrDefault(x, 0) + 1);

if (map.containsKey(target)) {
    int idx = map.get(target);
}
```

常见场景：

- 两数之和
- 字符频次统计
- 前缀和 + 哈希表
- LRU 缓存

### HashSet

适合去重、判断是否出现过。

```java
Set<Integer> set = new HashSet<>();
set.add(x);
set.contains(x);
set.remove(x);
```

常见场景：

- 最长连续序列
- 判断重复元素
- 图搜索记录 visited

### Deque

`Deque` 可以当栈、队列、单调队列使用。刷题中推荐用 `ArrayDeque`，不要用旧的 `Stack`。

```java
Deque<Integer> deque = new ArrayDeque<>();

// 当栈
deque.push(x);
int top = deque.pop();

// 当队列
deque.offer(x);
int first = deque.poll();

// 单调队列常用
deque.peekFirst();
deque.peekLast();
deque.pollFirst();
deque.pollLast();
```

注意：`ArrayDeque` 不能放 `null`。

### PriorityQueue

默认是小根堆。

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> Integer.compare(b, a));
```

对象比较建议用 `Integer.compare(a, b)`，避免 `a - b` 溢出。

常见场景：

- 合并 K 个有序链表
- 前 K 个高频元素
- 数据流中位数
- 会议室、最短路径

---

## 字符串与字符处理

### 字符和数字转换

```java
char c = '7';
int num = c - '0';

int x = 7;
char ch = (char) (x + '0');
```

判断字符：

```java
Character.isDigit(c);
Character.isLetter(c);
Character.isLetterOrDigit(c);
Character.toLowerCase(c);
```

### String 和 StringBuilder

大量拼接字符串时用 `StringBuilder`。

```java
StringBuilder sb = new StringBuilder();
sb.append('a');
sb.append("bc");
String ans = sb.toString();
```

常见坑：

- `String` 不可变，循环里直接 `s += x` 可能很慢。
- `substring(i, j)` 是左闭右开 `[i, j)`。
- 比较字符串内容用 `equals()`，不要用 `==`。

---

## 排序与比较器

### 基础排序

```java
Arrays.sort(nums);
Collections.sort(list);
list.sort(null);
```

### 自定义排序

```java
int[][] intervals = new int[][]{{1, 3}, {2, 6}};
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
```

多关键字排序：

```java
Arrays.sort(intervals, (a, b) -> {
    if (a[0] != b[0]) {
        return Integer.compare(a[0], b[0]);
    }
    return Integer.compare(a[1], b[1]);
});
```

常见场景：

- 合并区间
- 根据频率排序
- 贪心问题
- 区间调度

---

## 高频算法模板

### 双指针

适合有序数组、原地修改数组、链表快慢指针、回文判断。

```java
int left = 0;
int right = nums.length - 1;

while (left < right) {
    int sum = nums[left] + nums[right];
    if (sum == target) {
        break;
    } else if (sum < target) {
        left++;
    } else {
        right--;
    }
}
```

常见题型：

- 两数之和 II
- 三数之和
- 盛最多水的容器
- 移动零
- 回文串判断

### 滑动窗口

适合连续子数组、连续子串、满足条件的最长/最短区间。

```java
int left = 0;
Map<Character, Integer> window = new HashMap<>();

for (int right = 0; right < s.length(); right++) {
    char c = s.charAt(right);
    window.put(c, window.getOrDefault(c, 0) + 1);

    while (/* 窗口不满足条件 */) {
        char d = s.charAt(left);
        window.put(d, window.get(d) - 1);
        left++;
    }

    // 更新答案
}
```

常见题型：

- 无重复字符的最长子串
- 找到字符串中所有字母异位词
- 最小覆盖子串
- 长度最小的子数组

### 前缀和

适合快速求区间和。

```java
int[] pre = new int[n + 1];
for (int i = 0; i < n; i++) {
    pre[i + 1] = pre[i] + nums[i];
}

int sum = pre[right + 1] - pre[left];
```

前缀和 + 哈希表常用于统计满足条件的子数组个数：

```java
Map<Integer, Integer> count = new HashMap<>();
count.put(0, 1);
int preSum = 0;
int ans = 0;

for (int num : nums) {
    preSum += num;
    ans += count.getOrDefault(preSum - k, 0);
    count.put(preSum, count.getOrDefault(preSum, 0) + 1);
}
```

常见题型：

- 和为 K 的子数组
- 二维区域和检索
- 连续数组

### 二分查找

基础模板：

```java
int left = 0;
int right = nums.length - 1;

while (left <= right) {
    int mid = left + (right - left) / 2;
    if (nums[mid] == target) {
        return mid;
    } else if (nums[mid] < target) {
        left = mid + 1;
    } else {
        right = mid - 1;
    }
}

return -1;
```

找第一个大于等于 `target` 的位置：

```java
int left = 0;
int right = nums.length;

while (left < right) {
    int mid = left + (right - left) / 2;
    if (nums[mid] >= target) {
        right = mid;
    } else {
        left = mid + 1;
    }
}

return left;
```

常见题型：

- 搜索插入位置
- 搜索旋转排序数组
- 在排序数组中查找元素的第一个和最后一个位置
- 寻找峰值

### DFS / 回溯

适合枚举所有方案、组合、排列、子集、路径。

```java
List<List<Integer>> ans = new ArrayList<>();
List<Integer> path = new ArrayList<>();

void backtrack(int start, int[] nums) {
    ans.add(new ArrayList<>(path));

    for (int i = start; i < nums.length; i++) {
        path.add(nums[i]);
        backtrack(i + 1, nums);
        path.remove(path.size() - 1);
    }
}
```

常见题型：

- 子集
- 全排列
- 组合总和
- 电话号码的字母组合
- 单词搜索

### BFS

适合最短步数、层序遍历、网格扩散。

```java
Queue<int[]> queue = new ArrayDeque<>();
boolean[][] visited = new boolean[m][n];
queue.offer(new int[]{0, 0});
visited[0][0] = true;

int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

while (!queue.isEmpty()) {
    int[] cur = queue.poll();
    for (int[] d : dirs) {
        int nx = cur[0] + d[0];
        int ny = cur[1] + d[1];
        if (nx < 0 || nx >= m || ny < 0 || ny >= n || visited[nx][ny]) {
            continue;
        }
        visited[nx][ny] = true;
        queue.offer(new int[]{nx, ny});
    }
}
```

常见题型：

- 二叉树层序遍历
- 腐烂的橘子
- 岛屿数量
- 单词接龙

### 动态规划

DP 重点是定义状态和转移。

一维 DP 示例：

```java
int[] dp = new int[n + 1];
dp[0] = 1;

for (int i = 1; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
}
```

二维 DP 示例：

```java
int[][] dp = new int[m][n];
for (int i = 0; i < m; i++) {
    for (int j = 0; j < n; j++) {
        // dp[i][j] = ...
    }
}
```

常见题型：

- 爬楼梯
- 最大子数组和
- 最长递增子序列
- 最长公共子序列
- 编辑距离
- 背包问题

---

## 链表核心提醒

链表题建议单独复习：[leetcode_hot100_linked_list.md](leetcode_hot100_linked_list.md)。

最常用技巧：

- 虚拟头节点 `dummy`
- 快慢指针
- 三指针反转链表
- 比较节点引用用 `==`，不要比较 `val`
- 改 `next` 前先保存后继节点

反转链表模板：

```java
ListNode prev = null;
ListNode cur = head;

while (cur != null) {
    ListNode next = cur.next;
    cur.next = prev;
    prev = cur;
    cur = next;
}

return prev;
```

---

## Java 刷题常见坑

1. 比较对象内容用 `equals()`，比较引用才用 `==`。

```java
"abc".equals(s);
Objects.equals(a, b);
```

2. 整数可能溢出时用 `long`。

```java
long sum = 0L;
```

3. 计算中点避免溢出。

```java
int mid = left + (right - left) / 2;
```

4. 数组越界最常见，循环条件要想清楚开闭区间。

```java
for (int i = 0; i < nums.length; i++) {
    // [0, nums.length)
}
```

5. `Arrays.asList()` 返回的是固定大小列表，不能直接 `add()` 或 `remove()`。

```java
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3));
```

6. `int[]` 不能直接作为 `HashMap` 的 key 来按内容比较。

如果需要按内容比较，可以转成字符串或自定义对象。

```java
String key = Arrays.toString(arr);
```

7. 递归题注意栈深度。链表、树、图如果数据量很大，优先考虑迭代写法或确认题目规模。

8. 网格 DFS/BFS 先判断边界，再访问数组。

```java
if (x < 0 || x >= m || y < 0 || y >= n) {
    return;
}
```

9. `PriorityQueue` 默认小根堆，大根堆要传比较器。

10. `String.split()` 参数是正则表达式，按点号分割要写成 `split("\\.")`。

---

## 推荐复习顺序

1. Java 基础 API：数组、字符串、集合、Map、Set、Deque、PriorityQueue。
2. 双指针、滑动窗口、前缀和、二分查找。
3. 链表、二叉树、图的 DFS/BFS。
4. 排序、堆、贪心、动态规划。
5. 每类题整理 2 到 3 个固定模板，重点背“状态含义”和“边界条件”，不要只背完整代码。

---

## Hot 100 专题笔记索引

- [链表](leetcode_hot100_linked_list.md)
- [数组、哈希、前缀和、双指针、滑动窗口](leetcode_hot100_array_hash.md)
- [字符串、栈、队列、单调栈、单调队列](leetcode_hot100_string_stack_queue.md)
- [二叉树、DFS/BFS、图、岛屿类问题](leetcode_hot100_tree_graph.md)
- [回溯、动态规划、贪心基础](leetcode_hot100_backtracking_dp.md)
- [堆、排序、二分查找](leetcode_hot100_heap_sort_binary_search.md)
