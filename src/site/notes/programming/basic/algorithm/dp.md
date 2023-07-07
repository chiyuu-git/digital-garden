---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/dp/"}
---


# 将递归全都理解为动态规划

参考 [397. 整数替换](../leetcode/397.%20整数替换.md) 这种形式的递归处理, 都可以理解为动态规划

# 动态规划的优化

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
- ![1555510635702](/img/user/programming/basic/algorithm/dp/1555510635702.png)
- 复数的问题是否是同一个问题
- ![1555549152869](/img/user/programming/basic/algorithm/dp/1555549152869.png)

# 容器问题

## 最少硬币找零问题详解

### 问题

- 给定 4 种面额的硬币 1 分，2 分，5 分，6 分，如果要找**11 分**的零钱，怎么做才能使得找的硬币数量总和**最少**
- 可以理解为：**容量**是 11

### 分析

- **特性：**
  - 只有 11 分的组合才满足结果，但是不妨碍我们列出未达到 11 分的情况
  - 硬币的数量是无限的
- **共性：**
  - 在表格中，价格从上到下递增。我们填表分析的时候，其实是事先默认了这种递增的关系
  - **分析第 i 行时，它的物品组合仅能是小于等于 i 的情况**
  - 逐行填写表格，每一行的结果都是目前最优的选择，新一行的选择参考于上一行
- **填表分析：**
  - `T[i][j]` 是硬币数量

  ![img](dp/163a13b1855aaede)

### 逐行分析

**i = 0**

- 初始行，可以选择的只有 1 分的硬币
- 只有 1 分的硬币，则对应的容量是多少就需要多少颗 1 分硬币 `T[0][j]=j/val[0]`

**i = 1**

- 在这一行，可以由 1 分硬币 和 2 分硬币 进行自由组合，并且由于价值的递增，我们优先考虑填入 2 分硬币
- **优选项：**2 分硬币
- 当容量 `j` 小于 2 时，无法填入 2 分硬币，那么最优的情况就和初始行一致，参考上一行 `T[i-1][j]`
- 当容量 `j` 大于 2 时，可以填入 2 分硬币
  - 先填满 2 分硬币 `Math.floor(j/val[i])`
  - 再判断剩余的容量是多少 `remain = j%val[i]`
  - 参考上一行**该**剩余容量的选择 `T[i-1][remain]`
  - 总的数量：`Math.floor(j/val[i])+ T[i-1][remain]`。再和上一行的最优情况做对比，选择数量**最小**

  ```js
  T[i][j] = Math.min(priorityResult, T[i - 1][j])
  ```

### 输出结果

- 组合
- 由于这些原则的限制，数量最少的肯定在最后一行最后一格
- 如果这一行和上一行的最后一格相等，说明是**继承**了上一行，找到最优行的位置，优选项组合的一部分，用优选项填满，再根据剩余的容量，找到其他硬币，最后输出返回

### 实现

- ```js
  function lessCoin(val, sum, n) {
    let T = []
    for (let i = 0; i < n; i++) {
      T[i] = [];
      for (let j = 0; j <= sum; j++) {
        // 因为新的结果参照于旧的结果，也就是参照于上一行
        if (i === 0) {
          //第一行,只有一种选择，容量是多少，就需要多少颗
          T[i][j] = j/val[i]
        } else {
          // 不是第一行，以优选项填满，剩余容量查询上一行
          let priorityCount = Math.floor(j/val[i])
          let remain = j%val[i]
          // 该行的优选结果
          let priorityResult = priorityCount + T[i-1][remain]
          T[i][j] = Math.min(priorityResult, T[i - 1][j])
        }
      }
    }
    findValue(val,sum,n,T)
    return T;
  }
  function findValue(val, sum, n, T) {
  
    var i = n - 1, j = sum;
    while (i > 0 && j > 0) {
  
      if (T[i][j] != T[i - 1][j]) {
        console.log('选择硬币' + val[i] + ',数量' + Math.floor(j/val[i]) + ',价值：' + Math.floor(j/val[i])*val[i]);
        j = j%val[i];
        i--;
      } else {
        i--;  //如果相等，那么就到 i-1 行
      }
    }
    if (i == 0) {
      if (T[i][j] != 0) { //那么第一行的物品也可以取
        console.log('选择硬币' + val[i] + ',数量' + Math.floor(j/val[i]) + ',价值：' + Math.floor(j/val[i])*val[i]);
      }
    }
  }
  var values = [1, 2, 5, 6],
    sum = 11,
    n = values.length
  
  console.log(lessCoin(values, sum, n));
  ```
- ![1555303439831](/img/user/programming/basic/algorithm/dp/1555303439831.png)

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
+ ```js
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

## 容器问题总结

+ **剩余的容量，参考之前的最佳选择**

# 递推型

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

## 最长公共子序列 LCS

### 问题

- 给定两个字符串序列 **abcadf** , **acbad**，求这两个字符串的**最长**公共子序列

### 分析

- **共性：**
  - **分析第 i 行第 j 列时，它的字符组合仅能是小于等于 i 且小于等于 j 的情况。**

    > 比如我们要填写 `T[2][2]` 时，那么此时等同于求字符串 **ac**,**ab**的最长公共子序列，填写 `T[4][5]` 时，那么此时等同于求 **acba**，**abcad**的最长公共子序列长度。

  - 逐行填写表格，每一行的结果都是目前最优的选择，新一行的选择参考于上一行
- **填表分析：**
  - 我们给两个子序列前面都加一个空字符，即

    ```js
    str1 = ["","a","c","b","a","d"],
    str2 = ["","a","b","c","a","d","f"],
    ```

  - 这一步其实就是初始化的操作，因为这类问题横向纵向都需要分析
  - 为什么填一堆 0 呢？表示字符串无法匹配，你可以理解这是一种辅助的计算方式，**在分析具体子序列时，不把构建的空字符纳入考虑范围**。
  - 使用 `T[i][j]` 表示组合的子序列长度。

  ![img](dp/163a772c80859c7d)

### 逐行分析

**i = 1**

- `i=1 j=1`：此时等同于求字符串 **a**和**a**的最长公共子序列长度，很显然结果为 1。
- `i=1 j=2`：此时等同于求字符串 **a**和**ab**的最长公共子序列长度，结果为 1。
- `i=1 j=3`：此时等同于求字符串 **a**和**abc**的最长公共子序列长度，结果为 1。
- 只要一个序列只有一个字符，那么另一个序列无论多长，它们的最长公共子序列长度最多只能为 1。所以 i=1 行剩余空格都填 1。

### I = 2

- `i=2 j=1`：此时等同于求字符串 **ac**和**a**的最长公共子序列长度，结果为 1。
- `i=2 j=2`：此时等同于求字符串 **ac**和**ab**的最长公共子序列长度，结果为 1。
- `i=2 j=3`：此时等同于求字符串 **ac**和**abc**的最长公共子序列长度。因为根据一开始的分析，求最长公共子序列时，子序列是可以不连续的，因此这两个序列的最长公共子序列应该是 **ac**，所以这里表格应该填 2
- 我们从 `T[2][3]` 这一个展开分析规律：
  - 很显然去除 **c** 这个公共字符后，两个字符串还剩下 **a**, **ab**。这个其实就是填写 ` T[1][2] ` 时的组合
  - 也就是我们可以假设当 `str1[i] == str2[j]` 时 `T[i][j]=T[i-1][j-1]+1`。
  - 当 `str1[i] != str2[j]` 时，`T[i][j]` 的值，取它上方或左边的较大值，即 `[i][j] = max(T[i-1][j],T[i][j-1])`。
- 用一句通俗的话来描述这种 `T[i][j]` 规律，就是：
  - **有新的公共字符，等于左上角加一**
  - **没有新的公共字符，则取上或左最大值，如果上左一样大，优先取左。**

**注意**

- 从我们的分析结果也可以看出，新一格的结果可能由左上，左，上，中的某一个决定，在循环中初始化的困难太大，所以统一在前面加上一个空字符串，全部初始化为 0，可以大大减轻工作量

### 输出结果

- **我们子序列保存在名为 s 的数组中，从表格中反向搜索，找到目标字符后，每次都把目标字符插入到数组最前面。**
- 根据前面提供的填表口诀，我们可以反向得出寻找子序列的口诀： **如果 `T[i][j]` 来自左上角加一，则是子序列，否则向左或上回退。如果上左一样大，优先取左。**

### 实现

- ```js
  function longestSub(str1,str2){
    let T = []
    // 简化初始化行列的操作
    str1 = ' '+str1
    str2 = ' '+str2
    for (let i = 0; i < str1.length; i++) {
      T[i] = []
      for (let j = 0; j < str2.length; j++) {
        // 初始化操作
        if(i===0 || j===0){
          T[i][j]=0
          continue
        }
        // 初始化已经完成，剩下的直接操作即可
        if(str1[i]===str2[j]){
          // 发现新的公共字符，等于左上角(刨除公共字符后的子串)+1
          T[i][j] = T[i-1][j-1]+1
        }else{
          // 没有新的公共字符
          T[i][j] = Math.max(T[i-1][j],T[i][j-1])
        }
      }
    }
    findValue(str1,str2,T)
    return T
  }
  
  console.log(longestSub('acbad','abcadf'))
  ```
- ```js
  function findValue(str1,str2,T){
    let ret = ''
    let i = str1.length-1
    let j = str2.length-1
    while(i>=0&&j>=0){
      if(str1[i]===str2[j]){
        // 公共字符
        ret = str1[i] + ret
        // 回退到左上角
        i--
        j--
      }else{
        // 非公共字符，判断源,向源回退
        T[i-1][j]>T[i][j-1]?i--:j--
      }
    }
    console.log(ret)
  }
  ```

  ![1555314073094](/img/user/programming/basic/algorithm/dp/1555314073094.png)

### 优化实现

+ 负号索引初始化

  ```js
  function longestSub(str1,str2){
    const len1 = str1.length 
    const len2 = str2.length 
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
        }else{
          // 没有新的公共字符
          dp[i][j] = Math.max(dp[i-1][j],dp[i][j-1])
        }
      }
    }
    findValue(str1,str2,dp)
    return dp
  }
  ```

+ 滚动数组，此时无法返回子序列

  ```js
  function longestSub(str1,str2){
    const len1 = str1.length 
    const len2 = str2.length 
    // 滚动数组
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
        }else{
          // 没有新的公共字符
          // dp[i][j] = Math.max(dp[i-1][j],dp[i][j-1])
          currentLine[j] = Math.max(currentLine[j-1],referLine[j])
        }
      }
      // 滚动数组
      console.log(currentLine)
      ;([referLine,currentLine] = [currentLine,referLine])
    }
    return referLine[len1-1]
  }
  ```

## [516. 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

+ 参考回文串，本质是最长公共子序列

## [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

### 问题

- 给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
- 如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。
- 注意你不能在买入股票前卖出股票。
- 卖出价格要高于买入价格，也就是需要盈利

  ```
  输入: [7,1,5,3,6,4]
  输出: 5
  解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
  注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
  ```

### 分析

- **定义状态**：当天操作后的最佳收益
- **初始化分析：**第一天价格最小值就是当天的值，无法卖出，收益只能是 0
- **最优化分析：**
  - 这天的价格如果低于之前的最低价格，则为 0，最佳方案是 `T[i-1]`
  - 如果高于之前的价格，则收益为差价，与 `T[i-1]` 做对比，更大的则是最佳收益

  > 不必判断价格差的相对大小，直接相减，由正负决定大小

- **状态转移方程**：`T[i] = Math.max(prices[i]-min,T[i-1])`
- **边界分析：**无法完成交易都是 0

### 实现

- ```js
  var maxProfit = function(prices) {
    if(prices.length===0) return 0
    let T=[]
    // 初始化
    T[-1]= 0 
    let min = Number.MAX_SAFE_INTEGER
    // 状态转移
    for (let i = 0; i < prices.length; i++) { 
      min = Math.min(min,prices[i])
      T[i] = Math.max(prices[i]-min,T[i-1])
    }
    console.log(T)
    return Math.max(...T)
  }
  let prices = [7,1,5,3,6,4]
  console.log(maxProfit(prices))
  ```

+ 本质：找到长度为 2，最大的上升子序列



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

![1564546290940](/img/user/programming/basic/algorithm/dp/1564546290940.png)

![1564546391268](/img/user/programming/basic/algorithm/dp/1564546391268.png)

![1564546627231](/img/user/programming/basic/algorithm/dp/1564546627231.png)

![1564546638324](/img/user/programming/basic/algorithm/dp/1564546638324.png)

![1564546767373](/img/user/programming/basic/algorithm/dp/1564546767373.png)

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

- ```js
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

# 背包九讲

+ <https://segmentfault.com/a/1190000012829866/>

## 01 背包问题

### 问题描述：

+ 有 ${n}$ 件物品和 ${1}$ 个容量为 W 的背包。每种物品均**只有一件**，第 ${i}$ 件物品的重量为 ${weights[i]}$，价值为 ${values[i]}$，求解将哪些物品装入背包可使价值总和最大。
+ 对于一种物品，要么装入背包，要么不装。所以对于一种物品的装入状态只是 0 或 1, 此问题称为**01 背包问题**。

### 问题分析：

+ 数据：物品个数 ${n=5}$,物品重量 ${weights=[2，2，6，5，4]}$,物品价值 ${values=[6，3，5，4，6]}$,背包总容量 ${W=10}$。
+ 我们设置一个矩阵 ${f}$ 来记录结果，${f(i, j)}$ 表示可选物品为 ${i...n}$ 背包容量为 ${j(0<=j<=W)}$ 时， 背包中所放物品的最大价值。

| w    | v    | i\j  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 2    | 6    | 0    |      |      |      |      |      |      |      |      |      |      |      |
| 2    | 3    | 1    |      |      |      |      |      |      |      |      |      |      |      |
| 6    | 5    | 2    |      |      |      |      |      |      |      |      |      |      |      |
| 5    | 4    | 3    |      |      |      |      |      |      |      |      |      |      |      |
| 4    | 6    | 4    |      |      |      |      |      |      |      |      |      |      |      |

#### **第一行**

+ 我们先看第一行，物品 0 的体积为 2，价值为 6：
  + 当容量为 0 时，什么也放不下，因此第一个格式只能填 0，程序表示为 ${f(0,0) = 0}$ 或者 ${f[0][0] = 0}$
  + 当 ${j=1}$ 时，依然放不下 ${w_0}$，因此依然为 0，${f(0, 1) = 0}$
  + 当 ${j=2}$ 时，能放下 ${w_0}$，于是有 ${f(0, 2)\ = \ v_0=6}$
  + 当 ${j=3}$ 时，也能放下 ${w_0}$，但我们只有一个物品 0，因此它的值依然是 6
  + 于是一直到 ${j=10}$ 时，它的值都是 ${v_0}$

  > 并不需要进行排序，因为参考的时候无论顺序与否都是当前组合的最佳选择

| w    | v    | i\j  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 2    | 6    | 0    | 0    | 0    | 6    | 6    | 6    | 6    | 6    | 6    | 6    | 6    | 6    |
| 2    | 3    | 1    |      |      |      |      |      |      |      |      |      |      |      |
| 6    | 5    | 2    |      |      |      |      |      |      |      |      |      |      |      |
| 5    | 4    | 3    |      |      |      |      |      |      |      |      |      |      |      |
| 4    | 6    | 4    |      |      |      |      |      |      |      |      |      |      |      |

+ 根据第一行，我们得到如下方程

![clipboard.png](https://segmentfault.com/img/bV1ZNN?w=646&h=158)

+ 当背包容量少于物品価积时，总价值为 0，否则为物品的价值

#### **第二行**

+ 然后我们看第二行，确定 ${f(0,1...10)}$ 这 11 个元素的值。
  + 当 ${j=0}$ 时，依然什么也放不下，值为 0，但我们发觉它是上方格式的值一样的，${f(1,0)=0}$
  + 当 ${j=1}$ 时，依然什么也放不下，值为 0，但我们发觉它是上方格式的值一样的，$f(1,1)=0$
  + 当 ${j=2}$ 时，它可以选择放入物品 1 或不放。
    + 如果选择不放物品 1，背包里面有物品 0，最大价值为 6。
    + 如果选择放入物品 1，我们要用算出背包放入物品 1 后还有多少容量，然后根据容量查出它的价值，再加上物品 1 的价值，即 ${f(0,j-w_1)+v_1}$ 。由于我们的目标是尽可能装最值钱的物品， 因此放与不放， 我们需要通过比较来决定，于是有 $f(i,j)=max{f(0,j),f(0,j-w_1)+v_1}$
    + 显然 ${v_1=2，v_0=6}$, 因此这里填 ${v_0}$。

  > 从比较的这一步可以看出，不需要对价值进行排序，因为无论如何在当前可以选择的组合中，比较后都可以选择最佳选择

  + 当 ${j=3}$ 时， 情况相同。
  + 当 ${j=4}$，能同时放下物品 0 与物品 1，我们这个公式的计算结果也合乎我们的预期， 得到 ${f(1,4)=9}$。
  + 当 ${j>4}$ 时， 由于背包只能放物品 0 与物品 1，那么它的最大价值也一直停留在 ${v_0+v_1=9}$

| w    | v    | i\j  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 2    | 6    | 0    | 0    | 0    | 6    | 6    | 6    | 6    | 6    | 6    | 6    | 6    | 6    |
| 2    | 3    | 1    | 0    | 0    | 6    | 6    | 9    | 9    | 9    | 9    | 9    | 9    | 9    |
| 6    | 5    | 2    |      |      |      |      |      |      |      |      |      |      |      |
| 5    | 4    | 3    |      |      |      |      |      |      |      |      |      |      |      |
| 4    | 6    | 4    |      |      |      |      |      |      |      |      |      |      |      |

#### **第三行**

+ 当 ${j=0}$ 时，什么都放不下，${f(2,0)=0}$
+ 当 ${j=1}$ 时，依然什么也放不下，${f(2,1)=0}$
+ 当 ${j=2}$ 时，虽然放不下 ${w_2}$，但我们根据上表得知这个容号时，背包能装下的最大价值是 6。
+ 继续计算下去，其实与上面推导的公式结果是一致的，说明公式是有效的。
+ 当 ${j=8}$ 时，背包可以是放物品 0、1，或者放物品 1、2，或者放物品 0、2。物品 0、1 的价值，我们在表中就可以看到是 9，至于其他两种情况我们姑且不顾，我们目测就知道是最优值是 ${6+5=11}$， 恰恰我们的公式也能正确计算出来。
+ 当 ${j=10}$ 时,刚好三个物品都能装下，它们的总值为 14，即 ${f(2,10)=14}$

| w    | v    | i\j  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 2    | 6    | 0    | 0    | 0    | 6    | 6    | 6    | 6    | 6    | 6    | 6    | 6    | 6    |
| 2    | 3    | 1    | 0    | 0    | 6    | 6    | 9    | 9    | 9    | 9    | 9    | 9    | 9    |
| 6    | 5    | 2    | 0    | 0    | 6    | 6    | 9    | 9    | 9    | 9    | 11   | 11   | 14   |
| 5    | 4    | 3    |      |      |      |      |      |      |      |      |      |      |      |
| 4    | 6    | 4    |      |      |      |      |      |      |      |      |      |      |      |

+ 整理一下第 1，2 行的适用方程：

![clipboard.png](https://segmentfault.com/img/bV1ZN0?w=1262&h=162)

+ 我们根据此方程，继续计算下面各列，于是得到

| w    | v    | i\j  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 2    | 6    | 0    | 0    | 0    | 6    | 6    | 6    | 6    | 6    | 6    | 6    | 6    | 6    |
| 2    | 3    | 1    | 0    | 0    | 6    | 6    | 9    | 9    | 9    | 9    | 9    | 9    | 9    |
| 6    | 5    | 2    | 0    | 0    | 6    | 6    | 9    | 9    | 9    | 9    | 11   | 11   | 14   |
| 5    | 4    | 3    | 0    | 0    | 6    | 6    | 9    | 9    | 9    | 10   | 11   | 13   | 14   |
| 4    | 6    | 4    | 0    | 0    | 6    | 6    | 9    | 9    | 12   | 12   | 15   | 15   | 15   |

+ 至此，我们就可以得到解为 15.
+ 我们最后根据 0-1 背包问题的最优子结构性质，建立计算 ${f(i,j)}$ 的递归式：

![clipboard.png](https://segmentfault.com/img/bV1ZOc?w=1356&h=276)

```js
 function knapsack(weights, values, W){
    var n = weights.length -1
    var f = [[]]
    for(var j = 0; j <= W; j++){
        if(j < weights[0]){ //如果容量不能放下物品0的重量，那么价值为0
           f[0][j] = 0
        }else{ //否则等于物体0的价值
           f[0][j] = values[0]
        }
    }
    for(var j = 0; j <= W; j++){
        for(var i = 1; i <= n; i++ ){
            if(!f[i]){ //创建新一行
                f[i] = []
            }
            if(j < weights[i]){ //等于之前的最优值
                f[i][j] = f[i-1][j]
            }else{
                f[i][j] = Math.max(f[i-1][j], f[i-1][j-weights[i]] + values[i]) 
            }
        }
    }
    return f[n][W]
}
var a = knapsack([2,2,6,5,4],[6,3,5,4,6],10)
console.log(a)
```

### 各种优化：

#### 合并循环

+ 现在方法里面有两个大循环，它们可以合并成一个。

  ```js
  function knapsack(weights, values, W){
    var n = weights.length;
    var f = new Array(n)
    for(var i = 0 ; i < n; i++){
      f[i] = []
    }
    for(var i = 0; i < n; i++ ){
      for(var j = 0; j <= W; j++){
        if(i === 0){ //第一行
          f[i][j] = j < weights[i] ? 0 : values[i]
        }else{
          if(j < weights[i]){ //等于之前的最优值
            f[i][j] = f[i-1][j]
          }else{
            f[i][j] = Math.max(f[i-1][j], f[i-1][j-weights[i]] + values[i]) 
          }
        }
      }
    }
    return f[n-1][W]
  }
  ```

#### 初始化

+ 然后我们再认真地思考一下，为什么要孤零零地专门处理第一行呢？`f[i][j] = j < weights[i] ? 0 : values[i]` 是不是能适用于下面这一行 `f[i][j] = Math.max(f[i-1][j], f[i-1][j-weights[i]] + values[i]) `。Math.max 可以轻松转换为三元表达式，结构极其相似。而看一下 i-1 的边界问题，有的书与博客为了解决它，会添加第 0 行，全部都是 0，然后 i 再往下挪。其实我们也可以添加一个 ${-1}$ 行。那么在我们的方程中就不用区分 ${i==0}$ 与 ${0>0}$ 的情况，方程与其他教科书的一模一样了！

  ![clipboard.png](https://segmentfault.com/img/bV1ZOk?w=1234&h=154)

  ```js
  function knapsack(weights, values, W){
      var n = weights.length;
      var f = new Array(n)
      f[-1] = new Array(W+1).fill(0) //负1行，因为有W+1次内循环
      for(var i = 0 ; i < n ; i++){ //注意边界，没有等号
          f[i] = new Array(W).fill(0)
          for(var j=0; j<=W; j++){ //注意边界，有等号,因为重量等于10最大
              if( j < weights[i] ){ //注意边界， 没有等号
                  f[i][j] = f[i-1][j]
              }else{
                  f[i][j] = Math.max(f[i-1][j], f[i-1][j-weights[i]]+values[i]);//case 3
              }
          }
      }
      return f[n-1][W]
  }
  ```

| w    | v    | i\j  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   |      |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | ---- |
| X    | X    | -1   | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    | 0    |
| 2    | 6    | 0    | 0    | 0    | 6    | 6    | 6    | 6    | 6    | 6    | 6    | 6    | 6    |      |
| 2    | 3    | 1    | 0    | 0    | 6    | 6    | 9    | 9    | 9    | 9    | 9    | 9    | 9    |      |
| 6    | 5    | 2    | 0    | 0    | 6    | 6    | 9    | 9    | 9    | 9    | 11   | 11   | 14   |      |
| 5    | 4    | 3    | 0    | 0    | 6    | 6    | 9    | 9    | 9    | 10   | 11   | 13   | 14   |      |
| 4    | 6    | 4    | 0    | 0    | 6    | 6    | 9    | 9    | 12   | 12   | 15   | 15   | 15   |      |

+ 负一行的出现可以大大减少了在双层循环的分支判定。是一个很好的技巧。
+ 注意，许多旧的教程与网上文章，通过设置二维数组的第一行为 0 来解决 i-1 的边界问题（比如下图）。当然也有一些思维转不过来的缘故，他们还在坚持数字以 1 开始，而我们新世代的 IT 人已经确立从 0 开始的编程思想。

![image_1c3lm81p3gd09n5pjk1i4aif92d.png-110.2kB](https://segmentfault.com/img/remote/1460000012829872?w=686&h=120)

#### 使用滚动数组压缩空间

+ 所谓滚动数组，目的在于优化空间，因为目前我们是使用一个 ${i*j}$ 的二维数组来储存每一步的最优解。在求解的过程中，我们可以发现，当前状态只与前一行的状态有关，那么更之前存储的状态信息已经无用了，可以舍弃的，我们只需要存储当前状态和前一行状态，所以只需使用 ${2*j}$ 的空间，循环滚动使用，就可以达到跟 ${i*j}$ 一样的效果。这是一个非常大的空间优化。
+ `currentLine` 和 `referLine` 分别对应着 `dp[i]` 和 `dp`

  ```js
  function knapsack(weights, values, W) {
    const n = weights.length
    let lineA = new Array(W+1).fill(0)
    let lineB = new Array(W+1).fill(0)
    let referLine = lineA
    let currentLine = lineB
    for (let i = 0; i < n; i++) {
      for (let j = 0; j <= W; j++) {
        if (j < weights[i]) {
          // f[i][j] = f[i-1][j]
          currentLine[j] = referLine[j]
        } else {
          // f[i][j] = Math.max(f[i - 1][j], f[i - 1][j - weights[i]] + values[i])
          currentLine[j] = Math.max(referLine[j],referLine[j-weights[i]]+values[i])
        }
      }
      // 内循环结束，更新了一行，当前和参考互换
      const aux = referLine
      referLine = currentLine
      currentLine = aux
      // currentLine = new Array(W+1).fill(0) 没有真正的减小空间复杂度
      // [referLine,currentLine] = [currentLine,referLine] Cannot set property '[object Array]' of undefined
      // ;([referLine,currentLine] = [currentLine,referLine]) 可以正确解析
    }
    console.log(referLine)
    return referLine[W]
  }
  
  var a = knapsack([2,3,4,1],[2,5,3, 2],5)
  console.log(a)
  var b = knapsack([2,2,6,5,4],[6,3,5,4,6],10)
  console.log(b)
  ```

+ 我们还可以用更 hack 的方法代替 currLine, lastLine

  ```js
  function knapsack(weights, values, W){
      var n = weights.length
     
      var f = [new Array(W+1).fill(0),[]], now = 1, last //case1 在这里使用es6语法预填第一行
      for(var i = 0; i < n; i++){ 
          for(var j=0; j<=W; j++){
              f[now][j] = f[1-now][j] //case2 等于另一行的同一列的值
              if( j>= weights[i] ){                         
                  var a = f[1-now][j]
                  var b = f[1-now][j-weights[i]] + values[i]
                  f[now][j] = Math.max(a, b);//case3
              }
           }
           last = f[now]
           now = 1-now // 1 - 0 => 1;1 - 1 => 0; 1 - 0 => 1 ....
     }
     return last[W];
  }
  var a = knapsack([2,3,4,1],[2,5,3, 2],5)
  console.log(a)
  var b = knapsack([2,2,6,5,4],[6,3,5,4,6],10)
  console.log(b)
  ```

+ 注意，这种解法由于丢弃了之前 N 行的数据，因此很难解出挑选的物品，只能求最大价值。

#### 使用一维数组压缩空间

+ 观察我们的状态迁移方程:

  ![clipboard.png](https://segmentfault.com/img/bV1ZOr?w=1216&h=112)

+ weights 为每个物品的重量，values 为每个物品的价值，W 是背包的容量，i 表示要放进第几个物品，j 是背包现时的容量（假设我们的背包是魔术般的可放大，从 0 变到 W）。
+ 我们假令 i = 0

  ![clipboard.png](https://segmentfault.com/img/bV1ZOs?w=1260&h=108)

+ f 中的 -1 就变成没有意义，因为没有第 -1 行，而 weights[0], values[0] 继续有效，${f(0,j)}$ 也有意义，因为我们全部放到一个一维数组中。于是:

  ![clipboard.png](https://segmentfault.com/img/bV1ZOv?w=1270&h=106)

+ 这方程后面多加了一个限制条件，要求是从大到小循环。为什么呢？
+ 假设有物体 ${\cal z}$ 容量 2，价值 ${v_z}$ 很大，背包容量为 5，如果 j 的循环顺序不是逆序，那么外层循环跑到物体 ${\cal z}$ 时， 内循环在 ${j=2}$ 时 ，${\cal z}$ 被放入背包。当 ${j=4}$ 时，寻求最大价值，物体 z 放入背包，${f(4)=max(f(4),f(2)+v_z) }$， 这里毫无疑问后者最大。 但此时 ${f(2)+v_z}$ 中的 ${f(2)}$ 已经装入了一次 ${\cal z}$，这样一来 ${\cal z}$ 被装入两次不符合要求， 如果逆序循环 j， 这一问题便解决了。
+ 从后面往前方参考的是没有修改过的上一行，本质上是不断的修改同一行
+ javascript 实现：

  ```js
  function knapsack(weights, values, W){
      var n = weights.length;
      var f = new Array(W+1).fill(0)
      for(var i = 0; i < n; i++) {
        	// j<weight[i]的与上一轮相等
          for(var j = W; j >= weights[i]; j--){  
              f[j] = Math.max(f[j], f[j-weights[i]] +values[i]);
          }
          console.log(f.concat()) //调试
      }
      return f[W];
  }
  var b = knapsack([2,2,6,5,4],[6,3,5,4,6],10)
  console.log(b)
  ```

### 选择物品

- 上面讲解了如何求得最大价值，现在我们看到底选择了哪些物品，这个在现实中更有意义。许多书与博客很少提到这一点，就算给出的代码也不对，估计是在设计状态矩阵就出错了。
- 仔细观察矩阵，从 ${f(n-1,W)}$ 逆着走向 ${f(0,0)}$，设 i=n-1,j=W，如果 ${f(i,j)}$==${f(i-1,j-w_i)+v_i}$ 说明包里面有第 i 件物品，因此我们只要当前行不等于上一行的总价值，就能挑出第 i 件物品，然后 j 减去该物品的重量，一直找到 j = 0 就行了。

  ```js
  function knapsack(weights, values, W){
      var n = weights.length;
      var f = new Array(n)
      f[-1] = new Array(W+1).fill(0)
      var selected = [];
      for(var i = 0 ; i < n ; i++){ //注意边界，没有等号
          f[i] = [] //创建当前的二维数组
          for(var j=0; j<=W; j++){ //注意边界，有等号
              if( j < weights[i] ){ //注意边界， 没有等号
                  f[i][j] = f[i-1][j]//case 1
              }else{
                  f[i][j] = Math.max(f[i-1][j], f[i-1][j-weights[i]]+values[i]);//case 2
              }
          }
      }
      var j = W, w = 0
      for(var i=n-1; i>=0; i--){
           if(f[i][j] > f[i-1][j]){
               selected.push(i)
               console.log("物品",i,"其重量为", weights[i],"其价格为", values[i])
               j = j - weights[i];
               w +=  weights[i]
           }
       }
      console.log("背包最大承重为",W," 现在重量为", w, " 总价值为", f[n-1][W])
      return [f[n-1][W], selected.reverse() ]
  }
  var a = knapsack([2,3,4,1],[2,5,3, 2],5)
  console.log(a)
  var b = knapsack([2,2,6,5,4],[6,3,5,4,6],10)
  console.log(b)
  ```

  ![image_1c3k62d511dtgo7gud815q866m16.png-22.8kB](https://segmentfault.com/img/remote/1460000012829873?w=326&h=142)

  ![image_1c3k617gc6jp10pn1ean1lv81boqp.png-28.5kB](https://segmentfault.com/img/remote/1460000012829874?w=389&h=165)

### 1.4 递归法解 01 背包

+ 由于这不是动态规则的解法，大家多观察方程就理解了：

  ```js
  function knapsack(n, W, weights, values, selected) {
      if (n == 0 || W == 0) {
          //当物品数量为0，或者背包容量为0时，最优解为0
          return 0;
      } else {
          //从当前所剩物品的最后一个物品开始向前，逐个判断是否要添加到背包中
          for (var i = n - 1; i >= 0; i--) {
              //如果当前要判断的物品重量大于背包当前所剩的容量，那么就不选择这个物品
              //在这种情况的最优解为f(n-1,C)
              if (weights[i] > W) {
                  return knapsack(n - 1, W, weights, values, selected);
              } else {
                  var a = knapsack(n - 1, W, weights, values, selected); //不选择物品i的情况下的最优解
                  var b = values[i] + knapsack(n - 1, W - weights[i], weights, values, selected); //选择物品i的情况下的最优解
                  //返回选择物品i和不选择物品i中最优解大的一个
                  if (a > b) {
                      selected[i] = 0; //这种情况下表示物品i未被选取
                      return a;
                  } else {
                      selected[i] = 1; //物品i被选取
                      return b;
                  }
              }
          }
      }
  }        
  var selected = [], ws = [2,2,6,5,4], vs = [6,3,5,4,6]
  var b = knapsack( 5, 10, ws, vs, selected)
  console.log(b) //15
  selected.forEach(function(el,i){
      if(el){
          console.log("选择了物品"+i+ " 其重量为"+ ws[i]+" 其价值为"+vs[i])
      }
  })
  ```

![image_1c3kfq8nhddj12m11eh1r68189520.png-16.8kB](https://segmentfault.com/img/remote/1460000012829876?w=236&h=88)

## 完全背包问题

### 2.1 问题描述：

+ 有 ${n}$ 件物品和 ${1}$ 个容量为 W 的背包。每种物品没有上限，第 ${i}$ 件物品的重量为 ${weights[i]}$，价值为 ${values[i]}$，求解将哪些物品装入背包可使价值总和最大。

### 2.2 问题分析：

+ 最简单思路就是把完全背包拆分成 01 背包，就是把 01 背包中状态转移方程进行扩展，也就是说 01 背包只考虑放与不放进去两种情况，而完全背包要考虑 放 0、放 1、放 2...的情况，

  ![clipboard.png](https://segmentfault.com/img/bV1ZOy?w=1418&h=776)

+ 这个 k 当然不是无限的，它受背包的容量与单件物品的重量限制，即 ${j/weights[i]}$。假设我们只有 1 种商品，它的重量为 20，背包的容量为 60，那么它就应该放 3 个，在遍历时，就 0、1、2、3 地依次尝试。
+ 程序需要求解 ${n*W}$ 个状态，每一个状态需要的时间为 ${O（W/w_i）}$，总的复杂度为 ${O(nW*Σ(W/w_i))}$。
+ 我们再回顾 01 背包经典解法的核心代码

  ```js
  for(var i = 0 ; i < n ; i++){ 
     for(var j=0; j<=W; j++){ 
         f[i][j] = Math.max(f[i-1][j], f[i-1][j-weights[i]]+values[i]))
        }
     }
  }
  ```

+ 现在多了一个 k，就意味着多了一重循环

  ```js
  for(var i = 0 ; i < n ; i++){ 
     for(var j=0; j<=W; j++){ 
         for(var k = 0; k < j / weights[i]; k++){
            f[i][j] = Math.max(f[i-1][j], f[i-1][j-k*weights[i]]+k*values[i]))
          }
        }
     }
  }
  ```

  > 考虑硬币最少找零的问题，因为价值是递增，所以直接全部塞满，剩下的再参考上一行，但是这里价值不一定是递增的，所以需要多一层循环，来判断，超出 k 的个数变动中价值最高的那个 `f[i][j]`

+ javascript 的完整实现：

  ```js
  function completeKnapsack(weights, values, W){
      var f = [], n = weights.length;
      f[-1] = [] //初始化边界
      for(var i = 0; i <= W; i++){
          f[-1][i] = 0
      }
      for (var i = 0;i < n;i++){
          f[i] = new Array(W+1)
          for (var j = 0;j <= W;j++) {
              f[i][j] = 0;
              var bound = j / weights[i];
              for (var k = 0;k <= bound;k++) {
                  f[i][j] = Math.max(f[i][j], f[i - 1][j - k * weights[i]] + k * values[i]);
              }
          }
      }
      return f[n-1][W];
  }
  //物品个数n = 3，背包容量为W = 5，则背包可以装下的最大价值为40.
  var a = completeKnapsack([3,2,2],[5,10,20], 5) 
  console.log(a) //40
  ```

### 2.3 O(nW) 优化

+ 我们再进行优化，改变一下 f 思路，让 ${f(i,j)}$ 表示出在前 i 种物品中选取若干件物品放入容量为 j 的背包所得的最大价值。
+ 所以说，对于第 i 件物品有放或不放两种情况，而放的情况里又分为放 1 件、2 件、......${j/w_i}$ 件
+ 如果不放, 那么 ${f(i,j)=f(i-1,j)}$；
+ 如果放，那么当前背包中应该出现至少一件第 i 种物品，所以 f(i,j) 中至少应该出现一件第 i 种物品,即 ${f(i,j)=f(i,j-w_i)+v_i}$，为什么会是 ${f(i,j-w_i)+v_i}$？
+ 因为我们要把当前物品 i 放入包内，因为物品 i 可以无限使用，所以要用 ${f(i,j-w_i)}$；如果我们用的是 ${f(i-1,j-w_i)}$，${f(i-1,j-w_i)}$ 的意思是说，我们只有一件当前物品 i，所以我们在放入物品 i 的时候需要考虑到第 i-1 个物品的价值 ${f(i-1,j-w_i)}$；但是现在我们有无限件当前物品 i，我们不用再考虑第 i-1 个物品了，我们所要考虑的是在当前容量下是否再装入一个物品 i，而 ${(j-w_i)}$ 的意思是指要确保 ${f(i,j)}$ 至少有一件第 i 件物品，所以要预留 c(i) 的空间来存放一件第 i 种物品。总而言之，如果放当前物品 i 的话，它的状态就是它自己 "i"，而不是上一个 "i-1"。
+ 只要参考同一行就好了，因为在同一行容量是递增的，k 的可能数量也是逐步增加的
+ 所以说状态转移方程为：

  ![clipboard.png](https://segmentfault.com/img/bV1ZOF?w=1120&h=104)

+ 与 01 背包的相比，只是一点点不同，我们也不需要三重循环了

  ![clipboard.png](https://segmentfault.com/img/bV1ZOI?w=1086&h=112)

+ javascript 的完整实现：

  ```js
  function unboundedKnapsack(weights, values, W) {
      var f = [],
          n = weights.length;
      f[-1] = []; //初始化边界
      for (let i = 0; i <= W; i++) {
          f[-1][i] = 0;
      }
      for (let i = 0; i < n; i++) {
          f[i] = [];
          for (let j = 0; j <= W; j++) {
              if (j < weights[i]) {
                  f[i][j] = f[i - 1][j];
              } else {
                  f[i][j] = Math.max(f[i - 1][j], f[i][j - weights[i]] + values[i]);
              }
          }
          console.log(f[i].concat());//调试
      }
      return f[n - 1][W];
  }
  
  var a = unboundedKnapsack([3, 2, 2], [5, 10, 20], 5); //输出40
  console.log(a);
  var b = unboundedKnapsack([2, 3, 4, 7], [1, 3, 5, 9], 10); //输出12
  console.log(b);
  ```

+ 我们可以继续优化此算法，可以用一维数组写
+ 我们用 ${f(j)}$ 表示当前可用体积 j 的价值，我们可以得到和 01 背包一样的递推式：

  ![clipboard.png](https://segmentfault.com/img/bV1ZOL?w=964&h=132)

  ```js
  function unboundedKnapsack(weights, values, W) {
    var n = weights.length,
      f = new Array(W + 1).fill(0);
    for (var i = 0; i < n; ++i) {
      // 因为完全背包只需要参考同一行的，所以不需要从尾到头覆写
      for (j = weights[i]; j <= W; ++j) {
        f[j] = Math.max(f[j], f[j - weights[i]] + values[i]);
      }
    }
    console.log(f)//调试
    return f[W];
  }
  var a = unboundedKnapsack([3, 2, 2], [5, 10, 20], 5); //输出40
  console.log(a);
  var b = unboundedKnapsack([2, 3, 4, 7], [1, 3, 5, 9], 10); //输出12
  console.log(b);
  ```

## 多重背包问题

### 3.1 问题描述：

+ 有 ${n}$ 件物品和 ${1}$ 个容量为 W 的背包。每种物品最多有 numbers[i] 件可用，第 ${i}$ 件物品的重量为 ${weights[i]}$，价值为 ${values[i]}$，求解将哪些物品装入背包可使价值总和最大。

### 3.2 问题分析：

+ 多重背包就是一个进化版完全背包。在我们做完全背包的第一个版本中，就是将它转换成 01 背包，然后限制 k 的循环
+ 直接套用 01 背包的一维数组解法

  ```js
  function knapsack(weights, values, numbers,  W){
      var n = weights.length;
      var f= new Array(W+1).fill(0)
      for(var i = 0; i < n; i++) {
          for(var k=0; k<numbers[i]; k++)//其实就是把这类物品展开，调用numbers[i]次01背包代码  
           for(var j=W; j>=weights[i]; j--)//正常的01背包代码  
               f[j]=Math.max(f[j],f[j-weights[i]]+values[i]);  
      }
      return f[W];
  }
  var b = knapsack([2,3,1 ],[2,3,4],[1,4,1],6)
  console.log(b)
  ```

### 3.3 使用二进制优化

+ 其实说白了我们最朴素的多重背包做法是将有数量限制的相同物品看成多个不同的 0-1 背包。这样的时间复杂度为 ${O(W*Σn(i))}$, W 为空间容量 ，n(i) 为每种背包的数量限制。如果这样会超时，我们就得考虑更优的拆分方法，由于拆成 1 太多了，我们考虑拆成二进制数，对于 13 的数量，我们拆成 1，2，4，6（有个 6 是为了凑数）。 19 我们拆成 1，2，4，8，4 （最后的 4 也是为了凑和为 19）。经过这样的拆分我们可以组合出任意的小于等于 n(i) 的数目（二进制啊，必然可以）。j 极大程度缩减了等效为 0-1 背包时候的数量。 大概可以使时间复杂度缩减为 ${O(W*log(ΣN(i))}$；

  ```js
  定理：一个正整数n可以被分解成1,2,4,…,2^(k-1),n-2^k+1（k是满足n-2^k+1>0的最大整数）的形式，且1～n之内的所有整数均可以唯一表示成1,2,4,…,2^(k-1),n-2^k+1中某几个数的和的形式。
  
  证明如下：
  
  （1） 数列1,2,4,…,2^(k-1),n-2^k+1中所有元素的和为n，所以若干元素的和的范围为：[1, n]；
  
  （2）如果正整数t<= 2^k – 1,则t一定能用1,2,4,…,2^(k-1)中某几个数的和表示，这个很容易证明：我们把t的二进制表示写出来，很明显，t可以表示成n=a0*2^0+a1*2^1+…+ak*2^（k-1），其中ak=0或者1，表示t的第ak位二进制数为0或者1.
  
  （3）如果t>=2^k,设s=n-2^k+1，则t-s<=2^k-1，因而t-s可以表示成1,2,4,…,2^(k-1)中某几个数的和的形式，进而t可以表示成1,2,4,…,2^(k-1)，s中某几个数的和（加数中一定含有s）的形式。
  
  （证毕！）
  ```

  ```js
  function mKnapsack(weights, values, numbers, W) {
      var kind = 0; //新的物品种类
      var ws = []; //新的物品重量
      var vs = []; //新的物品价值
      var n = weights.length;
      /**
       * 二进制分解
       * 100=1+2+4+8+16+32+37，观察可以得出100以内任何一个数都可以由以上7个数选择组合得到，
       * 所以对物品数目就不是从0都100遍历，而是0，1，2，4，8，16，32，37遍历，时间大大优化。
       */
      for (let i = 0; i < n; i++) {
          var w = weights[i];
          var v = values[i];
          var num = numbers[i];
          for (let j = 1; ; j *= 2) {
              if (num >= j) {
                  ws[kind] = j * w;
                  vs[kind] = j * v;
                  num -= j;
                  kind++;
              } else {
                  ws[kind] = num * w;
                  vs[kind] = num * v;
                  kind++;
                  break;
              }
          }
      }
      //01背包解法
      var f = new Array(W + 1).fill(0);
      for (let i = 0; i < kind; i++) {
          for (let j = W; j >= ws[i]; j--) {
              f[j] = Math.max(f[j], f[j - ws[i]] + vs[i]);
          }
      }
      return f[W];
  }
  
  var b = mKnapsack([2,3,1 ],[2,3,4],[1,4,1],6)
  console.log(b) //9
  ```

## 混合背包问题

+ 不同的物品选择不同的背包即可

  ![1564155547916](/img/user/programming/basic/algorithm/dp/1564155547916.png)

## 二维费用背包问题

### 问题描述

+ 二维费用的背包问题是指：对于每件物品，具有两种不同的费用，选择这件物品必须同时付出这两种费用。对于每种费用都有一个可付出的最大值（背包容量）。
+ 问怎样选择物品可以得到最大的价值。设第 ` i ` 件物品所需的两种费用分别为 `Ci` 和 `Di`。两种费用可付出的最大值（也即两种背包容量）分别为 ` V ` 和 ` U`。物品的价值为 ` Wi`。

### 问题分析

+ 费用加了一维，只需状态也加一维即可。设 F[i, v, u] 表示前 i 件物品付出两种费用分别为 v 和 u 时可获得的最大价值。状态转移方程就是：

  ![1564155685943](/img/user/programming/basic/algorithm/dp/1564155685943.png)

### Fate

#### 题目

+ 最近 xhd 正在玩一款叫做 FATE 的游戏，为了得到极品装备，xhd 在不停的杀怪做任务。久而久之 xhd 开始对杀怪产生的厌恶感，但又不得不通过杀怪来升完这最后一级。现在的问题是，xhd 升掉最后一级还需 n 的经验值，xhd 还留有 m 的忍耐度，每杀一个怪 xhd 会得到相应的经验，并减掉相应的忍耐度。当忍耐度降到 0 或者 0 以下时，xhd 就不会玩这游戏。xhd 还说了他最多只杀 s 只怪。请问他能升掉这最后一级吗？
+ 输入数据有多组：
  + 对于每组数据第一行输入 n，m，v，s(0 < n,m,v,s < 100) 四个正整数。分别表示还需的经验值，保留的忍耐度，怪的种数和最多的杀怪数。
  + 接下来输入 v 行数据。每行数据输入两个正整数 a，b(0 < a,b < 20)；分别表示杀掉一只这种怪 xhd 会得到的经验值和会减掉的忍耐度。(每种怪都有无数个)
+ 输出升完这级还能保留的最大忍耐度，如果无法升完这级输出 -1。

  ```
  输入：
  10 10 1 10
  1 1
  10 10 1 9
  1 1
  9 10 2 10
  1 1
  2 2
  输出
  0
  -1
  1
  分析
  1.还需要10点经验，仍有10点忍耐，只有1种，可以杀10只怪，杀完10只怪，刚刚10点经验，消耗10点忍耐，升级，剩余0忍耐
  2.少了一只怪，无法升级
  3.杀4只两经验怪+一只1经验怪，剩余1点忍耐
  ```

#### 分析

+ 状态：经验
+ 费用：杀怪数和忍耐度
+ 二维费用完全背包问题，参考同一行，对比上一行
+ 状态转移方程：`dp[i][k][p]=Math.max(dp[i-1][k][p],dp[i-1][k-1][p-cost[i][1]])+cost[i][0]`
+ 初始化分析：要初始化三维比较困难，针对

  ```js
  var fate = function(exe,patience,costs,kill){
  
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
    console.log(dp)
    // 可选择怪的种类
    for (let i = 0; i < costs.length; i++) {
      // 杀怪数
      for (let k = 0; k <= kill; k++) {
        // 忍耐度
        for (let p = 0; p <= patience; p++) {
          if(p<costs[i][1]){
            // 忍耐度不够，直接参考上一种怪
            dp[i][k][p] = dp[i-1][k][p]
          }else{
            // 优先选择第i种怪，在p忍耐度，k杀怪数时的最优情况
            const priority = dp[i][k-1][p-costs[i][1]]+costs[i][0]
            dp[i][k][p]=Math.max(dp[i-1][k][p],priority)
          }
        }
      }
    }
    // 判断是否能升级
    return dp[costs.length-1][kill][patience]>=exe
  }
  
  let exe = 10, // 所需经验
      patience = 10, // 忍耐度
      costs = [[1,1]], // 每组怪物可获得的经验和消耗的忍耐度
      kill = 10 // 杀怪数
  console.log(fate(exe,patience,costs,kill))
  ```

### [879. 盈利计划](https://leetcode-cn.com/problems/profitable-schemes/)

### Vivo 正式批 2

+ 15 10
+ 3 4 5000
+ 4 6 7000
+ 5 8 8000
