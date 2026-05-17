# LeetCode Hot 100 二叉树与图知识点整理（Java）

## 核心题型

1. 二叉树遍历
   - 94. 二叉树的中序遍历
   - 102. 二叉树的层序遍历
   - 104. 二叉树的最大深度
   - 226. 翻转二叉树

2. 二叉树递归信息
   - 101. 对称二叉树
   - 543. 二叉树的直径
   - 124. 二叉树中的最大路径和
   - 236. 二叉树的最近公共祖先

3. 二叉搜索树
   - 98. 验证二叉搜索树
   - 230. 二叉搜索树中第 K 小的元素

4. 树构造
   - 105. 从前序与中序遍历序列构造二叉树

5. 图与网格
   - 200. 岛屿数量
   - 994. 腐烂的橘子
   - 207. 课程表
   - 208. 实现 Trie

---

## Java 基础结构

```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode() {}

    TreeNode(int val) {
        this.val = val;
    }

    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

---

## 二叉树模板

### 1. DFS 递归

```java
void dfs(TreeNode root) {
    if (root == null) {
        return;
    }

    dfs(root.left);
    dfs(root.right);
}
```

最大深度：

```java
public int maxDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
}
```

### 2. 层序遍历 BFS

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> ans = new ArrayList<>();
    if (root == null) {
        return ans;
    }

    Queue<TreeNode> queue = new ArrayDeque<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();

        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();
            level.add(node.val);

            if (node.left != null) {
                queue.offer(node.left);
            }
            if (node.right != null) {
                queue.offer(node.right);
            }
        }

        ans.add(level);
    }

    return ans;
}
```

### 3. 中序遍历验证 BST

BST 中序遍历应该严格递增。

```java
Long prev = null;

public boolean isValidBST(TreeNode root) {
    if (root == null) {
        return true;
    }

    if (!isValidBST(root.left)) {
        return false;
    }

    if (prev != null && root.val <= prev) {
        return false;
    }
    prev = (long) root.val;

    return isValidBST(root.right);
}
```

### 4. 最近公共祖先

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) {
        return root;
    }

    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);

    if (left != null && right != null) {
        return root;
    }

    return left != null ? left : right;
}
```

### 5. 树的直径

```java
int ans = 0;

public int diameterOfBinaryTree(TreeNode root) {
    depth(root);
    return ans;
}

private int depth(TreeNode root) {
    if (root == null) {
        return 0;
    }

    int left = depth(root.left);
    int right = depth(root.right);
    ans = Math.max(ans, left + right);

    return Math.max(left, right) + 1;
}
```

---

## 图与网格模板

### 1. 网格 DFS

```java
int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

void dfs(char[][] grid, int x, int y) {
    int m = grid.length;
    int n = grid[0].length;

    if (x < 0 || x >= m || y < 0 || y >= n || grid[x][y] != '1') {
        return;
    }

    grid[x][y] = '0';

    for (int[] d : dirs) {
        dfs(grid, x + d[0], y + d[1]);
    }
}
```

### 2. 网格 BFS

```java
Queue<int[]> queue = new ArrayDeque<>();
queue.offer(new int[]{x, y});
grid[x][y] = '0';

while (!queue.isEmpty()) {
    int[] cur = queue.poll();

    for (int[] d : dirs) {
        int nx = cur[0] + d[0];
        int ny = cur[1] + d[1];

        if (nx < 0 || nx >= m || ny < 0 || ny >= n || grid[nx][ny] != '1') {
            continue;
        }

        grid[nx][ny] = '0';
        queue.offer(new int[]{nx, ny});
    }
}
```

### 3. 课程表拓扑排序

```java
List<List<Integer>> graph = new ArrayList<>();
for (int i = 0; i < numCourses; i++) {
    graph.add(new ArrayList<>());
}

int[] indegree = new int[numCourses];
for (int[] p : prerequisites) {
    graph.get(p[1]).add(p[0]);
    indegree[p[0]]++;
}

Queue<Integer> queue = new ArrayDeque<>();
for (int i = 0; i < numCourses; i++) {
    if (indegree[i] == 0) {
        queue.offer(i);
    }
}

int count = 0;
while (!queue.isEmpty()) {
    int cur = queue.poll();
    count++;

    for (int next : graph.get(cur)) {
        indegree[next]--;
        if (indegree[next] == 0) {
            queue.offer(next);
        }
    }
}

boolean canFinish = count == numCourses;
```

### 4. Trie

```java
class Trie {
    private Trie[] children = new Trie[26];
    private boolean end;

    public void insert(String word) {
        Trie node = this;
        for (char c : word.toCharArray()) {
            int idx = c - 'a';
            if (node.children[idx] == null) {
                node.children[idx] = new Trie();
            }
            node = node.children[idx];
        }
        node.end = true;
    }

    public boolean search(String word) {
        Trie node = find(word);
        return node != null && node.end;
    }

    public boolean startsWith(String prefix) {
        return find(prefix) != null;
    }

    private Trie find(String s) {
        Trie node = this;
        for (char c : s.toCharArray()) {
            int idx = c - 'a';
            if (node.children[idx] == null) {
                return null;
            }
            node = node.children[idx];
        }
        return node;
    }
}
```

---

## 常见坑

1. 二叉树递归先写 `root == null` 的出口。
2. 全局变量 `ans` 在 LeetCode 多次调用场景下要在入口函数里初始化。
3. BST 验证不能只比较父子节点，要用中序递增或上下界。
4. BST 节点值可能等于 `Integer.MIN_VALUE` / `Integer.MAX_VALUE`，上下界建议用 `long`。
5. 层序遍历要先保存当前层 `size`，否则新入队节点会影响当前层循环。
6. 网格 DFS/BFS 要先判边界，再访问数组。
7. 网格 visited 可以用单独数组，也可以直接修改原网格，取决于题目是否允许。
8. 拓扑排序里边方向要看清：课程表通常是 `prerequisite -> course`。
9. 递归深度太大可能栈溢出，网格大时可以考虑 BFS。

---

## 建议刷题顺序

1. 94. 二叉树的中序遍历
2. 104. 二叉树的最大深度
3. 226. 翻转二叉树
4. 102. 二叉树的层序遍历
5. 101. 对称二叉树
6. 543. 二叉树的直径
7. 98. 验证二叉搜索树
8. 230. 二叉搜索树中第 K 小的元素
9. 236. 二叉树的最近公共祖先
10. 105. 从前序与中序遍历序列构造二叉树
11. 124. 二叉树中的最大路径和
12. 200. 岛屿数量
13. 994. 腐烂的橘子
14. 207. 课程表
15. 208. 实现 Trie
