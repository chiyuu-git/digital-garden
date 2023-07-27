---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/practice/"}
---


# 字符串题目

## 正则

### 删除最后一个字符

+ ```js
  var string = "2017-06-26";
  var reg = /\w$/
  
  console.log(string.replace(reg,""))
  ```
+ 或者使用两次 slice 方法

### 交换字符串的两个单词

- 下面的例子演示了如何交换一个字符串中两个单词的位置，这个脚本使用 $1 和 $2 代替替换文本。

  ```js
  var re = /(\w+)\s(\w+)/;
  var str = "John Smith";
  var newstr = str.replace(re, "$2, $1");
  // Smith, John
  console.log(newstr);
  ```

+ 格式化 123，456，789.00 的数值

### 大写字母位移

- 把一个字符串的大写字母放到字符串的后面，各个字符的相对位置不变，且不能申请额外的空间。
- 按照 Unicode 编码，找出所有大写字母，然后移到最后即可。
- 注意：不能申请额外的空间，常数级的空间就 OK

  ```js
  function moveChar (str){
    // [A-Z] 65-90
  
    let len =  str.length
    let upper = ''
    for (let i = 0; i < len;) {
      if(str.charCodeAt(i)>=65 && str.charCodeAt(i)<=90){
        // 是大写字母，移动到最后
        upper += str[i]
        // 如何删除一个字符？
        let tempA = str.slice(0,i)
        let tempB = str.slice(i+1)
        str = tempA + tempB
      }else{
        i++
      }
    }
    return str+upper
  }
  
  console.log(moveChar('AkleBiCeilD'))
  ```

- ```js
  function moveChar (str){
    // [A-Z] 65-90
    let upper = ''
    let lower = str.replace(/([A-Z])/g,function(match,p,offset,string){
      // 操作str，在最后拼上即可
      // string += p
      // 解决死循环的问题，不能直接加在原本的str，新建一个保存即可
      upper += p
      // 返回值作为匹配的字符的替换值，相当于是删除了匹配字符
      return ""
    })
  
    // replace 全局替换完毕
    return lower + upper
  }
  
  console.log(moveChar('AkleBiCeilD'))
  ```

## 二进制

### 二进制求和

+ ```js
  var addBinary = function(a, b) {
    // 先转化为数字，用Math方法搞定，转转换为二进制字符串
    let decimalA = parseInt(a,2)
    let decimalB = parseInt(b,2)
    let decimalResult = decimalA + decimalB
    console.log(decimalA,decimalB,decimalResult)
    return decimalResult.toString(2)
  };
  console.log(addBinary('11','1')); // 100
  // 很容易溢出
  ```
+ 对齐之后二进制加法，直接操作字符串即可，由于 JS 的字符串索引属性是只读的，要计算出结果只能拼串，所以先把二进制倒序，再进行相加，最后还要再倒序 // 太耗空间了
+ 既然倒序可以相加，那么不到序也是可以做到的，因为 i--， // 其实不行
+ 因为无法清除 result 的首位是进位导致的还是相加导致，所以最后还是要给一个标志来做

  ```js
  var addBinary = function(a, b) {
    let aLen = a.length
    let bLen = b.length
    //给较短的补零
    if(aLen<bLen){
      a =  new Array(bLen-aLen).fill('0').join('') + a
    }else(
      b =  new Array(aLen-bLen).fill('0').join('') + b
    )
    let len = a.length
    let result = ''
    let isAdd = false
    //循环相加
    for (let i = len-1; i >= 0 ; i--) {
      // 转换成数字相加
      let sum = a[i]*1 + b[i]*1
      if(isAdd){
        // 1+0 1+1 1+2 , 只需要考虑1,因为上一次的相加进位而造成的
        switch(''+sum){
          case '1': 
            result = '10' + result.slice(1)
            isAdd = true
            break
          case '2':
            result = '11' + result.slice(1)
            isAdd = true
            break
          case '0':
            isAdd = false
            break
        }
      }else{
        // 不是一的话，没有进位，此时result比进度少一位，直接相加即可
        switch(''+sum){
          case '1': 
            result = '1' + result
            isAdd = false
            break
          case '2':
            result = '10' + result
            isAdd = true
            break
          case '0':
            result = '0'  + result
            isAdd = false
            break
        }
      }
    }
    return result
  };
  ```

## 结构性

### leetCode6.Z 字形变换

#### 题目

+ 将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

  比如输入字符串为 "LEETCODEISHIRING" 行数为 3 时，排列如下：

  ```
  L   C   I   R
  E T O E S I I G
  E   D   H   N
  ```

+ 之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如："LCIRETOESIIGEDHN"。
+ 请你实现这个将字符串进行指定行数变换的函数

#### 实现

+ ```js
  /**
   * @param {string} s
   * @param {number} numRows
   * @return {string}
   */
  var convert = function(s, numRows) {
    if(numRows===1) return s
    let i = 0 , 
        j = 0
    let flag = 'down'
    let M = []
    for (let i = 0; i < numRows; i++) {
      M[i] = []
    }
    for (let count = 0; count < s.length; count++) {
      M[i][j] = s[count]
      switch(flag){
        case 'down':
          i++
          if(i===numRows){
            i=numRows-2
            j++
            flag='up' 
          }
          break
        case 'up':
          i--
          j++
          if(i===-1){
            i=1
            j--
            flag='down'
          }
          break
      }
    }
    return M.reduce(function(acc,val,i,arr){
      return acc + M[i].join('')
    },'')
  };
  // console.log(convert("LEETCODEISHIRING",3));
  console.log(convert('AB',1));
  ```
+ 优化，根本没必要使用数组的形式，直接一行行的字符串拼接即可

  ```js
  var convert = function(s, numRows) {
    if(numRows===1) return s
    let i = 0 , 
        j = 0
    let flag = 'down'
    let M = new Array(numRows).fill('')
  
    for (let count = 0; count < s.length; count++) {
      M[i] += s[count]
      switch(flag){
        case 'down':
          i++
          if(i===numRows){
            i=numRows-2
            flag='up' 
          }
          break
        case 'up':
          i--
          if(i===-1){
            i=1
            flag='down'
          }
          break
      }
    }
    return M.reduce(function(acc,val,i,arr){
      return acc + M[i]
    },'')
  };
  ```

+ 方法二：找规律 https://leetcode-cn.com/problems/zigzag-conversion/solution/

### [205. 同构字符串](https://leetcode-cn.com/problems/isomorphic-strings/)

#### 题目

+ 给定两个字符串 s 和 t，判断它们是否是同构的。
+ 如果 s 中的字符可以被替换得到 t ，那么这两个字符串是同构的。
+ 所有出现的字符都必须用另一个字符替换，同时保留字符的顺序。两个字符不能映射到同一个字符上，但字符可以映射自己本身。

  ```js
  输入: s = "egg", t = "add"
  输出: true
  
  输入: s = "paper", t = "title"
  输出: true
  ```

#### 哈希映射

+ 用数组去重的思路，给第一次出现的字母一个编号，用数字编号来表示该字符串的结构，最后比较该数字串

  ```js
  var isIsomorphic = function(s, t) {
    const _s = translate(s)
    const _t = translate(t)
    return _s === _t
    function translate(str){
      const map = {}
      let counter = 0
      let res = ''
      for (let i = 0; i < str.length; i++) {
        if(map[str[i]]===undefined) map[str[i]] = counter++
        res += map[str[i]]
      }
      return res
    }
  }
  ```

+ 在优化一下就是一个循环同时进行判断，一旦不相同马上返回

#### 利用 indexOf()

+ 对比两个字符串对应位置的字符在字符串内第一次出现的位置。

  ```js
  const isIsomorphic = (s, t) => {
    if (s === t || s === '') return true
    for (let i = 0; i < s.length - 1; i++) {
      if (s.indexOf(s[i], i+1) !== t.indexOf(t[i], i+1)) return false
    }
    return true
  }
  ```

## 子串、子序列

### leetCode916.单词子集

+ ```js
  /**
   * @param {string[]} A
   * @param {string[]} B
   * @return {string[]}
   */
  
  var wordSubsets = function(A, B) {
    let AMap = [],
        BMap = {},
        BTemp = {},
        res = []
    for (let i = 0; i < B.length; i++) {
      // 计算B中每个元素，每个字母出现的最大次数
      for (let j = 0; j < B[i].length; j++) {
        if(BTemp[B[i][j]]===undefined){
          BTemp[B[i][j]] = 1
        }else{
          BTemp[B[i][j]]++
        }
      }
      // 判断刚刚遍历完的单词，是否在BMap中？比较大小：推入
      for (const key of Object.keys(BTemp)) {
        if(BMap[key]){
          BMap[key] = Math.max(BMap[key],BTemp[key])
        }else{
          BMap[key] = BTemp[key]
        }
      }
      BTemp = {}
    }
    // 遍历得出A中每一个单词的字母词频对象
    for (let m = 0; m < A.length; m++) {
      AMap[m] = {}
      for (let n = 0; n < A[m].length; n++) {
        if(AMap[m][A[m][n]]===undefined){
          AMap[m][A[m][n]] = 1
        }else{
          AMap[m][A[m][n]]++
        }
      }
    }
    
    // 判断BMap 是否是 AMap[i] 的子集
    for (let i = 0; i < A.length; i++) {
      let flag = true
      for (const key of Object.keys(BMap)) {
        if(!AMap[i][key] ||  BMap[key] > AMap[i][key]){
          // AMap[i] 中不包含该字母，或者 频次小于 BMap , 不符合条件
          flag = false
        }
      }
      if(flag) res.push(A[i])
    }
    return res
  };
  wordSubsets(["amazon","apple","facebook","google","leetcode"],["e","o"])
  ```

## 循环同构串

最小表示法，超出面试范围，估计是 ACM 比赛里玩的：https://oi-wiki.org/string/minimal-string/

# 链表

## [19. 删除链表的倒数第N个节点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

### 题目

+ 给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

  ```
  示例：
  
  给定一个链表: 1->2->3->4->5, 和 n = 2.
  
  当删除了倒数第二个节点后，链表变为 1->2->3->5.
  ```

### 分析

+ 第一次扫描，获取长度，第二次扫描删除，删除倒数，等于删除正数第 Len - n + 1 个

  ```js
  var removeNthFromEnd = function(head, n) {
    let len = 0
    let i = head
    while(i!==null){
      i = i.next
      len++
    }
    
    let current = head,
        prev = null
    let index = 1,
    // 删除倒数第n个，相当于是删除 len - n + 1 个
        target = len - n + 1
    while(index<target){
      prev = current
      current = current.next
      index++
    }
    // 出循环 index === target
    if(target===1){
      // 移除头节点
      head = head.next
    }else{
      prev.next = current.next
      // 函数结束后已经没有指针指向current了，会被自动回收
    }
    return head
  }
  let head = {val:1,next:{val:2,next:null}}
  console.log(removeNthFromEnd(head,1))
  ```

### 使用哑结点优化

+ 哑结点消除了对头节点的讨论，在链表归并中也有相同的应用

  ```js
  function ListNode(val){
    this.val = val
    this.next = null
  }
  var removeNthFromEnd = function(head, n) {
    const dummyHead = new ListNode(0)
    dummyHead.next = head
    let len = 0
    let i = head
    while(i!==null){
      i = i.next
      len++
    }
  
    let current = head,
        prev = dummyHead // prev = null
    let index = 1,
        // 删除倒数第二个，相当于是删除 len - n + 1 个
        target = len - n + 1
    while(index<target){
      prev = current
      current = current.next
      index++
    }
  
    // 出循环 index === target
    prev.next = current.next
    return dummyHead.next
  }
  let head = {val:1,next:{val:2,next:null}}
  console.log(removeNthFromEnd(head,1))
  ```

### 间距指针

+ 详见双指针

# 回文

## 基本

### 判断回文串 & [9. 回文数](https://leetcode-cn.com/problems/palindrome-number/)

#### 题目

- 判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

  ```
  示例 1:
  
  输入: 121
  输出: true
  示例 2:
  
  输入: -121
  输出: false
  解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
  ```

#### 反转

- 转化成字符串反转

  ```js
  var isPalindrome = function(x) {
    const str = x + ''
    return str.split("").reverse().join("") === str
  }
  ```

**临界情况**

- 如果是直接反转数字本身，有可能造成溢出，需要加一层判断

**复杂度分析**

+ 时间复杂度 O(3n)，leetCode 356 ms

#### 反转一半

+ ```js
  var isPalindrome = function(x) {
    const str = x+''
    // 反转一半，无法搞定小于1的情况
    if(str.length<=1) return true
    const half = str.length>>>1
    const partOne = str.slice(0,half)
    const partTwo = str.slice(-half).split('').reverse().join('')
    return partOne === partTwo
  }
  ```

**复杂度**

+ 是全部反转其实差不多

#### 头尾比对

+ ```js
  var isPalindrome = function(x) {
    const str = x+""
    const len =str.length
    for(let i=0;i<len>>>1;i++){
      if(str[i] !== str[len-1-i]) return false
    }
    return true
  }
  ```

- ```js
  var isPalindrome = function(x) {
    const str = x+""
    const arr = [...str]
    const half = arr.length>>>1
    // 不可以在内部length，因为一边 shift和pop，length一直在改变
    for(let i=0;i<half;i++){
        if(arr.shift()!==arr.pop()) return false
    }
    return arr.length<=1
  }
  
  let x = 1001
  console.log(isPalindrome(x))
  ```
- 性能最好，空间换时间

#### 数学法

- 将数字本身反转，然后将反转后的数字与原始数字进行比较，如果它们是相同的，那么这个数字就是回文。 但是，如果反转后的数字大于 \text{int.MAX}int.MAX，我们将遇到整数溢出问题。
- 为了避免数字反转可能导致的溢出问题，为什么不考虑只反转 int 数字的一半？毕竟，如果该数字是回文，其后半部分反转后应该与原始数字的前半部分相同。
- 首先，我们应该处理一些临界情况。所有负数都不可能是回文，例如：-123 不是回文，因为 `-` 不等于 `3`。所以我们可以对所有负数返回 false。
- 现在，让我们来考虑如何反转后半部分的数字。 对于数字 1221，如果执行 1221 % 10，我们将得到最后一位数字 1，要得到倒数第二位数字，我们可以先通过除以 10 把最后一位数字从 1221 中移除，1221 / 10 = 122，再求出上一步结果除以 10 的余数，122 % 10 = 2，就可以得到倒数第二位数字。如果我们把最后一位数字乘以 10，再加上倒数第二位数字，1 * 10 + 2 = 12，就得到了我们想要的反转后的数字。如果继续这个过程，我们将得到更多位数的反转数字。
- 现在的问题是，**我们如何知道反转数字的位数已经达到原始数字位数的一半**？
- 我们将原始数字除以 10，然后给反转后的数字乘上 10，所以，当原始数字小于反转后的数字时，就意味着我们已经处理了一半位数的数字。

  ```js
  var isPalindrome = function(x) {
    // 特殊情况：
    // 如上所述，当 x < 0 时，x 不是回文数。
    // 同样地，如果数字的最后一位是 0，为了使该数字为回文，
    // 则其第一位数字也应该是 0
    // 只有 0 满足这一属性
    if(x < 0 || (x % 10 === 0 && x !== 0)) {
      return false;
    }
    var reverted = 0
    while(x>reverted){
      reverted = reverted*10 + x%10
      x = parseInt(x/10)
    }
    // 当数字长度为奇数时，我们可以通过 reverted/10 去除处于中位的数字。
    // 例如，当输入为 12321 时，在 while 循环的末尾我们可以得到 x = 12，reverted = 123，
    // 由于处于中位的数字不影响回文（它总是与自己相等），所以我们可以简单地将其去除。
    return x === reverted || x === Math.floor(reverted/10);
  };
  ```

### 构造回文 & [409. 最长回文串](https://leetcode-cn.com/problems/longest-palindrome/)

#### 题目

+ 给定一个包含大写字母和小写字母的字符串，找到通过这些字母构造成的最长的回文串。
+ 在构造过程中，请注意区分大小写。比如 `"Aa"` 不能当做一个回文字符串。

  ```
  输入:
  "abccccdd"
  输出:
  7
  解释:
  我们可以构造的最长的回文串是"dccaccd", 它的长度是 7。
  ```

#### 分析

+ 回文串是一个正读和反读都一样的字符串。对一个左边的字符 i 右边一定会有一个对称 i。比如 'abcba'， 'aa'，'bb' 这几个回文串。其中第一个有点特殊，中间的 c 是唯一的。
+ 对于每个字母，假设它出现了 v 次。我们可以让 v // 2 * 2 个字母左右对称。例如，对于字符串 'aaaaa'，其中 'aaaa' 是左右对称，其长度为 5 // 2 * 2 = 4。
+ 最后，如果有任何一个满足 v % 2 == 1 的 v，那么这个字符就可能是回文串中唯一的那个中心。针对这种情况，我们需要判断 v % 2 == 1 && ans % 2 == 0，后面的判断主要是为了防止重复计算。

#### 实现

+ ```js
  var longestPalindrome = function(s) {
    const len = s.length
    const map = {}
    let ans = 0
    for (let i = 0; i < len; i++) {
      if(map[s[i]]===undefined){
        map[s[i]] = 1
      }else{
        map[s[i]]++
      }
    }
    for (const count of Object.values(map)) {
      ans += (count>>>1)*2
      // 中心只能选择一次，之后ans % 2 !== 0
      if (count % 2 == 1 && ans % 2 == 0) ans++
    }
    return ans
  }
  
  console.log(longestPalindrome('aabbbccc'))
  ```

**复杂度分析**

+ 时间复杂度： O(N) ，N 为 s 的长度，至少遍历每个字符一次。
+ 空间复杂度： O(1) 我们需要开辟额外空间来计数，其大小为 s 中字母的个数，我们都知道字母最多为 26 个

## 实战

### [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

#### 问题

- 给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。

**示例 1：**

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

**示例 2：**

```
输入: "cbbd"
输出: "bb"
```

#### 暴力法

- 遍历所有的子串，然后判断子串是否是回文串

  ```js
  /**
   * @param {string} s
   * @return {string}
   */
  var longestPalindrome = function(s) {
    let res = '',
     		temp = '',
    		tempRev = ''
    for (let i = 0; i < s.length; i++) {
      temp = s[i]
      for (let j = i+1; j < s.length; j++) {      
        temp += s[j]
        if(res.length>temp.length) continue
        
        tempRev = temp.split('').reverse().join('')
        if(temp===tempRev){
          res = res.length<temp.length?temp:res
        }
      }
    }
    return res
  };
  
  console.log(longestPalindrome('abba'));
  ```

#### 最长公共子串

- 反转字符串，找到最长的公共子串，判断是否是回文串，否则继续判断第二长的公共子串，判断是否是回文串
- 这似乎是可行的，让我们看看下面的一些例子。
- 例如，`S=“caba”`, `S′=“abac”`：S 以及 S' 之间的最长公共子串为 “aba”，恰恰是答案。
- 让我们尝试一下这个例子：`S=“abcxfcba”`, `S′=“abcfxcba”`：S 以及 S' 之间的最长公共子串为 “abc”。显然，这不是回文
- 当 S 的其他部分中存在**非回文子串的反向副本**时，最长公共子串法就会失败。
- 我们还需要判断该字符串倒置前的下标和当前的字符串下标是不是匹配。

**分析实例**

+ 比如 S="caba"，S'="abac" ，S’ 中 aba 的下标是 0 1 2 ，倒置前是 3 2 1，和 S 中 aba 的下标符合，所以 aba 就是我们需要找的。当然我们不需要每个字符都判断，我们只需要判断末尾字符就可以。

  ![image.png](https://pic.leetcode-cn.com/3910e68a438bce27ef2bf53fb89c3566f234582c4b8137327dfdca917ae40dcf-image.png)

+ 首先 i，j 始终指向子串的末尾字符。所以 j 指向的红色的 a 倒置前的下标是 `beforeRev = length-1-j=4-1-2=1`，对应的是字符串首位的下标，我们还需要加上公共字符串的长度才是末尾字符的下标，也就是 `beforeRev+arr[i][j]-1=1+3-1=3`，因为 ` arr[i][j] ` 保存的就是当前子串的长度，也就是图中的数字 3。此时再和它与 i 比较，如果相等，则说明它是我们要找的回文串。

**实现**

+ ```java
  var longestPalindrome = function(s) {
    const len = s.length
    if(len===0) return ''
    const origin = s
    const reverse = s.split('').reverse().join('')
    // 初始化一维数组
    const dp = new Array(len).fill(0)
    dp[-1] = 0
    let maxLen = ''
    let lastIndex = null
    for (let i = 0; i < len; i++) {
      for (let j = len-1; j >= 0; j--) {
        if(origin[i]===reverse[j]){
          dp[j] = dp[j-1] + 1
          if(dp[j]>maxLen){
            // 判断下标是否对应
            const beforeRev = len - 1 - j
            if(beforeRev + dp[j] - 1 === i ){
              maxLen = dp[j]
              lastIndex = i
            }
          }
        }else dp[j] = 0
      }
    }
    const start = lastIndex - maxLen + 1
    return s.slice(start,start+maxLen)
  }
  ```

**复杂度分析**

+ 时间复杂度 O(n²)。
+ 空间复杂度降为 O(n)。

#### 中心扩展 + 动态规划思想

- 为了改进暴力法，我们首先观察如何避免在验证回文时进行不必要的重复计算。考虑 `ababa ` 这个示例。如果我们已经知道 `bab` 是回文，那么很明显，`ababa` 一定是回文，因为它的左首字母和右尾字母是相同的。

  ![1562053846760](/img/user/programming/basic/algorithm/practice/1562053846760.png)

- 求 长度为 1 和长度为 2 的 P(i,j) 时不能用上边的公式，因为我们代入公式后会遇到 `P[i][j] 中 i > j ` 的情况，比如求 `P[1][2]` 的话，我们需要知道 `P[1+1][2−1]=P[2][1] `，而 `P[2][1] ` 代表着 `S[2,1] ` 不是回文串，显然是不对的，所以我们需要单独判断。
- 所以我们先初始化长度是 1 的回文串的 `P[i,j]`，这样利用上边提出的公式 `P(i,j)=(P(i+1,j−1)&&S[i]==S[j])`，然后两边向外各扩充一个字符，长度为 3 的，为 5 的，所有奇数长度的就都求出来了。
- 同理，初始化长度是 2 的回文串 `P[i,i+1]`，利用公式，长度为 4 的，6 的所有偶数长度的就都求出来了。

  ```java
  public String longestPalindrome(String s) {
    int length = s.length();
    boolean[][] P = new boolean[length][length];
    int maxLen = 0;
    String maxPal = "";
    for (int len = 1; len <= length; len++) //遍历所有的长度
      for (int start = 0; start < length; start++) {
        int end = start + len - 1;
        if (end >= length) //下标已经越界，结束本次循环
          break;
        P[start][end] = (len == 1 || len == 2 || P[start + 1][end - 1]) && s.charAt(start) == s.charAt(end); //长度为 1 和 2 的单独判断下
        if (P[start][end] && len > maxLen) {
          maxPal = s.substring(start, end + 1);
        }
      }
    return maxPal;
  }
  ```

### [516. 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

#### 题目

+ 给定一个字符串 `s`，找到其中最长的回文子序列。可以假设 `s` 的最大长度为 `1000`。

  ```js
  示例 1:
  输入:
  "bbbab"
  输出:
  4
  一个可能的最长回文子序列为 "bbbb"。
  
  示例 2:
  输入:
  "cbbd"
  输出:
  2
  一个可能的最长回文子序列为 "bb"。
  ```

#### 最长公共子序列

+ 反转字符串，然后找出最长公共子序列
+ 如何剔除非回文的反向副本？因为是子序列，只要把非对称的部分删掉，就是回文了

  ```
  abcxfcba
  abcxfcba
  可以构成：abcxcba || abcfcba
  ```

**实现**

+ ```js
  var longestPalindromeSubseq = function(s) {
    const str1 = s
    const str2 = s.split('').reverse().join('')
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
          currentLine[j] = referLine[j-1] + 1
        }else{
          currentLine[j] = Math.max(currentLine[j-1],referLine[j])
        }
      }
      ;([referLine,currentLine] = [currentLine,referLine])
    }
    return referLine[len1-1]
  };
  ```

# 结构性算法

## 归并

### 合并有序链表

+ 其实可以随便弄一个头节点，最后再把他去掉就是了 @@@ `head = head.next`

  ```js
  var mergeTwoLists = function (l1, l2) {
    function ListNode(val) {
      this.val = val;
      this.next = null;
    }
      // 如果l1是null
      if(!l1){
        if(!l2){
          // l1 l2 均为空
          return null
        }else{
          // l1为空，l2不为空
          return l2
        }
      }else{
        if(!l2){
          // l1不为空 l2 为空
          return l1
        }
      }
      
    let head = new ListNode(null)
    if(l1.val<l2.val){
      head.val = l1.val
      l1 = l1.next
    }else{
      head.val = l2.val
      l2 = l2.next
    }
    let tail = head
    // 循环比较完其中一个，剩下的拼上去即可，因为链表本身就是有序的
    while(l1&&l2){
      if(l1.val<=l2.val){
        // 已排序的尾结点 指向下一个
        tail.next = l1
        // 更新已排序的尾结点
        tail = l1
        // 更新待排序的节点
        l1 = l1.next
      }else{
        // 已排序的尾结点 指向下一个
        tail.next = l2
        // 更新已排序的尾结点
        tail = l2
        // 更新待排序的节点
        l2 = l2.next
      }
    }
    // 循环完毕把剩下的拼在尾结点之后
    if(l1){
        console.log(l1.val)
      tail.next = l1
    }
    if(l2){
    console.log(l2.val)
      tail.next = l2
    }
    return head
  };
  ```

+ 优化，使用 dummyHead，以及消除副作用

  ```js
  var mergeTwoLists = function(l1, l2) {
    let dummyHead = new ListNode(-1)
    let cur = dummyHead
    while(l1!==null && l2!==null){
      if(l1.val<=l2.val){
        cur.next = new ListNode(l1.val)
        l1 = l1.next
      }else{
        cur.next = new ListNode(l2.val)
        l2 = l2.next
      }
      cur = cur.next
    }
    if(l1!==null) cur.next = l1
    if(l2!==null) cur.next = l2
    const head = dummyHead.next
    dummyHead = null
    return head
  }
  let l1 = {
    val:1,
    next:{val:2,next:{val:3,next:null}}
  }
  let l2 = {
    val:1,
    next:{val:2,next:{val:3,next:null}}
  }
  const head = mergeTwoLists(l1,l2)
  console.log(head.val)
  console.log(head.next.val)
  ```

+ 优化，所有的循环都可以用递归实现 @@@

  ```js
  var mergeTwoLists = function (l1, l2) {
    function ListNode(val) {
      this.val = val;
      this.next = null;
    }
    // 优化
    if (l1 == null){
      return l2
    }
    if (l2 == null){
      return l1
    }
    let head = new ListNode(null)
    if(l1.val<l2.val){
      head= l1
      head.next = mergeTwoLists(l1.next,l2)
    }else{
      head = l2
      head.next = mergeTwoLists(l1,l2.next)
    }
    return head
  }
  ```

### 字符串加法

+ 详见 math

### [23. 合并K个排序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

#### 暴力法

+ 取得值，排序后，重建链表

  ```js
  function ListNode(val){
    this.val = val
    this.next = null
  }
  var mergeKLists = function(lists) {
    const arr = []
    list.forEach((list) => {
      while(list!==null){
        arr.push(list.val)
        list = list.next
      }
    })
    arr.sort((a,b)=>a-b)
    const val = arr.shift()
    // 边界情况
    if(val===undefined) return null
    const head = new ListNode(val)
    let cur = head
    // 重建链表
    for (let i = 0; i < arr.length; i++) {
      cur.next = new ListNode(arr[i])
      cur = cur.next
    }
    return head
  }
  ```

**复杂度分析**

+ 时间复杂度：O(NlogN) ，其中 N 是节点的总数目。
  + 遍历所有的值需花费 O(N) 的时间。
  + 一个稳定的排序算法花费 O(NlogN) 的时间。
  + 遍历同时创建新的有序链表花费 O(N) 的时间。
+ 空间复杂度：O(N)。
  + 排序花费 O(N) 空间（这取决于你选择的算法）。
  + 创建一个新的链表花费 O(N) 的空间。

#### 方法 2：逐一比较

+ 比较 k 个节点（每个链表的首节点），获得最小值的节点。将选中的节点接在最终有序链表的后面。

**复杂度分析**

+ 时间复杂度： O(kN) ，其中 k 是链表的数目。
  + 几乎最终有序链表中每个节点的时间开销都为 O(k) （k-1 次比较）。
  + 总共有 N 个节点在最后的链表中。
+ 空间复杂度：
  + O(n) 。创建一个新的链表空间开销为 O(n)。
  + O(1) ，重复利用原来的链表节点，每次选择节点时将它直接接在最后返回的链表后面，而不是创建一个新的节点。

#### 方法 3：用优先队列优化方法 2

+ 几乎与上述方法一样，除了将 **比较环节** 用 **优先队列** 进行了优化。你可以参考 [这里](https://baike.baidu.com/item/优先队列/9354754?fr=aladdin) 获取更多信息。

  ```python
  from Queue import PriorityQueue
  
  class Solution(object):
      def mergeKLists(self, lists):
          """
          :type lists: List[ListNode]
          :rtype: ListNode
          """
          head = point = ListNode(0)
          q = PriorityQueue()
          for l in lists:
              if l:
                  q.put((l.val, l))
          while not q.empty():
              val, node = q.get()
              point.next = ListNode(val)
              point = point.next
              node = node.next
              if node:
                  q.put((node.val, node))
          return head.next
  ```

#### 方法 4：逐一两两合并链表

+ 将合并 k 个链表的问题转化成合并 2 个链表 k-1 次。这里是 合并两个有序链表 的题目。

**复杂度分析**

+ 时间复杂度： O(kN) ，其中 k 是链表的数目。
  + 我们可以在 O(n) 的时间内合并两个有序链表，其中 n 是两个链表的总长度
  + 把所有合并过程所需的时间加起来，我们可以得到：O(kN)
+ 空间复杂度：O(1)

#### 方法 5：分治

+ 这个方法沿用了上面的解法，但是进行了较大的优化。我们不需要对大部分节点重复遍历多次。
  + 将 k 个链表配对，并将同一对中的链表合并
  + 重复这一过程，直到我们得到了最终的有序链表。
+ 因此，我们在每一次配对合并的过程中都会遍历几乎全部 N 个节点，并重复这一过程 log2^*K* 次。

  ![image.png](https://pic.leetcode-cn.com/6f70a6649d2192cf32af68500915d84b476aa34ec899f98766c038fc9cc54662-image.png)

+ 把 list 数组当成队列使用，合并 2 条链表后的重新 push，从头部 shift

  ```js
  function ListNode(val){
    this.val = val
    this.next = null
  }
  var mergeKLists = function(lists) {
    // 边际情况
    if(lists.length===0) return null
    // 只剩一个链表，停止递归
    if(lists.length===1) return lists[0]
  
    let l1 = lists.shift(),
        l2 = lists.shift()
    let dummyHead = cur = new ListNode(-1)
    while(l1 && l2){
      if(l1.val<l2.val){
        cur.next = l1
        l1 = l1.next
      }else{
        cur.next = l2
        l2 = l2.next
      }
      cur = cur.next
    }
    if(!l1) cur.next = l2
    else  cur.next = l1
    // 处理头部
    const head = dummyHead.next
    dummyHead.next = null
    lists.push(head)
    return mergeKLists(lists)
  }
  ```

**复杂度分析**

- 时间复杂度： O*(*Nlogk) ，其中 k 是链表的数目
- 空间复杂度：O(1)

## 哈希表 #

### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

**暴力法**

+ 两遍 for 循环

  ```js
  /**
   * @param {number[]} nums
   * @param {number} target
   * @return {number[]}
   */
  var twoSum = function(nums, target) {
    for (let i = 0; i < nums.length; i++) {
      for (let j = i+1; j < nums.length; j++) {
        const another = target -nums[i]
        if(another === nums[j]){
          return [i,j]
        }
      }
    }
  }
  ```

**哈希表**

+ 哈希表

  ```js
  var twoSum = function(nums, target) {
    const map = new Map()
    let ret = []
    nums.some(function(value,index){
      const another = target - value
      // 判断是否已经有该值
      if(map.has(another)){
        // 查两次表，因为get方法得返回值可能是数值，隐式转换导致判断错误
        ret = [map.get(another),index]
        return true
      }else{
        // 添加到字典中
        map.set(value,index)
      }
    })
    return ret
  }
  ```

  ![1559814161683](/img/user/programming/basic/algorithm/practice/1559814161683.png)

+ 最主要的优化是：查表得时间复杂度是 $O(1)$，而 `indexOf` 是 $O(n)$

### [454. 四数相加 II](https://leetcode-cn.com/problems/4sum-ii/)

#### 题目

- 给定四个包含整数的数组列表 A , B , C , D ,计算有多少个元组 (i, j, k, l) ，使得 A[i] + B[j] + C[k] + D[l] = 0。

  ```
  输入:
  A = [ 1, 2]
  B = [-2,-1]
  C = [-1, 2]
  D = [ 0, 2]
  
  输出:
  2
  
  解释:
  两个元组如下:
  1. (0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0
  2. (1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0
  ```

#### 分析

- 可以用哈希表把乘法循环，转换成除法循环
- 可以用指针对循环进行剪枝

  ```js
  /**
   * @param {number[]} A
   * @param {number[]} B
   * @param {number[]} C
   * @param {number[]} D
   * @return {number}
   */
  var fourSumCount = function(A, B, C, D) {
    let ret = 0
    // 四个数组具有相同的长度
    const len = A.length
    Array.from(arguments).forEach((arr) => {
      arr.sort((a,b)=>a-b)
    })
    for (let a = 0; a < len;a++) {
      for (let b = 0; b < len;b++) { // 这题相同的不用跳过
        let c = 0
        let d = len - 1
        while(c<len && d >= 0){
          const sum = A[a] + B[b] + C[c] + D[d]
          if(sum===0){
            // ret ++
            // 更好的语义
            // while(c<len && C[c] === C[++c]){ret++}
            // while(d>=0 && D[d] === D[--d]){ret++}
  
            // 上面是 相加， 而实际的个数是相乘
            // 找到此时C,D的重复个数，直接相乘，然后break
            const cCount = C.reduce((count,val) => {
              if(val===C[c]){
                return ++count
              }else{
                return count
              }
            },0)
            const dCount = D.reduce((count,val) => {
              if(val===D[d]){
                return ++count
              }else{
                return count
              }
            },0)
            ret += cCount * dCount
            break
            // 相等之后，如果c和d的变化幅度是一样的，还是可以
          }
          if(sum<0 && c<len){
            // 太小了，要增大，
            while(C[c] === C[++c]){}
          }
          if(sum>0 && d>=0){
            // 太大了，要变小
            while(D[d] === D[--d]){}
          }
        }
      }
    }
    return ret
  };
   let A = [-1,-1],
  B = [-1,1],
  C = [-1,1],
  D = [-1,1] 
  
  /* let A = [-1,0,1],
  B =[-1,0,1],
  C = [0,0,1],
  D =[-1,1,1] */
  console.log(fourSumCount(A, B, C, D))
  ```

- 要考虑重复的，使用哈希表
- 不考虑重复的使用指针 + 剪枝

  ```js
  /**
   * @param {number[]} A
   * @param {number[]} B
   * @param {number[]} C
   * @param {number[]} D
   * @return {number}
   */
  var fourSumCount = function(A, B, C, D) {
    let ret = 0
    // 四个数组具有相同的长度
    const len = A.length
  
    // a,b 两两组合
    const mapAB = []
    for (let a = 0; a < A.length; a++) {
      for (let b = 0; b < B.length; b++) {
        mapAB.push(A[a]+B[b])
      }
    }
    const mapCD = []
    for (let c = 0; c < C.length; c++) {
      for (let d = 0; d < D.length; d++) {
        mapCD.push(C[c]+D[d])
      }
    }
    // map 两两匹配
    for (let i = 0; i < mapAB.length; i++) {
      for (let j = 0; j < mapCD.length; j++) {
        if(mapAB[i]+mapCD[j]===0) ret++
      }
    }
    
    return ret
  };
  ```

- 超时了，使用 map 优化

  ```js
  /**
   * @param {number[]} A
   * @param {number[]} B
   * @param {number[]} C
   * @param {number[]} D
   * @return {number}
   */
  var fourSumCount = function(A, B, C, D) {
    let ret = 0
    // 四个数组具有相同的长度
    const len = A.length
    // a,b 两两组合
    const mapAB = {}
    for (let a = 0; a < len; a++) {
      for (let b = 0; b < len; b++) {
        if(mapAB[A[a]+B[b]]){
          mapAB[A[a]+B[b]]++
        }else{
          mapAB[A[a]+B[b]]=1
        }
      }
    }
    const mapCD = {}
    for (let c = 0; c < len; c++) {
      for (let d = 0; d < len; d++) {
        if(mapCD[C[c]+D[d]]){
          mapCD[C[c]+D[d]]++
        }else{
          mapCD[C[c]+D[d]]=1
        }
      }
    }
    // map 两两匹配
    for (const key in mapAB) {
      const target = 0 - key
      if (mapCD.hasOwnProperty(target)) {
        ret += mapAB[key]*mapCD[target]
      }
    }
    return ret
  };
  ```

- 进一步优化，两边哈希，到一遍哈希

  ```js
  var fourSumCount = function(A, B, C, D) {
    let ret = 0
    // 四个数组具有相同的长度
    const len = A.length
    // a,b 两两组合
    const mapAB = {}
    for (let a = 0; a < len; a++) {
      for (let b = 0; b < len; b++) {
        if(mapAB[A[a]+B[b]]){
          mapAB[A[a]+B[b]]++
        }else{
          mapAB[A[a]+B[b]]=1
        }
      }
    }
    for (let c = 0; c < len; c++) {
      for (let d = 0; d < len; d++) {
        const target = 0 - (C[c]+D[d])
        if(mapAB.hasOwnProperty(target)) ret += mapAB[target]
      }
    }
    return ret
  };
  ```

### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

+ 见对撞指针

### [16. 最接近的三数之和](https://leetcode-cn.com/problems/3sum-closest/)

+ 见对撞指针

### [18. 四数之和](https://leetcode-cn.com/problems/4sum/)

+ 见对撞指针

## 二进制

### 分析

+ 利用了栈的逆序性质
+ 第一次模除的结果是进制的最后一位，余下的
+ 倒数第二位，就是除以 2 之后再次模除，乘以二就是左移一位，除以二就是右移一位，之后再次判断余下的是什么
+ 每次都除以 radix，就是模拟 a~n~ * radix^n-1^
+ 最后再倒过来就是结果

### 从十进制到二进制

- ```js
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
