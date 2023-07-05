---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/pointer/"}
---


# 双指针

这几道题感觉没有啥模板, 就是要根据题意活用双指针

[86. 分隔链表](../leetcode/86.%20分隔链表.md)

[328. 奇偶链表](../leetcode/328.%20奇偶链表.md)

[160. 相交链表](../leetcode/160.%20相交链表.md)

[88. 合并两个有序数组](../leetcode/88.%20合并两个有序数组.md)

[475. 供暖器](../leetcode/475.%20供暖器.md)

# 同向双指针

[611. 有效三角形的个数](../leetcode/611.%20有效三角形的个数.md): 同向多指针, 三数不等式, 寻找得是区间, 而不是排除. 同样是通过排序, 降低一层循环

他们的共同点, 其实是: 排序 + 子数组, 不要求有序, 不是子串

# 对撞指针 / 头尾指针

[发现算法之美-双指针之对撞指针 - 知乎](https://zhuanlan.zhihu.com/p/374545334)

可以用来做反转, reverse

用于缩小搜索范围, 用于排序过的数组, 感觉和前缀和那个有点模糊不清, 需要一个专题处理一下

+ [167. 两数之和 II - 输入有序数组](../leetcode/167.%20两数之和%20II%20-%20输入有序数组.md)
+ [15. 三数之和](../leetcode/15.%20三数之和.md)
+ [18. 四数之和](../leetcode/18.%20四数之和.md)
+ [633. 平方数之和](../leetcode/633.%20平方数之和.md)
+ [11. 盛最多水的容器](../leetcode/11.%20盛最多水的容器.md) 单纯的头尾指针

| File                                                                             | difficulty | etags                                                                                                                                          |
| -------------------------------------------------------------------------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| [[programming/basic/leetcode/633. 平方数之和\|633. 平方数之和]]                         | medium     | <ul><li>#leetcode/math/squre</li><li>#leetcode/pointer/collision-pointer</li></ul>                                                             |
| [[programming/basic/leetcode/167. 两数之和 II - 输入有序数组\|167. 两数之和 II - 输入有序数组]]   | medium     | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/binary-search</li><li>#leetcode/unsolved</li></ul>                               |
| [[programming/basic/leetcode/18. 四数之和\|18. 四数之和]]                             | medium     | <ul><li>#leetcode/hash-table/count</li><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/skip-dup</li></ul>                            |
| [[programming/basic/leetcode/345. 反转字符串中的元音字母\|345. 反转字符串中的元音字母]]             | easy       | <ul><li>#leetcode/pointer/collision-pointer</li></ul>                                                                                          |
| [[programming/basic/leetcode/680. 验证回文串 II\|680. 验证回文串 II]]                   | easy       | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/palindrome</li></ul>                                                             |
| [[programming/basic/leetcode/15. 三数之和\|15. 三数之和]]                             | medium     | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/skip-dup</li></ul>                                                               |
| [[programming/basic/leetcode/16. 最接近的三数之和\|16. 最接近的三数之和]]                     | medium     | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/unsolved</li></ul>                                                               |
| [[programming/basic/leetcode/27. 移除元素\|27. 移除元素]]                             | easy       | <ul><li>#leetcode/pointer/collision-pointer</li><li>#leetcode/pointer/fast-slow</li><li>#leetcode/space/in-place</li></ul>                     |
| [[programming/basic/leetcode/653. 两数之和 IV - 输入二叉搜索树\|653. 两数之和 IV - 输入二叉搜索树]] | easy       | <ul><li>#leetcode/tree/traversal</li><li>#leetcode/hash-table</li><li>#leetcode/unsolved</li><li>#leetcode/pointer/collision-pointer</li></ul> |

{ .block-language-dataview}

哈希表:

+ [1. 两数之和](../leetcode/1.%20两数之和.md)

# 快慢指针

快慢指针算法是一种常见的算法技巧，通常用于解决链表相关问题。该算法利用两个指针，在链表上同时移动，但每次移动的步长不同，以达到特定的目的

> 从这个定义来看, 严格意义上的快慢指针不包含数组原地删除那一块逻辑, 但是官解上都说了是快慢指针, 所以就放在一起吧

## 处理链表

### 特定节点

让两个指针拥有固定的间距, 从而达到目的

寻找链表的中间节点：使用快慢指针，快指针每次移动两步，慢指针每次移动一步，当快指针到达链表尾部时，慢指针正好指向链表的中间节点。寻找其他特定节点, 只需要通过快指针和慢指针

链表的倒数第 K 个节点：使用快慢指针，让快指针先移动 K 步，然后快慢指针同时移动，当快指针到达链表尾部时，慢指针指向的节点即为倒数第 K 个节点。

[19. 删除链表的倒数第 N 个结点](../leetcode/19.%20删除链表的倒数第%20N%20个结点.md)

[143. 重排链表](../leetcode/143.%20重排链表.md)

### 处理回文链表

判断链表的回文结构：使用快慢指针和反转链表的技巧，可以判断链表是否是回文结构。快指针每次移动两步，慢指针每次移动一步，当快指针到达链表尾部时，慢指针指向链表的中间节点。然后反转慢指针后面的链表，再使用两个指针从头和中间开始同时遍历比较，如果节点值不相等，则链表不是回文结构。

### 处理环

判断链表是否存在环：

+ 使用快慢指针，如果存在环，快指针最终会追上慢指针，可以通过比较两个指针是否相等来判断是否存在环。
+ 不存在环的话就会遍历结束

感觉不如哈希表快? 参考 [160. 相交链表](../leetcode/160.%20相交链表.md) 的处理, 记录一下, 如果重复了就存在环

但是哈希表不能判断环的长度? 可以的, 记录的时候同时记一下长度就行, 然后重复的时候就是环的长度, 第一个重复的点就是环的起点, 但是这种题目的考点更多还是希望能够使用快慢指针实现

[141. 环形链表](../leetcode/141.%20环形链表.md)

[142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

[457. Circular Array Loop](../leetcode/457.%20Circular%20Array%20Loop.md)

我们可以使用在无向图中找环的一个经典算法：快慢指针来解决本题

## 判圈算法

Flyod 龟兔算法 快慢指针

### 原理

对于赛道来说，如果赛道中有环，那么速度快的兔子一定会在某个地点追上乌龟，并且兔子所跑的距离减去乌龟所跑的距离，一定是环长度的整数倍。

假设令龟、兔为指针，并且指向起点位置，兔子每次移动两个节点，乌龟每次移动一个节点。如果两者在起始节点外相遇，则说明有环。如果兔子在走到链表尾部还没有与乌龟相遇，说明无环。

### 环长度

通过上述算法判断出存在环 C 时，显然龟兔位于同一节点 B，此时，令兔子保持不动，而乌龟不断推进，记录移动距离，等再次相遇时，移动步数即环 C 长度。

### 环起点

乌龟所跑距离为 `S = m + n + aC`（C 为环长度),

兔子所跑距离为 `2S = m + n + bC`（因为兔子速度为乌龟 `2` 倍）

故得 `S = (b - a)C = m + n + aC => (b - 2a)C = m + n`, 由此可知，`m + n` 为环 `C` 长度整数倍

两者相遇之后, 让兔子回到起点 H, 乌龟停留在相遇点 B, 随后, 同时让乌龟与兔子不断推进一步, 兔子移动 m 到达点 A, 距离点 B 还有 n, 乌龟移动 m 也会到达点 A, 再走 n 就饶了 `(b-2a)` 圈，到达点 B

所以龟兔会在点 A 相遇，之后一直为伴走距离 n 再次到达点 B

  ![](https://raw.githubusercontent.com/chiyuu-git/chiyuu-pic/master/20170423200520994.png)

### 实现

```js
function flyodCircle(head) {
  let rabbit = head,
    tortoise = head;
  do {
    // 兔子先走，走两步
    if (rabbit.next != null) rabbit = rabbit.next;
    else return false;
    if (rabbit.next != null) rabbit = rabbit.next;
    else return false;
	
    tortoise = tortoise.next;
  } while (rabbit !== tortoise);
  // 出循环，此时龟兔相遇在点B
  // 求环长度C
  let circleLen = 0;
  do {
    tortoise = tortoise.next;
    circleLen++;
  } while (tortoise !== rabbit);
  // 求环起点A
  tortoise = head;
  while (tortoise !== rabbit) {
    tortoise = tortoise.next;
    rabbit = rabbit.next;
  }
  // 出循环，此时龟兔相遇在起点A
  const start = tortoise;
  return { start, circleLen };
}
```

### 时间复杂度

如果乌龟走到环起点 P 时，此时显然兔子已经在环内某节点，之后兔子最多走一圈就会与乌龟相遇。假设链表起始节点到环起点距离为 m， 环长度为 n, 故时间复杂度为 O(m + n)

### 空间复杂度

算法仅需要创建指针 `tortoise,rabbit` ，环长 `circleLen` 以及环起点 `start` 故空间复杂度为 O(1)

## Brent 龟兔算法

### 原理

这是一个倍增算法，让乌龟保持不动，兔子走 `2^i ` 步，看这个过程中龟兔有没有相遇，没有的话，让乌龟的位置变成兔子的位置（如果乌龟位置一直不变，它可能不会进入环中），让兔子走 `2^(i+1)` 步，看看会不会相遇，如此循环。这个算法也是 O(n) 的，但是它会比 Floyd 表现的更好，且 Floyd 是这个算法最差时的表现。

### 求环的长度

因为乌龟一直处在兔子更改步长上限时的位置，所以更改步长后，兔子走了几步与乌龟相遇，环的长度就是几步（就是代码中的 count 变量）

### 求环的起点

Floyd 判圈算法利用了乌龟和兔子的距离是环长整数倍的性质来求出起点，所以可以让乌龟回到起点，兔子回到距离起点 C（C 指环的长度）处，然后接下来的步骤和 Floyd 一样。

### 实现

```js
function breantCycle(head) {
  let rabbit = (tortoise = head);
  let stride = 2,
    count = 0;
  do {
    while (count <= stride) {
      if (rabbit !== tortise) {
        rabbit = rabbit.next;
        if (rabbit === null) return false;
        count++;
      } else {
        break;
      }
    }
    tortoise = rabbit;
    interval *= 2;
    count = 0;
  } while (rabbit !== tortoise);
  // 出循环，龟兔相遇
  const circleLen = count;
  // 求环起点A
  tortoise = rabbit = head;
  while (count > 0) {
    rabbit = rabbit.next;
    count--;
  }
  while (tortoise !== rabbit) {
    tortoise = tortoise.next;
    rabbit = rabbit.next;
  }
  return;
}
```

## 数组原地删除

两题都是要求使用原地算法删除元素, 其实删除和保留是同一个逻辑

快指针跳过要处理元素, 跳过元素就相当于是删除元素, 慢指针永远指向可替换的位置

在慢指针之前的位置都是要保留的元素

其中一个是有序的, 一个是无序的

无序的只能去除指定值, 因为去除重复数相当于是要去除多个指定值了, 所以无序的做不到

- [[programming/basic/leetcode/26. 删除有序数组中的重复项\|26. 删除有序数组中的重复项]]
- [[programming/basic/leetcode/27. 移除元素\|27. 移除元素]]
- [[programming/basic/leetcode/80. 删除有序数组中的重复项 II\|80. 删除有序数组中的重复项 II]]
- [[programming/basic/leetcode/82. 删除排序链表中的重复元素 II\|82. 删除排序链表中的重复元素 II]]
- [[programming/basic/leetcode/83. 删除排序链表中的重复元素\|83. 删除排序链表中的重复元素]]

{ .block-language-dataview}

[283. 移动零](../leetcode/283.%20移动零.md) 与合并数组好像, 即是原地算法, 但是又和之前的原地标记不同, 这里借用的是双指针, 数组原地删除、 数组原地交换, 数组原地合并, 这几个很类似, 要好好总结一下才行

## [234. 回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

### 直觉法

```js
  var isPalindrome = function (head) {
    if (head === null) return true;
	
    let cur = head;
    let nodes = [];
    while (cur) {
      nodes.push(cur.val);
      cur = cur.next;
    }
    const half = nodes.length >>> 1;
    nodes.reverse();
    for (let i = 0, j = head; i < half; i++) {
      if (j.val !== nodes[i]) return false;
      j = j.next;
    }
    return true;
  };
```

### 快慢指针

#### 如何找到链表的中间位置

通常设置一个快指针 `fast` 和满指针 `slow`，快指针一次走两步，慢指针一次，当快指针遍历到最后时，满指针指在链表中部。

```java
  ListNode fast = head;
  ListNode slow = head;
  while(fast != null && slow != null){
      fast = fast.next.next;
      slow = slow.next;
  }
```

我们这里敲定一下细节：

- 如果只有一个节点 1，fast = slow = 1
- 如果有 1->2 两个节点，fast = null，slow = 2
- 如果有 1->2->3 三个节点，fast = null，slow = 2
- 如果有 1->2->3->4 四个节点，fast = null，slow = 3
我们可以得出结论：
- 当链表长度为 **偶数** 时，`slow` 指针指向链表中间位置靠后的那一个，右中位数
- 当链表长度为 **奇数** 时，`slow` 指针指向链表中间位置

## 反转链表

这道题的难点是如何达到 `O(1)` 的空间复杂度，并且要求不改变原链表的结构

思路如下：

  1. 先找到链表的中间位置
  2. 反转后半部分链表
  3. 同时从头和尾部向中间遍历，如果有不同值，则不是回文
  4. 再次反转后半部分链表，恢复链表原有结构

### 示例

1->2->1->null

从前一部分我们可以发现，slow 指针指向 2

我们反转后半部分链表 (包括中间位置的节点)，得到 1->(null-<)2<-1

然后开始同时从头和尾开始对比，当尾部的遍历指针为 null 结束

最后再一次反转链表，得到 1->2->1->null

并且，奇数和偶数的过程是一样的，代码上不做区分

### 实现

 ```java
  var isPalindrome = function(head) {
    if(head == null || head.next == null) return true
    let slow = head, fast = head.next,
        cur = null, prev = null
	
 while(fast != null && fast.next != null) {
      //先移动指针再来反转
      cur = slow
      slow = slow.next
      fast = fast.next.next
      //反转前半段链表
      cur.next = prev
      prev = cur
    }
    // 出循环，到一半
    /**
     * fast == null 奇数长 , slow 等于中位数
     *   right = slow.next
     *   slow.next = prev 反转链表
     *   left = slow.next
     * fast !== null 偶数长 , slow 等于左中位数
     *   right = slow.next
     *   slow.next = prev 反转链表
     *   left = slow
     */
    let right = slow.next
    let next = fast == null? slow : slow.next
    slow.next = prev
    let left = fast == null? slow.next : slow
    slow = fast = prev = null
    while(left != null) {
      if(left.val != right.val) return false
      // 先移动指针再反转，还原链表
      cur = left
      left = left.next
      right = right.next
      cur.next = next
      next = cur
    }
    return true
  }
	
  let head = {
	  val:1,next:{
		  val:2,next:{
			  val:3,next:{
				  val:4,next:{
					  val:5,next:{
						  val:6,next:null
						  }
					  }
				  }
			  }
		  }
	  }
  // let head = {val:1,next:{val:2,next:{val:3,next:{val:4,next:{val:5,next:null}}}}}
  console.log(isPalindrome(head))
  ```

# 滑动窗口

答案要求在某一个范围之内

滑动窗口是数组/字符串问题中常用的抽象概念。 窗口通常是在数组/字符串中由开始和结束索引定义的一系列元素的集合，即 ` [i, j)`（左闭，右开）。而滑动窗口是可以将两个边界向某一方向“滑动”的窗口。

例如，我们将 ` [i, j)` 向右滑动 1 个元素，则它将变为 `[i+1, j+1)`（左闭，右开）

## Solution Tips

```js
while (right < len) {
	// expand window
	// ... do something

	while (left...) {
		// narrow window
		// do something
	}
}
```

### 关键词

子串, 子数组

### 窗口长度

窗口长度是固定的

[187. 重复的DNA序列](../leetcode/187.%20重复的DNA序列.md)

[643. 子数组最大平均数 I](../leetcode/643.%20子数组最大平均数%20I.md)

窗口长度是动态的, 根据题意伸缩

[674. 最长连续递增序列](../leetcode/674.%20最长连续递增序列.md)

[209. 长度最小的子数组](../leetcode/209.%20长度最小的子数组.md)

[424. 替换后的最长重复字符](../leetcode/424.%20替换后的最长重复字符.md)

### 维护窗口的数据结构

找到维护窗口的合理数据结构, 是解题的关键

单纯的变量: 常见的 sum, 减去一个, 加上一个

自平衡二叉树：插入、删除、搜索都是 O(log k)

[220. 存在重复元素 III](../leetcode/220.%20存在重复元素%20III.md)

**哈希表**：插入、删除、搜索都是常数级

> 数组：插入和删除是线性的，搜索也是线性的, 数组其实也是哈希表的一种形态

不是连续的结构就可以考虑使用哈希表存储数据

[438. Find All Anagrams in a String](../leetcode/438.%20Find%20All%20Anagrams%20in%20a%20String.md)

[3. 无重复字符的最长子串](../leetcode/3.%20无重复字符的最长子串.md)

[424. 替换后的最长重复字符](../leetcode/424.%20替换后的最长重复字符.md)

[219. 存在重复元素 II](../leetcode/219.%20存在重复元素%20II.md)

## [76. 最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)

### 固定窗口法

```js
  var minWindow = function (s, t) {
    const len = s.length;
    // p的哈希表
    const tMap = getMap(t);
    // 要最小的，从k开始增加长度
    for (let k = t.length; k < s.length + 1; k++) {
      const subMap = getMap(s.slice(0, k));
      for (let i = 0, j = i + k; j < len + 1; i++, j++) {
        let match = true;
        for (const [key, val] of Object.entries(tMap)) {
          // 有这个字母，且数量大于等于val
          if (subMap.hasOwnProperty(key)) {
            if (subMap[key] < val) {
              match = false;
              break;
            }
          } else {
            match = false;
            break;
          }
        }
        if (match) return s.slice(i, j);
        subMap[s[i]]--;
        if (subMap[s[j]] === 0 || subMap[s[j]] === undefined) subMap[s[j]] = 1;
        else subMap[s[j]]++;
      }
    }
    return "";
    function getMap(str) {
      const map = {};
      for (let i = 0; i < str.length; i++) {
        const key = str[i];
        if (map.hasOwnProperty(key)) map[key]++;
        else map[key] = 1;
      }
      return map;
    }
  };
  let s = "ADOBECODEBANC",
    t = "ABC";
  console.log(minWindow(s, t));
  ```

### 滑动窗口法

滑动窗口法可以减少很多多余的判断

滑动窗口算法的思路是这样：

1. 我们在字符串 S 中使用双指针中的左右指针技巧，初始化 left = right = 0，把索引闭区间 `[left, right)` 称为一个「窗口」。
2. 我们先不断地增加 right 指针扩大窗口 `[left, right)`，直到窗口中的字符串符合要求（包含了 T 中的所有字符）。
3. 此时，我们停止增加 right，转而不断增加 left 指针缩小窗口 `[left, right)`，直到窗口中的字符串不再符合要求（不包含 T 中的所有字符了）。同时，每次增加 left，我们都要更新一轮结果。
  4. 重复第 2 和第 3 步，直到 right 到达字符串 S 的尽头。

这个思路其实也不难，第 2 步相当于在寻找一个「可行解」，然后第 3 步在优化这个「可行解」，最终找到最优解。左右指针轮流前进，窗口大小增增减减，窗口不断向右滑动。

上述过程可以简单地写出如下伪码框架：

```js
  let s, t
  // 在 s 中寻找 t 的「最小覆盖子串」
  let left = 0, right = 0
  let res = ''
	
  while(right < s.size()) {
    window.add(s[right])
    // 如果符合要求，移动 left 缩小窗口
    while (window 符合要求) {
      // 如果这个窗口的子串更短，则更新 res
      res = minLen(res, window)
      window.remove(s[left])
      left++
    }
    right++
  }
  return res
```

**实现**

```js
  var minWindow = function (s, t) {
    const len = s.length;
    // p的哈希表
    const tMap = getMap(t);
    const subMap = {};
    let left = 0,
      right = 0;
    let res = "",
      minLen = s.length;
    while (right < len) {
      if (subMap[s[right]] === 0 || subMap[s[right]] === undefined)
        subMap[s[right]] = 1;
      else subMap[s[right]]++;
      let match = isMatch(subMap);
      while (match && left < len) {
        // 如果长度更小，则更新
        if (right - left < minLen) {
          res = s.slice(left, right + 1);
          minLen = right - left + 1;
        }
        subMap[s[left]]--;
        left++;
        // 再次判断是否符合
        match = isMatch(subMap);
      }
      right++;
    }
    return res;
	
    function getMap(str) {
      const map = {};
      for (let i = 0; i < str.length; i++) {
        const key = str[i];
        if (map.hasOwnProperty(key)) map[key]++;
        else map[key] = 1;
      }
      return map;
    }
    function isMatch(subMap) {
      for (const [key, val] of Object.entries(tMap)) {
        // 有这个字母，且数量大于等于val
        if (subMap.hasOwnProperty(key)) {
          if (subMap[key] < val) return false;
        } else return false;
      }
      return true;
    }
  };
  ```

依然超时，每一次 match 都遍历太耗时了

### Match 继续优化

```js
  var minWindow = function (s, t) {
    const len = s.length;
    // p的哈希表
    const needs = getMap(t);
    const subMap = {};
    let left = 0,
      right = 0;
    let res = "",
      minLen = s.length;
    let match = 0; // 记录已经匹配needs的长度
    while (right < len) {
      const char = s[right];
      if (needs.hasOwnProperty(char)) {
        subMap[char] === undefined ? (subMap[char] = 1) : subMap[char]++;
        // 等于，在等于的那一刻match++，之后再添加也不能增加match的长度 'bb' 'b' ，match最多也就是1
        if (subMap[char] === needs[char]) match++;
      }
      // 不可以等于
      while (match === needs.length && left < len) {
        // 如果长度更小，则更新
        if (right - left < minLen) {
          res = s.slice(left, right + 1);
          minLen = right - left + 1;
        }
        // 再次判断是否符合
        const char = s[left];
        if (needs.hasOwnProperty(char)) {
          subMap[char]--;
          // 在小于的那一刻--
          if (subMap[char] < needs[char]) match--;
        }
        left++;
      }
      right++;
    }
    return res;
	
    function getMap(str) {
      const map = {};
      map.length = 0;
      for (let i = 0; i < str.length; i++) {
        const key = str[i];
        if (map.hasOwnProperty(key)) map[key]++;
        else {
          map[key] = 1;
          map.length++;
        }
      }
      return map;
    }
  };
  let s = "bbaa",
    t = "aba";
  console.log(minWindow(s, t));
  ```

#### 复杂度分析

这个算法的时间复杂度是 O(M+N)，M 和 N 分别是字符串 S 和 T 的长度。因为我们先用 for 循环遍历了字符串 T 来初始化 needs，时间 O(N)，之后的两个 while 循环最多执行 2M 次，时间 O(M)。

读者也许认为嵌套的 while 循环复杂度应该是平方级，但是你这样想，while 执行的次数就是双指针 left 和 right 走的总路程，最多是 2M 嘛。

### 再进一步优化

<https://leetcode-cn.com/problems/minimum-window-substring/solution/zui-xiao-fu-gai-zi-chuan-by-leetcode-2/>

## [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)

### 哈希表

- 见二分查找

### 二分查找

### 判圈算法

- 因为 nums 中的每个数字都在 1 和 n 之间，所以它必须指向存在的索引。此外，由于 0 不能作为 nums 中的值出现，nums[0] 不能作为循环的一部分。
- 由于存在重复数，所以形成了圈
