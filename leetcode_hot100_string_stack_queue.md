# LeetCode Hot 100 字符串、栈与队列知识点整理（Java）

## 核心题型

1. 字符串处理
   - 3. 无重复字符的最长子串
   - 49. 字母异位词分组
   - 438. 找到字符串中所有字母异位词
   - 76. 最小覆盖子串
   - 5. 最长回文子串

2. 栈
   - 20. 有效的括号
   - 394. 字符串解码
   - 739. 每日温度
   - 84. 柱状图中最大的矩形

3. 队列
   - 102. 二叉树的层序遍历
   - 994. 腐烂的橘子

4. 单调栈 / 单调队列
   - 739. 每日温度
   - 84. 柱状图中最大的矩形
   - 239. 滑动窗口最大值

---

## Java 容器选择

栈和队列优先用 `ArrayDeque`。

```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1);
int a = stack.pop();

Queue<Integer> queue = new ArrayDeque<>();
queue.offer(1);
int b = queue.poll();
```

注意：

- `Deque<Integer> stack = new ArrayDeque<>();` 可以用 `push()` / `pop()`。
- `Queue<Integer> queue = new ArrayDeque<>();` 适合普通队列，用 `offer()` / `poll()`。
- `PriorityQueue` 是堆，不是普通队列或栈。
- `ArrayDeque` 不能存 `null`。

---

## 字符串常用模板

### 1. 字符和数字转换

```java
char c = '7';
int num = c - '0';

int x = 7;
char ch = (char) (x + '0');
```

一段数字字符串转整数：

```java
int value = Integer.parseInt("123");
```

### 2. StringBuilder

大量拼接字符串时用 `StringBuilder`。

```java
StringBuilder sb = new StringBuilder();
sb.append('a');
sb.append("bc");
String ans = sb.toString();
```

### 3. 字母异位词 key

方式一：排序字符串。

```java
char[] arr = s.toCharArray();
Arrays.sort(arr);
String key = new String(arr);
```

方式二：计数数组。

```java
int[] count = new int[26];
for (char c : s.toCharArray()) {
    count[c - 'a']++;
}
String key = Arrays.toString(count);
```

---

## 栈模板

### 1. 有效括号

```java
public boolean isValid(String s) {
    Deque<Character> stack = new ArrayDeque<>();

    for (char c : s.toCharArray()) {
        if (c == '(') {
            stack.push(')');
        } else if (c == '[') {
            stack.push(']');
        } else if (c == '{') {
            stack.push('}');
        } else {
            if (stack.isEmpty() || stack.pop() != c) {
                return false;
            }
        }
    }

    return stack.isEmpty();
}
```

### 2. 字符串解码

思路：遇到 `[` 保存当前数字和字符串，遇到 `]` 弹出并重复。

```java
Deque<Integer> countStack = new ArrayDeque<>();
Deque<StringBuilder> strStack = new ArrayDeque<>();
StringBuilder cur = new StringBuilder();
int num = 0;

for (char c : s.toCharArray()) {
    if (Character.isDigit(c)) {
        num = num * 10 + (c - '0');
    } else if (c == '[') {
        countStack.push(num);
        strStack.push(cur);
        num = 0;
        cur = new StringBuilder();
    } else if (c == ']') {
        int count = countStack.pop();
        StringBuilder prev = strStack.pop();
        for (int i = 0; i < count; i++) {
            prev.append(cur);
        }
        cur = prev;
    } else {
        cur.append(c);
    }
}
```

---

## 单调栈与单调队列

### 1. 每日温度

栈里存下标，保持对应温度递减。

```java
public int[] dailyTemperatures(int[] temperatures) {
    int n = temperatures.length;
    int[] ans = new int[n];
    Deque<Integer> stack = new ArrayDeque<>();

    for (int i = 0; i < n; i++) {
        while (!stack.isEmpty() && temperatures[i] > temperatures[stack.peek()]) {
            int idx = stack.pop();
            ans[idx] = i - idx;
        }
        stack.push(i);
    }

    return ans;
}
```

### 2. 滑动窗口最大值

双端队列里存下标，保持对应值递减。

```java
Deque<Integer> deque = new ArrayDeque<>();
int[] ans = new int[nums.length - k + 1];

for (int i = 0; i < nums.length; i++) {
    while (!deque.isEmpty() && deque.peekFirst() <= i - k) {
        deque.pollFirst();
    }
    while (!deque.isEmpty() && nums[deque.peekLast()] <= nums[i]) {
        deque.pollLast();
    }
    deque.offerLast(i);

    if (i >= k - 1) {
        ans[i - k + 1] = nums[deque.peekFirst()];
    }
}
```

### 3. 柱状图最大矩形

遇到更矮柱子时，结算栈顶柱子作为高度的最大面积。

```java
int n = heights.length;
int[] arr = new int[n + 2];
for (int i = 0; i < n; i++) {
    arr[i + 1] = heights[i];
}

Deque<Integer> stack = new ArrayDeque<>();
int ans = 0;

for (int i = 0; i < arr.length; i++) {
    while (!stack.isEmpty() && arr[i] < arr[stack.peek()]) {
        int h = arr[stack.pop()];
        int w = i - stack.peek() - 1;
        ans = Math.max(ans, h * w);
    }
    stack.push(i);
}
```

---

## 常见坑

1. `String` 内容比较用 `equals()`，不要用 `==`。
2. `split(".")` 不是按点号分割，点号要写成 `split("\\.")`。
3. `Integer.parseInt()` 接收的是 `String`，不是 `char`；单个字符数字用 `c - '0'` 更方便。
4. 栈用 `Deque` 声明，队列用 `Queue` 或 `Deque` 声明，堆用 `PriorityQueue`。
5. 单调栈通常存下标，不只存值；因为答案往往要用距离或窗口范围。
6. 单调队列滑动窗口要先清理过期下标，再维护单调性。
7. 柱状图最大矩形建议加左右哨兵，减少边界处理。

---

## 建议刷题顺序

1. 20. 有效的括号
2. 3. 无重复字符的最长子串
3. 49. 字母异位词分组
4. 438. 找到字符串中所有字母异位词
5. 5. 最长回文子串
6. 394. 字符串解码
7. 739. 每日温度
8. 239. 滑动窗口最大值
9. 84. 柱状图中最大的矩形
10. 76. 最小覆盖子串
