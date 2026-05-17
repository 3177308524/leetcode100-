# LeetCode Hot 100 回溯、动态规划与贪心知识点整理（Java）

## 核心题型

1. 回溯
   - 17. 电话号码的字母组合
   - 22. 括号生成
   - 39. 组合总和
   - 46. 全排列
   - 78. 子集
   - 79. 单词搜索

2. 一维动态规划
   - 70. 爬楼梯
   - 198. 打家劫舍
   - 279. 完全平方数
   - 322. 零钱兑换
   - 300. 最长递增子序列

3. 二维动态规划
   - 62. 不同路径
   - 64. 最小路径和
   - 1143. 最长公共子序列
   - 72. 编辑距离
   - 5. 最长回文子串

4. 背包与布尔 DP
   - 416. 分割等和子集
   - 139. 单词拆分

5. 贪心
   - 55. 跳跃游戏
   - 45. 跳跃游戏 II
   - 121. 买卖股票的最佳时机
   - 763. 划分字母区间

---

## 回溯模板

回溯三件事：选择、递归、撤销选择。

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

### 1. 全排列

```java
List<List<Integer>> ans = new ArrayList<>();
List<Integer> path = new ArrayList<>();
boolean[] used;

void backtrack(int[] nums) {
    if (path.size() == nums.length) {
        ans.add(new ArrayList<>(path));
        return;
    }

    for (int i = 0; i < nums.length; i++) {
        if (used[i]) {
            continue;
        }

        used[i] = true;
        path.add(nums[i]);
        backtrack(nums);
        path.remove(path.size() - 1);
        used[i] = false;
    }
}
```

### 2. 组合 / 子集

组合和子集通常用 `start` 控制下一层从哪里开始，避免重复选择。

```java
void backtrack(int start, int[] nums) {
    ans.add(new ArrayList<>(path));

    for (int i = start; i < nums.length; i++) {
        path.add(nums[i]);
        backtrack(i + 1, nums);
        path.remove(path.size() - 1);
    }
}
```

### 3. 单词搜索

```java
boolean dfs(char[][] board, String word, int idx, int x, int y) {
    if (idx == word.length()) {
        return true;
    }

    int m = board.length;
    int n = board[0].length;

    if (x < 0 || x >= m || y < 0 || y >= n || board[x][y] != word.charAt(idx)) {
        return false;
    }

    char temp = board[x][y];
    board[x][y] = '#';

    boolean found = dfs(board, word, idx + 1, x + 1, y)
            || dfs(board, word, idx + 1, x - 1, y)
            || dfs(board, word, idx + 1, x, y + 1)
            || dfs(board, word, idx + 1, x, y - 1);

    board[x][y] = temp;
    return found;
}
```

---

## 动态规划模板

### 1. DP 思考步骤

1. 定义 `dp[i]` 或 `dp[i][j]` 表示什么。
2. 明确状态转移方程。
3. 初始化边界。
4. 确定遍历顺序。
5. 返回答案。

### 2. 打家劫舍

```java
public int rob(int[] nums) {
    int prev2 = 0;
    int prev1 = 0;

    for (int num : nums) {
        int cur = Math.max(prev1, prev2 + num);
        prev2 = prev1;
        prev1 = cur;
    }

    return prev1;
}
```

### 3. 最大子数组和

```java
int ans = nums[0];
int cur = nums[0];

for (int i = 1; i < nums.length; i++) {
    cur = Math.max(nums[i], cur + nums[i]);
    ans = Math.max(ans, cur);
}
```

### 4. 最长递增子序列

基础 DP：

```java
int n = nums.length;
int[] dp = new int[n];
Arrays.fill(dp, 1);
int ans = 1;

for (int i = 0; i < n; i++) {
    for (int j = 0; j < i; j++) {
        if (nums[j] < nums[i]) {
            dp[i] = Math.max(dp[i], dp[j] + 1);
        }
    }
    ans = Math.max(ans, dp[i]);
}
```

二分优化：

```java
int[] tails = new int[nums.length];
int size = 0;

for (int num : nums) {
    int left = 0;
    int right = size;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (tails[mid] < num) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    tails[left] = num;
    if (left == size) {
        size++;
    }
}
```

### 5. 0-1 背包

416. 分割等和子集：

```java
boolean[] dp = new boolean[target + 1];
dp[0] = true;

for (int num : nums) {
    for (int j = target; j >= num; j--) {
        dp[j] = dp[j] || dp[j - num];
    }
}
```

0-1 背包一维优化时，容量必须倒序遍历，避免同一个物品被重复使用。

### 6. 完全背包

322. 零钱兑换：

```java
int max = amount + 1;
int[] dp = new int[amount + 1];
Arrays.fill(dp, max);
dp[0] = 0;

for (int coin : coins) {
    for (int j = coin; j <= amount; j++) {
        dp[j] = Math.min(dp[j], dp[j - coin] + 1);
    }
}

return dp[amount] == max ? -1 : dp[amount];
```

完全背包一维优化时，容量通常正序遍历，允许同一个物品重复使用。

### 7. 二维字符串 DP

最长公共子序列：

```java
int m = text1.length();
int n = text2.length();
int[][] dp = new int[m + 1][n + 1];

for (int i = 1; i <= m; i++) {
    for (int j = 1; j <= n; j++) {
        if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
            dp[i][j] = dp[i - 1][j - 1] + 1;
        } else {
            dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
        }
    }
}
```

---

## 贪心模板

### 1. 跳跃游戏

```java
int farthest = 0;

for (int i = 0; i < nums.length; i++) {
    if (i > farthest) {
        return false;
    }
    farthest = Math.max(farthest, i + nums[i]);
}

return true;
```

### 2. 买卖股票一次交易

```java
int minPrice = Integer.MAX_VALUE;
int ans = 0;

for (int price : prices) {
    minPrice = Math.min(minPrice, price);
    ans = Math.max(ans, price - minPrice);
}
```

---

## 常见坑

1. 回溯加入答案时要复制路径：`new ArrayList<>(path)`。
2. 回溯递归后必须撤销选择，恢复现场。
3. 排列用 `used[]`，组合和子集通常用 `start`。
4. 有重复元素时，先排序，再在同层跳过重复选择。
5. DP 不要急着写代码，先写清楚 `dp[i]` 的含义。
6. 一维背包里，0-1 背包容量倒序，完全背包容量正序。
7. DP 初始化经常决定成败，比如 `dp[0] = true`、`dp[0] = 0`。
8. 贪心题要证明当前选择不会影响全局最优，不会证明时优先考虑 DP。

---

## 建议刷题顺序

1. 70. 爬楼梯
2. 121. 买卖股票的最佳时机
3. 198. 打家劫舍
4. 53. 最大子数组和
5. 55. 跳跃游戏
6. 17. 电话号码的字母组合
7. 46. 全排列
8. 78. 子集
9. 39. 组合总和
10. 79. 单词搜索
11. 300. 最长递增子序列
12. 416. 分割等和子集
13. 322. 零钱兑换
14. 139. 单词拆分
15. 1143. 最长公共子序列
16. 72. 编辑距离
