---
{"dg-publish":true,"permalink":"/programming/basic/leetcode/leetcode/"}
---


# 攻略

## 基本思路

同一类型的题集中刷，边刷边总结

对于一些简单题, 直接看题解, 看看有没有值得学习的方案, 有再记录下来, 不要直接上手做了, 省点时间

hard 没有思路就直接看答案, 一般都不会考这么难的

没有顺利解答的, 要标记一下, 使用 solved 标记

国内面试 200 题足以, 常考的是链表、字符串、动态规划, 但是要面试外企的话需要 500 题, 工作年限越长, 算法考的越少

### 分类列表

[有没有人一起从零开始刷力扣 - 力扣（LeetCode）](https://leetcode.cn/circle/discuss/4k2shg/)

+ 字符串
	+ 数字与字符串间转换, 做完了第一行就行, 第一行倒数第一个也没做, 留着以后做吧
	+ 字符串匹配只做了前 2 题, 第 2 题还是 unsolved
+ 中心扩展法的两题也先不做了, 感觉是动态规划的衍生题目
	+ 数与位
	+ 数字的位操作, 做到 231
	+ 简单数学题, 只做了第一题, 感觉考的不会太多, 太离谱了
+ 栈
	+ 388 没做, 看着就烦
	+ 括号匹配: 32 没做
	+ 递归的也先不做, 因为这里的重点是如何用栈去解决递归问题, 得想学好递归再回来简化
+ 链表最后一题没做
+ 哈希表
	+ 哈希表最后一题没做
+ 贪心算法都没有做, 等之后和动态规划一起处理吧
+ 双指针
	+ 头尾指针, 最后一题接雨水没做, 属于动态规划 + 栈的复合题目, 最后再总结吧
	+ 滑动窗口, 最后两道困难题没有做, 留着总结完之后再尝试吧
	+ 快慢指针最后一题, 多方法没做的
+ 四叉树没做
+ 回溯法, 有一些没做, 按照随想录走的, 剩余的做查漏补, go to next!
	+ 几个回溯法的都先跳过, 直接从 BFS 开始, bfs 也到此为止吧, 直接从动态规划开始了, 后面再查漏补缺

### 题解大全

我的建议是先 acwing 再代码随想录 + labuladong 的算法小抄。做为非科班转码选手，可能会有人想知道**我是怎么刷 Leetcode 的**，在此献上:[大家都是如何刷 LeetCode 的？](https://www.zhihu.com/question/280279208/answer/2684962292)

官方的题解基本就够用了，多拓宽思路

+ https://programmercarl.com/0704.%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE.html#%E6%80%9D%E8%B7%AF
+ https://leetcode-solution-leetcode-pp.gitbook.io/leetcode-solution/medium/get-kth-magic-number-lcci
+ [ac_oier - 力扣（LeetCode）](https://leetcode.cn/u/ac_oier/)
+ https://xiaochen1024.com/series/6196129fc1553b002e57bef5/6196208ec1553b002e57bef6

[搞定大厂算法面试之leetcode精讲](https://xiaochen1024.com/courseware/60b4f11ab1aa91002eb53b18)

# 总结

深度遍历与栈，深度遍历可以通过栈形式的迭代代替

广度遍历与队列，广度遍历只能通过队列形式，无法通过递归

## 注意入参

提交有 case 不通过是有惩罚的

最常见的问题: 示例给出的都是大于 0 的正数, 我会下意识的以为所有的输入都是正数, 但是其实是会有负数的

这时候初始化值就不能为 0, 而应该是 `Number.MIN_SAFE_INTERGER`

## 优先队列

一个数组, 排序的同时, 要获取每个数的 order , 要对应上原来的位置

[135. 分发糖果](135.%20分发糖果.md)

## 减少特判

dummyHead

网格问题, 填充额外的行, col 不用填充, 读取到 undefined 自动结束递归

## 递归改迭代

用一个额外的变量维护结果.

+ 链表的 prev cur
+ 树的层次遍历队列

[[programming/basic/data-structure/linked-list\|linked-list]]

[[programming/basic/data-structure/tree-traversal\|../data-structure/tree-traversal]]

两种数据结构都很常使用递归和迭代, 可以稍微总结一下递归的套路、迭代的套路

递归的一般思路:

1. 确定终止条件
2. 确定单层递归的逻辑
3. 需要用到什么参数就补充什么参数, 再确定返回值即可

### 递归与回溯

[backtracking](../algorithm/backtracking.md)

## Index 问题

有 n 个 item, 每个 item 的 index 从 0 开始, 每个 item 的前后都可以插入新的 item

> 这个可插入的 slot 就像是尺子上面 5cm 那个长条, 除了线段是一段一段的, 每一段前后都会有一根长条, 这个长条归属于谁?

一个解决方案就是在 list 的最后再添加一个空的 item, 这个 item 的唯一作用就是把所有的 slot 都定义成了在 item 前面. 解决了 slot 的归属问题. 如果你没有这个空的 slot , 就不清楚最后一个 slot 是谁的了.

这个问题从小到遇到过无数遍了, 比如 星期一到星期五之间有多少天? 包不包含头? 包不包含尾?

[48. Rotate Image](48.%20Rotate%20Image.md) 这一题为了处理好第 n 列和索引 i j 的关系, 浪费了不少时间.

我们在日常中描述矩阵的时候, 都是 one index 的, 但是在代码里却要切换成 0 index

先用正常逻辑去处理 index, 如果出现边界情况, 比如 index 为负 1, 这时候再把所有的 index + 1

从特殊到一般, 先想想最简单的情况 index 的关系是怎么样的, 然后再拓展到一般情况

[前缀和](!leetcode.md#前缀和)

## Arithmetic-sequence

| File                                                                                                                           | solution tips overview                                          |
| ------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------- |
| [[programming/basic/leetcode/645. Set Mismatch\|645. Set Mismatch]]                                                         | [[programming/basic/leetcode/645. Set Mismatch#solution tips\|645. Set Mismatch#solution tips]]                             |
| [[programming/basic/leetcode/448. Find All Numbers Disappeared in an Array\|448. Find All Numbers Disappeared in an Array]] | [[programming/basic/leetcode/448. Find All Numbers Disappeared in an Array#solution tips\|448. Find All Numbers Disappeared in an Array#solution tips]] |
| [[programming/basic/leetcode/442. Find All Duplicates in an Array\|442. Find All Duplicates in an Array]]                   | [[programming/basic/leetcode/442. Find All Duplicates in an Array#solution tips\|442. Find All Duplicates in an Array#solution tips]]          |
| [[programming/basic/leetcode/41. First Missing Positive\|41. First Missing Positive]]                                       | [[programming/basic/leetcode/41. First Missing Positive#solution tips\|41. First Missing Positive#solution tips]]                    |

{ .block-language-dataview}

## Top-k

| File                                                                                                           | solution tips overview                                  |
| -------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| [[programming/basic/leetcode/628. Maximum Product of Three Numbers\|628. Maximum Product of Three Numbers]] | [[programming/basic/leetcode/628. Maximum Product of Three Numbers#solution tips\|628. Maximum Product of Three Numbers#solution tips]] |
| [[programming/basic/leetcode/414. Third Maximum Number\|414. Third Maximum Number]]                         | [[programming/basic/leetcode/414. Third Maximum Number#solution tips\|414. Third Maximum Number#solution tips]]             |
| [[programming/basic/leetcode/230. 二叉搜索树中第K小的元素\|230. 二叉搜索树中第K小的元素]]                                         | [[programming/basic/leetcode/230. 二叉搜索树中第K小的元素#solution tips\|230. 二叉搜索树中第K小的元素#solution tips]]                     |

{ .block-language-dataview}

## Time-sequence 时间类题目

### 主要特点

1. 注重时间开始和结束，比如 1s 初，1s 末
2. 注重时间的间隔，1s 初和 1s 末，间隔了 1s

### Solution Tips

| File                                                         | overview                       |
| ------------------------------------------------------------ | ------------------------------ |
| [[programming/basic/leetcode/636. 函数的独占时间\|636. 函数的独占时间]] | [[programming/basic/leetcode/636. 函数的独占时间#solution tips\|636. 函数的独占时间#solution tips]] |
| [[programming/basic/leetcode/495. 提莫攻击\|495. 提莫攻击]]       | [[programming/basic/leetcode/495. 提莫攻击#solution tips\|495. 提莫攻击#solution tips]]    |

{ .block-language-dataview}

想不明白的时候多画时序图

## 节省空间复杂度的方法

### 原地标记

负数标记, 或者随便弄一个能识别出来的标记, -0, xxx 都行的

![73. 矩阵置零](programming/basic/leetcode/73.%20矩阵置零.md#Solution%20Tips)

#### 滚动数组都是类似的原理

其实本质上就是利用数组自身的空间

循环数组也是一样的, 旋转数组

## 前缀和

也被归类为 dp ,然后因为可以根据前一个计算后一个, 也是一种动态的, 可以利用滚动思想, 节约空间复杂度

一次性搞定前缀和: [Loading Question... - 力扣（LeetCode）](https://leetcode.cn/problems/unique-substrings-in-wraparound-string/solution/xi-fa-dai-ni-xue-suan-fa-yi-ci-gao-ding-qian-zhui-/)

[Loading Question... - 力扣（LeetCode）](https://leetcode.cn/problems/subarray-sum-equals-k/solution/de-liao-yi-wen-jiang-qian-zhui-he-an-pai-yhyf/)

涉及连续子数组的问题，我们通常有两种思路：一是滑动窗口、二是前缀和。

[209. 长度最小的子数组 题解 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-size-subarray-sum/solution/chang-du-zui-xiao-de-zi-shu-zu-by-leetcode-solutio/)

## 矩阵操作

遍历矩阵的正确姿势: 两个 for 循环的标记变量直接使用 row 和 col, 不需要再声明 i j 来获取临时的 row col

如何遍历矩阵最外面的那一圈? 用一个路径数组, `[0,1],[1,0]`, 这样就可以遍历一圈了

```js
    const firstRow = Array.from({ length: board[0].length }, (val, index) => [0, index]);
    const lastRow = Array.from({ length: board[0].length }, (val, index) => [board.length - 1, index]);
    const firstCol = Array.from({ length: board.length - 2 }, (val, index) => [index + 1, 0]);
    const lastCol = Array.from({ length: board.length - 2 }, (val, index) => [index + 1, board[0].length - 1]);
    const step = [...firstRow, ...lastCol, ...lastRow, ...firstCol];
```

## 位运算

### 异或

异或的特性就是自己异或自己为 0, 利用这个特性, 可以用于处理奇偶数次的题目

## 自然数组

数组长度为 n, 且包含 0 到 n - 1 的自然数

特点就是每个数会对应 index, 少了哪个 index 代表那个数缺失了, 被访问过

跟随 index 跳动会成环, 记得还有一道题是超出了 n 的, 但是超出之后会取模, 所以本质上还是自然数组

| File                                                                                                                           | solution tips overview                                          |
| ------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------- |
| [[programming/basic/leetcode/565. 数组嵌套\|565. 数组嵌套]]                                                                         | [[programming/basic/leetcode/565. 数组嵌套#solution tips\|565. 数组嵌套#solution tips]]                                     |
| [[programming/basic/leetcode/448. Find All Numbers Disappeared in an Array\|448. Find All Numbers Disappeared in an Array]] | [[programming/basic/leetcode/448. Find All Numbers Disappeared in an Array#solution tips\|448. Find All Numbers Disappeared in an Array#solution tips]] |
| [[programming/basic/leetcode/442. Find All Duplicates in an Array\|442. Find All Duplicates in an Array]]                   | [[programming/basic/leetcode/442. Find All Duplicates in an Array#solution tips\|442. Find All Duplicates in an Array#solution tips]]          |
| [[programming/basic/leetcode/41. First Missing Positive\|41. First Missing Positive]]                                       | [[programming/basic/leetcode/41. First Missing Positive#solution tips\|41. First Missing Positive#solution tips]]                    |

{ .block-language-dataview}

## 跳过重复

| File                                                       | solution tips overview        |
| ---------------------------------------------------------- | ----------------------------- |
| [[programming/basic/leetcode/90. 子集 II\|90. 子集 II]]     | [[programming/basic/leetcode/90. 子集 II#solution tips\|90. 子集 II#solution tips]]   |
| [[programming/basic/leetcode/491. 递增子序列\|491. 递增子序列]]   | [[programming/basic/leetcode/491. 递增子序列#solution tips\|491. 递增子序列#solution tips]]  |
| [[programming/basic/leetcode/47. 全排列 II\|47. 全排列 II]]   | [[programming/basic/leetcode/47. 全排列 II#solution tips\|47. 全排列 II#solution tips]]  |
| [[programming/basic/leetcode/40. 组合总和 II\|40. 组合总和 II]] | [[programming/basic/leetcode/40. 组合总和 II#solution tips\|40. 组合总和 II#solution tips]] |
| [[programming/basic/leetcode/18. 四数之和\|18. 四数之和]]       | [[programming/basic/leetcode/18. 四数之和#solution tips\|18. 四数之和#solution tips]]    |
| [[programming/basic/leetcode/15. 三数之和\|15. 三数之和]]       | [[programming/basic/leetcode/15. 三数之和#solution tips\|15. 三数之和#solution tips]]    |

{ .block-language-dataview}

## ACM 考点

https://oi-wiki.org/string/minimal-string/

https://www.bilibili.com/video/BV1rT411u7b9?spm_id_from=333.1007.tianma.1-2-2.click&vd_source=db8a4b4129af2e1d7a3e3f6357bb4d45

# Leetcode Overview

| File                                                                                                                           | difficulty | etags                                                                                                                                                                                     | unsolved | date-created                |
| ------------------------------------------------------------------------------------------------------------------------------ | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | --------------------------- |
| [[programming/basic/leetcode/968. 监控二叉树\|968. 监控二叉树]]                                                                       | hard       | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/dp</li><li>#leetcode/unsolved</li><li>#leetcode/tree/traversal/sub-tree</li></ul>                                                    | true     | 2023-07-09-Sun, 8:14:47 pm  |
| [[programming/basic/leetcode/738. 单调递增的数字\|738. 单调递增的数字]]                                                                   | medium     | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/math/monotone</li><li>#leetcode/unsolved</li></ul>                                                                                   | true     | 2023-07-09-Sun, 7:21:25 pm  |
| [[programming/basic/leetcode/56. 合并区间\|56. 合并区间]]                                                                           | medium     | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/math/interval</li></ul>                                                                                                              | false    | 2023-07-09-Sun, 11:25:41 am |
| [[programming/basic/leetcode/763. 划分字母区间\|763. 划分字母区间]]                                                                     | medium     | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/math/interval</li><li>#leetcode/unsolved</li></ul>                                                                                   | true     | 2023-07-09-Sun, 10:52:30 am |
| [[programming/basic/leetcode/435. 无重叠区间\|435. 无重叠区间]]                                                                       | medium     | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/math/interval</li><li>#leetcode/dp</li><li>#leetcode/unsolved</li></ul>                                                              | true     | 2023-07-08-Sat, 9:18:12 pm  |
| [[programming/basic/leetcode/452. 用最少数量的箭引爆气球\|452. 用最少数量的箭引爆气球]]                                                           | medium     | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/unsolved</li><li>#leetcode/math/interval</li></ul>                                                                                   | true     | 2023-07-08-Sat, 8:20:17 pm  |
| [[programming/basic/leetcode/406. 根据身高重建队列\|406. 根据身高重建队列]]                                                                 | medium     | <ul><li>#leetcode/hash-table/order</li><li>#leetcode/unsolved</li><li>#leetcode/greedy-algorithm/arrangement</li><li>#leetcode/priority-queue</li><li>#leetcode/sort</li></ul>            | true     | 2023-07-08-Sat, 3:44:39 pm  |
| [[programming/basic/leetcode/45. 跳跃游戏 II\|45. 跳跃游戏 II]]                                                                     | medium     | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/unsolved</li></ul>                                                                                                                   | true     | 2023-07-07-Fri, 9:06:04 am  |
| [[programming/basic/leetcode/860. 柠檬水找零\|860. 柠檬水找零]]                                                                       | easy       | <ul><li>#leetcode/greedy-algorithm</li></ul>                                                                                                                                              | false    | 2023-07-07-Fri, 3:33:00 pm  |
| [[programming/basic/leetcode/135. 分发糖果\|135. 分发糖果]]                                                                         | hard       | <ul><li>#leetcode/hash-table/order</li><li>#leetcode/unsolved</li><li>#leetcode/greedy-algorithm/arrangement</li><li>#leetcode/priority-queue</li></ul>                                   | true     | 2023-07-07-Fri, 2:57:11 pm  |
| [[programming/basic/leetcode/134. 加油站\|134. 加油站]]                                                                           | medium     | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/unsolved</li></ul>                                                                                                                   | true     | 2023-07-07-Fri, 2:20:06 pm  |
| [[programming/basic/leetcode/1005. K 次取反后最大化的数组和\|1005. K 次取反后最大化的数组和]]                                                     | easy       | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/math</li></ul>                                                                                                                       | false    | 2023-07-07-Fri, 10:36:09 am |
| [[programming/basic/leetcode/55. 跳跃游戏\|55. 跳跃游戏]]                                                                           | medium     | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/unsolved</li></ul>                                                                                                                   | true     | 2023-07-06-Thu, 8:10:20 pm  |
| [[programming/basic/leetcode/122. 买卖股票的最佳时机 II\|122. 买卖股票的最佳时机 II]]                                                         | medium     | <ul><li>#leetcode/brainteasers</li><li>#leetcode/math/monotone</li><li>#leetcode/dp</li><li>#leetcode/unsolved</li><li>#leetcode/greedy-algoritm</li><li>#leetcode/sub/sequence</li></ul> | true     | 2023-07-06-Thu, 7:50:10 pm  |
| [[programming/basic/leetcode/53. 最大子数组和\|53. 最大子数组和]]                                                                       | medium     | <ul><li>#leetcode/brainteasers</li><li>#leetcode/dp</li><li>#leetcode/unsolved</li><li>#leetcode/greedy-algoritm</li><li>#leetcode/sub/onsecutive</li></ul>                               | true     | 2023-07-06-Thu, 7:37:09 pm  |
| [[programming/basic/leetcode/376. 摆动序列\|376. 摆动序列]]                                                                         | medium     | <ul><li>#leetcode/brainteasers</li><li>#leetcode/math/monotone</li><li>#leetcode/dp</li><li>#leetcode/unsolved</li><li>#leetcode/greedy-algoritm</li><li>#leetcode/sub/sequence</li></ul> | true     | 2023-07-06-Thu, 10:37:33 am |
| [[programming/basic/leetcode/455. 分发饼干\|455. 分发饼干]]                                                                         | easy       | <ul><li>#leetcode/greedy-algorithm</li></ul>                                                                                                                                              | false    | 2023-07-04-Tue, 9:17:42 pm  |
| [[programming/basic/leetcode/30. 被围绕的区域\|30. 被围绕的区域]]                                                                       | medium     | <ul><li>#leetcode/graph/traversal</li><li>#leetcode/array/matrix</li><li>#leetcode/graph/matrix</li><li>#unsolved</li></ul>                                                               | true     | 2023-07-04-Tue, 7:36:29 pm  |
| [[programming/basic/leetcode/37. 解数独\|37. 解数独]]                                                                             | hard       | <ul><li>#leetcode/backtracking/permutation/sudoku</li><li>#leetcode/bit-manipulation</li><li>#leetcode/unsolved</li><li>#leetcode/matrix</li></ul>                                        | true     | 2023-06-29-Thu, 8:52:53 am  |
| [[programming/basic/leetcode/200. 岛屿数量\|200. 岛屿数量]]                                                                         | medium     | <ul><li>#leetcode/grid/island</li><li>#leetcode/graph/traversal</li><li>#leetcode/unsolved</li><li>#leetcode/union-find</li></ul>                                                         | true     | 2023-06-29-Thu, 6:33:11 pm  |
| [[programming/basic/leetcode/133. 克隆图\|133. 克隆图]]                                                                           | medium     | <ul><li>#leetcode/graph/traversal</li><li>#leetcode/unsolved</li></ul>                                                                                                                    | true     | 2023-06-29-Thu, 2:10:23 pm  |
| [[programming/basic/leetcode/51. N 皇后\|51. N 皇后]]                                                                           | hard       | <ul><li>#leetcode/backtracking/permutation/chessboard</li><li>#leetcode/bit-manipulation</li><li>#leetcode/unsolved</li><li>#leetcode/matrix</li></ul>                                    | true     | 2023-06-27-Tue, 8:38:11 am  |
| [[programming/basic/leetcode/332. 重新安排行程\|332. 重新安排行程]]                                                                     | hard       | <ul><li>#leetcode/backtracking</li><li>#leetcode/traversal/dfs</li><li>#leetcode/graph/path</li></ul>                                                                                     | false    | 2023-06-25-Sun, 10:45:29 am |
| [[programming/basic/leetcode/47. 全排列 II\|47. 全排列 II]]                                                                       | medium     | <ul><li>#leetcode/backtracking/permutation</li><li>#leetcode/skip-dup</li></ul>                                                                                                           | false    | 2023-06-24-Sat, 10:46:34 am |
| [[programming/basic/leetcode/46. 全排列\|46. 全排列]]                                                                             | medium     | <ul><li>#leetcode/backtracking/permutation</li></ul>                                                                                                                                      | false    | 2023-06-24-Sat, 10:19:14 am |
| [[programming/basic/leetcode/491. 递增子序列\|491. 递增子序列]]                                                                       | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li><li>#leetcode/skip-dup</li></ul>      | true     | 2023-06-22-Thu, 7:49:52 pm  |
| [[programming/basic/leetcode/90. 子集 II\|90. 子集 II]]                                                                         | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li><li>#leetcode/skip-dup</li></ul>      | true     | 2023-06-22-Thu, 3:06:59 pm  |
| [[programming/basic/leetcode/78. 子集\|78. 子集]]                                                                               | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li></ul>                                 | true     | 2023-06-22-Thu, 11:55:52 am |
| [[programming/basic/leetcode/93. 复原 IP 地址\|93. 复原 IP 地址]]                                                                   | medium     | <ul><li>#leetcode/backtracking/combination/partition</li><li>#leetcode/unsolved</li></ul>                                                                                                 | true     | 2023-06-22-Thu, 11:07:29 am |
| [[programming/basic/leetcode/131. 分割回文串\|131. 分割回文串]]                                                                       | medium     | <ul><li>#leetcode/backtracking/combination/partition</li><li>#leetcode/dp/memo</li><li>#leetcode/unsolved</li></ul>                                                                       | true     | 2023-06-21-Wed, 8:48:00 pm  |
| [[programming/basic/leetcode/40. 组合总和 II\|40. 组合总和 II]]                                                                     | medium     | <ul><li>#leetcode/backtracking/combination</li><li>#leetcode/skip-dup</li></ul>                                                                                                           | false    | 2023-06-21-Wed, 7:22:58 pm  |
| [[programming/basic/leetcode/39. 组合总和\|39. 组合总和]]                                                                           | medium     | <ul><li>#leetcode/backtracking/combination</li></ul>                                                                                                                                      | false    | 2023-06-21-Wed, 5:06:05 pm  |
| [[programming/basic/leetcode/216. 组合总和 III\|216. 组合总和 III]]                                                                 | medium     | <ul><li>#leetcode/backtracking/combination</li><li>#leetcode/backtracking/binary</li><li>#leetcode/sub/sequence</li></ul>                                                                 | false    | 2023-06-21-Wed, 4:14:57 pm  |
| [[programming/basic/leetcode/77. 组合\|77. 组合]]                                                                               | medium     | <ul><li>#leetcode/backtracking/combination</li><li>#leetcode/sub/sequence</li></ul>                                                                                                       | false    | 2023-06-21-Wed, 2:45:54 pm  |
| [[programming/basic/leetcode/526. 优美的排列\|526. 优美的排列]]                                                                       | medium     | <ul><li>#leetcode/backtracking</li><li>#leetcode/unsolved</li><li>#leetcode/dp/status-compression</li></ul>                                                                               | true     | 2023-06-20-Tue, 4:48:50 pm  |
| [[programming/basic/leetcode/397. 整数替换\|397. 整数替换]]                                                                         | medium     | <ul><li>#leetcode/dp/memo</li><li>#leetcode/math</li><li>#leetcode/unsolved</li><li>#leetcode/graph/traversal/dfs</li></ul>                                                               | true     | 2023-06-20-Tue, 2:55:57 pm  |
| [[programming/basic/leetcode/17. 电话号码的字母组合\|17. 电话号码的字母组合]]                                                                 | medium     | <ul><li>#leetcode/backtracking/combination</li><li>#leetcode/graph/traversal/dfs</li></ul>                                                                                                | false    | 2023-06-20-Tue, 2:25:19 pm  |
| [[programming/basic/leetcode/565. 数组嵌套\|565. 数组嵌套]]                                                                         | medium     | <ul><li>#leetcode/graph/circle</li><li>#leetcode/space/in-place</li><li>#leetcode/unsolved</li><li>#leetcode/array/natural-array</li></ul>                                                | true     | 2023-06-18-Sun, 9:45:38 pm  |
| [[programming/basic/leetcode/99. 恢复二叉搜索树\|99. 恢复二叉搜索树]]                                                                     | medium     | <ul><li>#leetcode/tree/traversal</li><li>#leetcode/unsolved</li></ul>                                                                                                                     | true     | 2023-06-17-Sat, 9:32:40 pm  |
| [[programming/basic/leetcode/501. 二叉搜索树中的众数\|501. 二叉搜索树中的众数]]                                                               | medium     | <ul><li>#leetcode/tree/traversal/morris</li><li>#leetcode/hash-table/count</li></ul>                                                                                                      | false    | 2023-06-16-Fri, 10:36:36 am |
| [[programming/basic/leetcode/236. 二叉树的最近公共祖先\|236. 二叉树的最近公共祖先]]                                                             | medium     | <ul><li>#leetcode/tree/traversal</li><li>#leetcode/tree/ansector</li><li>#leetcode/unsolved</li></ul>                                                                                     | true     | 2023-06-15-Thu, 7:20:18 pm  |
| [[programming/basic/leetcode/235. 二叉搜索树的最近公共祖先\|235. 二叉搜索树的最近公共祖先]]                                                         | medium     | <ul><li>#leetcode/tree/traversal</li><li>#leetcode/tree/bst</li><li>#leetcode/tree/ansector</li></ul>                                                                                     | false    | 2023-06-15-Thu, 11:48:03 am |
| [[programming/basic/leetcode/114. 二叉树展开为链表\|114. 二叉树展开为链表]]                                                                 | medium     | <ul><li>#leetcode/tree/transform</li><li>#leetcode/linked-list/transoform</li></ul>                                                                                                       | false    | 2023-06-14-Wed, 7:33:31 pm  |
| [[programming/basic/leetcode/106. 从中序与后序遍历序列构造二叉树\|106. 从中序与后序遍历序列构造二叉树]]                                                   | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/divide-and-conquer</li><li>#leetcode/unsolved</li></ul>                                                                       | true     | 2023-06-13-Tue, 10:14:12 am |
| [[programming/basic/leetcode/105. 从前序与中序遍历序列构造二叉树\|105. 从前序与中序遍历序列构造二叉树]]                                                   | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/divide-and-conquer</li><li>#leetcode/unsolved</li></ul>                                                                       | true     | 2023-06-12-Mon, 2:25:56 pm  |
| [[programming/basic/leetcode/109. 有序链表转换二叉搜索树\|109. 有序链表转换二叉搜索树]]                                                           | medium     | <ul><li>#leetcoe/tree/bst</li><li>#leetcode/divide-and-conquer</li><li>#leetcode/unsolved</li></ul>                                                                                       | true     | 2023-06-11-Sun, 6:46:29 pm  |
| [[programming/basic/leetcode/108. 将有序数组转换为二叉搜索树\|108. 将有序数组转换为二叉搜索树]]                                                       | easy       | <ul><li>#leetcoe/tree/bst</li><li>#leetcode/divide-and-conquer</li></ul>                                                                                                                  | false    | 2023-06-11-Sun, 4:57:08 pm  |
| [[programming/basic/leetcode/95. 不同的二叉搜索树 II\|95. 不同的二叉搜索树 II]]                                                             | medium     | <ul><li>#leetcode/dp/exhaustion</li><li>#leetcoe/tree/bst</li><li>#leetcode/unsolved</li><li>#leetcode/divide-and-conquer</li></ul>                                                       | true     | 2023-06-11-Sun, 3:37:01 pm  |
| [[programming/basic/leetcode/669. 修剪二叉搜索树\|669. 修剪二叉搜索树]]                                                                   | medium     | <ul><li>#leetcode/tree/bst</li></ul>                                                                                                                                                      | false    | 2023-06-10-Sat, 3:59:42 pm  |
| [[programming/basic/leetcode/98. 验证二叉搜索树\|98. 验证二叉搜索树]]                                                                     | medium     | <ul><li>#leetcode/tree/bst</li></ul>                                                                                                                                                      | false    | 2023-06-10-Sat, 12:51:22 pm |
| [[programming/basic/leetcode/230. 二叉搜索树中第K小的元素\|230. 二叉搜索树中第K小的元素]]                                                         | medium     | <ul><li>#leetcode/tree/bst</li><li>#leetcode/top-k</li><li>#leetcode/tree/avl</li></ul>                                                                                                   | false    | 2023-06-09-Fri, 8:13:39 pm  |
| [[programming/basic/leetcode/538. 把二叉搜索树转换为累加树\|538. 把二叉搜索树转换为累加树]]                                                         | medium     | <ul><li>#leetcode/tree/bst</li></ul>                                                                                                                                                      | false    | 2023-06-09-Fri, 7:50:18 pm  |
| [[programming/basic/leetcode/530. 二叉搜索树的最小绝对差\|530. 二叉搜索树的最小绝对差]]                                                           | easy       | <ul><li>#leetcode/tree/bst</li></ul>                                                                                                                                                      | false    | 2023-06-09-Fri, 7:33:01 pm  |
| [[programming/basic/leetcode/700. 二叉搜索树中的搜索\|700. 二叉搜索树中的搜索]]                                                               | easy       | <ul><li>#leetcode/tree/bst</li><li>#leetcode/tree/traversal/sub-tree</li></ul>                                                                                                            | false    | 2023-06-09-Fri, 5:36:56 pm  |
| [[programming/basic/leetcode/449. 序列化和反序列化二叉搜索树\|449. 序列化和反序列化二叉搜索树]]                                                       | medium     | <ul><li>#leetcode/tree/traversal</li><li>#leetcode/unsolved</li><li>#leetcode/tree/bst</li></ul>                                                                                          | true     | 2023-06-09-Fri, 3:12:46 pm  |
| [[programming/basic/leetcode/652. 寻找重复的子树\|652. 寻找重复的子树]]                                                                   | medium     | <ul><li>#leetcode/tree/serialize</li><li>#leetcode/hash-table</li><li>#leetcode/sub/consecutive</li><li>#leetcode/unsolved</li></ul>                                                      | true     | 2023-06-09-Fri, 10:16:18 am |
| [[programming/basic/leetcode/331. 验证二叉树的前序序列化\|331. 验证二叉树的前序序列化]]                                                           | medium     | <ul><li>#leetcode/tree/serialize</li><li>#leetcode/graph/degree</li><li>#leetcode/unsolved</li></ul>                                                                                      | true     | 2023-06-08-Thu, 8:39:33 pm  |
| [[programming/basic/leetcode/606. 根据二叉树创建字符串\|606. 根据二叉树创建字符串]]                                                             | easy       | <ul><li>#leetcode/tree/serialize</li></ul>                                                                                                                                                | false    | 2023-06-08-Thu, 7:27:39 pm  |
| [[programming/basic/leetcode/297. 二叉树的序列化与反序列化\|297. 二叉树的序列化与反序列化]]                                                         | hard       | <ul><li>#leetcode/tree/serialize</li><li>#leetcode/unsolved</li></ul>                                                                                                                     | true     | 2023-06-08-Thu, 7:16:40 pm  |
| [[programming/basic/leetcode/543. 二叉树的直径\|543. 二叉树的直径]]                                                                     | easy       | <ul><li>#leetcode/tree/traversal/sub-tree</li></ul>                                                                                                                                       | false    | 2023-06-08-Thu, 2:58:58 pm  |
| [[programming/basic/leetcode/117. 填充每个节点的下一个右侧节点指针 II\|117. 填充每个节点的下一个右侧节点指针 II]]                                           | medium     | <ul><li>#leetcode/tree/traversal/linear</li><li>#leetcode/linked-list</li></ul>                                                                                                           | false    | 2023-06-06-Tue, 7:50:36 pm  |
| [[programming/basic/leetcode/116. 填充每个节点的下一个右侧节点指针\|116. 填充每个节点的下一个右侧节点指针]]                                                 | medium     | <ul><li>#leetcode/tree/traversal/linear</li></ul>                                                                                                                                         | false    | 2023-06-06-Tue, 7:44:44 pm  |
| [[programming/basic/leetcode/655. 输出二叉树\|655. 输出二叉树]]                                                                       | medium     | <ul><li>#leetcode/tree/traversal</li><li>#leetcode/unsolved</li><li>#leetcode/tree/index</li></ul>                                                                                        | true     | 2023-06-06-Tue, 7:23:54 pm  |
| [[programming/basic/leetcode/199. 二叉树的右视图\|199. 二叉树的右视图]]                                                                   | medium     | <ul><li>#leetcode/tree/traversal/linear</li></ul>                                                                                                                                         | false    | 2023-06-06-Tue, 6:12:20 pm  |
| [[programming/basic/leetcode/404. 左叶子之和\|404. 左叶子之和]]                                                                       | medium     | <ul><li>#leetcode/tree/traversal</li></ul>                                                                                                                                                | false    | 2023-06-06-Tue, 5:06:07 pm  |
| [[programming/basic/leetcode/129. 求根节点到叶节点数字之和\|129. 求根节点到叶节点数字之和]]                                                         | medium     | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/linear</li><li>#leetcode/unsolved</li></ul>                                                                        | true     | 2023-06-06-Tue, 3:59:26 pm  |
| [[programming/basic/leetcode/113. 路径总和 II\|113. 路径总和 II]]                                                                   | medium     | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/linear</li></ul>                                                                                                   | false    | 2023-06-06-Tue, 3:43:29 pm  |
| [[programming/basic/leetcode/112. 路径总和\|112. 路径总和]]                                                                         | easy       | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/linear</li></ul>                                                                                                   | false    | 2023-06-06-Tue, 3:39:50 pm  |
| [[programming/basic/leetcode/111. 二叉树的最小深度\|111. 二叉树的最小深度]]                                                                 | easy       | <ul><li>#leetcode/tree/traversal</li></ul>                                                                                                                                                | false    | 2023-06-06-Tue, 3:35:41 pm  |
| [[programming/basic/leetcode/653. 两数之和 IV - 输入二叉搜索树\|653. 两数之和 IV - 输入二叉搜索树]]                                               | easy       | <ul><li>#leetcode/tree/traversal</li><li>#leetcode/hash-table</li><li>#leetcode/unsolved</li><li>#leetcode/pointer/collision-pointer</li></ul>                                            | true     | 2023-06-06-Tue, 3:27:14 pm  |
| [[programming/basic/leetcode/623. 在二叉树中增加一行\|623. 在二叉树中增加一行]]                                                               | medium     | <ul><li>#leetcode/tree/traversal</li></ul>                                                                                                                                                | false    | 2023-06-06-Tue, 2:50:11 pm  |
| [[programming/basic/leetcode/257. 二叉树的所有路径\|257. 二叉树的所有路径]]                                                                 | easy       | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/linear</li></ul>                                                                                                   | false    | 2023-06-06-Tue, 2:26:33 pm  |
| [[programming/basic/leetcode/662. 二叉树最大宽度\|662. 二叉树最大宽度]]                                                                   | medium     | <ul><li>#leetcode/tree/traversal/linear</li><li>#leetcode/unsolved</li><li>#leetcode/tree/index</li></ul>                                                                                 | true     | 2023-06-05-Mon, 9:41:39 am  |
| [[programming/basic/leetcode/513. 找树左下角的值\|513. 找树左下角的值]]                                                                   | medium     | <ul><li>#leetcode/tree/traversal</li><li>#leetcode/unsolved</li></ul>                                                                                                                     | true     | 2023-06-05-Mon, 4:53:43 pm  |
| [[programming/basic/leetcode/671. 二叉树中第二小的节点\|671. 二叉树中第二小的节点]]                                                             | easy       | <ul><li>#leetcode/tree/traversal</li></ul>                                                                                                                                                | false    | 2023-06-05-Mon, 4:38:00 pm  |
| [[programming/basic/leetcode/690. 员工的重要性\|690. 员工的重要性]]                                                                     | medium     | <ul><li>#leetcode/tree/traversal</li><li>#leetcode/hash-table</li></ul>                                                                                                                   | false    | 2023-06-04-Sun, 5:05:09 pm  |
| [[programming/basic/leetcode/87. 扰乱字符串\|87. 扰乱字符串]]                                                                         | hard       | <ul><li>#leetcode/unsolved</li><li>#leetcode/tree/traversal</li><li>#leetcode/dp</li></ul>                                                                                                | true     | 2023-06-04-Sun, 3:08:42 pm  |
| [[programming/basic/leetcode/687. 最长同值路径\|687. 最长同值路径]]                                                                     | medium     | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/sub-tree</li></ul>                                                                                                 | false    | 2023-06-04-Sun, 3:01:09 pm  |
| [[programming/basic/leetcode/572. 另一棵树的子树\|572. 另一棵树的子树]]                                                                   | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/kmp</li><li>#leetcode/sub/consecutive</li><li>#leetcode/unsolved</li></ul>                                                    | true     | 2023-06-03-Sat, 8:42:12 pm  |
| [[programming/basic/leetcode/508. 出现次数最多的子树元素和\|508. 出现次数最多的子树元素和]]                                                         | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/hash-table/count</li></ul>                                                                                                    | false    | 2023-06-03-Sat, 8:04:36 pm  |
| [[programming/basic/leetcode/617. 合并二叉树\|617. 合并二叉树]]                                                                       | easy       | <ul><li>#leetcode/tree/traversal</li></ul>                                                                                                                                                | false    | 2023-06-03-Sat, 7:29:10 pm  |
| [[programming/basic/leetcode/226. 翻转二叉树\|226. 翻转二叉树]]                                                                       | easy       | <ul><li>#leetcode/tree/traversal/sub-tree</li></ul>                                                                                                                                       | false    | 2023-06-03-Sat, 12:57:46 pm |
| [[programming/basic/leetcode/101. 对称二叉树\|101. 对称二叉树]]                                                                       | easy       | <ul><li>#leetcode/tree/traversal</li></ul>                                                                                                                                                | false    | 2023-06-03-Sat, 12:28:38 pm |
| [[programming/basic/leetcode/654. 最大二叉树\|654. 最大二叉树]]                                                                       | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/stack/monotonic-stack</li><li>#leetcode/unsolved</li></ul>                                                                    | true     | 2023-06-03-Sat, 10:12:52 pm |
| [[programming/basic/leetcode/222. 完全二叉树的节点个数\|222. 完全二叉树的节点个数]]                                                             | easy       | <ul><li>#leetcode/tree/traversal/linear</li><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/tree/complete</li></ul>                                                               | false    | 2023-06-02-Fri, 5:08:35 pm  |
| [[programming/basic/leetcode/100. 相同的树\|100. 相同的树]]                                                                         | easy       | <ul><li>#leetcode/tree/traversal</li></ul>                                                                                                                                                | false    | 2023-06-02-Fri, 10:33:53 am |
| [[programming/basic/leetcode/457. Circular Array Loop\|457. Circular Array Loop]]                                           | medium     | <ul><li>#leetcode/pointer/fast-slow</li><li>#leetcode/graph/circle</li></ul>                                                                                                              | false    | 2023-05-31-Wed, 7:55:16 pm  |
| [[programming/basic/leetcode/234. 回文链表\|234. 回文链表]]                                                                         | easy       | <ul><li>#leetcode/linked-list/traversal</li><li>#leetcode/pointer/fast-slow</li><li>#leetcode/palindrome</li></ul>                                                                        | false    | 2023-05-31-Wed, 12:24:37 pm |
| [[programming/basic/leetcode/143. 重排链表\|143. 重排链表]]                                                                         | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/pointer/fast-slow</li></ul>                                                                                                               | false    | 2023-05-30-Tue, 5:12:55 pm  |
| [[programming/basic/leetcode/283. 移动零\|283. 移动零]]                                                                           | easy       | <ul><li>#leetcode/pointer/fast-slow</li></ul>                                                                                                                                             | false    | 2023-05-30-Tue, 12:06:52 pm |
| [[programming/basic/leetcode/141. 环形链表\|141. 环形链表]]                                                                         | easy       | <ul><li>#leetcode</li></ul>                                                                                                                                                               | false    | 2023-05-30-Tue, 10:49:46 am |
| [[programming/basic/leetcode/475. 供暖器\|475. 供暖器]]                                                                           | medium     | <ul><li>#leetcode/pointer/double</li><li>#leetcode/binary-search</li><li>#leetcode/unsolved</li></ul>                                                                                     | true     | 2023-05-30-Tue, 10:06:36 am |
| [[programming/basic/leetcode/88. 合并两个有序数组\|88. 合并两个有序数组]]                                                                   | easy       | <ul><li>#leetcode/pointer/double</li><li>#leetcode/unsolved</li><li>#leetcode/merge-sort</li></ul>                                                                                        | true     | 2023-05-29-Mon, 8:42:20 pm  |
| [[programming/basic/leetcode/160. 相交链表\|160. 相交链表]]                                                                         | easy       | <ul><li>#leetcode/hash-table</li><li>#leetcode/linked-list/double</li><li>#leetcode/pointer/double</li><li>#leetcode/brainteasers</li><li>#leetcode/unsolved</li></ul>                    | true     | 2023-05-29-Mon, 6:52:32 pm  |
| [[programming/basic/leetcode/328. 奇偶链表\|328. 奇偶链表]]                                                                         | medium     | <ul><li>#leetcode/linked-list/double</li><li>#leetcode/pointer/double</li></ul>                                                                                                           | false    | 2023-05-29-Mon, 4:46:00 pm  |
| [[programming/basic/leetcode/86. 分隔链表\|86. 分隔链表]]                                                                           | medium     | <ul><li>#leetcode/linked-list/double</li><li>#leetcode/pointer/double</li><li>#leetcode/unsolved</li></ul>                                                                                | true     | 2023-05-29-Mon, 4:24:11 pm  |
| [[programming/basic/leetcode/424. 替换后的最长重复字符\|424. 替换后的最长重复字符]]                                                             | medium     | <ul><li>#leetcode/pointer/sliding-window</li><li>#leetcode/hash-table</li></ul>                                                                                                           | false    | 2023-05-29-Mon, 10:37:34 am |
| [[programming/basic/leetcode/567. 字符串的排列\|567. 字符串的排列]]                                                                     | medium     | <ul><li>#leetcode/pointer/sliding-window</li><li>#leetcode/hash-table</li></ul>                                                                                                           | false    | 2023-05-28-Sun, 9:41:39 pm  |
| [[programming/basic/leetcode/438. Find All Anagrams in a String\|438. Find All Anagrams in a String]]                       | medium     | <ul><li>#leetcode/pointer/sliding-window</li><li>#leetcode/hash-table</li></ul>                                                                                                           | false    | 2023-05-28-Sun, 5:32:41 pm  |
| [[programming/basic/leetcode/3. 无重复字符的最长子串\|3. 无重复字符的最长子串]]                                                                 | medium     | <ul><li>#leetcode/pointer/sliding-window</li><li>#leetcode/hash-table</li><li>#leetcode/sub/consecutive</li></ul>                                                                         | false    | 2023-05-28-Sun, 4:53:45 pm  |
| [[programming/basic/leetcode/209. 长度最小的子数组\|209. 长度最小的子数组]]                                                                 | medium     | <ul><li>#leetcode/pointer/sliding-window</li><li>#leetcode/prefix-sum</li><li>#leetcode/sub/consecutive</li><li>#leetcode/unsolved</li><li>#leetcode/binary-search</li></ul>              | true     | 2023-05-28-Sun, 3:58:34 pm  |
| [[programming/basic/leetcode/674. 最长连续递增序列\|674. 最长连续递增序列]]                                                                 | easy       | <ul><li>#leetcode/pointer/sliding-window</li><li>#leetcode/greedy-algorithm</li><li>#leetcode/sub/consecutive</li></ul>                                                                   | false    | 2023-05-28-Sun, 2:52:17 pm  |
| [[programming/basic/leetcode/187. 重复的DNA序列\|187. 重复的DNA序列]]                                                                 | medium     | <ul><li>#leetcode/pointer/sliding-window</li><li>#leetcode/sub/consecutive</li></ul>                                                                                                      | false    | 2023-05-28-Sun, 12:55:38 pm |
| [[programming/basic/leetcode/611. 有效三角形的个数\|611. 有效三角形的个数]]                                                                 | medium     | <ul><li>#leetcode/pointer/double</li><li>#leetcode/unsolved</li><li>#leetcode/binary-search</li></ul>                                                                                     | true     | 2023-05-28-Sun, 12:12:22 pm |
| [[programming/basic/leetcode/82. 删除排序链表中的重复元素 II\|82. 删除排序链表中的重复元素 II]]                                                     | medium     | <ul><li>#leetcode/pointer/fast-slow</li><li>#leetcode/pointer/in-place</li></ul>                                                                                                          | false    | 2023-05-28-Sun, 10:26:16 am |
| [[programming/basic/leetcode/643. 子数组最大平均数 I\|643. 子数组最大平均数 I]]                                                             | easy       | <ul><li>#leetcode/pointer/sliding-window</li><li>#leetcode/sub/consecutive</li></ul>                                                                                                      | false    | 2023-05-28-Sun, 1:13:59 pm  |
| [[programming/basic/leetcode/83. 删除排序链表中的重复元素\|83. 删除排序链表中的重复元素]]                                                           | easy       | <ul><li>#leetcode/pointer/fast-slow</li><li>#leetcode/pointer/in-place</li></ul>                                                                                                          | false    | 2023-05-27-Sat, 7:42:55 pm  |
| [[programming/basic/leetcode/80. 删除有序数组中的重复项 II\|80. 删除有序数组中的重复项 II]]                                                       | medium     | <ul><li>#leetcode/pointer/fast-slow</li><li>#leetcode/space/in-place</li></ul>                                                                                                            | false    | 2023-05-27-Sat, 6:37:23 pm  |
| [[programming/basic/leetcode/27. 移除元素\|27. 移除元素]]                                                                           | easy       | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/pointer/fast-slow</li><li>#leetcode/space/in-place</li></ul>                                                                | false    | 2023-05-27-Sat, 12:29:21 pm |
| [[programming/basic/leetcode/26. 删除有序数组中的重复项\|26. 删除有序数组中的重复项]]                                                             | easy       | <ul><li>#leetcode/pointer/fast-slow</li><li>#leetcode/space/in-place</li></ul>                                                                                                            | false    | 2023-05-27-Sat, 12:25:30 pm |
| [[programming/basic/leetcode/11. 盛最多水的容器\|11. 盛最多水的容器]]                                                                     | medium     | <ul><li>#leetcode/pointer/double</li><li>#leetcode/math</li></ul>                                                                                                                         | false    | 2023-05-26-Fri, 5:15:23 pm  |
| [[programming/basic/leetcode/16. 最接近的三数之和\|16. 最接近的三数之和]]                                                                   | medium     | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/unsolved</li></ul>                                                                                                          | true     | 2023-05-26-Fri, 3:29:00 pm  |
| [[programming/basic/leetcode/15. 三数之和\|15. 三数之和]]                                                                           | medium     | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/skip-dup</li></ul>                                                                                                          | false    | 2023-05-26-Fri, 2:49:19 pm  |
| [[programming/basic/leetcode/680. 验证回文串 II\|680. 验证回文串 II]]                                                                 | easy       | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/palindrome</li></ul>                                                                                                        | false    | 2023-05-26-Fri, 2:13:29 pm  |
| [[programming/basic/leetcode/345. 反转字符串中的元音字母\|345. 反转字符串中的元音字母]]                                                           | easy       | <ul><li>#leetcode/pointer/collision-pointer</li></ul>                                                                                                                                     | false    | 2023-05-26-Fri, 12:33:28 pm |
| [[programming/basic/leetcode/605. 种花问题\|605. 种花问题]]                                                                         | easy       | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/unsolved</li></ul>                                                                                                                   | true     | 2023-05-26-Fri, 12:01:50 pm |
| [[programming/basic/leetcode/525. 连续数组\|525. 连续数组]]                                                                         | medium     | <ul><li>#leetcode/hash-table</li><li>#leetcode/prefix-sum</li><li>#leetcode/unsolved</li></ul>                                                                                            | true     | 2023-05-26-Fri, 10:02:58 am |
| [[programming/basic/leetcode/523. 连续的子数组和\|523. 连续的子数组和]]                                                                   | medium     | <ul><li>#leetcode/hash-table</li><li>#leetcode/prefix-sum</li><li>#leetcode/unsolved</li><li>#leetcode/sub/consecutive</li></ul>                                                          | true     | 2023-05-25-Thu, 8:47:00 pm  |
| [[programming/basic/leetcode/560. 和为 K 的子数组\|560. 和为 K 的子数组]]                                                               | medium     | <ul><li>#leetcode/hash-table</li><li>#leetcode/prefix-sum</li><li>#leetcode/unsolved</li><li>#leetcode/sub/consecutive</li></ul>                                                          | true     | 2023-05-25-Thu, 5:48:31 pm  |
| [[programming/basic/leetcode/18. 四数之和\|18. 四数之和]]                                                                           | medium     | <ul><li>#leetcode/hash-table/count</li><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/skip-dup</li></ul>                                                                       | false    | 2023-05-25-Thu, 3:51:31 pm  |
| [[programming/basic/leetcode/454. 四数相加 II\|454. 四数相加 II]]                                                                   | medium     | <ul><li>#leetcode/hash-table/count</li><li>#leetcode/math</li></ul>                                                                                                                       | false    | 2023-05-25-Thu, 11:24:49 am |
| [[programming/basic/leetcode/554. 砖墙\|554. 砖墙]]                                                                             | medium     | <ul><li>#leetcode/hash-table/count</li></ul>                                                                                                                                              | false    | 2023-05-25-Thu, 11:03:39 am |
| [[programming/basic/leetcode/350. 两个数组的交集 II\|350. 两个数组的交集 II]]                                                             | easy       | <ul><li>#leetcode/hash-table/count</li><li>#leetcode/pointer</li></ul>                                                                                                                    | false    | 2023-05-25-Thu, 10:35:31 am |
| [[programming/basic/leetcode/594. 最长和谐子序列\|594. 最长和谐子序列]]                                                                   | easy       | <ul><li>#leetcode/hash-table/count</li><li>#leetcode/sub/sequence</li></ul>                                                                                                               | false    | 2023-05-25-Thu, 10:24:55 am |
| [[programming/basic/leetcode/220. 存在重复元素 III\|220. 存在重复元素 III]]                                                             | hard       | <ul><li>#leetcode/unsolved</li><li>#leetcode/pointer/sliding-window</li><li>#leetcode/bucket-sort</li><li>#leetcode/tree-set</li></ul>                                                    | true     | 2023-05-24-Wed, 9:09:09 pm  |
| [[programming/basic/leetcode/219. 存在重复元素 II\|219. 存在重复元素 II]]                                                               | easy       | <ul><li>#leetcode/hash-table</li><li>#leetcode/pointer/sliding-window</li></ul>                                                                                                           | false    | 2023-05-24-Wed, 8:46:52 pm  |
| [[programming/basic/leetcode/167. 两数之和 II - 输入有序数组\|167. 两数之和 II - 输入有序数组]]                                                 | medium     | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/binary-search</li><li>#leetcode/unsolved</li></ul>                                                                          | true     | 2023-05-24-Wed, 6:59:41 pm  |
| [[programming/basic/leetcode/1. 两数之和\|1. 两数之和]]                                                                             | easy       | <ul><li>#leetcode/hash-table/index</li></ul>                                                                                                                                              | false    | 2023-05-24-Wed, 4:25:58 pm  |
| [[programming/basic/leetcode/466. 统计重复个数\|466. 统计重复个数]]                                                                     | hard       | <ul><li>#leetcode/hash-table</li><li>#leetcode/unsolved</li><li>#leetcode/math/pigeonhole-principle</li></ul>                                                                             | true     | 2023-05-24-Wed, 3:46:55 pm  |
| [[programming/basic/leetcode/166. 分数到小数\|166. 分数到小数]]                                                                       | medium     | <ul><li>#leetcode/math/high-precision-computation</li><li>#leetcode/hash-table</li><li>#leetcode/unsolved</li></ul>                                                                       | true     | 2023-05-24-Wed, 2:56:31 pm  |
| [[programming/basic/leetcode/202. 快乐数\|202. 快乐数]]                                                                           | easy       | <ul><li>#leetcode/hash-table</li><li>#leetcode/pointer/fast-slow</li><li>#leetcode/unsolved</li><li>#leetcode/pattern/convergence</li></ul>                                               | true     | 2023-05-24-Wed, 12:40:01 pm |
| [[programming/basic/leetcode/128. 最长连续序列\|128. 最长连续序列]]                                                                     | medium     | <ul><li>#leetcode/hash-table</li><li>#leetcode/unsolved</li></ul>                                                                                                                         | true     | 2023-05-24-Wed, 12:19:39 pm |
| [[programming/basic/leetcode/633. 平方数之和\|633. 平方数之和]]                                                                       | medium     | <ul><li>#leetcode/math/squre</li><li>#leetcode/pointer/collision-pointer</li></ul>                                                                                                        | false    | 2023-05-24-Wed, 10:59:41 am |
| [[programming/basic/leetcode/21. 合并两个有序链表\|21. 合并两个有序链表]]                                                                   | medium     | <ul><li>#leetcode/linked-list</li></ul>                                                                                                                                                   | false    | 2023-05-23-Tue, 4:23:46 pm  |
| [[programming/basic/leetcode/445. 两数相加 II\|445. 两数相加 II]]                                                                   | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/math/high-precision-computation</li><li>#leetcode/stack</li></ul>                                                                         | false    | 2023-05-23-Tue, 4:10:35 pm  |
| [[programming/basic/leetcode/2. 两数相加\|2. 两数相加]]                                                                             | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/math/high-precision-computation</li></ul>                                                                                                 | false    | 2023-05-23-Tue, 4:02:17 pm  |
| [[programming/basic/leetcode/25. K 个一组翻转链表\|25. K 个一组翻转链表]]                                                                 | hard       | <ul><li>#leetcode/linked-list</li><li>#leetcode/unsolved</li></ul>                                                                                                                        | true     | 2023-05-23-Tue, 3:05:28 pm  |
| [[programming/basic/leetcode/92. 反转链表 II\|92. 反转链表 II]]                                                                     | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/unsolved</li></ul>                                                                                                                        | true     | 2023-05-23-Tue, 11:05:45 am |
| [[programming/basic/leetcode/24. 两两交换链表中的节点\|24. 两两交换链表中的节点]]                                                               | medium     | <ul><li>#leetcode/linked-list</li></ul>                                                                                                                                                   | false    | 2023-05-23-Tue, 10:03:40 am |
| [[programming/basic/leetcode/61. 旋转链表\|61. 旋转链表]]                                                                           | medium     | <ul><li>#leetcode/linked-list/rotate</li><li>#leetcode/linked-list/traversal</li></ul>                                                                                                    | false    | 2023-05-22-Mon, 7:41:43 pm  |
| [[programming/basic/leetcode/430. 扁平化多级双向链表\|430. 扁平化多级双向链表]]                                                               | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/traversal/dfs</li></ul>                                                                                                                   | false    | 2023-05-22-Mon, 4:20:24 pm  |
| [[programming/basic/leetcode/19. 删除链表的倒数第 N 个结点\|19. 删除链表的倒数第 N 个结点]]                                                       | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/pointer/fast-slow</li></ul>                                                                                                               | false    | 2023-05-22-Mon, 4:06:36 pm  |
| [[programming/basic/leetcode/591. 标签验证器\|591. 标签验证器]]                                                                       | hard       | <ul><li>#leetcode/stack</li><li>#leetcode/unsolved</li></ul>                                                                                                                              | true     | 2023-05-21-Sun, 6:43:37 pm  |
| [[programming/basic/leetcode/636. 函数的独占时间\|636. 函数的独占时间]]                                                                   | medium     | <ul><li>#leetcode/stack</li><li>#leetcode/time-sequence</li></ul>                                                                                                                         | false    | 2023-05-21-Sun, 6:24:26 pm  |
| [[programming/basic/leetcode/20. 有效的括号\|20. 有效的括号]]                                                                         | easy       | <ul><li>#leetcode/stack</li></ul>                                                                                                                                                         | false    | 2023-05-21-Sun, 6:16:14 pm  |
| [[programming/basic/leetcode/224. 基本计算器\|224. 基本计算器]]                                                                       | hard       | <ul><li>#leetcode/stack</li><li>#leetcode/math</li><li>#leetcode/unsolved</li></ul>                                                                                                       | true     | 2023-05-21-Sun, 5:47:35 pm  |
| [[programming/basic/leetcode/71. 简化路径\|71. 简化路径]]                                                                           | medium     | <ul><li>#leetcode/stack</li></ul>                                                                                                                                                         | false    | 2023-05-20-Sat, 9:42:39 pm  |
| [[programming/basic/leetcode/682. 棒球比赛\|682. 棒球比赛]]                                                                         | easy       | <ul><li>#leetcode/stack</li></ul>                                                                                                                                                         | false    | 2023-05-20-Sat, 9:34:41 pm  |
| [[programming/basic/leetcode/492. 构造矩形\|492. 构造矩形]]                                                                         | easy       | <ul><li>#leetcode/math</li></ul>                                                                                                                                                          | false    | 2023-05-20-Sat, 7:18:15 pm  |
| [[programming/basic/leetcode/231. 2 的幂\|231. 2 的幂]]                                                                         | easy       | <ul><li>#leetcode/math</li><li>#leetcode/bit-manipulation</li></ul>                                                                                                                       | false    | 2023-05-20-Sat, 5:17:27 pm  |
| [[programming/basic/leetcode/9. 回文数\|9. 回文数]]                                                                               | easy       | <ul><li>#leetcode/palindrome</li></ul>                                                                                                                                                    | false    | 2023-05-20-Sat, 4:32:14 pm  |
| [[programming/basic/leetcode/7. 整数反转\|7. 整数反转]]                                                                             | medium     | <ul><li>#leetcode/math/high-precision-computation</li></ul>                                                                                                                               | false    | 2023-05-20-Sat, 4:13:40 pm  |
| [[programming/basic/leetcode/5. 最长回文子串\|5. 最长回文子串]]                                                                         | medium     | <ul><li>#leetcode/palindrome</li><li>#leetcode/unsolved</li></ul>                                                                                                                         | true     | 2023-05-19-Fri, 5:01:31 pm  |
| [[programming/basic/leetcode/686. 重复叠加字符串匹配\|686. 重复叠加字符串匹配]]                                                               | medium     | <ul><li>#leetcode/sub/consecutive</li><li>#leetcode/unsolved</li><li>#leetcode/kmp</li></ul>                                                                                              | true     | 2023-05-19-Fri, 4:27:11 pm  |
| [[programming/basic/leetcode/68. 文本左右对齐\|68. 文本左右对齐]]                                                                       | hard       | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/string/transform</li></ul>                                                                                                           | false    | 2023-05-18-Thu, 3:33:41 pm  |
| [[programming/basic/leetcode/6. N 字形变换\|6. N 字形变换]]                                                                         | medium     | <ul><li>#leetcode/string/transform</li><li>#leetcode/unsolved</li></ul>                                                                                                                   | true     | 2023-05-17-Wed, 3:03:16 pm  |
| [[programming/basic/leetcode/482. 密钥格式化\|482. 密钥格式化]]                                                                       | easy       | <ul><li>#leetcode/string/transform</li><li>#leetcode/string/regexp</li></ul>                                                                                                              | false    | 2023-05-17-Wed, 2:40:46 pm  |
| [[programming/basic/leetcode/306. 累加数\|306. 累加数]]                                                                           | medium     | <ul><li>#leetcode/math/high-precision-computation</li><li>#leetcode/unsolved</li><li>#leetcode/pattern/math</li></ul>                                                                     | true     | 2023-05-17-Wed, 2:07:46 pm  |
| [[programming/basic/leetcode/43. 字符串相乘\|43. 字符串相乘]]                                                                         | medium     | <ul><li>#leetcode/math/high-precision-computation</li><li>#leetcode/unsolved</li></ul>                                                                                                    | true     | 2023-05-17-Wed, 11:08:22 am |
| [[programming/basic/leetcode/415. 字符串相加\|415. 字符串相加]]                                                                       | easy       | <ul><li>#leetcode/math/high-precision-computation</li><li>#leetcode/unsolved</li></ul>                                                                                                    | true     | 2023-05-16-Tue, 8:40:13 pm  |
| [[programming/basic/leetcode/67. 二进制求和\|67. 二进制求和]]                                                                         | easy       | <ul><li>#leetcode/math/high-precision-computation</li><li>#leetcode/unsolved</li><li>#leetcode/bit-manipulation</li></ul>                                                                 | true     | 2023-05-16-Tue, 8:15:33 pm  |
| [[programming/basic/leetcode/66. 加一\|66. 加一]]                                                                               | easy       | <ul><li>#leetcode/math/high-precision-computation</li></ul>                                                                                                                               | false    | 2023-05-16-Tue, 7:21:56 pm  |
| [[programming/basic/leetcode/522. 最长特殊序列 II\|522. 最长特殊序列 II]]                                                               | medium     | <ul><li>#leetcode/sub/sequence</li><li>#leetcode/unsolved</li></ul>                                                                                                                       | true     | 2023-05-16-Tue, 4:31:46 pm  |
| [[programming/basic/leetcode/521. 最长特殊序列 Ⅰ\|521. 最长特殊序列 Ⅰ]]                                                                 | easy       | <ul><li>#leetcode/sub/sequence</li><li>#leetcode/unsolved</li></ul>                                                                                                                       | true     | 2023-05-16-Tue, 4:05:52 pm  |
| [[programming/basic/leetcode/524. 通过删除字母匹配到字典里最长单词\|524. 通过删除字母匹配到字典里最长单词]]                                                 | medium     | <ul><li>#leetcode/sub/sequence</li><li>#leetcode/dp/lcs</li><li>#leetcode/unsolved</li></ul>                                                                                              | true     | 2023-05-16-Tue, 11:35:07 am |
| [[programming/basic/leetcode/392. 判断子序列\|392. 判断子序列]]                                                                       | easy       | <ul><li>#leetcode/sub/sequence</li><li>#leetcode/dp/lcs</li><li>#leetcode/unsolved</li></ul>                                                                                              | true     | 2023-05-15-Mon, 8:38:50 pm  |
| [[programming/basic/leetcode/299. 猜数字游戏\|299. 猜数字游戏]]                                                                       | medium     | <ul><li>#leetcode/string/hash-table</li><li>#leetcode/string/map-to-num</li></ul>                                                                                                         | false    | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/467. 环绕字符串中唯一的子字符串\|467. 环绕字符串中唯一的子字符串]]                                                       | medium     | <ul><li>#leetcode/string/hash-table</li><li>#leetcode/dp</li><li>#leetcode/prefix-sum</li><li>#leetcode/solved</li><li>#leetcode/sub/consecutive</li></ul>                                | false    | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/537. Complex Number Multiplication\|537. Complex Number Multiplication]]                       | medium     | <ul><li>#leetcode/math</li><li>#leetcode/string/map-to-num</li></ul>                                                                                                                      | false    | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/539. 最小时间差\|539. 最小时间差]]                                                                       | medium     | <ul><li>#leetcode/math/pigeonhole-principle</li><li>#leetcode/solved</li></ul>                                                                                                            | false    | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/553. 最优除法\|553. 最优除法]]                                                                         | medium     | <ul><li>#leetcode/unsolved</li><li>#leetcode/dp</li><li>#leetcode/math/division</li></ul>                                                                                                 | true     | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/592. Fraction Addition and Subtraction\|592. Fraction Addition and Subtraction]]               | medium     | <ul><li>#leetcode/math</li><li>#leetcode/string/map-to-num</li></ul>                                                                                                                      | false    | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/696. 计数二进制子串\|696. 计数二进制子串]]                                                                   | easy       | <ul><li>#leetcode/string/hash-table</li><li>#leetcode/sub/consecutive</li><li>#leetcode/solved</li></ul>                                                                                  | false    | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/389. 找不同\|389. 找不同]]                                                                           | easy       | <ul><li>#leetcode/string/ascii</li><li>#leetcode/string/hash-table</li></ul>                                                                                                              | false    | 2023-05-14-Sun, 1:29:08 am  |
| [[programming/basic/leetcode/423. 从英文中重建数字\|423. 从英文中重建数字]]                                                                 | medium     | <ul><li>#leetcode/string/hash-table</li></ul>                                                                                                                                             | false    | 2023-05-14-Sun, 1:29:08 am  |
| [[programming/basic/leetcode/451. 根据字符出现频率排序\|451. 根据字符出现频率排序]]                                                             | medium     | <ul><li>#leetcode/string/hash-table</li><li>#leetcode/bucket-sort</li></ul>                                                                                                               | false    | 2023-05-14-Sun, 1:29:08 am  |
| [[programming/basic/leetcode/49. 字母异位词分组\|49. 字母异位词分组]]                                                                     | medium     | <ul><li>#leetcode/string/ascii</li><li>#leetcode/string/sort</li><li>#leetcode/string/hash-table</li></ul>                                                                                | false    | 2023-05-14-Sun, 1:29:08 am  |
| [[programming/basic/leetcode/125. Valid Palindrome\|125. Valid Palindrome]]                                                 | easy       | <ul><li>#leetcode/palindrome</li></ul>                                                                                                                                                    | false    | 2023-05-13-Sat, 1:29:08 am  |
| [[programming/basic/leetcode/14. Longest Common Prefix\|14. Longest Common Prefix]]                                         | easy       | <ul><li>#leetcode/longest-common-prefix</li></ul>                                                                                                                                         | false    | 2023-05-13-Sat, 1:29:08 am  |
| [[programming/basic/leetcode/520. Detect Capital\|520. Detect Capital]]                                                     | easy       | <ul><li>#leetcode/string</li></ul>                                                                                                                                                        | false    | 2023-05-13-Sat, 1:29:08 am  |
| [[programming/basic/leetcode/541. 反转字符串 II\|541. 反转字符串 II]]                                                                 | easy       | <ul><li>#leetcode/string/reverse</li></ul>                                                                                                                                                | false    | 2023-05-13-Sat, 1:29:08 am  |
| [[programming/basic/leetcode/238. 除自身以外数组的乘积\|238. 除自身以外数组的乘积]]                                                             | medium     | <ul><li>#leetcode/prefix-sum</li></ul>                                                                                                                                                    | false    | 2023-05-10-Wed, 1:29:08 am  |
| [[programming/basic/leetcode/289. 生命游戏\|289. 生命游戏]]                                                                         | medium     | <ul><li>#leetcode/array/matrix</li><li>#leetcode/space/in-place</li></ul>                                                                                                                 | false    | 2023-05-10-Wed, 1:29:08 am  |
| [[programming/basic/leetcode/304. 二维区域和检索 - 矩阵不可变\|304. 二维区域和检索 - 矩阵不可变]]                                                   | medium     | <ul><li>#leetcode/prefix-sum</li></ul>                                                                                                                                                    | false    | 2023-05-10-Wed, 1:29:08 am  |
| [[programming/basic/leetcode/303. 区域和检索 - 数组不可变\|303. 区域和检索 - 数组不可变]]                                                       | easy       | <ul><li>#leetcode/prefix-sum</li></ul>                                                                                                                                                    | false    | 2023-05-10-Wed, 1:29:08 am  |
| [[programming/basic/leetcode/498. 对角线遍历\|498. 对角线遍历]]                                                                       | medium     | <ul><li>#leetcode/array/matrix/traversal</li></ul>                                                                                                                                        | false    | 2023-05-10-Wed, 1:29:08 am  |
| [[programming/basic/leetcode/189. Rotate Array\|189. Rotate Array]]                                                         | medium     | <ul><li>#leetcode/array/rotate</li><li>#leetcode/space/in-place</li><li>#leetcode/traversal/dfs</li><li>#leetcode/solved</li></ul>                                                        | false    | 2023-05-09-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/396. 旋转函数\|396. 旋转函数]]                                                                         | medium     | <ul><li>#leetcode/array/traversal</li><li>#leetcode/dp</li><li>#leetcode/array/rotate</li></ul>                                                                                           | false    | 2023-05-09-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/54. 螺旋矩阵\|54. 螺旋矩阵]]                                                                           | medium     | <ul><li>#leetcode/array/matrix/traversal</li><li>#leetcode/array/matrix/transform</li></ul>                                                                                               | false    | 2023-05-09-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/59. Spiral Matrix II\|59. Spiral Matrix II]]                                                   | medium     | <ul><li>#leetcode/array/matrix/traversal</li><li>#leetcode/array/matrix/transform</li></ul>                                                                                               | false    | 2023-05-09-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/73. 矩阵置零\|73. 矩阵置零]]                                                                           | medium     | <ul><li>#leetcode/array/matrix</li><li>#leetcode/space/in-place</li></ul>                                                                                                                 | false    | 2023-05-09-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/48. Rotate Image\|48. Rotate Image]]                                                           | medium     | <ul><li>#leetcode/array/matrix/transform</li><li>#leetcode/space/in-place</li><li>#leetcode/traversal/dfs</li><li>#leetcode/solved</li></ul>                                              | false    | 2023-05-02-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/566. Reshape the Matrix\|566. Reshape the Matrix]]                                             | easy       | <ul><li>#leetcode/array/matrix/transform</li></ul>                                                                                                                                        | false    | 2023-05-01-Mon, 12:28:33 pm |
| [[programming/basic/leetcode/419. Battleships in a Board\|419. Battleships in a Board]]                                     | medium     | <ul><li>#leetcode/array/matrix</li></ul>                                                                                                                                                  | false    | 2023-05-01-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/598. Range Addition II\|598. Range Addition II]]                                               | easy       | <ul><li>#leetcode/array/traversal</li></ul>                                                                                                                                               | false    | 2023-04-05-Wed, 7:39:18 pm  |
| [[programming/basic/leetcode/661. Image Smoother\|661. Image Smoother]]                                                     | easy       | <ul><li>#leetcode/array/matrix</li></ul>                                                                                                                                                  | false    | 2022-12-31-Sat, 4:38:14 pm  |
| [[programming/basic/leetcode/118. Pascal's Triangle\|118. Pascal's Triangle]]                                               | easy       | <ul><li>#leetcode/math/sequence/fibonacci</li><li>#leetcode/array/multi-dimensional</li><li>#leetcode/space/scrolling-array</li></ul>                                                     | false    | 2022-12-28-Wed, 12:33:27 pm |
| [[programming/basic/leetcode/665. Non-decreasing Array\|665. Non-decreasing Array]]                                         | medium     | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/math/monotone</li><li>#leetcode/solved</li></ul>                                                                                     | false    | 2022-09-20-Tue, 3:36:55 pm  |
| [[programming/basic/leetcode/!leetcode\|!leetcode]]                                                                         | \-         | <ul></ul>                                                                                                                                                                                 | false    | 2022-09-13-Tue, 9:06:50 am  |
| [[programming/basic/leetcode/645. Set Mismatch\|645. Set Mismatch]]                                                         | easy       | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li><li>#leetcode/bit-manipulation</li></ul>                                                                      | false    | 2022-09-13-Tue, 9:06:49 am  |
| [[programming/basic/leetcode/453. Minimum Moves to Equal Array Elements\|453. Minimum Moves to Equal Array Elements]]       | medium     | <ul><li>#leetcode/pattern/relative-size</li><li>#leetcode/math/equation</li><li>#leetcode/solved</li></ul>                                                                                | false    | 2022-09-13-Tue, 3:08:51 pm  |
| [[programming/basic/leetcode/274. H-Index\|274. H-Index]]                                                                   | medium     | <ul><li>#leetcode/solved</li><li>#leetcode/sort/backward</li><li>#leetcode/sort/counting-sort</li></ul>                                                                                   | false    | 2022-09-12-Mon, 8:22:42 pm  |
| [[programming/basic/leetcode/448. Find All Numbers Disappeared in an Array\|448. Find All Numbers Disappeared in an Array]] | easy       | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li><li>#leetcode/space/in-place</li><li>#leetcode/array/natural-array</li></ul>                                  | false    | 2022-09-12-Mon, 3:53:27 pm  |
| [[programming/basic/leetcode/41. First Missing Positive\|41. First Missing Positive]]                                       | hard       | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li><li>#leetcode/space/in-place</li><li>#leetcode/array/natural-array</li></ul>                                  | false    | 2022-09-12-Mon, 3:52:00 pm  |
| [[programming/basic/leetcode/442. Find All Duplicates in an Array\|442. Find All Duplicates in an Array]]                   | medium     | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li><li>#leetcode/space/in-place</li><li>#leetcode/array/natural-array</li></ul>                                  | false    | 2022-09-12-Mon, 3:50:54 pm  |
| [[programming/basic/leetcode/697. Degree of an Array\|697. Degree of an Array]]                                             | easy       | <ul><li>#leetcode/hash-table</li></ul>                                                                                                                                                    | false    | 2022-09-11-Sun, 4:39:54 pm  |
| [[programming/basic/leetcode/414. Third Maximum Number\|414. Third Maximum Number]]                                         | easy       | <ul><li>#leetcode/sort</li><li>#leetcode/top-k</li></ul>                                                                                                                                  | false    | 2022-09-08-Thu, 8:46:18 am  |
| [[programming/basic/leetcode/485. 最大连续 1 的个数\|485. 最大连续 1 的个数]]                                                             | easy       | <ul><li>#leetcode/array/traversal</li></ul>                                                                                                                                               | false    | 2022-09-08-Thu, 10:33:59 pm |
| [[programming/basic/leetcode/495. 提莫攻击\|495. 提莫攻击]]                                                                         | easy       | <ul><li>#leetcode/array/traversal</li><li>#leetcode/time-sequence</li></ul>                                                                                                               | false    | 2022-09-07-Wed, 11:17:06 pm |
| [[programming/basic/leetcode/628. Maximum Product of Three Numbers\|628. Maximum Product of Three Numbers]]                 | easy       | <ul><li>#leetcode/pattern/categorized-discussion</li><li>#leetcode/top-k</li><li>#leetcode/math</li></ul>                                                                                 | false    | 2022-09-06-Tue, 11:09:41 pm |
| [[programming/basic/leetcode/542. 01 矩阵\|542. 01 矩阵]]                                                                       | medium     | <ul><li>#leetcode/graph/traversal/bfs</li><li>#leetcode/dp</li><li>#leetcode/array/matrix</li><li>#leetcode/graph/matrix</li><li>#leetcode/unsolved</li></ul>                             | true     | \-                          |

{ .block-language-dataview}
