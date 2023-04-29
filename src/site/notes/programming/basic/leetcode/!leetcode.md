---
{"dg-publish":true,"permalink":"/programming/basic/leetcode/leetcode/"}
---


# 攻略


## 基本思路

同一类型的题集中刷，边刷边总结

### 分类列表

+ [有没有人一起从零开始刷力扣 - 力扣（LeetCode）](https://leetcode.cn/circle/discuss/4k2shg/)

### 题解大全

官方的题解基本就够用了，多拓宽思路

+ https://programmercarl.com/0704.%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE.html#%E6%80%9D%E8%B7%AF
+ https://leetcode-solution-leetcode-pp.gitbook.io/leetcode-solution/medium/get-kth-magic-number-lcci
+ [ac_oier - 力扣（LeetCode）](https://leetcode.cn/u/ac_oier/)
+ https://xiaochen1024.com/series/6196129fc1553b002e57bef5/6196208ec1553b002e57bef6

# 总结

## Index Slot 问题

有 n 个 item, 每个 item 的 index 从 0 开始, 每个 item 的前后都可以插入新的 item

> 这个可插入的 slot 就像是尺子上面 5cm 那个长条, 除了线段是一段一段的, 每一段前后都会有一根长条, 这个长条归属于谁?

一个解决方案就是在 list 的最后再添加一个空的 item, 这个 item 的唯一作用就是把所有的 slot 都定义成了在 item 前面. 解决了 slot 的归属问题. 如果你没有这个空的 slot , 就不清楚最后一个 slot 是谁的了.

这个问题从小到遇到过无数遍了, 比如 星期一到星期五之间有多少天? 包不包含头? 包不包含尾?

## Arithmetic-sequence

| File                                                                                                                           | solution tips overview                                          |
| ------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------- |
| [[programming/basic/leetcode/448. Find All Numbers Disappeared in an Array\|448. Find All Numbers Disappeared in an Array]] | [[programming/basic/leetcode/448. Find All Numbers Disappeared in an Array#solution tips\|448. Find All Numbers Disappeared in an Array#solution tips]] |
| [[programming/basic/leetcode/442. Find All Duplicates in an Array\|442. Find All Duplicates in an Array]]                   | [[programming/basic/leetcode/442. Find All Duplicates in an Array#solution tips\|442. Find All Duplicates in an Array#solution tips]]          |
| [[programming/basic/leetcode/41. First Missing Positive\|41. First Missing Positive]]                                       | [[programming/basic/leetcode/41. First Missing Positive#solution tips\|41. First Missing Positive#solution tips]]                    |
| [[programming/basic/leetcode/645. Set Mismatch\|645. Set Mismatch]]                                                         | [[programming/basic/leetcode/645. Set Mismatch#solution tips\|645. Set Mismatch#solution tips]]                             |

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

| File                                                   | overview                    |
| ------------------------------------------------------ | --------------------------- |
| [[programming/basic/leetcode/495. 提莫攻击\|495. 提莫攻击]] | [[programming/basic/leetcode/495. 提莫攻击#solution tips\|495. 提莫攻击#solution tips]] |

{ .block-language-dataview}

+ 想不明白的时候多画时序图

## 节省空间复杂度的方法

原地标记

滚动数组都是类似的原理

## ACM 考点

https://oi-wiki.org/string/minimal-string/

https://www.bilibili.com/video/BV1rT411u7b9?spm_id_from=333.1007.tianma.1-2-2.click&vd_source=db8a4b4129af2e1d7a3e3f6357bb4d45

# Leetcode Overview

| File                                                                                                                           | etags                                                                                                                                 | date           |
| ------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- | -------------- |
| [[programming/basic/leetcode/598. Range Addition II\|598. Range Addition II]]                                               | <ul><li>#leetcode</li></ul>                                                                                                           | 2023-04-05-Wed |
| [[programming/basic/leetcode/598. 范围求和 II\|598. 范围求和 II]]                                                                   | <ul><li>#leetcode</li></ul>                                                                                                           | 2023-04-05-Wed |
| [[programming/basic/leetcode/661. Image Smoother\|661. Image Smoother]]                                                     | <ul><li>#leetcode/array/matrix</li></ul>                                                                                              | 2022-12-31-Sat |
| [[programming/basic/leetcode/118. Pascal's Triangle\|118. Pascal's Triangle]]                                               | <ul><li>#leetcode/math/sequence/fibonacci</li><li>#leetcode/array/multi-dimensional</li><li>#leetcode/space/scrolling-array</li></ul> | 2022-12-28-Wed |
| [[programming/basic/leetcode/665. Non-decreasing Array\|665. Non-decreasing Array]]                                         | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/math/monotone</li><li>#leetcode/unsolved</li></ul>                               | 2022-09-20-Tue |
| [[programming/basic/leetcode/453. Minimum Moves to Equal Array Elements\|453. Minimum Moves to Equal Array Elements]]       | <ul><li>#leetcode/relative-size</li><li>#leetcode/math/equation</li></ul>                                                             | 2022-09-13-Tue |
| [[programming/basic/leetcode/274. H-Index\|274. H-Index]]                                                                   | <ul><li>#leetcode/counting-sort</li><li>#leetcode/unsolved</li></ul>                                                                  | 2022-09-12-Mon |
| [[programming/basic/leetcode/41. First Missing Positive\|41. First Missing Positive]]                                       | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li><li>#leetcode/space/原地标记</li></ul>                        | 2022-09-12-Mon |
| [[programming/basic/leetcode/442. Find All Duplicates in an Array\|442. Find All Duplicates in an Array]]                   | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li></ul>                                                     | 2022-09-12-Mon |
| [[programming/basic/leetcode/448. Find All Numbers Disappeared in an Array\|448. Find All Numbers Disappeared in an Array]] | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li></ul>                                                     | 2022-09-12-Mon |
| [[programming/basic/leetcode/697. Degree of an Array\|697. Degree of an Array]]                                             | <ul><li>#leetcode/hash-table</li></ul>                                                                                                | 2022-09-11-Sun |
| [[programming/basic/leetcode/645. Set Mismatch\|645. Set Mismatch]]                                                         | <ul><li>#leetcode/math/sequence/arithmetic</li><li>#leetcode/hash-table</li><li>#leetcode/bit-manipulation</li></ul>                  | 2022-09-08-Thu |
| [[programming/basic/leetcode/628. Maximum Product of Three Numbers\|628. Maximum Product of Three Numbers]]                 | <ul><li>#leetcode/categorized-discussion</li><li>#leetcode/top-k</li><li>#leetcode/math</li></ul>                                     | 2022-09-06-Tue |
| [[programming/basic/leetcode/414. Third Maximum Number\|414. Third Maximum Number]]                                         | <ul><li>#leetcode/array/sort</li><li>#leetcode/top-k</li></ul>                                                                        | 2022-09-04-Sun |
| [[programming/basic/leetcode/485. 最大连续 1 的个数\|485. 最大连续 1 的个数]]                                                             | <ul><li>#leetcode/array/traverse</li></ul>                                                                                            | 2022-09-04-Sun |
| [[programming/basic/leetcode/495. 提莫攻击\|495. 提莫攻击]]                                                                         | <ul><li>#leetcode/array/traverse</li><li>#leetcode/time-sequence</li></ul>                                                            | 2022-09-04-Sun |
| [[programming/basic/leetcode/!leetcode\|!leetcode]]                                                                         | <ul></ul>                                                                                                                             | \-             |

{ .block-language-dataview}
