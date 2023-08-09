---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/prefix-sum/"}
---


# 处理子数组和问题

## 暴力法

遍历每一个子数组, 然后求和, 其实很多子数组的求和都是重复的, 可以参考动态规划的思路.

通过动态规划优化之后, 我们总结出一种经典场景, 就是前缀和

## 子数组和等于前缀和的差

对于数组 nums, 定义它的前缀和 `s[0] = 0`, `s[i+1] = sum(nums[0-j])`

> 定义 `s[0]`, 最终 prefixSum 数组的长度为 nums.length + 1, 因为初始化工作比较简单, 就不像动态规划的子序列问题那样定义 i - 1 去处理了, 直接用 dummyHead 思想, 通过哨兵解决

根据这个定义, 有 `s[i+1] = s[i] + nums[i]`

例如 `nums = [1,2,1,2]`, 对应的前缀和数组为 `s = [0, 1, 3, 4, 6]`

通过前缀和, **我们可以把子数组的元素和转换成两个前缀的差**, 即:

$$\sum_{j=\text{left}}^{\text{right}} \text{nums}[j] = \sum_{j=0}^{\text{right}} \text{nums}[j] - \sum_{j=0}^{\text{left}-1} \text{nums}[j] = s[\text{right}+1] - s[\text{left}]$$

## 子数组和的最值问题

### 53. 最大子数组和

![方案三 前缀和](../leetcode/53.%20最大子数组和.md#方案三%20前缀和)

### 1749. 任意子数组和的绝对值的最大值

![方案二 前缀和](../leetcode/1749.%20任意子数组和的绝对值的最大值.md#方案二%20前缀和)
