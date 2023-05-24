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

### 题解大全

官方的题解基本就够用了，多拓宽思路

+ https://programmercarl.com/0704.%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE.html#%E6%80%9D%E8%B7%AF
+ https://leetcode-solution-leetcode-pp.gitbook.io/leetcode-solution/medium/get-kth-magic-number-lcci
+ [ac_oier - 力扣（LeetCode）](https://leetcode.cn/u/ac_oier/)
+ https://xiaochen1024.com/series/6196129fc1553b002e57bef5/6196208ec1553b002e57bef6

# 总结

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

{ .block-language-dataview}

## Time-sequence 时间类题目

### 主要特点

1. 注重时间开始和结束，比如 1s 初，1s 末
2. 注重时间的间隔，1s 初和 1s 末，间隔了 1s

### Solution Tips

| File                                                         | overview                       |
| ------------------------------------------------------------ | ------------------------------ |
| [[programming/basic/leetcode/495. 提莫攻击\|495. 提莫攻击]]       | [[programming/basic/leetcode/495. 提莫攻击#solution tips\|495. 提莫攻击#solution tips]]    |
| [[programming/basic/leetcode/636. 函数的独占时间\|636. 函数的独占时间]] | [[programming/basic/leetcode/636. 函数的独占时间#solution tips\|636. 函数的独占时间#solution tips]] |

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

## 矩阵操作

遍历矩阵的正确姿势: 两个 for 循环的标记变量直接使用 row 和 col, 不需要再声明 i j 来获取临时的 row col

## 位运算

### 异或

异或的特性就是自己异或自己为 0, 利用这个特性, 可以用于处理奇偶数次的题目

## ACM 考点

https://oi-wiki.org/string/minimal-string/

https://www.bilibili.com/video/BV1rT411u7b9?spm_id_from=333.1007.tianma.1-2-2.click&vd_source=db8a4b4129af2e1d7a3e3f6357bb4d45

# Leetcode Overview

| File                                                                                                                           | difficulty | etags                                                                                                                                      | date-created                |
| ------------------------------------------------------------------------------------------------------------------------------ | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------- |
| [[programming/basic/leetcode/21. 合并两个有序链表\|21. 合并两个有序链表]]                                                                   | medium     | <ul><li>#leetcode/linked-list</li></ul>                                                                                                    | 2023-05-23-Tue, 4:23:46 pm  |
| [[programming/basic/leetcode/445. 两数相加 II\|445. 两数相加 II]]                                                                   | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/math/high-precision-computation</li><li>#leetcode/stack</li></ul>                          | 2023-05-23-Tue, 4:10:35 pm  |
| [[programming/basic/leetcode/25. K 个一组翻转链表\|25. K 个一组翻转链表]]                                                                 | hard       | <ul><li>#leetcode/linked-list</li><li>#leetcode/unsolved</li></ul>                                                                         | 2023-05-23-Tue, 3:05:28 pm  |
| [[programming/basic/leetcode/24. 两两交换链表中的节点\|24. 两两交换链表中的节点]]                                                               | medium     | <ul><li>#leetcode/linked-list</li></ul>                                                                                                    | 2023-05-23-Tue, 10:03:40 am |
| [[programming/basic/leetcode/61. 旋转链表\|61. 旋转链表]]                                                                           | medium     | <ul><li>#leetcode/linked-list/rotate</li><li>#leetcode/linked-list/traverse</li></ul>                                                      | 2023-05-22-Mon, 7:41:43 pm  |
| [[programming/basic/leetcode/19. 删除链表的倒数第 N 个结点\|19. 删除链表的倒数第 N 个结点]]                                                       | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/double-pointer/fast-slow</li></ul>                                                         | 2023-05-22-Mon, 4:06:36 pm  |
| [[programming/basic/leetcode/591. 标签验证器\|591. 标签验证器]]                                                                       | hard       | <ul><li>#leetcode/stack</li><li>#leetcode/unsolved</li></ul>                                                                               | 2023-05-21-Sun, 6:43:37 pm  |
| [[programming/basic/leetcode/20. 有效的括号\|20. 有效的括号]]                                                                         | easy       | <ul><li>#leetcode/stack</li></ul>                                                                                                          | 2023-05-21-Sun, 6:16:14 pm  |
| [[programming/basic/leetcode/71. 简化路径\|71. 简化路径]]                                                                           | medium     | <ul><li>#leetcode/stack</li></ul>                                                                                                          | 2023-05-20-Sat, 9:42:39 pm  |
| [[programming/basic/leetcode/682. 棒球比赛\|682. 棒球比赛]]                                                                         | easy       | <ul><li>#leetcode/stack</li></ul>                                                                                                          | 2023-05-20-Sat, 9:34:41 pm  |
| [[programming/basic/leetcode/492. 构造矩形\|492. 构造矩形]]                                                                         | easy       | <ul><li>#leetcode/math</li></ul>                                                                                                           | 2023-05-20-Sat, 7:18:15 pm  |
| [[programming/basic/leetcode/231. 2 的幂\|231. 2 的幂]]                                                                         | easy       | <ul><li>#leetcode/math</li><li>#leetcode/bit-manipulation</li></ul>                                                                        | 2023-05-20-Sat, 5:17:27 pm  |
| [[programming/basic/leetcode/9. 回文数\|9. 回文数]]                                                                               | easy       | <ul><li>#leetcode/palindrome</li></ul>                                                                                                     | 2023-05-20-Sat, 4:32:14 pm  |
| [[programming/basic/leetcode/7. 整数反转\|7. 整数反转]]                                                                             | medium     | <ul><li>#leetcode/math/high-precision-computation</li></ul>                                                                                | 2023-05-20-Sat, 4:13:40 pm  |
| [[programming/basic/leetcode/686. 重复叠加字符串匹配\|686. 重复叠加字符串匹配]]                                                               | medium     | <ul><li>#leetcode/string/match</li><li>#leetcode/unsolved</li></ul>                                                                        | 2023-05-19-Fri, 4:27:11 pm  |
| [[programming/basic/leetcode/68. 文本左右对齐\|68. 文本左右对齐]]                                                                       | hard       | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/string/transform</li></ul>                                                            | 2023-05-18-Thu, 3:33:41 pm  |
| [[programming/basic/leetcode/6. N 字形变换\|6. N 字形变换]]                                                                         | medium     | <ul><li>#leetcode/string/transform</li><li>#leetcode/unsolved</li></ul>                                                                    | 2023-05-17-Wed, 3:03:16 pm  |
| [[programming/basic/leetcode/482. 密钥格式化\|482. 密钥格式化]]                                                                       | easy       | <ul><li>#leetcode/string/transform</li><li>#leetcode/string/regexp</li></ul>                                                               | 2023-05-17-Wed, 2:40:46 pm  |
| [[programming/basic/leetcode/306. 累加数\|306. 累加数]]                                                                           | medium     | <ul><li>#leetcode/math/high-precision-computation</li><li>#leetcode/unsolved</li></ul>                                                     | 2023-05-17-Wed, 2:07:46 pm  |
| [[programming/basic/leetcode/43. 字符串相乘\|43. 字符串相乘]]                                                                         | medium     | <ul><li>#leetcode/math/high-precision-computation</li><li>#leetcode/unsolved</li></ul>                                                     | 2023-05-17-Wed, 11:08:22 am |
| [[programming/basic/leetcode/415. 字符串相加\|415. 字符串相加]]                                                                       | easy       | <ul><li>#leetcode/math/high-precision-computation</li><li>#leetcode/unsolved</li></ul>                                                     | 2023-05-16-Tue, 8:40:13 pm  |
| [[programming/basic/leetcode/67. 二进制求和\|67. 二进制求和]]                                                                         | easy       | <ul><li>#leetcode/math/high-precision-computation</li><li>#leetcode/unsolved</li><li>#leetcode/bit-manipulation</li></ul>                  | 2023-05-16-Tue, 8:15:33 pm  |
| [[programming/basic/leetcode/66. 加一\|66. 加一]]                                                                               | easy       | <ul><li>#leetcode/math/high-precision-computation</li></ul>                                                                                | 2023-05-16-Tue, 7:21:56 pm  |
| [[programming/basic/leetcode/522. 最长特殊序列 II\|522. 最长特殊序列 II]]                                                               | medium     | <ul><li>#leetcode/sub/sub-sequence</li><li>#leetcode/unsolved</li></ul>                                                                    | 2023-05-16-Tue, 4:31:46 pm  |
| [[programming/basic/leetcode/521. 最长特殊序列 Ⅰ\|521. 最长特殊序列 Ⅰ]]                                                                 | easy       | <ul><li>#leetcode/sub/sub-sequence</li><li>#leetcode/unsolved</li></ul>                                                                    | 2023-05-16-Tue, 4:05:52 pm  |
| [[programming/basic/leetcode/524. 通过删除字母匹配到字典里最长单词\|524. 通过删除字母匹配到字典里最长单词]]                                                 | medium     | <ul><li>#leetcode/sub/sub-sequence</li><li>#leetcode/dp/longest-common-sub-sequence</li><li>#leetcode/unsolved</li></ul>                   | 2023-05-16-Tue, 11:35:07 am |
| [[programming/basic/leetcode/392. 判断子序列\|392. 判断子序列]]                                                                       | easy       | <ul><li>#leetcode/sub/sub-sequence</li><li>#leetcode/dp/longest-common-sub-sequence</li><li>#leetcode/unsolved</li></ul>                   | 2023-05-15-Mon, 8:38:50 pm  |
| [[programming/basic/leetcode/299. 猜数字游戏\|299. 猜数字游戏]]                                                                       | medium     | <ul><li>#leetcode/string/hash-table</li><li>#leetcode/string/map-to-num</li></ul>                                                          | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/467. 环绕字符串中唯一的子字符串\|467. 环绕字符串中唯一的子字符串]]                                                       | medium     | <ul><li>#leetcode/string/hash-table</li><li>#leetcode/dp</li><li>#leetcode/prefix-sum</li><li>#leetcode/solved</li></ul>                   | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/539. 最小时间差\|539. 最小时间差]]                                                                       | medium     | <ul><li>#leetcode/math/pigeonhole-principle</li><li>#leetcode/solved</li></ul>                                                             | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/553. 最优除法\|553. 最优除法]]                                                                         | medium     | <ul><li>#leetcode/unsolved</li><li>#leetcode/dp</li><li>#leetcode/math/division</li></ul>                                                  | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/592. Fraction Addition and Subtraction\|592. Fraction Addition and Subtraction]]               | medium     | <ul><li>#leetcode/math</li><li>#leetcode/string/map-to-num</li></ul>                                                                       | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/537. Complex Number Multiplication\|537. Complex Number Multiplication]]                       | medium     | <ul><li>#leetcode/math</li><li>#leetcode/string/map-to-num</li></ul>                                                                       | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/696. 计数二进制子串\|696. 计数二进制子串]]                                                                   | easy       | <ul><li>#leetcode/string/hash-table</li><li>#leetcode/sub/sub-string</li><li>#leetcode/solved</li></ul>                                    | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/389. 找不同\|389. 找不同]]                                                                           | easy       | <ul><li>#leetcode/string/ascii</li><li>#leetcode/string/hash-table</li></ul>                                                               | 2023-05-14-Sun, 1:29:08 am  |
| [[programming/basic/leetcode/423. 从英文中重建数字\|423. 从英文中重建数字]]                                                                 | medium     | <ul><li>#leetcode/string/hash-table</li></ul>                                                                                              | 2023-05-14-Sun, 1:29:08 am  |
| [[programming/basic/leetcode/451. 根据字符出现频率排序\|451. 根据字符出现频率排序]]                                                             | medium     | <ul><li>#leetcode/string/hash-table</li><li>#leetcode/bucket-sort</li></ul>                                                                | 2023-05-14-Sun, 1:29:08 am  |
| [[programming/basic/leetcode/49. 字母异位词分组\|49. 字母异位词分组]]                                                                     | medium     | <ul><li>#leetcode/string/ascii</li><li>#leetcode/string/sort</li><li>#leetcode/string/hash-table</li></ul>                                 | 2023-05-14-Sun, 1:29:08 am  |
| [[programming/basic/leetcode/125. Valid Palindrome\|125. Valid Palindrome]]                                                 | easy       | <ul><li>#leetcode/palindrome</li></ul>                                                                                                     | 2023-05-13-Sat, 1:29:08 am  |
| [[programming/basic/leetcode/14. Longest Common Prefix\|14. Longest Common Prefix]]                                         | easy       | <ul><li>#leetcode/longest-common-prefix</li></ul>                                                                                          | 2023-05-13-Sat, 1:29:08 am  |
| [[programming/basic/leetcode/520. Detect Capital\|520. Detect Capital]]                                                     | easy       | <ul><li>#leetcode/string</li></ul>                                                                                                         | 2023-05-13-Sat, 1:29:08 am  |
| [[programming/basic/leetcode/541. 反转字符串 II\|541. 反转字符串 II]]                                                                 | easy       | <ul><li>#leetcode/string/reverse</li></ul>                                                                                                 | 2023-05-13-Sat, 1:29:08 am  |
| [[programming/basic/leetcode/238. 除自身以外数组的乘积\|238. 除自身以外数组的乘积]]                                                             | medium     | <ul><li>#leetcode/prefix-sum</li></ul>                                                                                                     | 2023-05-10-Wed, 1:29:08 am  |
| [[programming/basic/leetcode/304. 二维区域和检索 - 矩阵不可变\|304. 二维区域和检索 - 矩阵不可变]]                                                   | medium     | <ul><li>#leetcode/prefix-sum</li></ul>                                                                                                     | 2023-05-10-Wed, 1:29:08 am  |
| [[programming/basic/leetcode/289. 生命游戏\|289. 生命游戏]]                                                                         | medium     | <ul><li>#leetcode/array/matrix</li><li>#leetcode/space/in-place</li></ul>                                                                  | 2023-05-10-Wed, 1:29:08 am  |
| [[programming/basic/leetcode/303. 区域和检索 - 数组不可变\|303. 区域和检索 - 数组不可变]]                                                       | easy       | <ul><li>#leetcode/prefix-sum</li></ul>                                                                                                     | 2023-05-10-Wed, 1:29:08 am  |
| [[programming/basic/leetcode/498. 对角线遍历\|498. 对角线遍历]]                                                                       | medium     | <ul><li>#leetcode/array/matrix/traverse</li></ul>                                                                                          | 2023-05-10-Wed, 1:29:08 am  |
| [[programming/basic/leetcode/189. Rotate Array\|189. Rotate Array]]                                                         | medium     | <ul><li>#leetcode/array/rotate</li><li>#leetcode/space/in-place</li><li>#leetcode/depth-first</li><li>#leetcode/solved</li></ul>           | 2023-05-09-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/396. 旋转函数\|396. 旋转函数]]                                                                         | medium     | <ul><li>#leetcode/array/traverse</li><li>#leetcode/dp</li><li>#leetcode/array/rotate</li></ul>                                             | 2023-05-09-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/54. 螺旋矩阵\|54. 螺旋矩阵]]                                                                           | medium     | <ul><li>#leetcode/array/matrix/traverse</li><li>#leetcode/array/matrix/transform</li></ul>                                                 | 2023-05-09-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/59. Spiral Matrix II\|59. Spiral Matrix II]]                                                   | medium     | <ul><li>#leetcode/array/matrix/traverse</li><li>#leetcode/array/matrix/transform</li></ul>                                                 | 2023-05-09-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/73. 矩阵置零\|73. 矩阵置零]]                                                                           | medium     | <ul><li>#leetcode/array/matrix</li><li>#leetcode/space/in-place</li></ul>                                                                  | 2023-05-09-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/48. Rotate Image\|48. Rotate Image]]                                                           | medium     | <ul><li>#leetcode/array/matrix/transform</li><li>#leetcode/space/in-place</li><li>#leetcode/depth-first</li><li>#leetcode/solved</li></ul> | 2023-05-02-Tue, 1:29:08 am  |
| [[programming/basic/leetcode/566. Reshape the Matrix\|566. Reshape the Matrix]]                                             | easy       | <ul><li>#leetcode/array/matrix/transform</li></ul>                                                                                         | 2023-05-01-Mon, 12:28:33 pm |
| [[programming/basic/leetcode/419. Battleships in a Board\|419. Battleships in a Board]]                                     | medium     | <ul><li>#leetcode/array/matrix</li></ul>                                                                                                   | 2023-05-01-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/598. Range Addition II\|598. Range Addition II]]                                               | easy       | <ul><li>#leetcode/array/traverse</li></ul>                                                                                                 | 2023-04-05-Wed, 7:39:18 pm  |
| [[programming/basic/leetcode/661. Image Smoother\|661. Image Smoother]]                                                     | easy       | <ul><li>#leetcode/array/matrix</li></ul>                                                                                                   | 2022-12-31-Sat, 4:38:14 pm  |
| [[programming/basic/leetcode/118. Pascal's Triangle\|118. Pascal's Triangle]]                                               | easy       | <ul><li>#leetcode/math/sequence/fibonacci</li><li>#leetcode/array/multi-dimensional</li><li>#leetcode/space/scrolling-array</li></ul>      | 2022-12-28-Wed, 12:33:27 pm |
| [[programming/basic/leetcode/665. Non-decreasing Array\|665. Non-decreasing Array]]                                         | medium     | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/math/monotone</li><li>#leetcode/solved</li></ul>                                      | 2022-09-20-Tue, 3:36:55 pm  |
| [[programming/basic/leetcode/!leetcode\|!leetcode]]                                                                         | \-         | <ul></ul>                                                                                                                                  | 2022-09-13-Tue, 9:06:50 am  |
| [[programming/basic/leetcode/645. Set Mismatch\|645. Set Mismatch]]                                                         | easy       | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li><li>#leetcode/bit-manipulation</li></ul>                       | 2022-09-13-Tue, 9:06:49 am  |
| [[programming/basic/leetcode/453. Minimum Moves to Equal Array Elements\|453. Minimum Moves to Equal Array Elements]]       | medium     | <ul><li>#leetcode/relative-size</li><li>#leetcode/math/equation</li><li>#leetcode/solved</li></ul>                                         | 2022-09-13-Tue, 3:08:51 pm  |
| [[programming/basic/leetcode/274. H-Index\|274. H-Index]]                                                                   | medium     | <ul><li>#leetcode/solved</li><li>#leetcode/sort/backward</li><li>#leetcode/sort/counting-sort</li></ul>                                    | 2022-09-12-Mon, 8:22:42 pm  |
| [[programming/basic/leetcode/448. Find All Numbers Disappeared in an Array\|448. Find All Numbers Disappeared in an Array]] | easy       | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li><li>#leetcode/space/in-place</li></ul>                         | 2022-09-12-Mon, 3:53:27 pm  |
| [[programming/basic/leetcode/41. First Missing Positive\|41. First Missing Positive]]                                       | hard       | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li><li>#leetcode/space/in-place</li></ul>                         | 2022-09-12-Mon, 3:52:00 pm  |
| [[programming/basic/leetcode/442. Find All Duplicates in an Array\|442. Find All Duplicates in an Array]]                   | medium     | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li><li>#leetcode/space/in-place</li></ul>                         | 2022-09-12-Mon, 3:50:54 pm  |
| [[programming/basic/leetcode/697. Degree of an Array\|697. Degree of an Array]]                                             | easy       | <ul><li>#leetcode/hash-table</li></ul>                                                                                                     | 2022-09-11-Sun, 4:39:54 pm  |
| [[programming/basic/leetcode/414. Third Maximum Number\|414. Third Maximum Number]]                                         | easy       | <ul><li>#leetcode/sort</li><li>#leetcode/top-k</li></ul>                                                                                   | 2022-09-08-Thu, 8:46:18 am  |
| [[programming/basic/leetcode/485. 最大连续 1 的个数\|485. 最大连续 1 的个数]]                                                             | easy       | <ul><li>#leetcode/array/traverse</li></ul>                                                                                                 | 2022-09-08-Thu, 10:33:59 pm |
| [[programming/basic/leetcode/495. 提莫攻击\|495. 提莫攻击]]                                                                         | easy       | <ul><li>#leetcode/array/traverse</li><li>#leetcode/time-sequence</li></ul>                                                                 | 2022-09-07-Wed, 11:17:06 pm |
| [[programming/basic/leetcode/628. Maximum Product of Three Numbers\|628. Maximum Product of Three Numbers]]                 | easy       | <ul><li>#leetcode/pattern/categorized-discussion</li><li>#leetcode/top-k</li><li>#leetcode/math</li></ul>                                  | 2022-09-06-Tue, 11:09:41 pm |
| [[programming/basic/leetcode/5. 最长回文子串\|5. 最长回文子串]]                                                                         | medium     | <ul><li>#leetcode</li></ul>                                                                                                                | \-                          |
| [[programming/basic/leetcode/224. 基本计算器\|224. 基本计算器]]                                                                       | hard       | <ul><li>#leetcode/stack</li><li>#leetcode/math</li><li>#leetcode/unsolved</li></ul>                                                        | \-                          |
| [[programming/basic/leetcode/636. 函数的独占时间\|636. 函数的独占时间]]                                                                   | medium     | <ul><li>#leetcode/stack</li><li>#leetcode/time-sequence</li></ul>                                                                          | \-                          |
| [[programming/basic/leetcode/430. 扁平化多级双向链表\|430. 扁平化多级双向链表]]                                                               | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/traverse/dfs</li></ul>                                                                     | \-                          |
| [[programming/basic/leetcode/92. 反转链表 II\|92. 反转链表 II]]                                                                     | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/unsolved</li></ul>                                                                         | \-                          |
| [[programming/basic/leetcode/2. 两数相加\|2. 两数相加]]                                                                             | medium     | <ul><li>#leetcode/linked-list</li><li>#leetcode/math/high-precision-computation</li></ul>                                                  | \-                          |
| [[programming/basic/leetcode/633. 平方数之和\|633. 平方数之和]]                                                                       | medium     | <ul><li>#leetcode/math/squre</li><li>#leetcode/double-pointer</li><li>#leetcode/unsolved</li></ul>                                         | \-                          |
| [[programming/basic/leetcode/128. 最长连续序列\|128. 最长连续序列]]                                                                     | medium     | <ul><li>#leetcode/hash-table</li><li>#leetcode/unsolved</li></ul>                                                                          | \-                          |

{ .block-language-dataview}
