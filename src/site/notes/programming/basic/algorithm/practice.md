---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/practice/"}
---


# 结构性算法

## 二进制

### 分析

+ 利用了栈的逆序性质
+ 第一次模除的结果是进制的最后一位，余下的
+ 倒数第二位，就是除以 2 之后再次模除，乘以二就是左移一位，除以二就是右移一位，之后再次判断余下的是什么
+ 每次都除以 radix，就是模拟 a~n~ * radix^n-1^
+ 最后再倒过来就是结果

### 从十进制到二进制

```js
  function divideBy2(decNumber) {
  
    const remainderStack = new Stack()
    let remainder,
        binaryString = ''
  
    while (decNumber > 0) { 
      // 取余数,加上向下取整忽略非整数的部分
      remainder = Math.floor(decNumber % 2)
      // 保存余数
      remainderStack.push(remainder)
      // 除以2向下取整
      decNumber = decNumber>>>1
    }
  
    while (!remainderStack.isEmpty()) { 
      binaryString += remainderStack.pop().toString();
    }
  
    return binaryString;
  }
  
  console.log(divideBy2(12.2)) // .2 会在第一轮循环之后被忽略
```

### 进制转换算法

- ```js
  function divideByRadix(decNumber,radix) {
  
    var remainderStack = new Stack(),
      remainder,
      radixString = '',
      digits = {
        [2]:'01',
        [4]:'0123',
        [8]:'01234567',
        [16]:'0123456789ABCDEF',
      }
  
    while (decNumber > 0) { //{1} 
      remainder = Math.floor(decNumber % radix); //{2} 取余数,加上向下取整忽略非整数的部分
      remainderStack.push(remainder); //{3} 保存余数
      decNumber = Math.floor(decNumber / radix); //{4} 除以base向下取整
    }
  
    while (!remainderStack.isEmpty()) { //{5} 
      radixString += digits[radix][remainderStack.pop()]; // 根据余数取对应的字符
    }
  
    return radixString;
  }
  
  console.log(divideByRadix(15,16)) // F
  ```

### [461. 汉明距离](https://leetcode-cn.com/problems/hamming-distance/)

#### 题目

+ 两个整数之间的 [汉明距离](https://baike.baidu.com/item/汉明距离) 指的是这两个数字对应二进制位不同的位置的数目。
+ 给出两个整数 `x` 和 `y`，计算它们之间的汉明距离。

  ```js
  输入: x = 1, y = 4
  
  输出: 2
  
  解释:
  1   (0 0 0 1)
  4   (0 1 0 0)
         ↑   ↑
  
  上面的箭头指出了对应二进制位不同的位置。
  ```

#### 归并式比对

+ ```js
  var hammingDistance = function(x, y) {
    let x0 = divideBy2(x),
          y0 = divideBy2(y)
    // 找出较长的，补零
    if(x0.length>y0.length){
      // y0 补零
      y0 = y0.padStart(x0.length,'0')
    }else{
      x0 = x0.padStart(y0.length,'0')
    }
    let res = 0
    for (let i = 0; i < x0.length; i++) {
      if(x0[i]!=y0[i]) res++
    }
    return res
  }
  
  function divideBy2(decNumber) {
  
    const remainderStack = []
    let remainder,
        binaryString = ''
  
    while (decNumber > 0) { 
      // 取余数,加上向下取整忽略非整数的部分
      remainder = Math.floor(decNumber % 2)
      // 保存余数
      remainderStack.push(remainder)
      // 除以2向下取整
      decNumber = decNumber>>>1
    }
  
    while (remainderStack.length>0) { 
      binaryString += remainderStack.pop().toString();
    }
  
    return binaryString;
  }
  ```

#### 利用异或的性质

+ ```js
  var hammingDistance = function(x, y) {
    const bit = x ^ y
    const bit0 = divideBy2(bit)
    let res = 0
    for (let i = 0; i < bit0.length; i++) {
      if(bit0[i]==='1') res++
    }
    return res
  }
  ```

### [483. 最小好进制](https://leetcode-cn.com/problems/smallest-good-base/)

+ ![1570422713588](/img/user/programming/basic/algorithm/practice/1570422713588.png)
+ 19 13
+ 16

# 递归

+ https://leetcode-cn.com/problems/count-and-say/submissions/

  ```js
  var countAndSay = function (n) {
  
      if(n==1) return '1'
      str = countAndSay(n-1)
      // 检索字符串返回每个数字的个数
      str = Array.from(str)
      const count = []
      let obj = {}
      str.forEach(function (val, i, arr) {
        //是否和上一个相同？
        if (val == obj.val) {
          // 相同，加一
          obj.count++
        } else {
          // 不相同
          // 在这个位置拼串
          obj = {}
          obj.val = val
          obj.count = 1
          count.push(obj)
        }
      })
      // 根据数组翻译出新的字符串
      let say = count.reduce(function (acc, obj, i, arr) {
        return acc + obj.count + obj.val
      }, '')
      console.log(say)
      return say
  }
  countAndSay(5) 
  ```

- 优化：搞定一组拼一次串，就不用保存下来了

  ```js
  var fibonacci = []; //{1} 
  fibonacci[1] = 1; //{2} 
  fibonacci[2] = 2; //{3} 
  
  for(var i = 3; i < 20; i++){ 
    fibonacci[i] = fibonacci[i-1] + fibonacci[i-2]; ////{4} 
  } 
  ```

- 不存在堆栈溢出，空间复杂度保存所有的结果，缓存，时间复杂度
- https://leetcode-cn.com/problems/climbing-stairs/solution/
- 暴力法
- 记忆化递归
- 尾递归
- 动态规划

# 聚合

## 斐波那契数列

## 汉诺塔

+ 有三根相邻的柱子，标号为 A,B,C，A 柱子上从下到上按金字塔状叠放着 n 个不同大小的圆盘，要把所有盘子一个一个移动到柱子 B 上，并且每次移动同一根柱子上都不能出现大盘子在小盘子上方，请问至少需要多少次移动，设移动次数为 H(n）。

### 动态规划

+ **最优化分析：**假设有 n 个盘子，(n>1)，把 n-1 个盘子看作一个整体，每次移动这个整体需要 T[n-1] 次操作，对于第 n 个盘子而言，只有一种可能方案：
  + 先把 n-1 个盘子，移动到 c 柱子上 (起辅助作用的柱子，非目标柱子)，然后第 n 个盘子移动到 b 柱子上 (目标柱子)，最后 n-1 个盘子移动到 c 柱子上。所以：
    + `T[n] = T[n-1] + 1 + T[n-1] = 2T[n-1]+1`
+ **初始化分析：**只有一个盘子的时候只需要一步，`T[0] = 1`
+ **返回值分析：**T[n-1]
+ https://baike.baidu.com/item/%E6%B1%89%E8%AF%BA%E5%A1%94/3468295?fr=aladdin#1_1
+ https://www.cnblogs.com/antineutrino/p/3334540.html

# 错题

+ ```js
  function fizzBuzz(num) {
    var ret = ''
    if(!num || typeof num !== 'number'){
      return false
    }else if(typeof num === 'number'){
      if(num % 3 ===0) {ret += 'fizz'}
      if(num % 5 ===0) {ret += 'buzz'}
      return ret?ret:num // 是number但是不是fizz buzz 也就是 字符串为空的时候应该返回num
    }
  }
  ```
+ https://www.nowcoder.com/profile/283709/codeBookDetail?submissionId=12713092
+ https://www.nowcoder.com/profile/7966329/codeBookDetail?submissionId=12702230
+ https://www.nowcoder.com/profile/9065466/codeBookDetail?submissionId=12700157
+ https://www.nowcoder.com/profile/8502945/codeBookDetail?submissionId=12698477

# 优化

## 算法优化

- 找质数，模根号下 i 就可以了，因为之后的因数前面已经出现过了，想想 36

### 二进制优化
