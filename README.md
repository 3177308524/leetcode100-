# LeetCode Hot 100 Java 笔记

这是一个面向 Java 刷题复习的 LeetCode Hot 100 知识点整理项目。内容按常见算法模块拆分，重点放在高频题型、核心模板、易错点和建议刷题顺序。

适合用于：

- 系统复习 LeetCode Hot 100
- 准备 Java 后端面试算法题
- 快速查找某类题的模板和注意事项
- 沉淀自己的刷题笔记

## 文档目录

| 文档 | 内容 |
| --- | --- |
| [leetcode_hot100_index.md](leetcode_hot100_index.md) | 总入口、Java API 速查、专题索引 |
| [leetcode_hot100_array_hash.md](leetcode_hot100_array_hash.md) | 数组、哈希、前缀和、双指针、滑动窗口 |
| [leetcode_hot100_linked_list.md](leetcode_hot100_linked_list.md) | 链表、快慢指针、链表反转、LRU |
| [leetcode_hot100_string_stack_queue.md](leetcode_hot100_string_stack_queue.md) | 字符串、栈、队列、单调栈、单调队列 |
| [leetcode_hot100_tree_graph.md](leetcode_hot100_tree_graph.md) | 二叉树、DFS/BFS、图、岛屿类问题 |
| [leetcode_hot100_backtracking_dp.md](leetcode_hot100_backtracking_dp.md) | 回溯、动态规划、贪心基础 |
| [leetcode_hot100_heap_sort_binary_search.md](leetcode_hot100_heap_sort_binary_search.md) | 堆、排序、二分查找 |

## 推荐学习顺序

1. 先看 [leetcode_hot100_index.md](leetcode_hot100_index.md)，熟悉 Java 常用 API、集合、字符串、排序和常见坑。
2. 按模块刷题，建议顺序是数组哈希、链表、字符串栈队列、树图、堆排序二分、回溯动态规划。
3. 每个专题先记住核心模板，再通过题目理解边界条件。
4. 遇到不会的题，优先总结“为什么想到这个方法”，不要只背最终代码。

## Java 刷题常用原则

- 栈和普通队列优先用 `ArrayDeque`。
- 堆用 `PriorityQueue`。
- 哈希计数用 `HashMap`，去重用 `HashSet`。
- 比较器优先用 `Integer.compare(a, b)`，避免 `a - b` 溢出。
- 字符串大量拼接用 `StringBuilder`。
- 二分查找用 `left + (right - left) / 2` 计算中点。
- 链表题优先考虑 `dummy` 虚拟头节点。

## 上传到 GitHub

如果这是一个新项目，可以按下面流程上传。

### 1. 在 GitHub 创建仓库

在 GitHub 新建一个 repository，例如：

```text
leetcode-hot100-java-notes
```

建议先不要勾选自动创建 README，因为本项目已经有 `README.md`。

### 2. 本地初始化 Git 仓库

在项目目录执行：

```bash
git init
git add .
git commit -m "docs: add leetcode hot100 java notes"
```

### 3. 关联远程仓库

把下面命令里的地址替换成你自己的 GitHub 仓库地址：

```bash
git branch -M main
git remote add origin https://github.com/你的用户名/leetcode-hot100-java-notes.git
git push -u origin main
```

### 4. 后续更新

以后修改笔记后，可以继续执行：

```bash
git add .
git commit -m "docs: update notes"
git push
```

## 说明

这些笔记以 LeetCode Hot 100 高频题为主，不追求覆盖所有算法细节，更偏向面试复习和刷题模板沉淀。后续可以继续按专题补充题解、复杂度分析和 Java 代码实现。
