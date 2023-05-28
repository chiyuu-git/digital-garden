---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/pointer/"}
---


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

哈希表:

+ [1. 两数之和](../leetcode/1.%20两数之和.md)

# 快慢指针

快慢指针算法是一种常见的算法技巧，通常用于解决链表相关问题。该算法利用两个指针，在链表上同时移动，但每次移动的步长不同，以达到特定的目的。下面是快慢指针算法的一些常见用途:

1. 判断链表是否存在环：使用快慢指针，如果存在环，快指针最终会追上慢指针，可以通过比较两个指针是否相等来判断是否存在环。
2. 寻找链表的中间节点：使用快慢指针，快指针每次移动两步，慢指针每次移动一步，当快指针到达链表尾部时，慢指针正好指向链表的中间节点。寻找其他特定节点, 只需要通过快指针和慢指针
3. 链表的倒数第 K 个节点：使用快慢指针，让快指针先移动 K 步，然后快慢指针同时移动，当快指针到达链表尾部时，慢指针指向的节点即为倒数第 K 个节点。
4. 判断链表的回文结构：使用快慢指针和反转链表的技巧，可以判断链表是否是回文结构。快指针每次移动两步，慢指针每次移动一步，当快指针到达链表尾部时，慢指针指向链表的中间节点。然后反转慢指针后面的链表，再使用两个指针从头和中间开始同时遍历比较，如果节点值不相等，则链表不是回文结构。
5. 合并两个有序链表：给定两个有序链表，可以使用头尾指针算法，通过比较两个链表节点的值，将较小的节点连接到新链表中，并移动对应的指针。

这些只是头尾指针算法的一些常见用途，实际上，该算法可以用于解决很多与链表相关的问题，例如检测循环、判断交点、查找环的起始位置等等

## 处理环

## 数组原地删除

两题都是要求使用原地算法删除元素, 其实删除和保留是同一个逻辑

快指针跳过要处理元素, 跳过元素就相当于是删除元素, 慢指针永远指向可替换的位置

在慢指针之前的位置都是要保留的元素

其中一个是有序的, 一个是无序的

无序的只能去除指定值, 因为去除重复数相当于是要去除多个指定值了, 所以无序的做不到

- [[programming/basic/leetcode/83. 删除排序链表中的重复元素\|83. 删除排序链表中的重复元素]]
- [[programming/basic/leetcode/80. 删除有序数组中的重复项 II\|80. 删除有序数组中的重复项 II]]
- [[programming/basic/leetcode/27. 移除元素\|27. 移除元素]]
- [[programming/basic/leetcode/26. 删除有序数组中的重复项\|26. 删除有序数组中的重复项]]
- [[programming/basic/leetcode/82. 删除排序链表中的重复元素 II\|82. 删除排序链表中的重复元素 II]]

{ .block-language-dataview}

## [283. 移动零](https://leetcode-cn.com/problems/move-zeroes/)

## 分析

splice 使用注意点：`nums.length` 是动态变化的，删除之后要 ` i--`，否则可能会跳过元素

  ```js
  var moveZeroes = function (nums) {
    const zero = [];
    for (let i = 0; i < nums.length; i++) {
      if (nums[i] === 0) {
        zero.push(nums.splice(i, 1)[0]);
        i--;
      }
    }
    return nums.push(...zero);
  };
  ```

### 方法二：空间最优，操作局部优化（双指针）

这种方法与上面的工作方式相同，即先满足一个需求，然后满足另一个需求。它以一种巧妙的方式做到了这一点。上述问题也可以用另一种方式描述，“将所有非 0 元素置于数组前面，保持它们的相对顺序相同”

这是双指针的方法。由变量 “cur” 表示的快速指针负责处理新元素

  1. 如果新找到的元素不是 0，我们就在最后找到的非 0 元素之后记录它。最后找到的非 0 元素的位置由慢指针 “lastNonZero” 变量表示
  2. 当我们不断发现新的非 0 元素时，我们只是在第 “lastNonZero+1” 个索引处覆盖它们。此覆盖不会导致任何数据丢失，因为我们已经处理了其中的内容（如果它是非 0 的，则它现在已经写入了相应的索引，或者如果它是 0，则稍后将进行处理）
在 “cur” 索引到达数组的末尾之后，我们现在知道所有非 0 元素都已按原始顺序移动到数组的开头。现在是时候满足其他要求了，“将所有 0 移动到末尾”。我们现在只需要从 “lastNonZero+1” 索引开始用 0 填充所有索引。

```js
  var moveZeroes = function (nums) {
    let lastNonZero = -1;
    for (let i = 0; i < nums.length; i++) {
      if (nums[i] !== 0) nums[++lastNonZero] = nums[i];
    }
    for (let i = lastNonZero + 1; i < nums.length; i++) {
      nums[i] = 0;
    }
    return nums;
  };
  let nums = [0, 1, 0, 3, 12];
	
  console.log(moveZeroes(nums));
```

### 最优解

前一种方法的操作是局部优化的。例如，所有（除最后一个）前导零的数组：[0，0，0，…，0，1]。对数组执行多少写操作？对于前面的方法，它写 0 n−1 次，这是不必要的。我们本可以只写一次。怎么用？… 只需固定非 0 元素。

最优方法也是上述解决方案的一个细微扩展。一个简单的实现是，如果当前元素是非 0 的，那么它的正确位置最多可以是当前位置或者更早的位置。如果是后者，则当前位置最终将被非 0 或 0 占据，该非 0 或 0 位于大于 “cur” 索引的索引处。我们马上用 0 填充当前位置，这样不像以前的解决方案，我们不需要在下一个迭代中回到这里。

换句话说，代码将保持以下不变：

  1. 慢指针（lastNonZero）之前的所有元素都是非零的。
  2. 当前指针和慢速指针之间的所有元素都是零。
因此，当我们遇到一个非零元素时，我们需要交换当前指针和慢速指针指向的元素，然后前进两个指针。如果它是零元素，我们只前进当前指针。

```js
  var moveZeroes = function (nums) {
    let lastNonZero = -1;
    for (let i = 0; i < nums.length; i++) {
      if (nums[i] !== 0) {
        lastNonZero++;
        [nums[lastNonZero], nums[i]] = [nums[i], nums[lastNonZero]];
      }
    }
    return nums;
  };
```

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

### 关键词

子串, 子数组

### 窗口长度

窗口长度是固定的

[187. 重复的DNA序列](../leetcode/187.%20重复的DNA序列.md)

[643. 子数组最大平均数 I](../leetcode/643.%20子数组最大平均数%20I.md)

窗口长度是动态的, 根据题意伸缩

[674. 最长连续递增序列](../leetcode/674.%20最长连续递增序列.md)

[209. 长度最小的子数组](../leetcode/209.%20长度最小的子数组.md)

### 维护窗口的数据结构

找到维护窗口的合理数据结构, 是解题的关键

单纯的变量: 常见的 sum, 减去一个, 加上一个

自平衡二叉树：插入、删除、搜索都是 O(log k)

**哈希表**：插入、删除、搜索都是常数级

> 数组：插入和删除是线性的，搜索也是线性的, 数组其实也是哈希表的一种形态

[438. Find All Anagrams in a String](../leetcode/438.%20Find%20All%20Anagrams%20in%20a%20String.md)

[3. 无重复字符的最长子串](../leetcode/3.%20无重复字符的最长子串.md)

## [217. 存在重复元素](https://leetcode-cn.com/problems/contains-duplicate/)

## [219. 存在重复元素 II](https://leetcode-cn.com/problems/contains-duplicate-ii/)

### 分析

维护了个 k 大小的滑动窗口，然后在这个窗口里面搜索是否存在跟当前元素相等的元素

## [220. 存在重复元素 III](https://leetcode-cn.com/problems/contains-duplicate-iii/)

### 线性搜索 超时

将每个元素与它之前的 _k_ 个元素比较，查看它们的数值之差是不是在 _t_ 以内。

解决这个问题需要找到一组满足以下条件的 _i_ 和 _j_：

![1563855257489](/img/user/programming/basic/algorithm/pointer/1563855257489.png)

我们需要维护了一个 k 大小的滑动窗口。这种情况下，第一个条件始终是满足的，只需要通过线性搜索来检查第二个条件是否满足就可以了

```java
  public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
      for (int i = 0; i < nums.length; ++i) {
          for (int j = Math.max(i - k, 0); j < i; ++j) {
              if (Math.abs(nums[i] - nums[j]) <= t) return true;
          }
      }
      return false;
  }
  // Time limit exceeded.
```

### 二叉搜索树 【JAVA 通过，JS 超时】

如果窗口中维护的元素是有序的，只需要用二分搜索检查条件二是否是满足的就可以了。利用自平衡二叉搜索树，可以在对数时间内通过 插入 和 删除 来对滑动窗口内元素排序。

方法一 **真正的瓶颈** 在于检查第二个条件是否满足需要扫描滑动窗口中所有的元素。因此我们需要考虑的是有没有比全扫描更好的方法。

如果窗口内的元素是有序的，那么用两次二分搜索就可以找到 x+t 和 x-t 这两个边界值了。

然而不幸的是，窗口中的元素是无序的。这里有一个初学者非常容易犯的错误，那就是将滑动窗口维护成一个有序的数组。虽然在有序数组中 搜索 只需要花费对数时间，但是为了让数组保持有序，我们不得不做插入和删除的操作，而这些操作是非常不高效的。想象一下，如果你有一个 k 大小的有序数组，当你插入一个新元素 x 的时候。虽然可以在 O(logk) 时间内找到这个元素应该插入的位置，但最后还是需要 O(k) 的时间来将 x 插入这个有序数组。因为必须得把当前元素应该插入的位置之后的所有元素往后移一位。当你要删除一个元素的时候也是同样的道理。在删除了下标为 i 的元素之后，还需要把下标 i 之后的所有元素往前移一位。因此，这种做法并不会比方法一更好。

为了能让算法的效率得到真正的提升，我们需要引入一个支持 插入，搜索，删除 操作的 **动态** 数据结构，那就是自平衡二叉搜索树。

下面给出整个算法的伪代码：

  - 初始化一颗空的二叉搜索树 set
  - 对于每个元素 x，遍历整个数组
    - 在 set 上查找大于等于 x 的最小的数，如果 `s−x≤t ` 则返回 true
    - 在 set 上查找小于等于 x 的最大的数，如果 `x−g≤t ` 则返回 true
    - 在 set 中插入 x
    - 如果树的大小超过了 k, 则移除最早加入树的那个数。
  - 返回 false

```js
  var containsNearbyAlmostDuplicate = function (nums, k, t) {
    const len = nums.length;
    const tree = new BinarySearchTree();
    for (let i = 0; i < len; i++) {
      const ceil = tree.ceiling(nums[i]);
      if (ceil != null && ceil <= nums[i] + t) return true;

      const floor = tree.floor(nums[i]);
      if (floor != null && nums[i] <= floor + t) return true;

      tree.insert(nums[i]);
      if (tree.size() > k) tree.remove(nums[i - k]);
    }
    return false;
  };
  let nums = [1, 5, 9, 1, 5, 9],
    k = 2,
    t = 3;
  console.log(containsNearbyDuplicate(nums, k));
```

### 桶

回到这个问题，我们尝试去解决的最大的问题在于：

  1. 对于给定的元素 x*x*, 在窗口中是否有存在区间 [x-t, x+t] 内的元素？
  2. 我们能在常量时间内完成以上判断嘛？

我们不妨把把每个元素当做一个人的生日来考虑一下吧。假设你是班上新来的一位学生，你的生日在 三月 的某一天，你想知道班上是否有人生日跟你生日在 t=30 天以内。在这里我们先假设每个月都是 30 天，很明显，我们只需要检查所有生日在 二月，三月，四月 的同学就可以了。

之所以能这么做的原因在于，我们知道每个人的生日都属于一个桶，我们把这个桶称作月份！每个桶所包含的区间范围都是 t，这能极大的简化我们的问题。很显然，任何不在同一个桶或相邻桶的两个元素之间的距离一定是大于 t 的。

我们把上面提到的桶的思想应用到这个问题里面来，我们设计一些桶，让他们分别包含区间 ..., `[0,t]`, `[t+1, 2t+1]` ,..., 我们把桶来当做窗口，于是每次我们只需要检查 x 所属的那个桶和相邻桶中的元素就可以了。终于，我们可以 **在常量时间解决在窗口中搜索的问题** 了。

还有一件值得注意的事，这个问题和桶排序的不同之处在于每次我们的桶里只需要 **包含最多一个元素** 就可以了，因为如果任意一个桶中包含了两个元素，那么这也就是意味着这两个元素是 足够接近的 了，这时候我们就直接得到答案了。因此，我们只需使用一个标签为桶序号的散列表就可以了。

```js
  var containsNearbyAlmostDuplicate = function (nums, k, t) {
    if (t < 0) return false;
    const map = {};
    // 防止0造成的infinity
    const bucketSize = t + 1;
    for (let i = 0; i < nums.length; i++) {
      const bucketID = getID(nums[i], bucketSize);
      if (
        map[bucketID] !== undefined &&
        Math.abs(nums[i] - map[bucketID]) <= t
      ) {
        return true;
      }
      if (
        map[bucketID - 1] !== undefined &&
        Math.abs(nums[i] - map[bucketID - 1]) <= t
      ) {
        return true;
      }
      if (
        map[bucketID + 1] !== undefined &&
        Math.abs(nums[i] - map[bucketID + 1]) <= t
      ) {
        return true;
      }
      map[bucketID] = nums[i];
      if (i > k - 1) {
        const removeID = getID(nums[i - k], bucketSize);
        map[removeID] = undefined;
      }
    }
    function getID(x, w) {
      return Math.floor(x / w);
    }
    return false;
  };
```

## [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

### 转化为低阶动态规划

对字符串进行去重，去重之后找到最长公共子串即是

不可以，去重之后就变成子序列了，而不是子串

### 暴力法

暴力循环所有子串，然后判断是不是包含重复字符，并且更新相应的最长长度

### 滑动窗口

在暴力法中，我们会反复检查一个子字符串是否含有有重复的字符，但这是没有必要的。如果从索引 i 到 j - 1 之间的子字符串 $s_{ij}$ 已经被检查为没有重复字符。我们只需要检查 s[j] 对应的字符是否已经存在于子字符串 $s_{ij}$ 中。

要检查一个字符是否已经在子字符串中，我们可以检查整个子字符串，这将产生一个复杂度为 `O(n^2)` 的算法，但我们可以做得更好。

通过使用 HashSet 作为滑动窗口，我们可以用 O\*(1) 的时间来完成对字符是否在当前的子字符串中的检查。

我们使用 HashSet 将字符存储在当前窗口 ` [i, j)`（最初 j = i）中。 然后我们向右侧滑动索引 j，如果它不在 HashSet 中，我们会继续滑动 j。直到 `s[j]` 已经存在于 HashSet 中。此时，我们找到的没有重复字符的最长子字符串将会以索引 i 开头。如果我们对所有的 i 这样做，就可以得到答案。

```js
  var lengthOfLongestSubstring = function (s) {
    const set = new Set();
    let max = 0;
    for (let i = 0; i < s.length; i++) {
      for (let j = i; j < s.length; j++) {
        // 遇到了重复的字符串 break
        if (set.has(s[j])) break;
        else set.add(s[j]);
      }
      // 跳出了内循环，更新长度
      max = Math.max(max, set.size);
      set.clear();
    }
    return max;
  };
```

**复杂度分析**

时间复杂度：O(2n)=O(n)，在最糟糕的情况下，每个字符将被 i 和 j 访问两次。

空间复杂度：O(min(m,n))，与之前的方法相同。滑动窗口法需要 O(k) 的空间，其中 k 表示 Set 的大小。而 Set 的大小取决于字符串 nn 的大小以及字符集 / 字母 m 的大小。

### 优化的滑动窗口

上述的方法最多需要执行 2n 个步骤。事实上，它可以被进一步优化为仅需要 n 个步骤。我们可以定义字符到索引的映射，而不是使用集合来判断一个字符是否存在。 当我们找到重复的字符时，我们可以立即跳过该窗口。

也就是说，如果 `s[j]` 在 `[i, j)` 范围内有与 索引 `j'` 重复的字符，我们不需要逐渐增加 `i` 。 我们可以直接跳过 `[i，j']` 范围内的所有元素，并将 `i` 变为 `j'+1`。

因为以 `[i, j')` 范围内每一个字符开头的最长的子串，都会在 `j` 处与 `j'` 重复，所以我们直接从 `j'+1` 开始即可

```js
  var lengthOfLongestSubstring = function (s) {
    const map = new Map();
    let max = 0;
    for (let i = 0, j = 0; j < s.length; j++) {
      if (map.has(s[j])) {
        // 有重复时 i 从 j'+1 处开始
        i = Math.max(map.get(s[j]) + 1, i);
      }
      // 如果重复了也要更新s[j]的值，所以在判断外执行该语句
      map.set(s[j], j);
      max = Math.max(max, j - i + 1);
    }
    return max;
  };
```

## [438. 找到字符串中所有字母异位词](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

### 固定窗口法

维护一个子串大小的窗口，判断窗口内的子串是否是异位词

虽然说是异位词，但是在本题中，相同的也 ok

```js
  var findAnagrams = function (s, p) {
    const len = s.length,
      k = p.length;
    const res = [];
    // p的哈希表
    const pMap = getMap(p);
	
    for (let i = 0, j = i + k; j < len + 1; i++, j++) {
      const sub = s.slice(i, j);
      const subMap = getMap(sub);
      let match = true;
      for (const [key, val] of Object.entries(pMap)) {
        // 不用判断hasOwn，如果没有则是undefined
        if (subMap[key] !== val) {
          match = false;
          break;
        }
      }
      match && res.push(i);
    }
    return res;
	
    function getMap(str) {
      const map = {};
      for (let i = 0; i < str.length; i++) {
        const key = str[i].charCodeAt();
        if (map.hasOwnProperty(key)) map[key]++;
        else map[key] = 1;
      }
      return map;
    }
  };
  let s = "cbaebabacd",
    p = "abc";
  console.log(findAnagrams(s, p));
  ```

### 判断优化

滑动窗口模板如此，优化的地方在于是否符合题意得判断

不用每一次都重新生成一个新的哈希表，只需要改变上一次字符，换成新的字符，也没有必要用 chaCodeAt

```js
  var findAnagrams = function (s, p) {
    const len = s.length,
      k = p.length;
    const res = [];
    // p的哈希表
    const pMap = getMap(p);
    const subMap = getMap(s.slice(0, k));
    for (let i = 0, j = i + k; j < len + 1; i++, j++) {
      let match = true;
      for (const [key, val] of Object.entries(pMap)) {
        // 不用判断hasOwn，如果没有则是undefined
        if (subMap[key] !== val) {
          match = false;
          break;
        }
      }
      match && res.push(i);
      // 令map[s[i]]=undefined，map[s[j+1]]++
      subMap[s[i]]--;
      if (subMap[s[j]] === 0 || subMap[s[j]] === undefined) subMap[s[j]] = 1;
      else subMap[s[j]]++;
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
  };
  let s = "cbaebabacd",
    p = "abc";
  console.log(findAnagrams(s, p));
```

### Match 进一步优化

```js
  var findAnagrams = function (s, p) {
    const len = s.length,
      k = p.length;
    const res = [];
    // p的哈希表
    const needs = getMap(p);
    // initial match
    let match = 0;
    const sub = s.slice(0, k);
    const subMap = {};
    for (let i = 0; i < sub.length; i++) {
      const char = sub[i];
      if (needs.hasOwnProperty(char)) {
        subMap[char] === undefined ? (subMap[char] = 1) : subMap[char]++;
        if (subMap[char] === needs[char]) match++;
      }
    }
    for (let i = 0, j = i + k; j <= len; i++, j++) {
      if (match === needs.length) res.push(i);
      const charI = s[i];
	
      if (needs.hasOwnProperty(charI)) {
        subMap[charI]--;
        if (subMap[charI] !== needs[charI] && match > 0) match--;
      }
      const charJ = s[j];
      if (needs.hasOwnProperty(charJ)) {
        subMap[charJ] === undefined ? (subMap[charJ] = 1) : subMap[charJ]++;
        if (subMap[charJ] === needs[charJ]) match++;
      }
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
```

### 滑动窗口法

```js
  var findAnagrams = function (s, p) {
    const len = s.length;
    // p的哈希表
    const needs = getMap(p);
    const subMap = {};
    let left = 0,
      right = 0;
    let res = [];
    let match = 0; // 记录已经匹配needs的长度
    while (right < len) {
      const char = s[right];
      if (needs.hasOwnProperty(char)) {
        subMap[char] === undefined ? (subMap[char] = 1) : subMap[char]++;
        // 等于，在等于的那一刻match++
        if (subMap[char] === needs[char]) match++;
      }
      // 不可以等于
      while (match === needs.length && left < len) {
        // 子串的长度相符，添加left
        if (right - left + 1 === p.length) res.push(left);
        const char = s[left];
        // 再次判断是否符合
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
```

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

# 判圈算法

## 数组去重思路

## Flyod 龟兔算法

- 对于赛道来说，如果赛道中有环，那么速度快的兔子一定会在某个地点追上乌龟，并且兔子所跑的距离减去乌龟所跑的距离，一定是环长度的整数倍。
- 快慢指针

### 原理

假设令龟、兔为指针，并且指向起点位置，兔子每次移动两个节点，乌龟每次移动一个节点。如果两者在起始节点外相遇，则说明有环。如果兔子在走到链表尾部还没有与乌龟相遇，说明无环。

### 环长度

通过上述算法判断出存在环 C 时，显然龟兔位于同一节点 B，此时，令兔子保持不动，而乌龟不断推进，记录移动距离，等再次相遇时，移动步数即环 C 长度。

### 环起点

乌龟所跑距离为 `S = m + n + aC`（C 为环长度),

兔子所跑距离为 `2S = m + n + bC`（因为兔子速度为乌龟 `2` 倍）

故得 `S = (b - a)C = m + n + aC ==> (b - 2a)C = m + n`, 由此可知，`m + n` 为环 `C` 长度整数倍

让兔子回到起点 H，乌龟停留在相遇点 B，随后，同时让乌龟与兔子不断推进一步，兔子移动 m 到达点 A，距离点 B 还有 n，乌龟移动 m 也会到达点 A，再走 n 就饶了 `(b-2a)` 圈，到达点 B

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

## [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

  ![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

### 哈希表

- 储存每个节点的引用，用 map 来判断是否重复
- 复杂度 O(n)

### 判圈算法

## [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)

### 哈希表

- 见二分查找

### 二分查找

### 判圈算法

- 因为 nums 中的每个数字都在 1 和 n 之间，所以它必须指向存在的索引。此外，由于 0 不能作为 nums 中的值出现，nums[0] 不能作为循环的一部分。
- 由于存在重复数，所以形成了圈

# 间距指针

## [19. 删除链表的倒数第 N 个节点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

### 使用哑结点优化

哑结点消除了对头节点的讨论，在链表归并中也有相同的应用

### 双指针

我们可以使用两个指针而不是一个指针。第一个指针从列表的开头向前移动 n+1 步，而第二个指针将从列表的开头出发。

现在，这两个指针被 n 个结点分开。

我们通过 **同时移动两个指针向前来保持这个恒定的间隔**，直到第一个指针到达最后一个结点。

此时第二个指针将指向从最后一个结点数起的第 n 个结点。我们重新链接第二个指针所引用的结点的 next 指针指向该结点的下下个结点

```js
  function ListNode(val) {
    this.val = val;
    this.next = null;
  }
  var removeNthFromEnd = function (head, n) {
    const dummyHead = new ListNode(0);
    dummyHead.next = head;
    let first = head,
      second = head,
      prev = dummyHead;
    // 倒数第n个节点与尾节点后的null间隔了 n - 1 个
    for (let i = 1; i <= n; i++) {
      first = first.next;
    }
    while (first !== null) {
      first = first.next;
      prev = second;
      second = second.next;
    }
    // 此时first指向尾节点后的null,second指向倒数第n个节点
    prev.next = second.next;
    return dummyHead.next;
  };
  let head = { val: 1, next: { val: 2, next: null } };
  console.log(removeNthFromEnd(head, 1));
```
