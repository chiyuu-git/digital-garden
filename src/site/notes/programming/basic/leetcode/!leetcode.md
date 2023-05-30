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

### 题解大全

我的建议是先 acwing 再代码随想录 + labuladong 的算法小抄。做为非科班转码选手，可能会有人想知道**我是怎么刷 Leetcode 的**，在此献上:[大家都是如何刷 LeetCode 的？](https://www.zhihu.com/question/280279208/answer/2684962292)

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

[Loading Question... - 力扣（LeetCode）](https://leetcode.cn/problems/subarray-sum-equals-k/solution/de-liao-yi-wen-jiang-qian-zhui-he-an-pai-yhyf/)

涉及连续子数组的问题，我们通常有两种思路：一是滑动窗口、二是前缀和。

[209. 长度最小的子数组 题解 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-size-subarray-sum/solution/chang-du-zui-xiao-de-zi-shu-zu-by-leetcode-solutio/)

## 矩阵操作

遍历矩阵的正确姿势: 两个 for 循环的标记变量直接使用 row 和 col, 不需要再声明 i j 来获取临时的 row col

## 位运算

### 异或

异或的特性就是自己异或自己为 0, 利用这个特性, 可以用于处理奇偶数次的题目

## ACM 考点

https://oi-wiki.org/string/minimal-string/

https://www.bilibili.com/video/BV1rT411u7b9?spm_id_from=333.1007.tianma.1-2-2.click&vd_source=db8a4b4129af2e1d7a3e3f6357bb4d45

# Leetcode Overview

``dataview
table 
difficulty,
file.etags as etags,
contains(file.etags, "unsolved") as unsolved,
date-created
from "programming/basic/leetcode"
where tags != null
sort date-created desc 
```
