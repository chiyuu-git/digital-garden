---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/pointer/sliding-window/"}
---


# 概述

答案要求在某一个范围之内

滑动窗口是数组/字符串问题中常用的抽象概念。 窗口通常是在数组/字符串中由开始和结束索引定义的一系列元素的集合，即 ` [i, j]`（左闭，右闭合）。而滑动窗口是可以将两个边界向某一方向“滑动”的窗口。

例如，我们将 ` [i, j]` 向右滑动 1 个元素，则它将变为 `[i+1, j+1]`（左闭，右闭合）

这就是滑动窗口最重点的地方, 就是窗口是左闭右闭的:

1. 这样 end 永远是移出区间的操作, 并且 end 大于等于 nums.length 就是退出循环的条件
2. start 永远是加入区间的操作
3. `left <= right && right < nums.length`

区间的操作可以直接 while 循环, 直到区间达到要操作的阈值, 而不是 for 循环一个个处理, 增加模拟的负担

76 左右都是闭合的也可以, 最重要的是定好不要变了

# Solution Tips

1. 我们先不断地增加 right 指针扩大窗口 `[left, right]`，直到窗口中的字符串符合要求（包含了 T 中的所有字符）。
2. 此时，我们停止增加 right，转而不断增加 left 指针缩小窗口 `[left, right]`，直到窗口中的字符串不再符合要求（不包含 T 中的所有字符了）。同时，每次增加 left，我们都要更新一轮结果。
3. 重复第 2 和第 3 步，直到 right 到达字符串 S 的尽头。

```js
while (left <= right && right < len) {
	while(right... && right < len) {
		// expand window
		// ... do something
		right++;
	}

	while (left... && left <= right) {
		// narrow window
		// do something
		left++;
	}
}
```

> 很多题解, right 是一个 for 循环, 在循环内有时候是 right ++ 有时候是 left++, 脑补 debug 不好处理. 我觉得不如 right++ 到不能加, left++ 到不能加, 这样更好理解. 脑补 debug 更方便

# 关键词

子串, 子数组, 连续的 sub

有某种约束条件, 限制了 sub 的最长长度, 到达临界条件就必须改变 sub 的区间了

# 窗口长度

## 窗口长度是固定

[187. 重复的DNA序列](../../leetcode/187.%20重复的DNA序列.md)

[643. 子数组最大平均数 I](../../leetcode/643.%20子数组最大平均数%20I.md)

## 窗口长度是动态

根据题意伸缩, 固定的是窗口的某种特征, f(window) = const

+ [674. 最长连续递增序列](../../leetcode/674.%20最长连续递增序列.md)
+ [209. 长度最小的子数组](../../leetcode/209.%20长度最小的子数组.md)
+ [424. 替换后的最长重复字符](../../leetcode/424.%20替换后的最长重复字符.md)

### 最小窗口

如果要求的是最小窗口

那么可以使用固定窗口法, 从最小的窗口大小开始寻找是否存在符合题意的解, 找到了即可返回, 复杂度 O(n^2)

[76. 最小覆盖子串](../../leetcode/76.%20最小覆盖子串.md)

## 去头去尾留中间

+ [1658. 将 x 减到 0 的最小操作数](../../leetcode/1658.%20将%20x%20减到%200%20的最小操作数.md)

### 反向思维

每次去除头部、尾部, 反过来就是不断的缩小中间, 这就是正统滑动窗口的题目了

| File                                                                           | solution tips overview                  |
| ------------------------------------------------------------------------------ | --------------------------------------- |
| [[programming/basic/leetcode/2516. 每种字符至少取 K 个\|2516. 每种字符至少取 K 个]]         | [[programming/basic/leetcode/2516. 每种字符至少取 K 个#solution tips\|2516. 每种字符至少取 K 个#solution tips]]     |
| [[programming/basic/leetcode/1658. 将 x 减到 0 的最小操作数\|1658. 将 x 减到 0 的最小操作数]] | [[programming/basic/leetcode/1658. 将 x 减到 0 的最小操作数#solution tips\|1658. 将 x 减到 0 的最小操作数#solution tips]] |

{ .block-language-dataview}
[1423. 可获得的最大点数 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-points-you-can-obtain-from-cards/)

### 两个窗口

如果反向思维不好操作, 也可以选择前缀和后缀, 维护 2 个窗口

或者是前缀和后缀维护在一个窗口内

# 维护窗口的数据结构

找到维护窗口的合理数据结构, 是解题的关键

单纯的变量: 常见的 sum, 减去一个, 加上一个

+ [209. 长度最小的子数组](../../leetcode/209.%20长度最小的子数组.md)

自平衡二叉树：插入、删除、搜索都是 O(log k)

+ [220. 存在重复元素 III](../../leetcode/220.%20存在重复元素%20III.md)

**哈希表**：插入、删除、搜索都是常数级

> 数组：插入和删除是线性的，搜索也是线性的, 数组其实也是哈希表的一种形态

不是连续的结构就可以考虑使用哈希表存储数据

- [438. Find All Anagrams in a String](../../leetcode/438.%20Find%20All%20Anagrams%20in%20a%20String.md)
- [3. 无重复字符的最长子串](../../leetcode/3.%20无重复字符的最长子串.md)
- [424. 替换后的最长重复字符](../../leetcode/424.%20替换后的最长重复字符.md)
- [219. 存在重复元素 II](../../leetcode/219.%20存在重复元素%20II.md)

## 哈希 Map

即需要存储每种元素的个数, 也需要统计整个 map 的大小

如果使用数组的话, 可以方便的统计元素, 但是统计集合大小的时候不方便删除, 得 indexOf + splice 执行删除

如果使用 object map, 也是可以很方便的统计元素, 也可以方便的删除, 但是无法方便的统计集合的大小

所以使用 map 是最优解.

[904. 水果成篮](../../leetcode/904.%20水果成篮.md): 需要统计每种水果的个数, 最多只能装 2 种水果

## 哈希 Object + MatchSize

哈希 obj 可以很方便的添加和删除, 但是无法方便的统计集合的大小, 所以额外增加一个变量用来统计集合的大小即可.

参考 [76. 最小覆盖子串](../../leetcode/76.%20最小覆盖子串.md), 这里比使用哈希 map 更合适, 因为需要保证两个 map 完全 match, 单独使用 match 变量就可以直接知道了

- [438. 找到字符串中所有字母异位词](../../leetcode/438.%20Find%20All%20Anagrams%20in%20a%20String.md)
- [187. 重复的DNA序列](../../leetcode/187.%20重复的DNA序列.md)
- [567. 字符串的排列](../../leetcode/567.%20字符串的排列.md)

438 的抵消和恢复, 没有 76 的 match 好理解, 时间复杂度是一样的

# 题集

| File                                                                                                     | solution tips overview                               |
| -------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| [[programming/basic/leetcode/904. 水果成篮\|904. 水果成篮]]                                                   | [[programming/basic/leetcode/904. 水果成篮#solution tips\|904. 水果成篮#solution tips]]                          |
| [[programming/basic/leetcode/76. 最小覆盖子串\|76. 最小覆盖子串]]                                                 | [[programming/basic/leetcode/76. 最小覆盖子串#solution tips\|76. 最小覆盖子串#solution tips]]                         |
| [[programming/basic/leetcode/674. 最长连续递增序列\|674. 最长连续递增序列]]                                           | [[programming/basic/leetcode/674. 最长连续递增序列#solution tips\|674. 最长连续递增序列#solution tips]]                      |
| [[programming/basic/leetcode/643. 子数组最大平均数 I\|643. 子数组最大平均数 I]]                                       | [[programming/basic/leetcode/643. 子数组最大平均数 I#solution tips\|643. 子数组最大平均数 I#solution tips]]                    |
| [[programming/basic/leetcode/567. 字符串的排列\|567. 字符串的排列]]                                               | [[programming/basic/leetcode/567. 字符串的排列#solution tips\|567. 字符串的排列#solution tips]]                        |
| [[programming/basic/leetcode/438. Find All Anagrams in a String\|438. Find All Anagrams in a String]] | [[programming/basic/leetcode/438. Find All Anagrams in a String#solution tips\|438. Find All Anagrams in a String#solution tips]] |
| [[programming/basic/leetcode/424. 替换后的最长重复字符\|424. 替换后的最长重复字符]]                                       | [[programming/basic/leetcode/424. 替换后的最长重复字符#solution tips\|424. 替换后的最长重复字符#solution tips]]                    |
| [[programming/basic/leetcode/3. 无重复字符的最长子串\|3. 无重复字符的最长子串]]                                           | [[programming/basic/leetcode/3. 无重复字符的最长子串#solution tips\|3. 无重复字符的最长子串#solution tips]]                      |
| [[programming/basic/leetcode/2516. 每种字符至少取 K 个\|2516. 每种字符至少取 K 个]]                                   | [[programming/basic/leetcode/2516. 每种字符至少取 K 个#solution tips\|2516. 每种字符至少取 K 个#solution tips]]                  |
| [[programming/basic/leetcode/220. 存在重复元素 III\|220. 存在重复元素 III]]                                       | [[programming/basic/leetcode/220. 存在重复元素 III#solution tips\|220. 存在重复元素 III#solution tips]]                    |
| [[programming/basic/leetcode/219. 存在重复元素 II\|219. 存在重复元素 II]]                                         | [[programming/basic/leetcode/219. 存在重复元素 II#solution tips\|219. 存在重复元素 II#solution tips]]                     |
| [[programming/basic/leetcode/209. 长度最小的子数组\|209. 长度最小的子数组]]                                           | [[programming/basic/leetcode/209. 长度最小的子数组#solution tips\|209. 长度最小的子数组#solution tips]]                      |
| [[programming/basic/leetcode/187. 重复的DNA序列\|187. 重复的DNA序列]]                                           | [[programming/basic/leetcode/187. 重复的DNA序列#solution tips\|187. 重复的DNA序列#solution tips]]                      |
| [[programming/basic/leetcode/1658. 将 x 减到 0 的最小操作数\|1658. 将 x 减到 0 的最小操作数]]                           | [[programming/basic/leetcode/1658. 将 x 减到 0 的最小操作数#solution tips\|1658. 将 x 减到 0 的最小操作数#solution tips]]              |
| [[programming/basic/leetcode/1248. 统计「优美子数组」\|1248. 统计「优美子数组」]]                                       | [[programming/basic/leetcode/1248. 统计「优美子数组」#solution tips\|1248. 统计「优美子数组」#solution tips]]                    |

{ .block-language-dataview}
