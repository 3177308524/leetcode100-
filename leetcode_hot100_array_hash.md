# LeetCode Hot 100 数组与哈希知识点整理（Java）

## 核心题型

1. 哈希表
   - 1. 两数之和
   - 49. 字母异位词分组
   - 128. 最长连续序列

2. 双指针
   - 283. 移动零
   - 11. 盛最多水的容器
   - 15. 三数之和

3. 滑动窗口
   - 3. 无重复字符的最长子串
   - 438. 找到字符串中所有字母异位词
   - 76. 最小覆盖子串

4. 前缀和
   - 560. 和为 K 的子数组
   - 238. 除自身以外数组的乘积

5. 矩阵
   - 73. 矩阵置零
   - 54. 螺旋矩阵
   - 48. 旋转图像
   - 240. 搜索二维矩阵 II

---

## 常用模板

### 1. HashMap 计数

适合统计频次、记录下标、判断差值是否出现。

```java
Map<Integer, Integer> map = new HashMap<>();

for (int num : nums) {
    map.put(num, map.getOrDefault(num, 0) + 1);
}
```

两数之和模板：

```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();

    for (int i = 0; i < nums.length; i++) {
        int need = target - nums[i];
        if (map.containsKey(need)) {
            return new int[]{map.get(need), i};
        }
        map.put(nums[i], i);
    }

    return new int[0];
}
```

### 2. 哈希去重

最长连续序列的关键：只从连续段起点开始扩展。

```java
Set<Integer> set = new HashSet<>();
for (int num : nums) {
    set.add(num);
}

int ans = 0;
for (int num : set) {
    if (!set.contains(num - 1)) {
        int cur = num;
        int len = 1;
        while (set.contains(cur + 1)) {
            cur++;
            len++;
        }
        ans = Math.max(ans, len);
    }
}
```

### 3. 双指针

有序数组、左右夹逼、原地修改数组常用。

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

移动零：

```java
int slow = 0;
for (int fast = 0; fast < nums.length; fast++) {
    if (nums[fast] != 0) {
        int temp = nums[slow];
        nums[slow] = nums[fast];
        nums[fast] = temp;
        slow++;
    }
}
```

三数之和固定套路：

```java
Arrays.sort(nums);

for (int i = 0; i < nums.length - 2; i++) {
    if (i > 0 && nums[i] == nums[i - 1]) {
        continue;
    }

    int left = i + 1;
    int right = nums.length - 1;

    while (left < right) {
        int sum = nums[i] + nums[left] + nums[right];
        if (sum == 0) {
            // 记录答案
            left++;
            right--;
            while (left < right && nums[left] == nums[left - 1]) {
                left++;
            }
            while (left < right && nums[right] == nums[right + 1]) {
                right--;
            }
        } else if (sum < 0) {
            left++;
        } else {
            right--;
        }
    }
}
```

### 4. 滑动窗口

适合连续子串、连续子数组。

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

无重复字符最长子串：

```java
Set<Character> set = new HashSet<>();
int left = 0;
int ans = 0;

for (int right = 0; right < s.length(); right++) {
    char c = s.charAt(right);
    while (set.contains(c)) {
        set.remove(s.charAt(left));
        left++;
    }
    set.add(c);
    ans = Math.max(ans, right - left + 1);
}
```

### 5. 前缀和 + HashMap

560. 和为 K 的子数组：

```java
public int subarraySum(int[] nums, int k) {
    Map<Integer, Integer> count = new HashMap<>();
    count.put(0, 1);

    int preSum = 0;
    int ans = 0;

    for (int num : nums) {
        preSum += num;
        ans += count.getOrDefault(preSum - k, 0);
        count.put(preSum, count.getOrDefault(preSum, 0) + 1);
    }

    return ans;
}
```

核心公式：

```text
preSum[j] - preSum[i] = k
=> preSum[i] = preSum[j] - k
```

### 6. 矩阵方向数组

网格题经常用方向数组。

```java
int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

for (int[] d : dirs) {
    int nx = x + d[0];
    int ny = y + d[1];
    if (nx < 0 || nx >= m || ny < 0 || ny >= n) {
        continue;
    }
}
```

---

## 常见坑

1. 数组长度是 `arr.length`，没有括号；字符串和集合是 `length()` / `size()`。
2. `subarray` 通常指连续子数组，`subsequence` 可以不连续。
3. 三数之和要先排序，并且对 `i`、`left`、`right` 都做好去重。
4. 前缀和统计次数时，必须先放 `count.put(0, 1)`，否则漏掉从下标 0 开始的子数组。
5. 数组和可能溢出时用 `long`，尤其是乘积、累加和、坐标编码。
6. `int[]` 作为 `HashMap` 的 key 默认按引用比较，不按内容比较。
7. 矩阵边界判断一定要在访问 `grid[x][y]` 之前。
8. 滑动窗口里移动 `left` 时，记得同步删掉或减少窗口计数。

---

## 建议刷题顺序

1. 1. 两数之和
2. 49. 字母异位词分组
3. 128. 最长连续序列
4. 283. 移动零
5. 11. 盛最多水的容器
6. 15. 三数之和
7. 3. 无重复字符的最长子串
8. 438. 找到字符串中所有字母异位词
9. 560. 和为 K 的子数组
10. 238. 除自身以外数组的乘积
11. 73. 矩阵置零
12. 54. 螺旋矩阵
13. 48. 旋转图像
14. 240. 搜索二维矩阵 II
