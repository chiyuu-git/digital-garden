---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/search/"}
---


# 搜索算法

## 二分查找算法

+ 二分搜索算法的原理和猜数字游戏类似，就是那个有人说 " 我正想着一个 1 到 100 的数字 " 的游戏。我们每回应一个数字，那个人就会说这个数字是高了、低了还是对了。
+ 这个算法要求被搜索的数据结构已排序，以下是该算法遵循的步骤：

  - 选择数组的中间值
  - 如果选中值是待搜索值，算法执行完毕（值找到了）
  - 如果待搜索值比选中值要小，则返回步骤 1 并在选中值左边的子数组中寻找
  - 如果待搜索值比选中值要大，则返回步骤 1 并在选种值右边的子数组中寻找

### 特点

+ 排序的数组
+ 查找的次数少

### [704. 二分查找](https://leetcode-cn.com/problems/binary-search/)

#### 题目

+ 给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

  ```
  输入: nums = [-1,0,3,5,9,12], target = 9
  输出: 4
  解释: 9 出现在 nums 中并且下标为 4
  ```

#### 实现

+ ```js
  var searchInsert = function(nums, target) {
    let left = 0
    let right = nums.length-1 //3
    let mid = 0
    let out = 0
    while(left<=right){ // 注意是等于
        mid = Math.floor((left+right)/2) // 1 2 3
        if(nums[mid]==target) return mid
        if(nums[mid]<target){
            left = mid+1
        }else{
            right = mid-1
        }
    }
    return -1
  };
  ```
+ 循环结束都没有找到，返回 `-1`
+ 图展示了二分搜索算法的执行过程：

  ![img](search/968d79444c32475772c0b0d1859a6334)

+ 我们实现的 BinarySearchTree 类有一个 search 方法，和这个二分搜索完全一样，只不过它是针对树数据结构的。

### 优化

+ ```js
  const mid = left + Math.floor((right - left) / 2); 
  ```
+ 当 left 和 right 是很大的整数的时候，如果写 int mid = (left + right) / 2; 这里 left + right 的值就有可能超过 int 类型能表示的最大值，因此使用 mid = left + (right - left) // 2 可以避免这种情况。
+ 事实上，mid = left + (right - left) // 2 在 right 很大、 left 是负数且很小的时候， right - left 也有可能超过 int 类型能表示的最大值，只不过一般情况下 left 和 right 表示的是数组索引值，left 是非负数，因此 right - left 溢出的可能性很小。
+ 在这里补充一个小细节：除以 2 这件事情，还可以用右移 1 位来代替，位移运算的效率更高些。具体来说是这样：

  1、Python 代码

  ```python
  mid = left + (right - left) // 2
  ```

  可以替换成：

  ```python
  mid = (left + right) >> 1
  ```

  2、Java 代码

  ```java
  int mid = l + (r - l) / 2;            
  ```

  可以替换成：

  ```java
  int mid = (left + right) >>> 1;
  ```

+ 注意：Java 中要使用 `>>>` 表示无符号右移，有符号位移是不行的。
+ 你没有看错，请注意，我现在又告诉你取 `(left + right)` 的一半，但不是除以 2 ，而是无符号右移一位，理由如下：

  1. `left` 和 `right` 一般表示数组或列表的索引，它们都是正数；
  2. 即使 `left` 和 `right` 都很大，对于 Java 语言来说，`(left + right)` 可能发生整型溢出，但是无符号右移一位仍然能够得到正确的结果，而 Python 整型溢出了以后，类型自动升为 long 类型。

+ 所以，在理解的基础上记住这个结论：取中位数用“+” 和“无符号右移”更优，事实上，Java 的 JDK 就是这么做的。

### [35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)

#### 题目

+ 给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
+ 你可以假设数组中无重复元素。

  ```
  输入: [1,3,5,6], 5
  输出: 2
  ```

#### 实现

- ```js
  var searchInsert = function(nums, target) {
    let left = 0
    let right = nums.length-1 //3
    let mid = 0
    let out = 0
    while(left<=right){ // 注意是等于
        mid = Math.floor((left+right)/2) // 1 2 3
        if(nums[mid]==target) return mid
        if(nums[mid]<target){
            left = mid+1
        }else{
            right = mid-1
        }
    }
    return left
  };
  ```
- 因为没有负数索引，而索引可是大于长度，所以返回 left，代表应该插入的位置，其实返回 right 也可以，只是最小的需要插入到 -1

#### 分析

+ 分析：根据题意并结合题目给出的 4 个示例，不难分析出这个问题的等价表述如下：

  1. 如果目标值（严格）大于排序数组的最后一个数，返回这个排序数组的长度，否则进入第 2 点。
  2. 返回排序数组从左到右，大于或者等于目标值的第 1 个数的索引。

+ 事实上，当给出数组中有很多数和目标值相等的时候，我们返回任意一个与之相等的数的索引值都可以，不过为了简单起见，也为了方便后面的说明，我们返回第 1 个符合题意的数的索引。
+ 题目告诉你“排序数组”，其实就是在疯狂暗示你用二分查找法。 二分查找法的思想并不难，但写好一个二分法并不简单，下面就借着这道题为大家做一个总结。
+ 注意：一定得返回左边界 left，
+ 如果返回右边界 right 提交代码不会通过

  ```
  理由是对于 [1,3,5,6]，target = 2，返回大于等于 target 的第 1 个数的索引，此时应该返回 1
  在上面的 while (left <= right) 退出循环以后，right < left，right = 0 ，left = 1
  根据题意应该返回 left，
  如果题目要求你返回小于等于 target 的所有数里最大的那个索引值，应该返回 right
  ```

+ 选择一个最简单的例子，决定返回 left 还是 right 即可

#### 传统二分查找法模板

+ 刚接触二分查找法的时候，使用上面的模板，我们可能会像下面这样写代码，我把这种写法容易出错的地方写在了注释里：

  ```java
  public int searchInsert(int[] nums, int target) {
      int len = nums.length;
      if (nums[len - 1] < target) {
          return len;
      }
      int left = 0;
      int right = len - 1;
  
      while (left <= right) {
          int mid = (left + right) / 2;
          // 等于的情况最简单，我们应该放在第 1 个分支进行判断
          if (nums[mid] == target) {
              return mid;
          } else if (nums[mid] < target) {
              // 题目要我们返回大于或者等于目标值的第 1 个数的索引
              // 此时 mid 一定不是所求的左边界，
              // 此时左边界更新为 mid + 1
              left = mid + 1;
          } else {
              // 既然不会等于，此时 nums[mid] > target
              // mid 也一定不是所求的右边界
              // 此时右边界更新为 mid - 1
              right = mid - 1;
          }
      }
      // 注意：一定得返回左边界 left，
      // 如果返回右边界 right 提交代码不会通过
      // 【注意】下面我尝试说明一下理由，如果你不太理解下面我说的，那是我表达的问题
      // 但我建议你不要纠结这个问题，因为我将要介绍的二分查找法模板，可以避免对返回 left 和 right 的讨论
  
      // 理由是对于 [1,3,5,6]，target = 2，返回大于等于 target 的第 1 个数的索引，此时应该返回 1
      // 在上面的 while (left <= right) 退出循环以后，right < left，right = 0 ，left = 1
      // 根据题意应该返回 left，
      // 如果题目要求你返回小于等于 target 的所有数里最大的那个索引值，应该返回 right
  
      return left;
  }
  ```

#### 说明

+ 当你把二分查找法的循环可以进行的条件写成 while (left <= right) 的话，在写最后一句 return 的时候，如果你不假思索，把左边界 left 返回回去，你写对了，但为什么不返回右边界 right 呢？
+ 但是事实上，返回 left 是有一定道理的，如果题目换一种问法，你可能就要返回右边界 right，这句话不太理解没有关系，我也不打算讲得很清楚（在上面代码的注释中我已经解释了原因），因为实在太绕了，这不是我要说的重点。
+ **可见**：传统二分查找法模板，当退出 while 循环的时候，在返回左边界还是右边界这个问题上，比较容易出错。
+ 那么，是不是可以回避这个问题呢？答案是肯定的，答案就在下面我要介绍的“神奇的”二分查找法模板里。

#### “神奇的”二分查找法模板

+ 二分查找法的思想：二分查找法的思想是“夹逼”，或者说“排除”，而“二分”只是手段，即“通过二分排除了候选区间的一大半的非目标元素”。具体说来，如下：
+ 在每一轮循环中，都可以排除候选区间里将近一半的元素，进而使得候选区间越来越小，直至有限个数（通常为 1 个），而这个数就有可能是我们要找的数（在一些情况下，还需要单独做判断）。
+ 在一些资料中，你可能看过别人写二分查找法，把循环可以进行的条件写成 `while (left < right) `，当时你是不是跟我一样有疑问：“咦？当左右边界一样的时候，那个数岂不是会被漏掉”。但是我要告诉你，这样写在绝大多数情况下是最好的，这也是“神奇的”二分查找法模板好用的一部分。
+ 理由很简单：写 `while (left < right)` 的时候，退出循环时，左边界等于右边界，因此你不必纠结要返回 left 还是 right ，此时返回 left 或者 right 都是可以的。
+ 回到这一节最开始的疑问：“区间左右边界相等（即收缩成 1 个数）时，这个数是否会漏掉”，解释如下：

  1. 如果你的业务逻辑保证了你要找的数一定在左边界和右边界所表示的区间里出现，那么可以放心地返回 l 或者 r，而无需再做判断；即使这个数 `nums[left]&&nums[right]` 没有经过 while 循环内的判断，很多时候这个数就是正确的答案
  2. 如果你的业务逻辑不能保证你要找的数一定在左边界和右边界所表示的区间里出现，那么只要在退出循环以后，再针对 nums[left] 或者 nums[right] （此时 nums[left] == nums[right]）单独作一次判断，看它是不是你要找的数即可，这一步操作常常叫做“后处理”。

+ ```java
  public class Solution {
    public int searchInsert(int[] nums, int target) {
      int len = nums.length;
  
      if (len == 0) {
        return 0;
      }
      if (target > nums[-1]) return size
  
      int left = 0
      int right = len-1
  
      while (left < right) {
        int mid = (left + right) >>> 1
        if (nums[mid] < target) {
          left = mid + 1
        } else {
          right = mid 
        }
      }
      return left
    }
  }
  ```

### 模板总结

1. 确定范围区间，一般就是 `[0,len-1]`，思考边界特例
2. 循环条件 `left<right`
3. 默认取左中位数 ` mid = (left+right)>>>1`
4. 分支逻辑先写可以排除中位数的逻辑，另一个分支无需排除中位数

   ```js
   if (nums[mid] < target) {
     // 排除中位数
     left = mid + 1
   } else {
     right = mid 
   }
   ```

5. 确认中位数的选择

   > 排除中位数的逻辑是 `left`，选择左中位数，可以被排除，不会进入死循环
   >
   > 排除中位数的逻辑是 `right`，选择右中位数，可以被排除，不会进入死循环

6. 退出循环，视情况判断 `left&&right` 是否符合题意
7. 返回 `left&&right`

### [69. x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

#### 题目

+ 实现 int sqrt(int x) 函数。

  计算并返回 x 的平方根，其中 x 是非负整数。

  由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

  ```
  输入: 8
  输出: 2
  说明: 8 的平方根是 2.82842..., 
       由于返回类型是整数，小数部分将被舍去。
  ```

#### 实现

+ ```js
  /**
   * @param {number} x
   * @return {number}
   */
  var mySqrt = function(x) {
    if(x<=1) return x
      let ans = 0
    let left=1,right=x
    let mid = 0
    while(left<=right){
      mid = (left+right) >>> 1
      if(mid**2 <= x){
        // 要增大
        left = mid + 1
        ans = mid
      }else{
        // 要变小
        right = mid -1
      }
    }
    return ans
  }
  ```
+ 根据经验 `right=x>>>1`

#### 模板实现

+ ```js
  var mySqrt = function(x) {
    if(x==0) return 0
    // 区间是1开始，要考虑特例0
    let left=1,right=x>>>1
    let mid = 0
    while(left<right){
      mid = (left+right+1) >>> 1
      if(mid**2 > x){
        // 要变小，右，选择右中位数
        right = mid -1
      }else{
        // 要增大
        left = mid
      }
    }
    return left || right
  }
  ```

### [153. 寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

#### 问题

+ 假设按照升序排序的数组在预先未知的某个点上进行了旋转。
+ ( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。
+ 请找出其中最小的元素。
+ 你可以假设数组中不存在重复元素。

  ```
  let nums = [3,4,5,1,2]
  输出: 1
  ```

#### 简单实现

+ 一遍遍历，直接使用内置的 `Math.min()` 函数
+ 利用旋转序列的特性，一直升序，在改变的一刻，即是最小值

  ```java
  class Solution {
  public:
      int findMin(vector<int>& nums) {
          int i=0,n=nums.size()-1;
          while(i<n){
              if(nums[i+1]>=nums[i])
                  i++;
              else
                  return nums[i+1];
          }
          return nums[0];
      }
  };
  ```

+ 时间复杂度都是 O(n)

#### 分析

+ 旋转过后的数组存在一个旋转点：
  + 所有旋转点左侧元素 > 数组第一个元素
  + 所有旋转点右侧元素 < 数组第一个元素
  + 如果旋转点在数组的两端，数组依然是有序的

1. 找到数组的中间元素 mid：
2. 如果中间元素 > 数组第一个元素，我们需要在 mid 右边搜索变化点。
3. 如果中间元素 < 数组第一个元素，我们需要在 mid 左边搜索变化点。

   ![153-4.png](https://pic.leetcode-cn.com/22494c6003c1718d8ea1e655b9bdb98e5256f884129bca2430d9611b325faee5-153-4.png)

   > 上面的例子中，中间元素 `6` 比第一个元素 `4` 大，因此在中间点右侧继续搜索。

4. 当我们找到变化点时停止搜索，当以下条件满足任意一个即可：

   + `nums[mid] > nums[mid + 1]`，此时 `mid+1 ` 是最小值。
   + `nums[mid - 1] > nums[mid]` 此时 `mid ` 是最小值。

#### 实现

+ ```java
  /**
   * @param {number[]} nums
   * @return {number}
   */
  var findMin = function(nums) {
    const len = nums.length
    if(len===0) return nums[0]
    let left = 0,right = len -1
    if (nums[right] > nums[0]) {
      return nums[0];
    }
    while(left<=right){
      const mid = (left+right)>>>1
      if(nums[mid] > nums[mid + 1]) return nums[mid+1]
      if(nums[mid - 1] > nums[mid]) return nums[mid]
      if(nums[mid]>nums[0]) left = mid + 1
      if(nums[mid]<nums[0]) right = mid - 1
    }
    return -1
  };
  
  let nums = [3, 4, 5, 1, 2]
  console.log(findMin(nums))
  ```

#### 模板实现

+ ```js
  var findMin = function (nums) {
    const len = nums.length
    if (len <= 1) return nums[0]
    let left = 0, right = len - 1
    // 旋转没有影响顺序
    if (nums[right] > nums[0]) {
      return nums[0];
    }
    while (left < right) {
      const mid = (left + right) >>> 1
      if (nums[mid] > nums[right]){
        left = mid + 1
      }else{
        right = mid
      }
    }
    return nums[left]
  };
  ```
+ 这题的难点在于找准排除中位数的逻辑，和 `nums[0],nums[len-1],nums[left],nums[right]` 哪一个作比较才可以保证排除中位数

### [154. 寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

#### 区别

+ 注意数组中可能存在重复的元素。

  ```
  let nums = [2,2,2,0,1]
  输出: 0
  ```

#### 模板实现

+ ```js
  var findMin = function(nums) {
    const len = nums.length
    if(len===1) return nums[0]
    let left = 0 , right = len -1 
    while(left<right){
      const mid = (left+right)>>>1
      if(nums[mid]>nums[right]){
        left = mid + 1
      }else if(nums[mid]<nums[right]){
        right = mid
      }else{
        // [3,3,3,1,3,3]
        right = right - 1
      }
    }
    return nums[left]
  };
  ```

### [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

#### 题目

- 假设按照升序排序的数组在预先未知的某个点上进行了旋转。

  ( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

- 搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。
- 你可以假设数组中不存在重复的元素。
- 你的算法时间复杂度必须是 O(log n) 级别。

  ```
  输入: nums = [4,5,6,7,0,1,2], target = 0
  输出: 4
  
  输入: nums = [4,5,6,7,0,1,2], target = 3
  输出: -1
  ```

#### 分析

- 有点像二分搜索
- 先进行一次二分搜索找到旋转点，然后判断应该在左半边还是右半边，再进行一次二分查找

  ```js
  
  ```

### [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)

#### 题目

+ 给定一个包含 n + 1 个整数的数组 nums，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。

  ```
  let nums = [1,3,4,2,2]
  输出: 2
  ```

#### 简单实现

+ 使用数组去重的思路

  ```js
  var findDuplicate = function(nums) {
    const len = nums.length
    const obj = {}
  	for(let i=0;i<len;i++){
      if(obj[val]===undefined){
        obj[val] = 1
      }else{
        return val
      }
    }
  }
  ```

+ 排序后比对后一个数

#### 分析

+ 关键：这道题的关键是对要定位的“数”做二分，而不是对数组的索引做二分。要定位的“数”根据题意在 1 和 n 之间，每一次二分都可以将搜索区间缩小一半。
+ 以 [1, 2, 2, 3, 4, 5, 6, 7] 为例，一共有 8 个数，每个数都在 1 和 7 之间。1 和 7 的中位数是 4，遍历整个数组，统计小于 4 的整数的个数，至多应该为 3 个，
  + 如果超过 3 个就说明重复的数存在于区间 [1,4) （注意：左闭右开）中；
  + 否则，重复的数存在于区间 [4,7]（注意：左右都是闭）中。
+ 这里小于 4 的整数有 4 个（它们是 1, 2, 2, 3），因此砍掉右半区间，连中位数也砍掉。
+ 以此类推，最后区间越来越小，直到变成 1 个整数，这个整数就是我们要找的重复的数。

#### 实现

+ ```js
  var findDuplicate = function(nums) {
    const len = nums.length
    let min=1,max=len-1
    while(min<max){
      const mid =(min+max)>>>1
      let counter = 0
      for (let i = 0; i < len; i++) {
        if(nums[i]<=mid){
          counter++
        }
      }
      if(counter>mid){
        max = mid
      }else{
        min = mid + 1
      }
    }
    return min
  }
  
  let nums = [1, 2,  3, 4, 5, 6,6, 7]
  console.log(findDuplicate(nums))
  ```

### [1095. 山脉数组中查找目标值](https://leetcode-cn.com/problems/find-in-mountain-array/)

#### 题目

+ 给你一个 **山脉数组** `mountainArr`，请你返回能够使得 `mountainArr.get(index)` **等于** `target` ，且使得其下标 `index` 值**最小**
+ 如果不存在这样的下标 `index`，就请返回 `-1`。
+ 你将 不能直接访问该山脉数组，必须通过 MountainArray 接口来获取数据：
  + MountainArray.get(k) - 会返回数组中索引为 k 的元素（下标从 0 开始）
  + MountainArray.length() - 会返回该数组的长度

#### 分析

+ 理解“山脉数组”，“山脉数组”可以根据山顶分为两部分，“前有序数组”是升序数组，“后有序数组”是降序数组
+ 题目还告诉我们“对 MountainArray.get 发起超过 100 次调用的提交将被视为错误答案”，就在疯狂暗示你使用时间复杂度低的算法，对于有序数组当然使用“二分查找法”。
+ 第 1 步：先找到山顶元素 mountaintop 所在的索引。
+ 第 2 步：在前有序且升序数组中找 target 所在的索引，如果找到了，就返回，如果没有找到，就执行第 3 步；
+ 第 3 步：如果步骤 2 找不到，就在后有序且降序数组中找 target 所在的索引。

#### 实现

+ ```js
  /**
   * // This is the MountainArray's API interface.
   * // You should not implement it, or speculate about its implementation
   * function MountainArray() {
   *
   *     @param {integer} index
   *     @return {integer}
   *     this.get = function(index) {
   *         ...
   *     };
   *
   *     @return {integer}
   *     this.length = function() {
   *         ...
   *     };
   * };
   */
  /**
   * @param {number} target
   * @param {MountainArray} mountainArr
   * @return {number}
   */
  var findInMountainArray = function (target, mountainArr) {
    const len = mountainArr.length()
    // 步骤 1：先找到山顶元素所在的索引
    const peakIndex = findInMountainArrayPeak(mountainArr, 0, len - 1)
    // 步骤 2：在前有序且升序数组中找 target 所在的索引
    const targetLeft = findIncrease(target,mountainArr,0,peakIndex)
    if(targetLeft!==-1) return targetLeft
    // 步骤 3：如果步骤 2 找不到，就在后有序且降序数组中找 target 所在的索引
    const targetRight = findDecrease(target,mountainArr,peakIndex+1,len-1)
  
    return targetRight
      
    function findInMountainArrayPeak(mountainArr, left, right) {
      while (left < right) {
        const mid = (left + right) >>> 1
        if (mountainArr.get(mid) < mountainArr.get(mid + 1)) {
          // 如果当前的数比右边的数小，它一定不是山顶
          left = mid + 1;
        } else {
          right = mid;
        }
      }
      return left
    }
    function findIncrease(target,mountainArr,left,right){
      while(left<right){
        const mid = (left+right)>>>1
        if(mountainArr.get(mid) < target){
          left = mid+1
        }else{
          right = mid
        }
      }
      return mountainArr.get(left)===target?left:-1
    }
    function findDecrease(target,mountainArr,left,right){
      while(left<right){
        const mid = (left+right)>>>1
        if(mountainArr.get(mid) > target){
          left = mid+1
        }else{
          right = mid
        }
      }
      return mountainArr.get(left)===target?left:-1
    }
  }
  ```
+ ![1563410728759](/img/user/programming/basic/algorithm/search/1563410728759.png)

### [658. 找到 K 个最接近的元素](https://leetcode-cn.com/problems/find-k-closest-elements/)

#### 题目

+ 给定一个**排序好**的数组，两个整数 k 和 x，从数组中找到最靠近 x（两数之差最小）的 k 个数。返回的结果必须要是按升序排好的。如果有两个数与 x 的差值一样，优先选择数值较小的那个数。

  ```
  let nums = [1,2,3,4,5], k=4, x=3
  输出: [1,2,3,4]
  ```

#### 分析

+ 一个一个删，因为是有序数组，且返回的是连续升序子数组，所以每一次删除的元素一定是位于**边界**；
+ 一共 5 个元素，要保留 4 个元素，因此要删除 1 个元素；
+ 因为要删除的元素都位于边界，于是可以使用双指针对撞的方式确定保留区间，即“最优区间”。
+ 因为差值一样的时候优先选择较小的，所以优先删除**右边界**

#### 实现

+ ```js
  var findClosestElements = function(arr, k, x) {
    const len = arr.length
    let left = 0 , right = len - 1
    while(right-left>k-1){
      const diffL = x - arr[left]
      const diffR = arr[right] - x
      if(diffR>=diffL){
        right -=1
      }else{
        left +=1
      }
    }
    return arr.slice(left,right+1)
  };
  ```

#### **复杂度分析**

- 时间复杂度：O(N)，这里 N 是数组的长度。
- 空间复杂度：O(1)，只使用了常数个额外的辅助空间。

#### 二分法分析

+ 题目中说有序数组，又易知：

  1、题目要求返回的是区间，并且是连续区间；

  2、区间长度是固定的，并且 k 的值为正数，且总是小于给定排序数组的长度，即 k 的值“不违规”；

+ 因此，只要我们找到了左边界的索引，从左边界开始数 k 个数，返回就好了。我们把这件事情定义为“寻找最优区间”，“寻找最优区间”等价于“寻找最优区间的左边界”。因此本题使用二分查找法在有序数组中定位含有 k 个元素的连续子区间的左边界，即使用二分法找“最优区间的左边界”。

#### 二分法实现

+ ```java
  var findClosestElements = function(arr, k, x) {
    const len = arr.length
    let left = 0 , right = len - k
    while(left<right){
      const mid = (right+left)>>>1
      if(x-arr[mid]>arr[mid+k]-x){
        left = mid+1
      }else{
        right = mid
      }
    }
    return arr.slice(left,left+k)
  };
  ```

## 二叉树

## 哈希表
