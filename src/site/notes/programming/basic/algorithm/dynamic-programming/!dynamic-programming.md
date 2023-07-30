---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/dynamic-programming/dynamic-programming/"}
---


![](/img/user/programming/basic/algorithm/dynamic-programming/!dynamic-programming/image-20230718193303758.png)

# 什么是动态规划

动态规划，英文：Dynamic Programming，简称 DP，如果某一问题有很多重叠子问题，使用动态规划是最有效的。

所以动态规划中每一个状态一定是由上一个状态推导出来的，**这一点就区分于贪心**，贪心没有状态推导，而是从局部直接选最优的，

在 [关于贪心算法，你该了解这些！ (opens new window)](https://programmercarl.com/%E8%B4%AA%E5%BF%83%E7%AE%97%E6%B3%95%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80.html) 中我举了一个背包问题的例子。

例如：有 N 件物品和一个最多能背重量为 W 的背包。第 i 件物品的重量是 `weight[i]`，得到的价值是 `value[i]` 。**每件物品只能用一次**，求解将哪些物品装入背包里物品价值总和最大。

动态规划中 `dp[j]` 是由 `dp[j-weight[i]]` 推导出来的，然后取 `max(dp[j], dp[j - weight[i]] + value[i])`

但如果是贪心呢，每次拿物品选一个最大的或者最小的就完事了，和上一个状态没有关系。

所以贪心解决不了动态规划的问题。

**其实大家也不用死扣动规和贪心的理论区别，后面做做题目自然就知道了**。

而且很多讲解动态规划的文章都会讲最优子结构啊和重叠子问题啊这些，这些东西都是教科书的上定义，晦涩难懂而且不实用。

大家知道动规是由前一个状态推导出来的，而贪心是局部直接选最优的，对于刷题来说就够用了。

上述提到的背包问题，后序会详细讲解。

# 解题步骤

## 思路一

做动规题目的时候，很多同学会陷入一个误区，就是以为把状态转移公式背下来，照葫芦画瓢改改，就开始写代码，甚至把题目 AC 之后，都不太清楚 `dp[i]` 表示的是什么。

这就是一种朦胧的状态，然后就把题给过了，遇到稍稍难一点的，可能直接就不会了，然后看题解，然后继续照葫芦画瓢陷入这种恶性循环中

状态转移公式（递推公式）是很重要，但动规不仅仅只有递推公式。

对于动态规划问题，我将拆解为如下五步曲，这五步都搞清楚了，才能说把动态规划真的掌握了

1. 确定 dp 数组（dp table）以及下标的含义
2. 确定递推公式
3. dp 数组如何初始化
4. 确定遍历顺序
5. 举例推导 dp 数组

一些同学可能想为什么要先确定递推公式，然后在考虑初始化呢？

**因为一些情况是递推公式决定了 dp 数组要如何初始化**

后面的讲解中我都是围绕着这五点来进行讲解。

可能刷过动态规划题目的同学可能都知道递推公式的重要性，感觉确定了递推公式这道题目就解出来了。

其实 确定递推公式 仅仅是解题里的一步而已！

一些同学知道递推公式，但搞不清楚 dp 数组应该如何初始化，或者正确的遍历顺序，以至于记下来公式，但写的程序怎么改都通过不了。

后序的讲解的大家就会慢慢感受到这五步的重要性了

## 思路二

动态规划的的四个解题步骤是：

- 定义子问题
- 写出子问题的递推关系
- 确定 DP 数组的计算顺序
- 空间优化（可选）

要分析题目, 找出子问题, 但是很多时候分析题目能读懂就不错了, 感觉很难找出子问题与子问题之间的联系

这里定义子问题其实就是定义了 `dp[i]`

# 动态规划应该如何 Debug

相信动规的题目，很大部分同学都是这样做的。

看一下题解，感觉看懂了，然后照葫芦画瓢，如果能正好画对了，万事大吉，一旦要是没通过，就怎么改都通过不了，对 dp 数组的初始化，递推公式，遍历顺序，处于一种黑盒的理解状态。

**找问题的最好方式就是把 dp 数组打印出来，看看究竟是不是按照自己思路推导的！**

一些同学对于 dp 的学习是黑盒的状态，就是不清楚 dp 数组的含义，不懂为什么这么初始化，递推公式背下来了，遍历顺序靠习惯就是这么写的，然后一鼓作气写出代码，如果代码能通过万事大吉，通过不了的话就凭感觉改一改。

**做动规的题目，写代码之前一定要把状态转移在 dp 数组的上具体情况模拟一遍，心中有数，确定最后推出的是想要的结果**。

然后再写代码，如果代码没通过就打印 dp 数组，看看是不是和自己预先推导的哪里不一样。

如果打印出来和自己预先模拟推导是一样的，那么就是自己的递归公式、初始化或者遍历顺序有问题了。

如果和自己预先模拟推导的不一样，那么就是代码实现细节有问题。

这也是我为什么在动规五步曲里强调推导 dp 数组的重要性。

- 这道题目我举例推导状态转移公式了么？
- 我打印 dp 数组的日志了么？
- 打印出来了 dp 数组和我想的一样么？

**如果这灵魂三问自己都做到了，基本上这道题目也就解决了**，或者更清晰的知道自己究竟是哪一点不明白，是状态转移不明白，还是实现代码不知道该怎么写，还是不理解遍历 dp 数组的顺序。

# 动态规划的优化

如果只需要依赖前面的固定个数的状态, 就可以做空间复杂度上的优化, 从 N 优化到常数级. 参考斐波那契数列

[509. 斐波那契数](../../leetcode/509.%20斐波那契数.md)

1. 定义 “状态”
2. 找到 “状态转移方程”
3. 初始化，内循环的才是行的长度，外循环是列的高度

> 如果列比较难初始化，可以在内部增加判断，如果 `j<weights[i]`

4. i 是竖着的那个，j 是横着的那个

   ```js
   let dp = new Array(len1) // 列
   dp[-1] = new Array(len2).fill(0) // 负1行
   dp[-1][-1] = 0
   for (let i = 0; i < len1; i++) { // 行
     dp[i] = new Array(len2)
     dp[i][-1] = 0
   }
   
   // 二维费用问题的初始化
   // dp代表的是可以获得的经验
   const dp = new Array(costs.length)
   // 三维数组的初始化，只需要二维
   dp[-1] = []
   for (let i = 0; i < costs.length; i++) {
     dp[i] = []
     dp[i][-1] = new Array(kill).fill(0)
     for (let k = 0; k <= kill; k++) {
       dp[i][k] = new Array(patience+1).fill(0)
       dp[-1][k] = new Array(patience+1).fill(0)
     }
   }
   
   // 从负1开始即可
   for (let i = -1; i < costs.length; i++) {
     dp[i] = []
     for (let k = -1; k <= kill; k++) {
       dp[i][k] = new Array(patience+1).fill(0)
     }
   }
   ```

   ```js
   let lineA =  new Array(len2).fill(0) // 负1行
   lineA[-1] = 0
   let lineB = new Array(len2) // 第一行
   lineB[-1] = 0
   let referLine = lineA
   let currentLine = lineB
   ```

- 要注意动态规划和分而治之（归并排序和快速排序算法中用到的那种）是不
  同的方法。分而治之方法是把问题分解成相互独立的子问题，然后组合它们的答
  案，而动态规划则是将问题分解成相互依赖的子问题。
- 已经确定的行列，可以初始化解决
- 一旦确定 `T[i][j]` 的值，即可使用 `continue` 跳出当轮循环
- 一些明显对结果没有增益的，可以直接跳过当轮循环
- ![1555510635702](/img/user/programming/basic/algorithm/dynamic-programming/!dynamic-programming/1555510635702.png)
- 复数的问题是否是同一个问题
- ![1555549152869](/img/user/programming/basic/algorithm/dynamic-programming/!dynamic-programming/1555549152869.png)

# 递归与 DP

参考 [397. 整数替换](../../leetcode/397.%20整数替换.md) 这种形式的递归处理, 都可以理解为动态规划

记忆化搜索 + 递归

动态规划

动态规划 + 滚动数组优化空间复杂度

矩阵快速幂

通项公式

背包问题, 也可以使用记忆化搜索 + 递归解决

因为递归 + 回溯, 其实是暴力法, 所以理论上来说是可以解决所有的问题的, 所有的动态规划问题都可以通过 回溯/递归 + 记忆化搜索解决, 问题是时间复杂度, 还是有可能不通过

要做好记忆化搜索, 很多时候就是递归的思路了, 因为很多时候只能返回子问题的答案. 父问题的答案是基于子问题去实现的, 其实就是动态规划的状态转移方程

为了更好的记忆化, 也是从子问题开始递归效率更好

[115. 不同的子序列](../../leetcode/115.%20不同的子序列.md)

| File                                                               | difficulty | etags                                                                                                                                                                                              | date-created                |
| ------------------------------------------------------------------ | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| [[programming/basic/leetcode/343. 整数拆分\|343. 整数拆分]]             | medium     | <ul><li>#leetcode/dp/fibonaci</li><li>#leetcode/math/product</li><li>#leetcode/unsolved</li><li>#leetcode/greedy-algorithm</li></ul>                                                               | 2023-07-10-Mon, 5:41:34 pm  |
| [[programming/basic/leetcode/509. 斐波那契数\|509. 斐波那契数]]           | easy       | <ul><li>#leetcode/math/sequence/fibonaci</li><li>#leetcode/recursive/memo</li><li>#leetcode/dp/fibonaci</li><li>#leetcode/space/scrolling-array</li><li>#leetcode/math/fast-power/matrix</li></ul> | 2023-07-10-Mon, 9:40:29 am  |
| [[programming/basic/leetcode/63. 不同路径 II\|63. 不同路径 II]]         | medium     | <ul><li>#leetcode/dp/fibonaci</li></ul>                                                                                                                                                            | 2023-07-10-Mon, 4:46:49 pm  |
| [[programming/basic/leetcode/62. 不同路径\|62. 不同路径]]               | medium     | <ul><li>#leetcode/dp/fibonaci</li></ul>                                                                                                                                                            | 2023-07-10-Mon, 4:27:58 pm  |
| [[programming/basic/leetcode/70. 爬楼梯\|70. 爬楼梯]]                 | easy       | <ul><li>#leetcode/dp/fibonaci</li><li>#leetcode/dp/combination</li><li>#leetcode/audition</li></ul>                                                                                                | 2023-07-10-Mon, 12:40:02 pm |
| [[programming/basic/leetcode/746. 使用最小花费爬楼梯\|746. 使用最小花费爬楼梯]]   | easy       | <ul><li>#leetcode/dp/fibonaci</li></ul>                                                                                                                                                            | 2023-07-10-Mon, 2:36:24 pm  |
| [[programming/basic/leetcode/95. 不同的二叉搜索树 II\|95. 不同的二叉搜索树 II]] | medium     | <ul><li>#leetcode/dp/fibonaci</li><li>#leetcode/tree/bst</li><li>#leetcode/unsolved</li><li>#leetcode/divide-and-conquer</li></ul>                                                                 | 2023-06-11-Sun, 3:37:01 pm  |

{ .block-language-dataview}

# 背包问题

[knapsack-problem](knapsack-problem.md)

# 容器问题

## 买橙子 - 无法找零

### 题目

+ 小明去附近的水果店买橙子，水果商贩只提供整袋购买，有每袋 6 个和每袋 8 个的包装（包装不可拆分）。可是小明只想购买恰好 n 个橙子，并且尽量少的袋数方便携带。如果不能购买恰好 n 个橙子，小明将不会购买。请根据此实现一个程序，要求：
+ 输入一个整数 n，表示小明想要购买 n（1≤n≤100）个橙子
+ 输出一个整数表示最少需要购买的袋数，如果不能买恰好 n 个橙子则输出 -1
+ 例如，输入 20，输出 3。

### 分析

+ 状态：
  + 使用了的袋子的个数 `dp[i][j]`
  + `i` 优选的袋子，有两种 `[6,8]`
  + `j` 容量，从 `1到n`
  + 总共需要买 `n` 个
+ 状态转移方程：优先选择优选项，剩余的容量参考之前的
  + `dp[i][j]=Math.min(dp[i-1,j],dp[i][j-bags[i]]+bags[i])`

```js
  function buyOrange(n){
    // 只有两种袋子
    const bags = [6,8]
    const dp = new Array(bags.length)
    dp[-1] = new Array(n+1).fill(0)
    for (let i = 0; i < bags.length; i++) {
      dp[i] = new Array(n+1).fill(0)
    }
  
  
    for (let i = 0; i < bags.length; i++) {
      for (let j = 0; j <= n; j++) { // 可以取等于
        if(j<bags[i]){
          dp[i][j] = 0
        }else{
          const ref = dp[i-1][j],
                pri = dp[i][j-bags[i]]+1
          dp[i][j] = Math.max(ref,pri)
          // 无法找零，无法装袋的情况？
          // 如果不是 6 和 8 的组合数，就没有办法装袋
        }
      }
    }
    // 定义状态为橘子数？那怎么返回袋数？
    console.log(dp)
    return dp[bags.length-1][n]
  }
  
  let n = 20
  console.log(buyOrange(n))
```

+ 正确做法参考：

  ```java
  作者：手里捧着窝窝头_
  链接：https://www.nowcoder.com/discuss/73425?type=0&order=0&pos=7&page=0
  来源：牛客网
  
  #include <cstdio>
  #include <cstring>
  #include <algorithm>
  
  using namespace std;
  
  int dp[101];
  
  void init()
  {
    memset(dp, 0, sizeof(dp));
    int i = 9;
    dp[6] = 1;
    dp[8] = 1;
    while(i <= 100)
    {
      if(dp[i - 6] > 0 && dp[i - 8] > 0)
      {
        dp[i] = min(dp[i - 6] + 1, dp[i - 8] + 1);
      }
      else if(dp[i - 6] > 0 || dp[i - 8] > 0)
      {
        dp[i] = dp[i - 6] > 0 ? dp[i - 6] + 1 : dp[i - 8] + 1;
      }
      i++;
    }
  }
  
  int main()
  {
    init();
    int n;
    while(~scanf("%d", &n))
    {
      printf("%d\n", dp[n]);
    }
    return 0;
  }
  ```

### 贪心算法

+ 优先用 8 型装袋，装完之后判断
  + 余数为 0，返回袋数
  + 余数不为 0
    + 是否是 6 的倍数，返回袋数
    + 返回 -1

# 递推型

斐波那契是不是也算是递推型的问题呢?

所有的 dp 都是由子问题求解而来的, 是不是都可以算作递推呢?

| File                                                           | difficulty | etags                                                                                                               | date-created                |
| -------------------------------------------------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| [[programming/basic/leetcode/198. 打家劫舍\|198. 打家劫舍]]         | medium     | <ul><li>#leetcode/dp/iteration</li><li>#leetcode/recursive/memo</li><li>#leetcode/dp/array</li></ul>                | 2023-07-14-Fri, 9:51:08 am  |
| [[programming/basic/leetcode/213. 打家劫舍 II\|213. 打家劫舍 II]]   | medium     | <ul><li>#leetcode/dp/iteration</li><li>#leetcode/recursive/memo</li><li>#leetcode/dp/array</li></ul>                | 2023-07-14-Fri, 10:56:11 am |
| [[programming/basic/leetcode/264. 丑数 II\|264. 丑数 II]]       | medium     | <ul><li>#leetcode/math</li><li>#leetcode/dp/iteration</li></ul>                                                     | 2023-07-25-Tue, 9:35:56 am  |
| [[programming/basic/leetcode/313. 超级丑数\|313. 超级丑数]]         | medium     | <ul><li>#leetcode/math</li><li>#leetcode/dp/iteration</li></ul>                                                     | 2023-07-25-Tue, 9:38:47 am  |
| [[programming/basic/leetcode/337. 打家劫舍 III\|337. 打家劫舍 III]] | medium     | <ul><li>#leetcode/dp/iteration</li><li>#leetcode/recursive/memo</li><li>#leetcode/tree/traversal/sub-tree</li></ul> | 2023-07-14-Fri, 11:53:56 am |

{ .block-language-dataview}

# 多状态

本质也是递推型

[剑指 Offer 63. 股票的最大利润](https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/)

| File                                                                     | difficulty | etags                                                                                                                                                                                                   | date-created                |
| ------------------------------------------------------------------------ | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| [[programming/basic/leetcode/123. 买卖股票的最佳时机 III\|123. 买卖股票的最佳时机 III]] | hard       | <ul><li>#leetcode/dp/multi-status</li><li>#leetcode/recursive/memo</li><li>#leetcode/unsolved</li></ul>                                                                                                 | 2023-07-15-Sat, 11:28:51 am |
| [[programming/basic/leetcode/121. 买卖股票的最佳时机\|121. 买卖股票的最佳时机]]         | easy       | <ul><li>#leetcode/array/traversal</li><li>#leetcode/greedy-algorithm</li><li>#leetcode/dp/multi-status</li><li>#leetcode/unsolved</li></ul>                                                             | 2023-07-14-Fri, 3:55:55 pm  |
| [[programming/basic/leetcode/122. 买卖股票的最佳时机 II\|122. 买卖股票的最佳时机 II]]   | medium     | <ul><li>#leetcode/brainteasers</li><li>#leetcode/math/monotone</li><li>#leetcode/dp/multi-status</li><li>#leetcode/unsolved</li><li>#leetcode/greedy-algorithm</li><li>#leetcode/sub/sequence</li></ul> | 2023-07-06-Thu, 7:50:10 pm  |
| [[programming/basic/leetcode/188. 买卖股票的最佳时机 IV\|188. 买卖股票的最佳时机 IV]]   | hard       | <ul><li>#leetcode/dp/multi-status</li><li>#leetcode/recursive/memo</li></ul>                                                                                                                            | 2023-07-15-Sat, 7:52:03 pm  |
| [[programming/basic/leetcode/309. 最佳买卖股票时机含冷冻期\|309. 最佳买卖股票时机含冷冻期]]   | medium     | <ul><li>#leetcode/dp/multi-status</li><li>#leetcode/recursive/memo</li></ul>                                                                                                                            | 2023-07-15-Sat, 7:25:06 pm  |

{ .block-language-dataview}

## 最佳收益

### [1025. 除数博弈](https://leetcode-cn.com/problems/divisor-game/)

#### 问题

- 爱丽丝和鲍勃一起玩游戏，他们轮流行动。爱丽丝先手开局。
- 最初，黑板上有一个数字 `N` 。在每个玩家的回合，玩家需要执行以下操作：
  - 选出任一 `x`，满足 `0 < x < N` 且 `N % x == 0` 。
  - 用 `N - x` 替换黑板上的数字 `N` 。
- 如果玩家无法执行这些操作，就会输掉游戏。
- 只有在爱丽丝在游戏中取得胜利时才返回 `True`，否则返回 `false`。假设两个玩家都以最佳状态参与游戏。

#### 分析

- 对于任意的 N，选择 x=1，可以使得 N 转换为 N-1，如果 N-1 是自己失败的结局，主动选择 1，就会导致对手失败
- 对于任意的 N，选择任意 x，使得 N 转换为 N-x，如果 N-x 是自己失败的结局，主动选择 x，就会导致对手失败
- 爱丽丝拥有主动权

  ```
  输入：2
  输出：true
  解释：爱丽丝选择 1，鲍勃无法进行操作。
  
  输入：3
  输出：false
  解释：爱丽丝选择 1，鲍勃也选择 1，然后爱丽丝无法进行操作。
  
  输入：4
  解释：爱丽丝选择 1，转换为3，输出!T[3],true
  		 爱丽丝选择 2，转换为2，输出!T[2],false
  		 
  输入：5
  解释：爱丽丝选择 1，转换为4，输出!T[4],false
  
  输入：6
  解释：爱丽丝选择 1，转换为5，输出!T[5],true
  		 爱丽丝选择 2，转换为4，输出!T[4],false
  		 爱丽丝选择 3，转换为3，输出!T[3],true
  ```

**再分析**

+ 数字 N 如果是奇数，它的约数必然都是奇数；若为偶数，则其约数可奇可偶。

  无论 N 初始为多大的值，游戏最终只会进行到 N=2 时结束，那么谁轮到 N=2 时谁就会赢。

  因为爱丽丝先手，N 初始若为偶数，爱丽丝则只需一直选 1，使鲍勃一直面临 N 为奇数的情况，这样爱丽丝稳赢； N 初始若为奇数，那么爱丽丝第一次选完之后 N 必为偶数，那么鲍勃只需一直选 1 就会稳赢。

  综述，判断 N 是奇数还是偶数，即可得出最终结果！

## 方案总数

### [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

#### 问题

- 假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。
- 每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？
- **注意：**给定 *n* 是一个正整数。

#### 分析

- **最优化分析：**到达最后一个台阶，只有 2 种选择：
  - 从前一个台阶爬到该台阶 `T[i-1]`
  - 从前两个台阶爬到该台阶 `T[i-2]`
  - 所以 `T[i]= T[i-1]+T[i-2]`
- **初始化分析：**
  - 如果只有一个台阶，只有一个方法
  - 如果有两个台阶，有两种方案：
    - 一种是直接爬 2 个台阶
    - 一种是在之前的基础上再爬一个台阶
- **返回值分析：**`T[i]` 代表到当前台阶的方案总数，返回 `T[n-1]`
- **边界分析：**如果 n 小于 2，直接返回 n 即可

#### 实现

- ```js
  var climbStairs = function(n) {
    if(n<=2) return n
    let T=[]
    // 初始化
    T[0]=1
    T[1]=2
    // 状态转移
    for (let i = 2; i < n; i++) {
      T[i]= T[i-1]+T[i-2]
    }
    return T[n-1]
  };
  ```

### [746. 使用最小花费爬楼梯](https://leetcode-cn.com/problems/min-cost-climbing-stairs/)

#### 问题

- 数组的每个索引做为一个阶梯，第 `i` 个阶梯对应着一个非负数的体力花费值 `cost[i]`(索引从 0 开始)。
- 每当你爬上一个阶梯你都要花费对应的体力花费值，然后你可以选择继续爬一个阶梯或者爬两个阶梯。
- 您需要找到达到楼层顶部（i+1）的最低花费。
- 在开始时，你可以选择从索引为 0 或 1 的元素作为初始阶梯。

  ```
  输入: cost = [10, 15, 20]
  输出: 15
  解释: 最低花费是从cost[1]开始，然后走两步即可到阶梯顶，一共花费15。
  ```

#### 分析

- **初始化分析：**需要初始化两项，值分别是 `cost[0],cost[1]`
- **最优分析：**之后到达该楼梯花费的最少能量有两种情况：
  - 从前两格楼梯跳到当前阶梯：`T[i]=cost[i-2]+cost[i]`
  - 从前一格楼梯跳到当前阶梯：`T[i]=cost[i-1]+cost[i]`
  - 两者当中最小值就是该格楼梯的最优状况
- **返回值分析：**`T[i]` 代表的是到达该格阶梯的最小代价，要到达楼顶（n+1），有两种选择：
  - 从最后一格出发
  - 从倒数第二格触发
  - 两者当中的最小值，就是返回值

#### 实现

- ```js
  var minCostClimbingStairs = function(cost) {
    let dp = []
    let len = cost.length
    // 初始化
    dp[0]=cost[0]
    dp[1]=cost[1]
    // 状态转移
    for(let i=2;i<len;++i){
        dp[i]=Math.min(dp[i-1]+cost[i],dp[i-2]+cost[i])
    }
    return Math.min(dp[len-1],dp[len-2])
  }
  
  console.log(minCostClimbingStairs([1, 100, 1, 1, 1, 100, 1, 1, 100, 1]))
  
  ```

### [198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

#### 问题

- 你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。
- 给定一个代表每个房屋存放金额的非负整数数组，计算你**在不触动警报装置的情况下，**能够偷窃到的最高金额。

  ```
  输入: [1,2,3,1]
  输出: 4
  解释: 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
       偷窃到的最高金额 = 1 + 3 = 4 。
  ```

#### 分析

- **最优分析：**要偷窃第 `i` 家房屋的最优情况，取决于第 `i-2`、`i-3` 家的最优情况
  - 如果偷窃 `i-2`，`T[i]=T[i-2]+val[i]`
  - 如果偷窃 `i-3`，`T[i]=T[i-3]+val[i]`
  - 两者中的最大值，即是最优情况
- **初始化分析：**需要初始化 3 项，`T[0]=val[0],T[1]=val[1],T[2]=val[2]+val[0]`

  > 或者可以在数组最前面插入一个零，这样就可以省去 `T[2]` 的最优判断

- **边界条件分析：**对于元素少于 3 个的，直接返回最大的即可，如果是空数组则返回 0
- **返回值分析：**返回 `T` 里的最大值即可

#### 实现

- ```js
  var rob = function(nums) {
    let len = nums.unshift(0)
      if(len<=3){ // 空数组插入0之后，也可以一起处理
      return Math.max(...nums)
    }
    let T = []
    // 初始化
    T[0]=nums[0],T[1]=nums[1],T[2]=nums[2]
    // 状态转移
    for (let i = 3; i < len; i++) {
      T[i] = Math.max(T[i-2]+nums[i],T[i-3]+nums[i])
    }
    return Math.max(...T)
  };
  ```

### [62. 不同路径](https://leetcode-cn.com/problems/unique-paths/)

#### 问题

+ 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

  问总共有多少条不同的路径？

  ![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/robot_maze.png)

  ```
  输入: m = 3, n = 2
  输出: 3
  解释:
  从左上角开始，总共有 3 条路径可以到达右下角。
  1. 向右 -> 向右 -> 向下
  2. 向右 -> 向下 -> 向右
  3. 向下 -> 向右 -> 向右
  ```

#### 分析

+ **状态分析：**`T[i][j]` 代表从起到到 ( i , j ) 的路径条数
+ **最优化分析：**到达点 `T[i][j]` 要么是从 `T[i-1][j]` 要么是 `T[i][j-1]`，`T[i][j]=T[i-1][j]+T[i][j-1]`
+ **初始化分析：**`T[0][*]=1`、`T[*][0]=1`
+ **边界分析：**全都符合状态转移方程
+ **返回值分析：**返回 `T[i][j]` 即可

#### 实现

+ ```js
  /**
   * @param {number} m
   * @param {number} n
   * @return {number}
   */
  var uniquePaths = function(m, n) {
    let T=[]
    for (let i = 0; i < m; i++) {
      T[i]=[]
      for (let j = 0; j < n; j++) {
        // 初始化
        if(i===0||j===0){
          // 第一行、第一列只有一条路径
          T[i][j]=1
        }else{
          T[i][j]=T[i-1][j]+T[i][j-1]
        }
      }
    }
    return (T[m-1][n-1]);
  };
  ```

### [63. 不同路径 II](https://leetcode-cn.com/problems/unique-paths-ii/)

#### 问题

+ 一个机器人位于一个 *m x n* 网格的左上角 （起始点在下图中标记为“Start” ）。
+ 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。
+ 现在考虑网格中**有障碍物**。那么从左上角到右下角将会有多少条不同的路径？

#### 分析

+ 有障碍物的位置标记为 0 即可，`if(obstacle) T[i][j] = 0`
+ 初始化第一格
  + 第一行的状态转移为：`T[i][j]=T[i][j-1]`
  + 第一列的状态转移为：`T[i][j]=T[i-1][j]`

#### 实现

+ ```js
  var uniquePathsWithObstacles = function(obstacleGrid) {
    let m = obstacleGrid.length
    let n = obstacleGrid[0].length
    // 边界情况分析
    if ( obstacleGrid == null 
      || obstacleGrid.length == 0 
      || obstacleGrid[0].length == 0 
      || obstacleGrid[0][0]===1 
      ) {
      return 0;
    }
    let T = []
    for (let i = 0; i < m; i++) {
      T[i]=[]
      for (let j = 0; j < n; j++) {
        if(i===0&&j===0){
          // 初始化第一格
          T[0][0]=1
          continue
        }       
        if(obstacleGrid[i][j] === 1){
          // 障碍物设置为0
          T[i][j]=0
        }else{
          // 初始化
          if(i===0){
            // 第一行
            T[i][j]=T[i][j-1]
          }else if(j===0){
            // 第一列
            T[i][j]=T[i-1][j]
          }else{
            // 非第一行，非第一列
            T[i][j]=T[i-1][j]+T[i][j-1]
          }
        }
      }
    }
    console.log (T[m-1][n-1]);
  };
  ```

### 虾皮

+ <https://www.nowcoder.com/questionTerminal/a71f3bd890734201986cd1e171807d30>

### 方案总数总结

+ 总是从终点考虑，前一个点到终点有多少的可能

# 序列型

为什么不用考虑两个都不选的场景? 编辑记录的一个 b 站视频有讲解

| File                                                                           | difficulty | etags                                                                                                                                                                                                   | date-created                |
| ------------------------------------------------------------------------------ | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| [[programming/basic/leetcode/1035. 不相交的线\|1035. 不相交的线]]                     | medium     | <ul><li>#leetcode/dp</li><li>#leetcode/sub/sequence</li><li>#leetcode/unsolved</li></ul>                                                                                                                | 2023-07-16-Sun, 7:54:32 pm  |
| [[programming/basic/leetcode/115. 不同的子序列\|115. 不同的子序列]]                     | hard       | <ul><li>#leetcode/dp</li><li>#leetcode/sub/sequence</li><li>#leetcode/recursive/memo</li></ul>                                                                                                          | 2023-07-17-Mon, 3:23:50 pm  |
| [[programming/basic/leetcode/122. 买卖股票的最佳时机 II\|122. 买卖股票的最佳时机 II]]         | medium     | <ul><li>#leetcode/brainteasers</li><li>#leetcode/math/monotone</li><li>#leetcode/dp/multi-status</li><li>#leetcode/unsolved</li><li>#leetcode/greedy-algorithm</li><li>#leetcode/sub/sequence</li></ul> | 2023-07-06-Thu, 7:50:10 pm  |
| [[programming/basic/leetcode/300. 最长递增子序列\|300. 最长递增子序列]]                   | medium     | <ul><li>#leetcode/dp</li><li>#leetcode/sub/sequence</li><li>#leetcode/greedy-algorithm</li><li>#leetcode/binary-search</li><li>#leetcode/unsolved</li></ul>                                             | 2023-07-16-Sun, 10:13:53 am |
| [[programming/basic/leetcode/392. 判断子序列\|392. 判断子序列]]                       | easy       | <ul><li>#leetcode/sub/sequence</li><li>#leetcode/dp/lcs</li><li>#leetcode/unsolved</li><li>#leetcode/double-pointer</li></ul>                                                                           | 2023-05-15-Mon, 8:38:50 pm  |
| [[programming/basic/leetcode/376. 摆动序列\|376. 摆动序列]]                         | medium     | <ul><li>#leetcode/brainteasers</li><li>#leetcode/math/monotone</li><li>#leetcode/dp</li><li>#leetcode/unsolved</li><li>#leetcode/greedy-algorithm</li><li>#leetcode/sub/sequence</li></ul>              | 2023-07-06-Thu, 10:37:33 am |
| [[programming/basic/leetcode/467. 环绕字符串中唯一的子字符串\|467. 环绕字符串中唯一的子字符串]]       | medium     | <ul><li>#leetcode/string/hash-table</li><li>#leetcode/dp</li><li>#leetcode/prefix-sum</li><li>#leetcode/solved</li><li>#leetcode/sub/consecutive</li></ul>                                              | 2023-05-15-Mon, 1:29:08 am  |
| [[programming/basic/leetcode/524. 通过删除字母匹配到字典里最长单词\|524. 通过删除字母匹配到字典里最长单词]] | medium     | <ul><li>#leetcode/sub/sequence</li><li>#leetcode/dp/lcs</li><li>#leetcode/unsolved</li></ul>                                                                                                            | 2023-05-16-Tue, 11:35:07 am |
| [[programming/basic/leetcode/516. 最长回文子序列\|516. 最长回文子序列]]                   | medium     | <ul><li>#leetcode/brainteasers</li><li>#leetcode/dp</li><li>#leetcode/sub/sequence</li><li>#leetcode/palindrome</li><li>#leetcode/unsolved</li></ul>                                                    | 2023-07-18-Tue, 7:23:14 pm  |
| [[programming/basic/leetcode/53. 最大子数组和\|53. 最大子数组和]]                       | medium     | <ul><li>#leetcode/brainteasers</li><li>#leetcode/dp</li><li>#leetcode/solved</li><li>#leetcode/greedy-algorithm</li><li>#leetcode/sub/consecutive</li></ul>                                             | 2023-07-06-Thu, 7:37:09 pm  |
| [[programming/basic/leetcode/583. 两个字符串的删除操作\|583. 两个字符串的删除操作]]             | medium     | <ul><li>#leetcode/dp</li><li>#leetcode/sub/sequence</li></ul>                                                                                                                                           | 2023-07-17-Mon, 4:01:05 pm  |
| [[programming/basic/leetcode/647. 回文子串\|647. 回文子串]]                         | medium     | <ul><li>#leetcode/brainteasers</li><li>#leetcode/dp</li><li>#leetcode/sub/consecutive</li><li>#leetcode/palindrome</li></ul>                                                                            | 2023-07-18-Tue, 4:17:13 pm  |
| [[programming/basic/leetcode/718. 最长重复子数组\|718. 最长重复子数组]]                   | medium     | <ul><li>#leetcode/dp</li><li>#leetcode/sub/consecutive</li><li>#leetcode/unsolved</li></ul>                                                                                                             | 2023-07-16-Sun, 1:05:51 pm  |

{ .block-language-dataview}

## 最长公共子序列 LCS

## [516. 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

+ 参考回文串，本质是最长公共子序列

## [300. 最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

### 问题

+ 给定一个无序的整数数组，找到其中最长上升子序列的长度。

  ```
  let nums = [10,9,2,5,3,7,101,18]
  输出: 4 
  解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
  ```

### 回溯

**分析**

+ 最简单的方法是找到所有增加的子序列，然后返回最长增加的子序列的最大长度。为了做到这一点，我们使用递归函数 `length of LIS` 返回从当前元素（对应于 ` nums[curIndex]`）开始可能的 ` lis ` 长度（包括当前元素）。在每个函数调用中，我们考虑两种情况：

  1. 当前元素大于包含在 ` lis ` 中的前一个元素。
     1. 在这种情况下，我们可以在 ` lis ` 中包含当前元素。因此，我们通过将其包含在内，得出了 ` lis ` 的长度。
     2. 此外，我们还通过不在 lis 中包含当前元素来找出 lis 的长度。
     3. 因此，当前函数调用返回的值是两个长度中的最大值。
  2. 当前元素小于包含在 lis 中的前一个元素。在这种情况下，我们不能在 lis 中包含当前元素。因此，我们只通过不在 lis 中包含当前元素（由当前函数调用返回）来确定 lis 的长度。

**实现**

+ ```js
  const lengthOfLIS = function(nums){
    return lengthofLIS(nums,Number.MIN_VALUE,0)
  }
  const lengthofLIS = function(nums,prev,curpos){
    if (curpos == nums.length) {
      return 0;
   }
   let taken = 0
   if(nums[curpos]>prev){
     taken = 1 + lengthofLIS(nums, nums[curpos], curpos + 1);
   }
   const nottaken = lengthofLIS(nums, prev, curpos + 1);
   return Math.max(taken, nottaken);
  }
  ```
+ 有点回溯的味道

**复杂度分析**

+ 时间复杂度：O(2^n)。递归树的大小将为 2^n 。
+ 空间复杂度：O(n^2)，使用大小为 n*n 的 memo 数组。

**错误的尝试**

+ ```js
  var lengthOfLIS = function(nums) {
    const len = nums.length
    if(len<=1) return len
    const T = []
    for (let i = 0; i < len; i++) {
      T[i] = [nums[i]]
      // 选出包含nums[i]在内的最长上升序列
      for (let j = i+1; j <len; j++) {
        if(nums[j]>T[i][T[i].length-1]){
          T[i].push(nums[j])
        }
      }
    }
    const lengthArr = T.map((subArr,i) => {
      return subArr.length
    })
    console.log(lengthArr)
    return Math.max(...lengthArr)
  };
  
  // let nums = [10,9,2,5,3,7,101,18] 
  let nums = [10,9,2,5,3,4] // 2 5 没法做到跳过5， 2 3 4
  ```

### 记忆化回溯

**分析**

+ 算法： 在前面的方法中，许多递归调用必须使用相同的参数进行一次又一次的调用。通过将为特定调用获得的结果存储在二维记忆数组 memo 中，可以消除这种冗余。`memo[i][j]` 表示使用 `nums[i]` 作为上一个被认为包含/不包含在 ` lis ` 中的元素的 ` lis ` 可能的长度，其中 ` nums[j] ` 作为当前被认为包含/不包含在 ` lis ` 中的元素。这里，`nums` 表示给定的数组。

**实现**

+ ```java
  const lengthOfLIS = function(nums){
    const len = nums.length
    const memo = new Array(len)
    // 初始化第一行，即只选择单个元素那一行
    memo[-1] = new Array(len).fill(0)
    for (let i = 0; i < len; i++) {
      memo[i] = []
    }
    return LIS(nums,-1,0,memo)
  }
  const LIS = function(nums,prevIndex,curIndex,memo){
    if (curIndex == nums.length) {
      return 0;
    }
    // 为了让初始化的情况，无法进入判断
    if (prevIndex >= 0 && memo[prevIndex][curIndex] >= 0) {
      return memo[prevIndex][curIndex];
    }
    let taken = 0
    // 为了让初始化的情况，无法进入判断
    if(prevIndex < 0 || nums[curIndex]>nums[prevIndex]){
      taken = 1 + LIS(nums, curIndex, curIndex + 1,memo)
    }
    const notTaken = LIS(nums, prevIndex, curIndex + 1,memo);
    memo[prevIndex][curIndex] = Math.max(taken, notTaken);
    return memo[prevIndex][curIndex];
  }
  ```
+ 难点在于第一个元素的选择，即 `prevIndex` 为 -1,`curIndex` 为 0 时，此时不应该进入判断一，同时需要进入判断二，然后在第一层 notTaken，会填满 `memo[-1]`，如果 `memo[-1]` 不初始化，可能会出现读取 undefined
+ 还是得打断点看一看

### 转化为低阶动态规划

+ 对序列进行排序，找到排序后的序列和原序列的最长公共子串，即是最长的上升子序列
+ **注意**：因为是严格上升，所以需要去重
+ **状态**：以 nums[j] 结尾的和 sort[i] 结尾的最长公共子序的长度

  ```js
  const lengthOfLIS = function(nums){
    const len = nums.length
      if(len <=1) return len
      const sort = [...new Set(nums)].sort((a,b) => a-b)
      // 初始化
      const dp = new Array(len)
      dp[-1] = new Array(len).fill(0)
      dp[-1][-1] = 0
      for (let i = 0; i < len; i++) {
        dp[i] = []
        dp[i][-1] = 0
      }
      for (let i = 0; i < len; i++) {
        for (let j = 0; j < len; j++) {
          if(nums[i]===sort[j]){
            dp[i][j] = dp[i-1][j-1] + 1
          }else{
            dp[i][j] = Math.max(dp[i][j-1],dp[i-1][j])
          }
        }
      }
    return dp[len-1][len-1]
  }
  let nums = [-2,1]
  console.log(lengthOfLIS(nums))
  ```

**复杂度分析**

+ 去重 O(n)，排序 O(logn)，动态规划 O(n^2)

### 高阶动态规划

**分析**

- **初始化分析**：如果只有一个元素，则返回 1，`T[-1] = -Infinity`
- **最优化分析**：新增一个元素，相对于子序列的最后一个是否是上升？
  - 是则 len+1，`T[i] = T[i-1]+1`
  - 否则还是等于之前的
  - 错误： 2 5 3 4
- **转换视角**：就像摆动序列有两种情况，此时有对应的 N 中情况，新增一元素，所有情况中符合上升的都 +1，然后选择最大的作为当前的，不符合的都维持不变
- **定义状态**：`dp[i] ` 表示以第 `i ` 个数字为结尾的最长上升子序列的长度。即在 ` [0, ..., i] ` 的范围内，选择 以数字 ` nums[i] ` 结尾 可以获得的最长上升子序列的长度。注意：以第 ` i ` 个数字为结尾，即 要求 ` nums[i] ` 必须被选取。反正一个子序列一定会以一个数字结尾，那我就将状态这么定义，这一点是常见的。
- **状态转移方程**：遍历到索引是 ` i ` 的数的时候，我们应该把索引是 ` [0, ... ,i - 1] ` 的 dp 都看一遍，如果当前的数 ` nums[i] ` 严格大于之前的某个数，那么 ` nums[i] ` 就可以接在这个数后面形成一个更长的上升子序列。把前面的 ` i ` 个数都看了，`dp[i] ` 就是它们的最大值加 1。即比当前数要小的那些里头，找最大的，然后加 1。
- 高阶马尔可夫模型

**实现**

+ ```java
  const lengthOfLIS = function(nums){
    const len = nums.length
      if(len <=1) return len
        const dp = new Array(len)
        dp[0] = 1
        let ret = 1
        for (let i = 1; i < dp.length; i++) {
          let max = 0;
          for (let j = 0; j < i; j++) {
            if (nums[i] > nums[j]) {
              max = Math.max(max, dp[j]);
            }
          }
          dp[i] = max + 1;
          ret = Math.max(ret, dp[i]);
        }
    return ret;
  }
  ```

**复杂度分析**

- 时间复杂度：O(n^2)*。有两个 n 的循环。
- 空间复杂度：O(n)，用了大小为 n 的矩阵 dp。

### 贪心算法 + 二分查找

+ <https://leetcode-cn.com/problems/longest-increasing-subsequence/solution/dong-tai-gui-hua-er-fen-cha-zhao-tan-xin-suan-fa-p/>

### 对比

+ 139.单词拆分
+ 最长连续序列：<https://blog.csdn.net/u013309870/article/details/70242770>

# 子串型\子段型

## 最长公共子串

### 分析

+ **定义状态**：`dp[i][j]` 表示以 `str1[i]和str2[j]` 结尾的最长公共子串的长度
+ **状态转移方程**：
  + `if(str1[i]==str2[j]) dp[i][j]=dp[i-1][j-1]+1`
  + `if(str1[i]!=str2[j]) dp[i][j]=0`

  ```
  		a　  b　　c
  		
  c　　0　　0　　1
  
  a　　1　　0　　0
  
  b　　0　　2　　0
  
  a　　1　　0　　0
  ```

### 实现

+ ```js
  function longestSub(str1,str2){
    const len1 = str1.length,
          len2 = str2.length
    let res = 0
    let lastIndex = null
    // 初始化行列的操作
    let dp = new Array(len1) // 列
    dp[-1] = new Array(len2).fill(0) // 负1行
    dp[-1][-1] = 0
    for (let i = 0; i < len1; i++) { // 行
      dp[i] = new Array(len2)
      dp[i][-1] = 0
    }
    for (let i = 0; i < len1; i++) {
      for (let j = 0; j < len2; j++) {
        if(str1[i]===str2[j]){
          // 发现新的公共字符，等于左上角(刨除公共字符后的子串)+1
          dp[i][j] = dp[i-1][j-1]+1
          if(dp[i][j]>res){
            res = dp[i][j]
            lastIndex = i
          }
        }else{
          // 不是相同的字符
          dp[i][j] = 0
        }
      }
    }
    console.log(dp)
    // 返回公共子串
    findCS(lastIndex,res,str1)
    function findCS(lastIndex,len,str){
      if(len===0) return ''
      const start = lastIndex - len + 1
      console.log(str.slice(start,start+len))
    }
    return res
  }
  console.log(longestSub('abagcabac','abadfabac'))
  ```

### 滚动数组

+ 每一行的结果只依赖于上一行

  ```js
  function longestSub(str1,str2){
    const len1 = str1.length,
          len2 = str2.length
    let res = 0
    let lastIndex = null
    // 初始化行的操作，内循环的才是行的长度
    let lineA =  new Array(len2).fill(0)
    lineA[-1] = 0
    let lineB = new Array(len2)
    lineB[-1] = 0
    let referLine = lineA
    let currentLine = lineB
    for (let i = 0; i < len1; i++) {
      for (let j = 0; j < len2; j++) {
        if(str1[i]===str2[j]){
          // 发现新的公共字符，等于左上角(刨除公共字符后的子串)+1
          // dp[i][j] = dp[i-1][j-1]+1
          currentLine[j] = referLine[j-1] + 1
          if(currentLine[j]>res){
            res = currentLine[j]
            lastIndex = i
          }
        }else{
          // 不是相同的字符
          // dp[i][j] = 0
          currentLine[j] = 0
        }
      }
      console.log(referLine)
      // 滚动数组
      ;([referLine,currentLine] = [currentLine,referLine])
    }
    // 返回公共子串
    findCS(lastIndex,res,str1)
    function findCS(lastIndex,len,str){
      if(len===0) return ''
      const start = lastIndex - len + 1
      console.log(str.slice(start,start+len))
    }
    return res
  }
  console.log(longestSub('abagcabac','abadfabac'))
  ```

### 一维数组

+ 反向填充实现只使用一个一维数组

  ```js
  function longestSub(str1,str2){
    const len1 = str1.length,
          len2 = str2.length
    let res = 0
    let lastIndex = null
    // 初始化行的操作
    let dp =  new Array(len1).fill(0)
    dp[-1] = 0
    for (let i = 0; i < len1; i++) {
      // 逆序填充
      for (let j = len2-1; j >= 0; j--) {
        if(str1[i]===str2[j]){
          // 此处的dp[j-1]是上一轮的dp[j-1]
          dp[j] = dp[j-1] + 1
          if(dp[j]>res){
            res = dp[j]
            lastIndex = i
          }
        }else dp[j] = 0
      }
      console.log(dp)
    }
    // 返回公共子串
    findCS(lastIndex,res,str1)
    function findCS(lastIndex,len,str){
      if(len===0) return ''
      const start = lastIndex - len + 1
      console.log(str.slice(start,start+len))
    }
    return res
  }
  console.log(longestSub('abagcabac','abadfabac'))
  ```

### 后缀树的方法

+ <http://www.ahathinking.com/archives/122.html>

## [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

+ 参考回文串，本质是最长公共子串

## [139. 单词拆分](https://leetcode-cn.com/problems/word-break/)

### 问题

- 给定一个**非空**字符串 *s* 和一个包含**非空**单词列表的字典 *wordDict*，判定 *s* 是否可以被空格拆分为一个或多个在字典中出现的单词。

  ```
  let s = "leetcode", wordDict = ["leet", "code"]
  输出: true
  解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
  ```

**说明：**

- 拆分时可以重复使用字典中的单词。
- 你可以假设字典中没有重复的单词。

### 回溯法

- 最简单的实现方法是用递归和回溯。为了找到解，我们可以检查字典单词中每一个单词的可能前缀，如果在字典中出现过，那么去掉这个前缀后剩余部分回归调用。同时，如果某次函数调用中发现整个字符串都已经被拆分且在字典中出现过了，函数就返回 true 。

  ```java
  var wordBreak = function (s, wordDict) {
    return wordBreakRe(s,wordDict,0)
    function wordBreakRe(s,wordDict,breakPoint){
      const len = s.length
      if(breakPoint === len ) return true
      // 由于slice的特性，end最大为len
      for (let end = breakPoint+1; end <= len; end++) {
        const sub = s.slice(breakPoint, end)
        if (wordDict.includes(sub) && wordBreakRe(s, wordDict, end)) {
          return true
        }
      }
      return false
    }
  }
  let s = "leetcode", wordDict = ["leet", "code"]
  console.log(wordBreak(s,wordDict))
  ```

**复杂度分析**

- 时间复杂度：O(n^n)。考虑最坏情况 s=aaaaaaa 。每一个前缀都在字典中，此时回溯树的复杂度会达到 n^n
- 空间复杂度：O(n) 。回溯树的深度最深达到 nn 。

### 记忆化回溯

- ```java
  var wordBreak = function (s, wordDict) {
    return wordBreakRe(s,wordDict,0)
      
    function wordBreakRe(s,wordDict,breakPoint,memo=[]){
      const len = s.length
      if(breakPoint === len ) return true
      // 记录false的情况，直接返回就可以
      if(memo[breakPoint]!==undefined) return memo[breakPoint]
      for (let end = breakPoint+1; end <= len; end++) {
        const sub = s.slice(breakPoint, end)
        if (wordDict.includes(sub) && wordBreakRe(s, wordDict, end,memo)) {
          return true
        }
      }
      return memo[breakPoint] = false
    }
  }
  ```

### 使用宽度优先搜索

- <https://leetcode-cn.com/problems/word-break/solution/dan-ci-chai-fen-by-leetcode/>

### 动态规划

- 核心是字符串的 slice() 方法，接受参数，表示开始切片的位置，和结束切片的位置，但是不包括结束的位置
- **状态分析：**当前的字符串能否切割成字典里的单词，有两种情况
  - 整个字符串用于匹配字典
  - 从前面**某个匹配点**之后截取剩余的字符，用于匹配字典
  - 两种情况只要有一个是 true，都可以认为是可匹配的字符串
  - 需要记录匹配点
- **返回值分析：**`T[i]` 代表索引 `s.slice(0, T[i])`**前面**的字符可以成功匹配

  ```js
  var wordBreak = function (s, wordDict) {
    let len = s.length
    let longestWord = wordDict.reduce(function (max, val, i, arr) {
      return Math.max(max, val.length)
    }, 0)
    let T = []
    T[0] = 0
    for (let i = 1; i <= len; i++) {
      // 优化1
      for (let j = 0; j < T.length ; j++) {
        // 如果最长的单词 比 截取的要长 才判断
        if(longestWord >= i - T[j]){
          // 从前一个匹配点开始截取的子串能否匹配成功
          if (wordDict.indexOf(s.slice(T[j], i)) != -1) {
            T.push(i)
            break;
          }
        }
      }
    }
    return T[T.length - 1] == s.length
  };
  ```

### 对比

+ 300.最长上升子序列

## [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

### 问题

- 给定一个整数数组 `nums` ，找到一个具有最大和的**连续**子数组（子数组最少包含一个元素），返回其最大和。

  ```
  输入: [-2,1,-3,4,-1,2,1,-5,4],
  输出: 6
  解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
  ```

### 分析

+ 定义状态 `T[j]`：以 `j` 为止的连续子数组的最大和

**j = 0**

- 可以选择的只有 `[-2]`，所以 `T[0]=-2`

**j = 1**

- 只有两种可能，要么是 `nums[0]+nums[1]`，要么是 `numss[1]`
- `T=[j] = Math.max(T[j-1]+arr[j],arr[j])`
- 如果 `T[j-1]` 是负数，那么加上自身后，肯定会比自身要小，那就没有必要相加了，选择自身，重新开启一个新的序列
- **状态转移方程**：`T[j] = Math.max(arr[j],T[j-1]+arr[j])`

**j = 2**

- 所以 `T[j]=T[j-1]+arr[j]=-2`，如果要包含 [-3]，那么最大的子序和只能是 -2

### 输出结果

- 逆序查找最大子序和的源，减去 `arr[j]`，查找上一项，直到为 0

### 实现

- ```js
  let maxSubArray = function(arr) {
    let T = []
    // 初始化操作
    T[-1]=0
    for (let i = 0; i < arr.length; i++) {
      T[i] = Math.max(arr[i],T[i-1]+arr[i])
    }
    return Math.max(...T)
  }
  ```
- 空间优化

  ```js
  let maxSubArray = function(arr) {
    // 赋值为null的写法是错误的，隐式转换为0，如果数组全部都是负数，最后输出0，结果错误
    // let ans=cur=null
    // 赋值为数组本身的元素即可，同时i从1开始
    let ans=cur=arr[0]
    for (let i = 1; i < arr.length; i++) {
      cur = Math.max(arr[i],cur+arr[i])
      // cur = arr[i] + Math.max(cur,0)
      ans = Math.max(ans,cur)
    }
    return ans
  }
  ```

- 查找源

  ```js
  function findValue(arr,T,max){
    let i = T.indexOf(max)
  
    let sub = []
  
    while(max>0){
      max = max-arr[i]
      sub.unshift(arr[i])
      i--
    }
    console.log(sub)
  }
  ```

## [918. 环形子数组的最大和](https://leetcode-cn.com/problems/maximum-sum-circular-subarray/)

### 题目

+ 给定一个由整数数组 A 表示的环形数组 C，求 C 的非空子数组的最大可能和。
+ 在此处，环形数组意味着数组的末端将会与开头相连呈环状。（形式上，当 0 <= i < A.length 时 C[i] = A[i]，而当 i >= 0 时 C[i+A.length] = C[i]）
+ 此外，子数组最多只能包含固定缓冲区 A 中的每个元素一次。（形式上，对于子数组 C[i], C[i+1], ..., C[j]，不存在 i <= k1, k2 <= j 其中 k1 % A.length = k2 % A.length）

  ```
  输入：[1,-2,3,-2]
  输出：3
  解释：从子数组 [3] 得到最大和 3
  
  输入：[3,-1,2,-1]
  输出：4
  解释：从子数组 [2,-1,3] 得到最大和 2 + (-1) + 3 = 4
  
  输入：[3,-2,2,-3]
  输出：3
  解释：从子数组 [3] 和 [3,-2,2] 都可以得到最大和 3
  ```

### 降维思想

+ 降为思想，转化为 53.最大子序和
+ 复制一个数组接在后面，保持子序的长度不超过 N
+ 如果是使用动态规划的思想，没有办法判断子序的长度，只有在暴力法才能做到，否决

### 最小值变种

+ 要求一个环状的数组的子段最大和：
  + 假如这个数组全都是整数，那么最大和就是数组每个元素相加
  + 假如这个数组有一个负数，那么最大和就是在数组和中扣去负数的部分
+ 因此，我们只需要找到环状数组的子段的最小和，这个最小和可能是负数，也可能是一个正数
  + 如果是负数的情况：
    + min 在环状连接处，那么环状的最大和等于和非环状一致
    + min 不是环状连接处，那么相当于扣去负数部分
  + 如果是正数的话，那么环状的最大和和非环状的是一样的
+ 如果数组的每个元素都是负数，`sum-min` 一定为 0，结果就会输出 0

  ```java
  var  maxSubarraySumCircular = function(nums) {
    const len = nums.length
    let max=nums[0],
        min=nums[0],
        curMax=nums[0],
        curMin=nums[0]
    // 求出非环状的 最大子序和
    for (let i = 1; i < len; i++) {
      curMax = Math.max(nums[i]+curMax,nums[i])
      curMin = Math.min(nums[i]+curMin,nums[i])
      max = Math.max(max, curMax)
      min = Math.min(min,curMin)
    }
    const sum = nums.reduce((acc,val)=>acc+val,0)
    // 处理全部负数的情况
    if(sum==min) return max
    return Math.max(sum-min,max)
  }
  ```

# 矩阵型

矩阵乘积

![1564546290940](/img/user/programming/basic/algorithm/dynamic-programming/!dynamic-programming/1564546290940.png)

![1564546391268](/img/user/programming/basic/algorithm/dynamic-programming/!dynamic-programming/1564546391268.png)

![1564546627231](/img/user/programming/basic/algorithm/dynamic-programming/!dynamic-programming/1564546627231.png)

![1564546638324](/img/user/programming/basic/algorithm/dynamic-programming/!dynamic-programming/1564546638324.png)

![1564546767373](/img/user/programming/basic/algorithm/dynamic-programming/!dynamic-programming/1564546767373.png)

# 区间型

# 状态压缩型

<https://www.nowcoder.com/discuss/173333?type=all&order=time&pos=&page=1>

<https://blog.csdn.net/birdstorm_s/article/details/20546879>

<https://blog.csdn.net/stone_fall/article/details/88669064>

<https://blog.csdn.net/haut_ykc/article/details/79606213>

<https://blog.csdn.net/dengpan1889/article/details/101646361>

<https://blog.csdn.net/V5ZSQ/article/details/83244233>

<https://www.cnblogs.com/ACRykl/p/8597593.html>

+ 为什么 k 要 100，k 又从 0 开始？什么几把

  ```js
  var tortoiseRun = function(str,m){
    const len = str.length,
          maxDistance = 2 * len
          md = maxDistance
    const dp = new Array(len)
    for(let i=-1;i<=len;i++){
      dp[i] = []
      for(let j= -1;j<=m;j++){
        dp[i][j] = []
        for(let k= -1;k<=md;k++){ // 起始位置为100，
          dp[i][j][k] = []
        }
      }
    }
  
    dp[0][0][0][1] = 1 // 初始位置，可以到达
  
    for(let i=1;i<=len;i++) // 第i个字符
    for(let j=0;j<=m;j++) // 操作了j次
    for(let k=0;k<md;k++){ // 能否到达位置k，1表示可以到达，0表示不可以到达，朝向dir为0或者1
      if(str[i]=='F'){
        // 选择转向，距离k不变，操作次数j减一，方向改变
        dp[i][j][k][1]|=dp[i-1][j-1][k][0]
        dp[i][j][k][0]|=dp[i-1][j-1][k][1]
        // 不转向，选择前进，方向不变
        dp[i][j][k+1][1]|=dp[i-1][j][k][1] // 方向为1，前进一步
        dp[i][j][k-1][0]|=dp[i-1][j][k][0] // 方向为0，后退一步
      }else{
        // 不转向，选择前进，方向不变
        dp[i][j][k+1][1]|=dp[i-1][j-1][k][1] //T变成F,再前进 
        dp[i][j][k-1][0]|=dp[i-1][j-1][k][0] //T变成F,再后退
        // 选择转向
        dp[i][j][k][1]|=dp[i-1][j][k][0]
        dp[i][j][k][0]|=dp[i-1][j][k][1]
      }
    }
    let ans = 0
    for(let k=0;k<=md;k++){
      if(dp[len][m][k][0]||dp[len][m][k][1]) ans=Math.max(ans,Math.abs(len-k))
    }
    return ans
  }
  
  console.log(tortoiseRun('FFFFTFFFF',3))
  ```

# 树型

## leetCode96.不同的二叉树

### 问题

- 给定一个整数 *n*，求以 1 ... *n* 为节点组成的二叉搜索树有多少种？

### 分析

- **最优化分析：**假设 n 个节点存在二叉排序树的个数是 G(n)，1 为根节点，2 为根节点，...，n 为根节点
  - 当 1 为根节点时，其左子树节点个数为 0，右子树节点个数为 n-1
  - 同理当 2 为根节点时，其左子树节点个数为 1，右子树节点为 n-2
  - 所以可得 G(n) = G(0)\*G(n-1)+G(1)\*(n-2)+...+G(n-1)*G(0)
- **初始化分析：**G(0)=1 ， G(1) = 1

### 实现

```js
  /**
   * @param {number} n
   * @return {number}
   */
  var numTrees = function(n) {
    let G = new Array(n+1).fill(0)
    G[0]=1
    G[1]=1
    for (let i = 2; i <= n; i++) {
      for (let j = 1; j <=i; j++) {
        G[i] += G[j-1] * G[i-j]
      }
    }
    return G[n]
  };
```

## leetCode95. 不同的二叉树 II

### 问题

- 给定一个整数 *n*，生成所有由 1 ... *n* 为节点所组成的**二叉搜索树**。

### 分析

- 先输出完左树，再输出右数
- **最优化分析：**假设 n 个节点存在二叉排序树的个数是 G(n)，1 为根节点，2 为根节点，...，n 为根节点
  - 当 1 为根节点时，其左子树节点个数为 0，右子树节点个数为 n-1
  - 同理当 2 为根节点时，其左子树节点个数为 1，右子树节点为 n-2
  - 所以可得 G(n) = G(0)\*G(n-1)+G(1)\*(n-2)+...+G(n-1)*G(0)
- 思路： 从小往大生成，新来一数，肯定比现有的节点都大 那么 n 可以成为现在所有树的父节点，并且他们都是 n 的左子树 第二步就是沿着现有子树的右侧尝试不断插入。 如果插入以后，n 还有子树，那么这些子树都是 n 的左子树。
- 以 n=5 为例,枚举 12345 这五个根节点
  - 1: 左: 空 右:[2,3,4,5] 组成的二叉搜索树
  - 2: 左:[1] 右:[3,4,5] 组成的二叉搜索树
  - 3: 左:[1,2] 组成的二叉搜索树, 右:[4,5] 组成的二叉搜索树
  - 4: 左:[1,2,3] 组成的二叉搜索树,右:[5]
  - 5: 左:[1,2,3,4] 组成的二叉搜索树,右: 空

  定义函数 helper(nums): 作用为为给定的有序数组生成所有二叉搜索树

# 再分类

关于动规，还有 树形 DP（打家劫舍系列里有一道），数位 DP，区间 DP ，概率型 DP，博弈型 DP，状态压缩 dp 等等等，这些我就不去做讲解了，面试中出现的概率非常低。

## leetCode264.丑数 II

- 见 Math

## leetCode313.超级丑数

+ 见 Math

## [467. 环绕字符串中唯一的子字符串](https://leetcode-cn.com/problems/unique-substrings-in-wraparound-string/)

### 问题

- 把字符串 `s` 看作是“abcdefghijklmnopqrstuvwxyz”的无限环绕字符串，所以 `s` 看起来是这样的："...zabcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyzabcd....".
- 现在我们有了另一个字符串 `p` 。你需要的是找出 `s` 中有多少个唯一的 `p` 的非空子串，尤其是当你的输入是字符串 `p` ，你需要输出字符串 `s` 中 `p` 的不同的非空子串的数目。
- **注意:** `p` 仅由小写的英文字母组成，p 的大小可能超过 10000。

  ```
  输入: "cac"
  输出: 2
  解释: 字符串 S 中的字符串“cac”只有两个子串“a”、“c”。.
  ```

### 分析

- 思路和动态分析不太一样，更多的突破点在于字符串的特性，**子串的特性**，以每个字母结尾的子串数量相加等于总的子串数量
  - `abcd`，1+2+3+4，总共 10 个。
- **状态**：我们需要记录以该字符结尾的，连续字符的个数
- **状态转移**：比起传统的转移，这里的更多是因为同一个字母结尾的子串是有重复的，因此选择长度更长的那个
- 与之前一个状态依赖于上一个状态不同，这次一个状态时依赖于上一轮的状态
- ```js
  /**
   * @param {string} p
   * @return {number}
   */
  var findSubstringInWraproundString = function(p) {
    const len =p.length
    if(len<=1) return len
    let dp=new Array(26).fill(0)
    let base = 'a'.charCodeAt(0)
    let count=1 // 连续的字符的个数
    dp[p.charCodeAt(0)-base]=1
    for(let i=1;i<len;i++){
      // 相邻的字符如果连续,count++,
      if(p.charCodeAt(i)-p.charCodeAt(i-1)===1 || p[i] === 'a' && p[i - 1] === 'z'){
        count++
      }else{
        count=1 // 重新开始计数
      }
      console.log(dp[p.charCodeAt(i)-base],count);
      // 记录以该字母结尾的连续字符的个数
      dp[p.charCodeAt(i)-base]=Math.max(dp[p.charCodeAt(i)-base]*1,count)
    }
    let sum=0
    for(let value of dp){
      sum +=value
    }
    return sum
  };
  
  console.log(findSubstringInWraproundString('zab'))
  ```

## 方案总结

- 状态之间的联系，由具体的特性决定，丑数的特性，环绕字符串的特性，单词拆分的特性

### [879. 盈利计划](https://leetcode-cn.com/problems/profitable-schemes/)
