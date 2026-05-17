# LeetCode Hot 100 堆、排序与二分知识点整理（Java）

## 核心题型

1. 堆
   - 215. 数组中的第 K 个最大元素
   - 347. 前 K 个高频元素
   - 23. 合并 K 个升序链表
   - 295. 数据流的中位数

2. 排序
   - 56. 合并区间
   - 75. 颜色分类
   - 148. 排序链表
   - 169. 多数元素

3. 二分查找
   - 35. 搜索插入位置
   - 33. 搜索旋转排序数组
   - 34. 在排序数组中查找元素的第一个和最后一个位置
   - 153. 寻找旋转排序数组中的最小值
   - 4. 寻找两个正序数组的中位数

---

## 堆 PriorityQueue

`PriorityQueue` 默认是小根堆。

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> Integer.compare(b, a));
```

也可以用接口声明：

```java
Queue<Integer> heap = new PriorityQueue<>();
```

但堆题里更推荐直接写 `PriorityQueue<Integer>`，语义更清楚。

### 1. 第 K 大元素

维护大小为 `k` 的小根堆，堆顶就是第 K 大。

```java
PriorityQueue<Integer> heap = new PriorityQueue<>();

for (int num : nums) {
    heap.offer(num);
    if (heap.size() > k) {
        heap.poll();
    }
}

int ans = heap.peek();
```

### 2. 前 K 个高频元素

```java
Map<Integer, Integer> count = new HashMap<>();
for (int num : nums) {
    count.put(num, count.getOrDefault(num, 0) + 1);
}

PriorityQueue<Integer> heap = new PriorityQueue<>(
        (a, b) -> Integer.compare(count.get(a), count.get(b))
);

for (int num : count.keySet()) {
    heap.offer(num);
    if (heap.size() > k) {
        heap.poll();
    }
}
```

### 3. 数据流中位数

一个大根堆放较小的一半，一个小根堆放较大的一半。

```java
PriorityQueue<Integer> small = new PriorityQueue<>((a, b) -> Integer.compare(b, a));
PriorityQueue<Integer> large = new PriorityQueue<>();

void addNum(int num) {
    if (small.isEmpty() || num <= small.peek()) {
        small.offer(num);
    } else {
        large.offer(num);
    }

    if (small.size() > large.size() + 1) {
        large.offer(small.poll());
    } else if (large.size() > small.size()) {
        small.offer(large.poll());
    }
}

double findMedian() {
    if (small.size() > large.size()) {
        return small.peek();
    }
    return ((double) small.peek() + large.peek()) / 2.0;
}
```

---

## 排序与比较器

### 1. 基础排序

```java
Arrays.sort(nums);
Collections.sort(list);
list.sort(null);
```

### 2. 二维数组排序

```java
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

### 3. 合并区间

```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
List<int[]> ans = new ArrayList<>();

for (int[] interval : intervals) {
    if (ans.isEmpty() || ans.get(ans.size() - 1)[1] < interval[0]) {
        ans.add(interval);
    } else {
        int[] last = ans.get(ans.size() - 1);
        last[1] = Math.max(last[1], interval[1]);
    }
}
```

### 4. 颜色分类

荷兰国旗三指针。

```java
int zero = 0;
int i = 0;
int two = nums.length - 1;

while (i <= two) {
    if (nums[i] == 0) {
        swap(nums, zero, i);
        zero++;
        i++;
    } else if (nums[i] == 2) {
        swap(nums, i, two);
        two--;
    } else {
        i++;
    }
}
```

---

## 二分查找

### 1. 基础二分

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

### 2. lower_bound

找第一个大于等于 `target` 的下标。

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

### 3. 查找左右边界

```java
int left = lowerBound(nums, target);
if (left == nums.length || nums[left] != target) {
    return new int[]{-1, -1};
}

int right = lowerBound(nums, target + 1) - 1;
return new int[]{left, right};
```

### 4. 旋转数组搜索

每次判断哪一半有序。

```java
int left = 0;
int right = nums.length - 1;

while (left <= right) {
    int mid = left + (right - left) / 2;
    if (nums[mid] == target) {
        return mid;
    }

    if (nums[left] <= nums[mid]) {
        if (nums[left] <= target && target < nums[mid]) {
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    } else {
        if (nums[mid] < target && target <= nums[right]) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
}

return -1;
```

---

## 常见坑

1. `PriorityQueue` 默认小根堆，大根堆要传比较器。
2. 比较器不要写 `a - b`，可能溢出；用 `Integer.compare(a, b)`。
3. `Queue<Integer> heap = new PriorityQueue<>();` 可以，但 `Deque<Integer> heap = new PriorityQueue<>();` 不可以。
4. 二分中点用 `left + (right - left) / 2`，避免溢出。
5. 二分要先确定区间语义：闭区间 `[left, right]` 还是左闭右开 `[left, right)`。
6. 查找右边界时，`target + 1` 可能溢出；极端场景可单独写 `upperBound`。
7. 排序二维数组时，如果有多关键字，比较规则要写完整。
8. 合并区间要先按左端点排序。
9. 颜色分类中，遇到 2 交换后不能立刻 `i++`，因为换过来的数还没检查。

---

## 建议刷题顺序

1. 35. 搜索插入位置
2. 34. 在排序数组中查找元素的第一个和最后一个位置
3. 33. 搜索旋转排序数组
4. 153. 寻找旋转排序数组中的最小值
5. 56. 合并区间
6. 75. 颜色分类
7. 215. 数组中的第 K 个最大元素
8. 347. 前 K 个高频元素
9. 23. 合并 K 个升序链表
10. 295. 数据流的中位数
11. 4. 寻找两个正序数组的中位数
