---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/math/"}
---


5/3

[加、减、乘、除、方、根及指对数的英文表达 - 知乎](https://zhuanlan.zhihu.com/p/136367900)

# 最大公约数

gcd

## 辗转相 (模) 除法

```js
function getGreatestCommonDivisor(a,b){
  if(b===0) return a
  return getGreatestCommonDivisor(b,a%b)
}
```

## 更相减损法

```js
function getGreatestCommonDivisor(a,b){
  if(a===0) return b*divisor
  if(b===0) return a*divisor
  
  let divisor = 1
  let difference = 0
  // 简化
  while(a%2===0 && b%2===0){
    a /= 2
    b /=2
    divisor *=2
  }
  // 更相减损
  let max = Math.max(a,b)
  let min = Math.min(a,b)

  while(min !== difference){
    difference = max - min
    max = Math.max(min,difference)
    min = Math.min(min,difference)
  }
  return min*divisor
}
```

## Stein 算法

```js
function getGreatestCommonDivisor(a,b){
  if(a===0) return b*divisor
  if(b===0) return a*divisor
  
  let divisor = 1
  let difference = 0
  // 简化
  while(a%2===0&&b%2===0){
    a /=2
    b /=2
    divisor *=2
  }  
  // 进一步简化
  while(a%2===0||b%2===0){
    a%2===0?a /=2:a
    b%2===0?b /=2:b
  }
	// 更相减损
  let max = Math.max(a,b)
  let min = Math.min(a,b)

  while(min !== difference){
    difference = max - min
    max = Math.max(min,difference)
    min = Math.min(min,difference)
  }
  return min * divisor
}
```

再素数特别大的时候，Stein 算法优于辗转相模

## 求多个数的 Gcd

```js
    let min = result[0]

    // 应该求的是最大公因数
    for (let i = 1; i < len; i++) {
        min = getGreatestCommonDivisor(result[i], min);
    }
```

# 最小公倍数

least commom multiple

lcm

基本思想是采用将两个数相乘，然后除以它们的最大公约数

```js
function getMinCommonMultiple(a, b){
  return a * b / getMaxCommonDivisor(a, b);
}
```

# 基本运算

## 加法

## 乘法

## 开根

### 牛顿法

![69. x 的平方根](../leetcode/69.%20x%20的平方根.md)

## 除法

+ <https://blog.csdn.net/Yichuan_Sun/article/details/79180638>
+ <https://blog.csdn.net/LJWLgl/article/details/52799179>

## 模除

超大数模除

  ```js
  var getCommon = function(a,b){
    if(b==0) return a
    return getCommon(b,a%b)
  }
  let a=4,b=10
  console.log(getCommon(a,b))
  // 模拟减法
  var minus = function(a,b){
  
  }
  console.log(minus(a,b))
  ```

+ <https://blog.csdn.net/tianhaobing/article/details/76022088>

# 统计学

## 百分位数

percentile 函数

### 基本方法

如何计算第 `p` 百分位数：

1. 将 `n` 个变量值从小到大排列
2. 计算指数 `i=np%`：
   1. 若 ` i` 不是整数，将 `i ` 向上取整。大于 `i` 的毗邻整数即为第 `p` 百分位数的位置
   2. 若 `i` 是整数，则第 `p` 百分位数是第 `i` 项与第 `(i+l)` 项数据的平均值

**示例**

```
如求数列S：3、2、5、4、6、7 的80分位数

第1步排序，2、3、4、5、6、7
第2步 i = 6*0.8 = 4.2
第3步 i=4.2不是整数，向上取整 5，那么S[5] = 6 就是该数列的80分位数
```

```
求数量L: 2、4、3、5、6 的80分数数

第1步排序，2、3、4、5、6
第2步 i = 5*0.8 = 4
第3步 i=4是整数, (L[i] + L[i + 1] ) / 2, 即 (5 + 6 ) / 2 = 5.5 就是该数列的80分位数
```

### 经验方案

方法是 [SPSS](https://link.juejin.cn/?target=https%3A%2F%2Fbaike.baidu.com%2Fitem%2Fspss%2F2351375%3Ffr%3Daladdin) 所用方法，也是 [SAS](https://link.juejin.cn/?target=https%3A%2F%2Fbaike.baidu.com%2Fitem%2FSAS%2F891078) 所用方法之一，excel 的 PERCENTILE.EXC 函数也用的这个方法

1. 将 `n` 个变量值从小到大排列，`X(j)` 表示此数列中第 `j` 个数
2. 计算指数，设 `(n+1)P%=j+g`，`j` 为整数部分，`g` 为小数部分
   1. 当 `g=0` 时：P 百分位数 `\= X(j)`
   2. 当 `g≠0` 时：P 百分位数 `\= g*X(j+1)+(1-g)*X(j) = X(j)+g*[X(j+1)-X(j)]`

```
求数列X: 2、4、3、5 的80分位数

第1步排序，2、3、4、5
第2步 i = (4+1)*0.8 = 4, 整数部分j为4，小数部分g为0
第3步 g=0, 那么80分位数=X(j)=X(4) = 5
```

```
如求数列X：3、2、5、4、6、7 的80分位数

第1步排序：2、3、4、5、6、7
第2步：i = (6+1) * 0.8 =  5 + 0.6 整数部分j为5，小数部分g为0.6
第3步g=0.6不为0，那么80分位数 = 0.6*X(5+1)+(1-0.6)*X(5) = 0.6*7+0.4*6= 4.2+2.4 = 6.6
=X(5)+0.6*[X(6) - X(5)] = 6+0.6*[7-6] = 6.6
```

### 结合实践

理论上的分位计算应该是一个精准的值，比如 80 分位，表示数据排序后，80% 位置上的一个确切值。

但实际上在大数据计算上，全部数据排序是非常耗时的，因此可分为两种方案：

1. 分位精准计算方案
2. 近似计算方案

进阶参考：http://luohuagu.com/2021/07/29/percentile/

# 算法几何

## 线段相交

+ <https://segmentfault.com/a/1190000004457595>

# 进制算法

<https://www.nowcoder.com/questionTerminal/50e67995c0894c4abc591b917061f21c?toCommentId=392498>

<http://blog.sina.com.cn/s/blog_6d932f2a0101jgyz.html>

![进制转换算法](../../font-end/primitive/es/es-number.md#进制转换算法)
