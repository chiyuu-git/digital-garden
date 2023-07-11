---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/backtracking/"}
---


# 概述

回溯法的常见思路就是递归, 通过递归的终止条件可以利用调用栈层层返回, 实现回溯.

甚至可以认为回溯就是递归的一种理解

回溯法是纯暴力的搜索, 并不是一个高效的算法, 因为递归甚至不好中端.

往往是 for 循环嵌套不好解决的问题, 可以考虑使用回溯法

## 回溯的常用场景

1. 组合问题：N 个数里面按一定规则找出 k 个数的集合
2. 切割问题：一个字符串按一定规则有几种切割方式 (切割问题, 其实是一种组合问题)
3. 子集问题：一个 N 个数的集合里有多少符合条件的子集 (也是一种组合问题)
4. 排列问题：N 个数按一定规则全排列，有几种排列方式
5. 棋盘问题：N 皇后，解数独等等

for 循环不能解决的穷举问题

### 什么时候使用 Used 数组，什么时候使用 Begin 变量

有些朋友可能会疑惑什么时候使用 used 数组，什么时候使用 begin 变量。这里为大家简单总结一下：

排列问题，讲究顺序（即 `[2, 2, 3]` 与 `[2, 3, 2]` 视为不同列表时），需要记录哪些数字已经使用过，此时用 used 数组；

组合问题，不讲究顺序（即 `[2, 2, 3]` 与 `[2, 3, 2]` 视为相同列表时），需要按照某种顺序搜索，此时使用 begin 变量。

注意：具体问题应该具体分析， 理解算法的设计思想 是至关重要的，请不要死记硬背。

| File                                                       | difficulty | etags                                                                                                                                                                                |
| ---------------------------------------------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [[programming/basic/leetcode/18. 四数之和\|18. 四数之和]]       | medium     | <ul><li>#leetcode/hash-table/count</li><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/skip-dup</li></ul>                                                                  |
| [[programming/basic/leetcode/15. 三数之和\|15. 三数之和]]       | medium     | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/skip-dup</li></ul>                                                                                                     |
| [[programming/basic/leetcode/40. 组合总和 II\|40. 组合总和 II]] | medium     | <ul><li>#leetcode/backtracking/combination</li><li>#leetcode/skip-dup</li></ul>                                                                                                      |
| [[programming/basic/leetcode/90. 子集 II\|90. 子集 II]]     | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li><li>#leetcode/skip-dup</li></ul> |
| [[programming/basic/leetcode/491. 递增子序列\|491. 递增子序列]]   | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li><li>#leetcode/skip-dup</li></ul> |
| [[programming/basic/leetcode/47. 全排列 II\|47. 全排列 II]]   | medium     | <ul><li>#leetcode/backtracking/permutation</li><li>#leetcode/skip-dup</li></ul>                                                                                                      |

{ .block-language-dataview}

### 有同学问了，什么时候 For 可以从 0 开始呢？

求排列问题的时候，就要从 0 开始，因为集合是有序的，{1, 2} 和{2, 1}是两个集合，排列问题我们后续的文章就会讲到的。

组合问题, 就从 startIndex 开始

## 回溯法与 N 叉树

回溯法抽象成图形结构更便于理解, 抽象成树形结构, 因为递归是有终止的, 这个终止条件就是树的叶子节点

树的宽度是集合的大小 n

树的深度是递归的深度, 使用递归处理

## 回溯模板

### N 叉树思路

```js
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

	// 这里的 for 循环就是遍历 n 叉树的 n 个子节点

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```

### 二元思路

考虑当前位置是否选择, 本质是一样的, 但是感觉没有 n 叉树的思路好理解, 可能是子集问题的模板? 但是也可以衍生到其他问题上

```js
vector<int> temp;
void dfs(int cur, int n) {
    if (cur == n + 1) {
        // 记录答案
        // ...
        return;
    }
    // 考虑选择当前位置
    temp.push_back(cur);
    dfs(cur + 1, n, k);
    temp.pop_back();
    // 考虑不选择当前位置
    dfs(cur + 1, n, k);
}
```

[131. 分割回文串](../leetcode/131.%20分割回文串.md) 只能使用二元思路的题目

## 与动态规划的区别

### 共同点

+ 用于求解多阶段决策问题。多阶段决策问题即：
+ 求解一个问题分为很多步骤（阶段）；
+ 每一个步骤（阶段）可以有多种选择。

### 不同点

+ 动态规划只需要求我们评估最优解是多少，最优解对应的具体解是什么并不要求。因此很适合应用于评估一个方案的效果；
+ 回溯算法可以搜索得到所有的方案（当然包括最优解），但是本质上它是一种遍历算法，时间复杂度很高。

# 组合问题

## 77. 组合

[77. 组合](https://leetcode.cn/problems/combinations/)

![pCG6VPg.png|400](https://s1.ax1x.com/2023/06/21/pCG6VPg.png)

直接的解法当然是使用 for 循环，例如示例中 k 为 2，很容易想到 用两个 for 循环，这样就可以输出 和示例中一样的结果。

代码如下：

```js
int n = 4;
for (int i = 1; i <= n; i++) {
    for (int j = i + 1; j <= n; j++) {
        cout << i << " " << j << endl;
    }
}
```

输入：n = 100, k = 3 那么就三层 for 循环，代码如下：

```js
int n = 100;
for (int i = 1; i <= n; i++) {
    for (int j = i + 1; j <= n; j++) {
        for (int u = j + 1; u <= n; n++) {
            cout << i << " " << j << " " << u << endl;
        }
    }
}
```

如果 n 为 100，k 为 50 呢，那就 50 层 for 循环，是不是开始窒息

递归来做层叠嵌套（可以理解是开 k 层 for 循环），**每一次的递归中嵌套一个 for 循环，那么递归就可以用于解决多层嵌套循环的问题了**

回溯法解决的问题都可以抽象为树形结构（N 叉树），用树形结构来理解回溯就容易多了

![](/img/user/programming/basic/algorithm/backtracking/image-20230621143705100.png)

每次从集合中选取元素，可选择的范围随着选择的进行而收缩，调整可选择的范围

图中可以发现 n 相当于树的宽度，k 相当于树的深度

图中每次搜索到了叶子节点，我们就找到了一个结果

### 递归函数的返回值以及参数

在这里要定义两个全局变量，一个用来存放符合条件单一结果，一个用来存放符合条件结果的集合。

代码如下：

```java
vector<vector<int>> result; // 存放符合条件结果的集合
vector<int> path; // 用来存放符合条件结果
```

其实不定义这两个全局变量也是可以的，把这两个变量放进递归函数的参数里，但函数里参数太多影响可读性，所以我定义全局变量了。

函数里一定有两个参数，既然是集合 n 里面取 k 个数，那么 n 和 k 是两个 int 型的参数。

然后还需要一个参数，为 int 型变量 startIndex，这个参数用来记录本层递归的中，集合从哪里开始遍历（集合就是 `[1,...,n]` ）。

为什么要有这个 startIndex 呢？

建议在 [77.组合视频讲解 (opens new window)](https://www.bilibili.com/video/BV1ti4y1L7cv) 中，07:36 的时候开始听，startIndex 就是防止出现重复的组合

从下图中红线部分可以看出，在集合 `[1,2,3,4]` 取 1 之后，下一层递归，就要在 `[2,3,4]` 中取数了，那么下一层递归如何知道从 `[2,3,4]` 中取数呢，靠的就是 startIndex。

所以需要 startIndex 来记录下一层递归，搜索的起始位置。

那么整体代码如下：

```java
vector<vector<int>> result; // 存放符合条件结果的集合
vector<int> path; // 用来存放符合条件单一结果
void backtracking(int n, int k, int startIndex)
```

### 回溯函数终止条件

什么时候到达所谓的叶子节点了呢？

path 这个数组的大小如果达到 k，说明我们找到了一个子集大小为 k 的组合了，在图中 path 存的就是根节点到叶子节点的路径。

此时用 result 二维数组，把 path 保存起来，并终止本层递归。

所以终止条件代码如下：

```java
if (path.size() == k) {
    result.push_back(path);
    return;
}
```

### 单层搜索的过程

回溯法的搜索过程就是一个树型结构的遍历过程，在如下图中，可以看出 for 循环用来横向遍历，递归的过程是纵向遍历。

![](/img/user/programming/basic/algorithm/backtracking/image-20230621144144031.png)

如此我们才遍历完图中的这棵树。

for 循环每次从 startIndex 开始遍历，然后用 path 保存取到的节点 i。

代码如下：

```java
for (int i = startIndex; i <= n; i++) { // 控制树的横向遍历
    path.push_back(i); // 处理节点
    backtracking(n, k, i + 1); // 递归：控制树的纵向遍历，注意下一层搜索要从i+1开始
    path.pop_back(); // 回溯，撤销处理的节点
}
```

可以看出 backtracking（递归函数）通过不断调用自己一直往深处遍历，总会遇到叶子节点，遇到了叶子节点就要返回。

backtracking 的下面部分就是回溯的操作了，撤销本次处理的结果。

### 剪枝优化

来举一个例子，n = 4，k = 4 的话，那么第一层 for 循环的时候，从元素 2 开始的遍历都没有意义了。 在第二层 for 循环，从元素 3 开始的遍历都没有意义了。

这么说有点抽象，如图所示：

![](/img/user/programming/basic/algorithm/backtracking/image-20230621144256678.png)

图中每一个节点（图中为矩形），就代表本层的一个 for 循环，那么每一层的 for 循环从第二个数开始遍历的话，都没有意义，都是无效遍历。

所以，可以剪枝的地方就在递归中每一层的 for 循环所选择的起始位置

如果 for 循环选择的起始位置之后的元素个数 已经不足 我们需要的元素个数了，那么就没有必要搜索了

接下来看一下优化过程如下：

1. 已经选择的元素个数：path.size();
2. 还需要的元素个数为: k - path.size();
3. 在集合 n 中至多要从该起始位置 : n - (k - path.size()) + 1，开始遍历

为什么有个 +1 呢，因为包括起始位置，我们要是一个左闭的集合。

举个例子，n = 4，k = 3， 目前已经选取的元素为 0（path.size 为 0），n - (k - 0) + 1 即 4 - ( 3 - 0) + 1 = 2。

从 2 开始搜索都是合理的，可以是组合 `[2, 3, 4]`

这里大家想不懂的话，建议也举一个例子，就知道是不是要 +1 了。

所以优化之后的 for 循环是：

```java
// i为本次搜索的起始位置
for (int i = startIndex; i <= n - (k - path.size()) + 1; i++) 
```

```js
let result = []
let path = []
var combine = function (n, k) {
    result = []
    combineHelper(n, k, 1)
    return result
};
const combineHelper = (n, k, startIndex) => {
    if (path.length === k) {
        result.push([...path])
        return
    }
    // let i = curIndex, 随着 index 不断扩大, 实现避免重复
    for (let i = startIndex; i <= n - (k - path.length) + 1; ++i) {
        path.push(i)
        combineHelper(n, k, i + 1)
        path.pop()
    }
}
```

### 二元思路

```js
var combine = function(n, k) {
    const ans = [];
    const dfs = (cur, n, k, temp) => {
        // 剪枝：temp 长度加上区间 [cur, n] 的长度小于 k，不可能构造出长度为 k 的 temp
        if (temp.length + (n - cur + 1) < k) {
            return;
        }
        // 记录合法的答案
        if (temp.length == k) {
            ans.push(temp);
            return;
        }
        // 考虑选择当前位置
        dfs(cur + 1, n, k, [...temp, cur]);
        // 考虑不选择当前位置
        dfs(cur + 1, n, k, temp);
    }
    dfs(1, n, k, []);
    return ans;
};
```

## 相关题目

| File                                                           | difficulty | etags                                                                                                                                                                                |
| -------------------------------------------------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [[programming/basic/leetcode/17. 电话号码的字母组合\|17. 电话号码的字母组合]] | medium     | <ul><li>#leetcode/backtracking/combination</li><li>#leetcode/graph/traversal/dfs</li></ul>                                                                                           |
| [[programming/basic/leetcode/77. 组合\|77. 组合]]               | medium     | <ul><li>#leetcode/backtracking/combination</li><li>#leetcode/sub/sequence</li></ul>                                                                                                  |
| [[programming/basic/leetcode/216. 组合总和 III\|216. 组合总和 III]] | medium     | <ul><li>#leetcode/backtracking/combination</li><li>#leetcode/backtracking/binary</li><li>#leetcode/sub/sequence</li></ul>                                                            |
| [[programming/basic/leetcode/39. 组合总和\|39. 组合总和]]           | medium     | <ul><li>#leetcode/backtracking/combination</li></ul>                                                                                                                                 |
| [[programming/basic/leetcode/40. 组合总和 II\|40. 组合总和 II]]     | medium     | <ul><li>#leetcode/backtracking/combination</li><li>#leetcode/skip-dup</li></ul>                                                                                                      |
| [[programming/basic/leetcode/131. 分割回文串\|131. 分割回文串]]       | medium     | <ul><li>#leetcode/backtracking/combination/partition</li><li>#leetcode/dp/memo</li><li>#leetcode/unsolved</li></ul>                                                                  |
| [[programming/basic/leetcode/93. 复原 IP 地址\|93. 复原 IP 地址]]   | medium     | <ul><li>#leetcode/backtracking/combination/partition</li><li>#leetcode/unsolved</li></ul>                                                                                            |
| [[programming/basic/leetcode/78. 子集\|78. 子集]]               | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li></ul>                            |
| [[programming/basic/leetcode/90. 子集 II\|90. 子集 II]]         | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li><li>#leetcode/skip-dup</li></ul> |
| [[programming/basic/leetcode/491. 递增子序列\|491. 递增子序列]]       | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li><li>#leetcode/skip-dup</li></ul> |

{ .block-language-dataview}

# 切割问题

二元思路更容易理解成组合的特殊形式: 是否要拼接上当前字符

N 叉树思路更贴近切割的概念: 通过 for 循环 index 来判断下一个切割, 到底在哪里

| File                                                         | difficulty | etags                                                                                                               |
| ------------------------------------------------------------ | ---------- | ------------------------------------------------------------------------------------------------------------------- |
| [[programming/basic/leetcode/131. 分割回文串\|131. 分割回文串]]     | medium     | <ul><li>#leetcode/backtracking/combination/partition</li><li>#leetcode/dp/memo</li><li>#leetcode/unsolved</li></ul> |
| [[programming/basic/leetcode/93. 复原 IP 地址\|93. 复原 IP 地址]] | medium     | <ul><li>#leetcode/backtracking/combination/partition</li><li>#leetcode/unsolved</li></ul>                           |

{ .block-language-dataview}

# 子集问题

## 78. 子集

如果把 子集问题、组合问题、分割问题都抽象为一棵树的话，**那么组合问题和分割问题都是收集树的叶子节点，而子集问题是找树的所有节点！**

其实子集也是一种组合问题，因为它的集合是无序的，子集{1,2} 和 子集{2,1}是一样的。

那么既然是无序，取过的元素不会重复取，写回溯算法的时候，for 就要从 startIndex 开始，而不是从 0 开始！

有同学问了，什么时候 for 可以从 0 开始呢？

求排列问题的时候，就要从 0 开始，因为集合是有序的，{1, 2} 和{2, 1}是两个集合，排列问题我们后续的文章就会讲到的。

以示例中 `nums = [1,2,3]` 为例把求子集抽象为树型结构，如下：

![](/img/user/programming/basic/algorithm/backtracking/image-20230622121007274.png)

从图中红线部分，可以看出**遍历这个树的时候，把所有节点都记录下来，就是要求的子集集合**。

### 方案一: N 叉树思路

```js
var subsets = function(nums) {
    let result = []
    let path = []
    function backtracking(startIndex) {
        result.push([...path])
        for(let i = startIndex; i < nums.length; i++) {
            path.push(nums[i])
            backtracking(i + 1)
            path.pop()
        }
    }
    backtracking(0)
    return result
};
```

### 方案二: 回溯 + 二元思路

```js
var subsets = function(nums) {
    // 感觉和组合问题也是很类似的呀
    // 两种思路: 一种加法, 一种减法
    // 二元思路: 加法, 是否挑选当前元素加入集合
    // 减法思路: for 循环 slice index
    const res = [];
    dfs([], 0);
    return res;


    function dfs(path, index) {
        if (index === nums.length) {
            res.push([...path])
            return;
        }

        path.push(nums[index]);
        dfs(path, index + 1);
        path.pop();
        dfs(path, index + 1);
    }
};
```

| File                                                     | difficulty | etags                                                                                                                                                                                |
| -------------------------------------------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [[programming/basic/leetcode/78. 子集\|78. 子集]]         | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li></ul>                            |
| [[programming/basic/leetcode/90. 子集 II\|90. 子集 II]]   | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li><li>#leetcode/skip-dup</li></ul> |
| [[programming/basic/leetcode/491. 递增子序列\|491. 递增子序列]] | medium     | <ul><li>#leetcode/backtracking/combination/subset</li><li>#leetcode/sub/subset</li><li>#leetcode/backtracking/binary</li><li>#leetcode/unsolved</li><li>#leetcode/skip-dup</li></ul> |

{ .block-language-dataview}

# 排列问题

| File                                                     | difficulty | etags                                                                                                                                                        |
| -------------------------------------------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [[programming/basic/leetcode/46. 全排列\|46. 全排列]]       | medium     | <ul><li>#leetcode/backtracking/permutation</li></ul>                                                                                                         |
| [[programming/basic/leetcode/47. 全排列 II\|47. 全排列 II]] | medium     | <ul><li>#leetcode/backtracking/permutation</li><li>#leetcode/skip-dup</li></ul>                                                                              |
| [[programming/basic/leetcode/51. N 皇后\|51. N 皇后]]     | hard       | <ul><li>#leetcode/backtracking/permutation/chessboard</li><li>#leetcode/bit-manipulation</li><li>#leetcode/unsolved</li><li>#leetcode/array/matrix</li></ul> |
| [[programming/basic/leetcode/37. 解数独\|37. 解数独]]       | hard       | <ul><li>#leetcode/backtracking/permutation/sudoku</li><li>#leetcode/bit-manipulation</li><li>#leetcode/unsolved</li><li>#leetcode/array/matrix</li></ul>     |

{ .block-language-dataview}
