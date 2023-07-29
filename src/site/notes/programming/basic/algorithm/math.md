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


# [264. 丑数 II](https://leetcode-cn.com/problems/ugly-number-ii/)

编写一个程序，找出第 `n` 个丑数。

丑数就是只包含质因数 `2, 3, 5` 的**正整数**。

```
输入: n = 10
输出: 12
解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。
```

## 分析


# [313. 超级丑数](https://leetcode-cn.com/problems/super-ugly-number/)



# 安全整数

## [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

### 题目

+ 给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

  ```
  示例 1:
  
  输入: 123
  输出: 321
   示例 2:
  
  输入: -123
  输出: -321
  示例 3:
  
  输入: 120
  输出: 21
  ```

### 当作字符串

```js
  var reverse = function(x) {
      var str = x.toString()
      var ret = 0
      if(x<0){
        	// 保留负号
          ret = "-" + str.slice(1).split("").reverse().join("") * 1
          if(ret < -2147483648) return 0 
          return ret
      }else{
          ret = str.split("").reverse().join("") * 1
          if(ret > 2147483647) return 0 
          return ret
      }
  }
  ```

### 数学位移

```java
  class Solution {
    public int reverse(int x) {
      int rev = 0;
      while (x != 0) {
        int pop = x % 10;
        x /= 10;
        if (rev > Integer.MAX_VALUE/10 || (rev == Integer.MAX_VALUE / 10 && pop > 7)) return 0;
        if (rev < Integer.MIN_VALUE/10 || (rev == Integer.MIN_VALUE / 10 && pop < -8)) return 0;
        rev = rev * 10 + pop;
      }
      return rev;
    }
  }
  ```

# 基本运算

## 加法

### [989. 数组形式的整数加法](https://leetcode-cn.com/problems/add-to-array-form-of-integer/)

#### 逐位相加

**思路**

+ 让我们逐位将数字加在一起。举一个例子，如果要计算 123 与 912 的和。我们顺次计算 3+2、2+1、1+9。任何时候，当加法的结果大于等于 10 ，我们要将进位的 1 加入下一位的计算中去，所以最终结果等于 1035。
+ 我们可以对以上的想法做一个小变化，让它**实现起来更容易** —— 我们将整个加数加入数组表示的数的最低位。
+ 继续之前的例子 123+912，我们把它表示成 [1, 2, 3+912]。然后，我们计算 3+912 = 915。5 留在当前这一位，将 910/10=91 以进位的形式加入下一位。
+ 然后，我们再重复这个过程，计算 [1, 2+91, 5]。我们得到 93，3 留在当前位，将 90/10=9 以进位的形式加入下一位。继而又得到 [1+9, 3, 5]，重复这个过程之后，最终得到结果 [1, 0, 3, 5]

**边际**

+ 如果是 [9,9] + 1，那么数组的位数就会不够，需要另外做判断，然后 unshift
+ 更好的处理时先反转数组，正向相加，那么最后如果有进位就正常赋值即可
+ 用 while 循环是更好的选择，只要 k>0，说明还没有相加完，继续一位位赋值即可

**优化**

+ 判断长短，用长的做基础即可

**实现**

```js
  输入：A = [1,2,0,0], K = 34
  输出：[1,2,3,4]
  解释：1200 + 34 = 1234
  ```

  ```js
  /**
   * @param {number[]} A
   * @param {number} K
   * @return {number[]}
   */
  const addToArrayForm = function(A, K) {
    const len = A.length
    const arr = [...A].reverse()
    const ret = []
    let cur = K
  
    let i = 0
    // A比K长 || K比A长
    while( i<len ||  cur > 0 ){
      // undefined 是 NaN
      cur += arr[i] || 0
      ret.push(cur%10)
      cur = Math.floor(cur/10)
      i++
    }
  
    return ret.reverse()
  };
  ```

### 总结

+ 2，67 中，两个都是字符串，使用了双指针
+ 989 中，只有一个是字符串，另一个是数字，而且总是在安全整数的范围内，所以单指针就可以了
+ 使用双指针就不需要担心超出安全整数的范围

## 乘法

### [43. 字符串相乘](https://leetcode-cn.com/problems/multiply-strings/)

**示例**

```
  输入: num1 = "2", num2 = "3"
  输出: "6"
```

#### 逐位相乘

+ 不存在数值操作，全部都要转换成字符串

  ```js
  /**
   * @param {string} num1
   * @param {string} num2
   * @return {string}
   */
  var multiply = function(num1, num2) {
    if(num1==='0'||num2==='0')return '0'
    const lenA = num1.length
    const lenB = num2.length
    num2 = num2.split('').reverse()
    num1 = num1.split('').reverse()
    const auxArr = []
    let remain = 0
    // 按位分别相乘
    for (let i = 0; i < lenB; i++) {
      // 直接相乘也有可能溢出
      // auxArr.push(num1*1*num2[i])
      const mul = num2[i]
      let j = 0
      let result = []
      let rest=0
      while(j<num1.length){
        sum = num1[j]*mul + rest
        result.push(sum%10)
        rest = Math.floor(sum/10)
        j++
      }
      if(rest>0) result.push(rest)
      // 用0补位 auxArr，第一个元素 乘以 1，第二个元素乘以10
      const zero = new Array(i).fill(0).join('')
      auxArr.push(zero+result.join(''))
    }
    console.log(auxArr)
    // 任何数与个位数相乘不会超过2个数量级，所以最后一个肯定是最长的
    // auxArr.length == lenB
    const maxLen = auxArr[lenB-1].length
    let i = 0
    let rest = 0
    let ret = []
    // 按位相加
    while(i<maxLen){
      if(auxArr[0][i]==undefined) auxArr.shift()
      let sum = auxArr.reduce((acc,num) => {
        return acc + (num[i]*1||0)
      },rest)
      ret.push(sum%10)
      rest = Math.floor(sum/10)
      i++
    }
    if(rest>0) ret.push(rest)
    return ret.reverse().join('')
  };
  
  ```

#### 竖式乘法

+ 长度为 n 和 m 相乘，则长度肯定小于 m + n + 1; 将每次相乘的结果放入数组中，在下次计算的时候加上当前位上的值.

  ```js

  
  console.log(multiply('55','55'))
  ```

+ 快速傅里叶
+ <https://www.bilibili.com/video/av45626580?from=search&seid=12783203490351621424>

## 开根

### 牛顿法

![69. x 的平方根](../leetcode/69.%20x%20的平方根.md)

## 除法

+ <https://blog.csdn.net/Yichuan_Sun/article/details/79180638>
+ <https://blog.csdn.net/LJWLgl/article/details/52799179>

### 模除

+ 超大数模除

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
