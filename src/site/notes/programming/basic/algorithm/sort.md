---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/sort/"}
---


# 概述

```js
  function ArrayList(){ 
   
    var array = []; //{1} 
   
    this.insert = function(item){ //{2} 
      array.push(item); 
    }; 
   
    this.toString= function(){ //{3} 
      return array.join(); 
    }; 
  } 
```

注意 ArrayList 类并没有任何方法来移除数据或插入数据到特定位置。我们刻意保持简单是为了能够专注于排序和搜索算法。所有的排序和搜索算法会添加至这个类中。

# 冒泡排序

## 基本实现

比较**相邻**的元素。如果第一个比第二个大，就交换他们两个。元素项向上移动至正确的顺序，就好像气泡升至表面一样，冒泡排序因此得名。

  ```js
this.bubbleSort = function () {
    var length = array.length;           //{1} 
    for (var i = 0; i < length; i++) {        //{2} 
        for (var j = 0; j < length - 1; j++) { //{3} 
            if (array[j] > array[j + 1]) {  //{4} 
                swap(array, j, j + 1);      //{5} 
            }
        }
    }
};
  ```

外循环（行{2}）会从数组的第一位迭代至最后一位，它控制了在数组中经过多少轮排序（应该是数组中每项都经过一轮，轮数和数组长度一致）。

然后，内循环将从第一位迭代至倒数第二位，内循环实际上进行当前项和下一项的比较（行{4}）。如果这两项顺序不对（当前项比下一项大），则交换它们（行{5}），意思是位置为 j+1 的值将会被换置到位置 j 处

现在我们得声明 swap 函数（一个私有函数，只能用在 ArrayList 类的内部代码中）：

  ```js
var swap = function (array, index1, index2) {
    var aux = array[index1];
    array[index1] = array[index2];
    array[index2] = aux;
}; 
  ```

我们使用下列代码来测试

```js
  function createNonSortedArray(size){ //{6} 
    var array = new ArrayList(); 
    for (var i = size; i> 0; i--){ 
      array.insert(i); 
    } 
    return array; 
  } 
  
  var array = createNonSortedArray(5); //{7} 
  console.log(array.toString());       //{8} 
  array.bubbleSort();                  //{9} 
  console.log(array.toString());       //{10} 
```

下面这个示意图展示了冒泡排序的工作过程：

![1554720485478](/img/user/programming/basic/algorithm/sort/1554720485478.png)

该示意图中每一小段表示外循环的一轮（行{2}），而相邻两项的比较则是在内循环中进行的（行{3}）。

**注意**: 当算法执行外循环的第二轮的时候，数字 4 和 5 已经是正确排序的了。尽管如此，在后续比较中，它们还一直在进行着比较，即使这是不必要的。因此，我们可以稍稍改进一下冒泡排序算法。

## 优化实现

如果从内循环减去外循环中已跑过的轮数，就可以避免内循环中所有不必要的比较（行{1}）。

> 第一轮执行后，最大的数一定到达正确的位置，且不再改变
  >
> 第二轮执行后，第二大的数一定到达正确的位置，且不再改变

  ```js
this.modifiedBubbleSort = function () {
    var length = array.length;
    for (var i = 0; i < length; i++) {
        for (var j = 0; j < length - 1 - i; j++) { //{1} 
            if (array[j] > array[j + 1]) {
                swap(j, j + 1);
            }
        }
    }
}; 
  ```

下面这个示意图展示了改进后的冒泡排序算法是如何执行的：

![1554720947859](/img/user/programming/basic/algorithm/sort/1554720947859.png)

> 注意已经在正确位置上的数字没有被比较。即便我们做了这个小改变，改进了一下冒泡排序算法，但我们还是不推荐该算法，它的复杂度是 O(n^2)。

进一步优化，如果在一轮内循环中，没有发生过交换，说明数组已经是有序的，就可以直接退出了

```js
  Array.prototype.bubbleSort = function(){
    const array = this 
    const len = array.length; 
    let flag = false
    for (let i=0; i<len; i++){ 
      flag = true // 初始化标识
      for (let j=0; j<len-1-i; j++ ){ //{1} 
        if (array[j] > array[j+1]){ 
          swap(array,j, j+1); 
          flag = false // 发生了交换，更改标识
        } 
      } 
      // 本轮没有进行过交换，说明数组已经有序
      if(flag) return
    } 
  }
```

## 复杂度分析

最好情形：正序，仅需 n - 1 次比较

最坏情形：反序，需要平方级的比较和平方级的交换

https://blog.csdn.net/hansionz/article/details/80822494

# 选择排序

选择排序算法是一种**原址比较排序**算法。选择排序大致的思路是找到数据结构中的最小值并将其放置在第一位，接着找到第二小的值并将其放在第二位，以此类推。

基本思路：不断从剩余数组中选出最小的数，然后移到前面

## 基本实现

```js
  Array.prototype.selectionSort = function(){ 
    const array = this
    const len = array.length          
    let minIndex 
    for (let i=0; i<len-1; i++){       
      minIndex = i
      // 前面的已经有序，内循环从i开始                      
      for (let j=i; j<len; j++){ 
        // 找到 第i小的值 的索引   
        if(array[minIndex]>array[j]) minIndex = j
      }
      // 如果该最小值和原最小值不同，交换，成为第n小的值 
      if (i !== minIndex) swap(array,i, minIndex)
    }
  }
```

下面的示意图展示了选择排序算法，此例基于之前代码中所用的数组。

![1554722109017](/img/user/programming/basic/algorithm/sort/1554722109017.png)

数组底部的箭头指示出当前迭代轮寻找最小值的**数组范围**（内循环{4}），示意图中的每一步则表示外循环。

## 复杂度分析

O(n^2)。需要约 N^2 / 2 次比较，与最多 N - 1 次交换

运行时间与输入无关，给定 N 的情况下，无论何种输入所需时间均相等

数据交换量最小

# 插入排序

插入排序**每次排一个数组项**，以此方式构建最后的排序数组。

假定第一项已经排序了，接着，它和第二项进行比较，第二项是应该待在原位还是插到第一项之前呢？这样，头两项就已正确排序，接着和第三项比较（它是该插入到第一、第二还是第三的位置呢？），以此类推。

```js
Array.prototype.insertionSort = function () {
    const array = this
    const len = array.length
    // 从1而不是0开始的,我们认为第一项已排序了
    for (let i = 1; i < len; i++) {
        let j = i
        let temp = array[i] // 待插入的值
        // 和已排序的比较，寻找插入的位置
        while (j > 0 && array[j - 1] > temp) {
            // 较大的值，往后挪位
            array[j] = array[j - 1]
            j--
        }
        // 插入到正确的位置
        array[j] = temp
    }
}
```

只要变量 j 比 0 大并且数组中前面的值比**待比较**的值大（行{5}），我们就把这个值移到当前位置上（行{6}）并减小 j。最终，该项目能插入到正确的位置上

下面的示意图展示了一个插入排序的实例：

![1554722628123](/img/user/programming/basic/algorithm/sort/1554722628123.png)

![æå¥æåº](https://github.com/mqyqingfeng/Blog/raw/master/Images/sort/insertion.gif)

举个例子，假定待排序数组是 `[3, 5, 1, 4, 2]`。这些值将被插入排序算法按照下面形容的步骤进行排序。

1. 3 已被排序，所以我们从数组第二个值 5 开始。3 比 5 小，所以 5 待在原位（数组的第二位）。3 和 5 排序完毕。
2. 下一个待排序和插到正确位置上去的值是 1（目前在数组的第三位）。5 比 1 大，所以 5 被移至第三位去了。我们得分析 1 是否应该被插入到第二位——1 比 3 大吗？不，所以 3 被移到第二位去了。接着，我们得证明 1 应该插入到数组的第一位上。因为 0 是第一个位置且没有负数位，所以 1 必须被插入到第一位。1、3、5 三个数字已经排序。
3. 4 应该在当前位置（索引 3）还是要移动到索引较低的位置上呢？4 比 5 小，所以 5 移动到索引 3 位置上去。那么应该把 4 插到索引 2 的位置上去吗？4 要比 3 大，所以 4 插入到数组的位置 3 上。
4. 下一个待插入的数字是 2（数组的位置 4）。5 比 2 大，所以 5 移动至索引 4。4 比 2 大，所以 4 也得移动（位置 3）。3 也比 2 大，所以 3 还得移动。1 比 2 小，所以 2 插入到数组的第二位置上。至此，数组已排序完成。

> 此表中的问好，非常的关键，实际上表明了并没有真正的发生交换，而是仍在寻找合适的插入位置
  >
> 3 ？5 4 2 → 3 5 5 4 2
  >
> 从代码可知，之所以这么做，是因为 temp 的位置还未确定，此时交换就结果而言是多余的
>
> 这是 **插入排序** 插入二字的精髓，就好像打麻将一直用手拿着寻找合适的位置再插入

## 复杂度分析

排序小型数组时，此算法比选择排序和冒泡排序性能要好

# 归并排序

归并排序是第一个可以被实际使用的排序算法。你在本书中学到的前三个排序算法性能不好，但归并排序性能不错，其复杂度为 O(nlog^n)。

> JavaScript 的 Array 类定义了一个 sort 函数（Array.prototype.sort）用以排序 JavaScript 数组（我们不必自己实现这个算法）。ECMAScript 没有定义用哪个排序算法，所以浏览器厂商可以自行去实现算法。例如，Mozilla Firefox 使用归并排序作为 Array.prototype.sort 的实现，而 Chrome 使用了一个快速排序（下面我们会学习的）的变体。

归并排序是一种**分治算法**。其思想是将原始数组切分成较小的数组，直到每个小数组只有一个位置，接着将小数组归并成较大的数组，直到最后只有一个排序完毕的大数组。

由于是分治法，归并排序也是递归的：

```js
  Array.prototype.mergeSort = function(){
    const array = this 
    const result = mergeSortRec(array)
    array.splice(0,array.length)
    array.push(...result)
  } 
```

像之前的章节一样，每当要实现一个递归函数，我们都会实现一个实际被执行的辅助函数。对归并排序我们也会这么做。我们将声明 mergeSort 方法以供随后使用，而 mergeSort 方法将会调用 mergeSortRec，该函数是一个递归函数：

  ```js
  function mergeSortRec (array){ 
    const len = array.length
    // 递归的停止条件 
    if(len === 1) return array  // {1}
    // 长度大于1，分割为小数组
    const mid = len>>>1,     
          left = array.slice(0, mid),      
          right = array.slice(mid, len)
    return merge(mergeSortRec(left), mergeSortRec(right)) // {2}
  }
  ```

归并排序将一个大数组转化为多个小数组直到只有一个项。由于算法是递归的，我们需要一个停止条件，在这里此条件是判断数组的长度是否为 1（行{1}）。如果是，则直接返回这个长度为 1 的**数组**，因为它已排序了。

下面的步骤是调用 merge 函数（行{2}），它负责**合并和排序小数组来产生大数组**，直到回到原始数组并已排序完成。为了不断将原始数组分成小数组，我们得再次对 left 数组和 right 数组递归调用 mergeSortRec，并同时作为参数传递给 merge 函数。

  ```js
  // merge函数接受两个数组作为参数，并将它们归并至一个大数组
  function merge(left,right){
    const result = []  // {7}
    let il = 0,  
        ir = 0 
    while(il < left.length && ir < right.length) { // {8} 
      if(left[il] < right[ir]) { 
        result.push(left[il++]);  // {9} 
      } else{ 
        result.push(right[ir++]); // {10} 
      } 
    } 
    // 将剩余项push进入result中
    while (il < left.length){     // {11} 
      result.push(left[il++]) 
    } 
    while (ir < right.length){    // {12} 
      result.push(right[ir++]) 
    } 
  
    return result; // {13} 
  }
  ```

排序发生在归并过程中。首先，需要声明归并过程要创建的新数组以及用来迭代两个数组（left 和 right 数组）所需的两个变量（行{7}）。

迭代两个数组的过程中（行{8}），我们比较来自 left 数组的项是否比来自 right 数组的项小。

  + 如果是，将该项从 left 数组添加至归并结果数组，并递增迭代数组的控制变量（行{9}）；
  + 否则，从 right 数组添加项并递增相应的迭代数组的控制变量（行{10}）。

接下来，将 left 数组或者 right 数组所有剩余的项添加到归并数组中（行{11}和行{12}）。最后，将归并数组作为结果返回（行{13}）。

> 如果 left 和 right 的长度不一致，或者 left 全部都比 right 小，只会有一个数组仍有剩余，所以把剩余的推入即可
  >
> 第一个 while 循环结束后，仍有剩余的数组的最小项都会比合并数组的最大项大

如果执行 mergeSort 函数，下图是具体的执行过程：

![1554730216945](/img/user/programming/basic/algorithm/sort/1554730216945.png)

可以看到，算法首先将原始数组分割直至只有一个元素的子数组，然后开始归并。归并过程也会完成排序，直至原始数组完全合并并完成排序。

# 快速排序

快速排序也许是最常用的排序算法了。它的复杂度为 O(nlog^n^)，且它的性能通常比其他的复杂度为 O(nlog^n^) 的排序算法要好。和归并排序一样，快速排序也使用**分治的方法**，将原始数组分为较小的数组（但它没有像归并排序那样将它们分割开）。

以下是递归算法的原理：

1. 首先，从数组中选择中间一项作为**主元**。
2. 创建两个指针，左边一个指向数组第一个项，右边一个指向数组最后一个项。移动左指针直到我们找到一个比主元大的元素，接着，移动右指针直到找到一个比主元小的元素，然后交换它们，重复这个过程，直到**左指针超过了右指针**。这个过程将使得比主元小的值都排在主元之前，而比主元大的值都排在主元之后。这一步叫作**划分操作**。
3. 接着，算法对划分后的小数组（较主元小的值组成的子数组，以及较主元大的值组成的子数组）重复之前的两个步骤，直至数组已完全排序。

让我们开始快速排序的实现吧：

```js
  Array.prototype.quickSort = function(){
    const array = this 
    quick(array,  0, array.length - 1)
  } 
```

就像归并算法那样，开始我们声明一个主方法来调用递归函数，传递待排序数组，以及索引 0 及其最末的位置作为参数。

```js
  function quick(array, left, right){ 
    let index //{1} 
    if (array.length > 1) { //{2}
      // 划分函数将数组分为两部分，左侧的数组比主元小，右侧的数组比主元大 
      index = partition(array, left, right) //{3} 
      if (left < index - 1) quick(array, left, index - 1)    //{5} 
      if (index < right) quick(array, index, right)        //{7} 
    } 
  } 
```

首先声明 index（行{1}），该变量能帮助我们将子数组分离为较小值数组和较大值数组，这样，我们就能再次递归的调用 quick 函数了。partition 函数返回值将赋值给 index（行{3}）。

> 得到的 index，即是主元的索引

如果数组的长度比 1 大（因为只有一个元素的数组必然是已排序了的（行{2}），我们将对给定子数组执行 partition 操作（第一次调用是针对整个数组）以得到 index（行{3}）。

如果子数组存在较小值的元素（行{4}），则对该数组重复这个过程（行{5}）。同理，对存在较大值得子数组也是如此，如果存在子数组存在较大值，我们也将重复快速排序过程（行{7}）。

## 划分过程

第一件要做的事情是选择主元（pivot），有好几种方式。最简单的一种是选择数组的第一项（最左项）。然而，研究表明对于几乎已排序的数组，这不是一个好的选择，它将导致该算法的最差表现。另外一种方式是随机选择一个数组项或是选择中间项。

现在，让我们看看划分过程：

  ```js
  function partition(array,left,right){
    // 选择中间项作为主元
    const pivot = array[(right+left)>>>1] //{8} 
    let i = left,                                      //{9} 
        j = right                                     //{10} 
  
    while (i <= j) {                 //{11} 
      while (array[i] < pivot) i++   //{12}
      while (array[j] > pivot) j--   //{13}
      if (i <= j) { //{14} 
        swap(array, i, j) //{15} 
        i++ 
        j-- 
      } 
    } 
    return i //{16} 
  } 
  ```

在本实现中，我们选择中间项作为主元（行{8}）。我们初始化两个指针：left（低——行{9}），初始化为数组第一个元素；right（高——行{10}），初始化为数组最后一个元素。

只要 left 和 right 指针没有相互交错（行{11}），就执行划分操作。

  + 首先，移动**left 指针**直到找到一个元素比主元**大**（行{12}）。
  + 对**right 指针**，我们做相反的事情，移动 right 指针直到我们找到一个元素比主元**小**。

当左指针指向的元素比主元大且右指针指向的元素比主元小，并且此时左指针索引没有右指针索引大（行{14}），意思是左项比右项大（值比较）。我们交换它们，然后移动两个指针，并重复此过程（从行{11}再次开始）。

在划分操作结束后，返回**左指针的索引**，用来在行{3}处创建子数组. 此时左指针的索引才是真实的主元, 在其左侧的数字都比他小, 在其右侧的数字都比他大

## 快速排序实战

让我来一步步地看一个快速排序的实际例子:

 ![1554732528622](/img/user/programming/basic/algorithm/sort/1554732528622.png)

数组 `[3, 5, 1, 6, 4, 7, 2]`，前面的示意图展示了划分操作的第一次执行。

下面的示意图展示了对有较小值的子数组执行的划分操作（注意 7 和 6 不包含在子数组之内）：

![1554732680302](/img/user/programming/basic/algorithm/sort/1554732680302.png)

接着，我们继续创建子数组，请看下图，但是这次操作是针对上图中有较大值的子数组（有 1 的那个较小子数组不用再划分了，因为它仅含有一个项）

![1554732734644](/img/user/programming/basic/algorithm/sort/1554732734644.png)

子数组（`[2, 3, 5, 4]`）中的较小子数组（`[2, 3]`）继续进行划分（算法代码中的行{5}）：

![1554732753183](/img/user/programming/basic/algorithm/sort/1554732753183.png)

然后子数组（`[2, 3, 5, 4]`）中的较大子数组（`[5, 4]`）也继续进行划分（算法中的行{7}），示意图如下：

![1554732826043](/img/user/programming/basic/algorithm/sort/1554732826043.png)

最终，较大子数组 `[6, 7]` 也会进行划分（partition）操作，快速排序算法的操作执行完成。

优化:<http://www.blogjava.net/killme2008/archive/2010/09/08/331404.html> #

# 最大堆排序

堆排序也是一种很高效的算法，因其**把数组当作二叉树来排序**而得名。这个算法会根据以下信息，把数组当作二叉树来管理

- 索引 0 是树的根节点；
- 除根节点外，任意节点 i 的父节点是 (i-1)/2；
- 节点 L 的左子节点是 2\*i+1
- 节点 R 的右子节点是 2\*i+2

举例来说，可以将数组 `[3, 5, 1, 6, 4, 7, 2]` 想象成下面的树：

![1554775529131](/img/user/programming/basic/algorithm/sort/1554775529131.png)

堆排序算法实现如下：

  ```js
this.heapSort = function () {
    var heapSize = array.length;
    buildHeap(array); //{1} 

    while (heapSize > 1) {
        // 减小堆的长度，相当于是把最大的元素排除了，即是：已排序
        heapSize--
        // 此时堆顶的堆中最大值，交换堆顶和堆尾的元素
        swap(array, 0, heapSize); //{2} 
        // 重新构建最大堆
        heapify(array, heapSize, 0); //{3} 
    }
}; 
  ```

第一步，构造一个满足 `array[parent(i)] ≥ array[i]` 的堆结构（行{1}）。

第二步，交换堆里第一个元素（数组中较大的值）和最后一个元素的位置（行{2}）。这样，最大的值就会出现在它已排序的位置。

第二步可能会丢掉堆的属性。因此，我们还需要执行一个 heapify 函数，再次将数组转换成 堆，也就是说，它会找到当前堆的根节点（较小的值），重新放到树的底部

buildHeap 函数实现如下：

```js
  function buildHeap(array){ 
    const heapSize = array.length; 
    for (let i = heapSize>>>1; i >= 0; i--) { 
      heapify(array, heapSize, i); 
    } 
  }
```

最后，heapify 函数实现如下：

```js
  // array是为了尾递归优化，真正的参数只有heapSize和i，这个函数就是要检查i的两个子节点是否都比小，否则就令他们之中最大的与i交换, 以此来满足堆排序的最基本的特性
  function heapify(array, heapSize, i){ 
    const left = i * 2 + 1, 
    right = i * 2 + 2, 
    largest = i; 
   
    if (left < heapSize && array[left] > array[largest]) { 
      largest = left; 
    } 
   
    if (right < heapSize && array[right] > array[largest]) { 
      largest = right; 
    } 
   
    if (largest !== i) { 
      swap(array, i, largest); 
      heapify(array, heapSize, largest); // 保证交换之后仍符合最大堆
    } 
  }; 
```

如果对数组 `[3, 5, 1, 6, 4, 7, 2]` 调用 buildHeap 函数，堆的构建过程如下：

![1563978051090](/img/user/programming/basic/algorithm/sort/1563978051090.png)

堆构造好之后，就可以应用堆排序的算法了，也就是行{2}和行{3}：

![1563979479733](/img/user/programming/basic/algorithm/sort/1563979479733.png)

## 完整代码

```js
this.heapSort = function () {
    let heapSize = array.length
    buildHeap(array); //{1} 

    while (heapSize > 1) {
        // 减小堆的长度，相当于是把最大的元素排除了，即是：已排序
        heapSize--
        // 此时堆顶的堆中最大值，交换堆顶和堆尾的元素
        swap(array, 0, heapSize); //{2} 
        // 重新构建最大堆
        heapify(array, heapSize, 0); //{3} 
    }
    function buildHeap(array) {
        const heapSize = array.length;
        for (let i = (heapSize >>> 1) - 1; i >= 0; i--) {
            heapify(array, heapSize, i);
        }
    }
    // array是为了尾递归优化，真正的参数只有heapSize和i，这个函数就是要检查i的两个子节点是否都比小，否则就令他们之中最大的与i交换
    function heapify(array, heapSize, i) {
        let left = i * 2 + 1,
            right = i * 2 + 2,
            largest = i;

        if (left < heapSize && array[left] > array[largest]) {
            largest = left;
        }

        if (right < heapSize && array[right] > array[largest]) {
            largest = right;
        }

        if (largest !== i) {
            swap(array, i, largest);
            heapify(array, heapSize, largest); // 保证交换之后仍符合最大堆
        }
    }
}
```

## 复杂度分析

### 建堆

n 个节点的堆，树高度是 h=floor(log n)。对深度为于 h-1 层的节点，比较 2 次，交换 1 次，这一层最多有 2^(h-1) 个节点，总共操作次数最多为 `3*(1*2^(h-1))`；对深度为 h-2 层的节点，总共有 2^(h-2) 个，每个节点最多比较 4 次，交换 2 次，所以操作次数最多为 `3*(2*2^(h-2))`……

以此类推，从最后一个父节点到根结点进行堆调整的总共操作次数为：

  ```
  s=3*[2^(h-1) + 2*2^(h-2) + 3*2^(h-3) + … + h*2^0]       a
  2s=3*[2^h + 2*2^(h-1) + 3*2(h-2) + … + h*2^1]           b
  b-a，得到一个等比数列，根据等比数列求和公式
  s = 2s - s = 3*[2^h + 2^(h-1) + 2^(h-2) + … + 2 - h]=3*[2^(h+1)-  2 - h]≈3*n
  ```

所以建堆的时间复杂度是 O(n)。

### 堆调整

从堆调整的代码可以看到是当前节点与其子节点比较两次，交换一次。父节点与哪一个子节点进行交换，就对该子节点递归进行此操作，设对调整的时间复杂度为 T(k)（k 为该层节点到叶节点的距离），那么有

`T(k)=T(k-1)+3, k∈[2,h]`

`T(1)=3`

迭代法计算结果为： `T(h)=3*h=3*floor(log n)`

所以堆调整的时间复杂度是 O(log n) 。

### 原地排序

空间复杂度 O(1)

# 构建最小堆

## 最小堆排序

```js
  Array.prototype.minHeapSort = function(){
    const array = this
    let heapSize = array.length
    buildHeap(array) //{1} 
   
    while (heapSize > 1) { 
      // 减小堆的长度，相当于是把最小的元素排除了，即是：已排序
      heapSize--
      // 此时堆顶的堆中最小，交换堆顶和堆尾的元素
      swap(array, 0, heapSize); //{2} 
      // 从顶部开始重新构建最小堆
      heapify(array, heapSize, 0); //{3} 
    }
    function buildHeap(array){ 
      const heapSize = array.length; 
      for (let i = (heapSize>>>1)-1; i >= 0; i--) { 
        heapify(array, heapSize, i); 
      } 
    }
    function heapify(array, heapSize, i){ 
      let left = i * 2 + 1, 
          right = i * 2 + 2, 
          smallest = i 
     	// 与最大堆的不同之处，小于，smallest
      if (left < heapSize && array[left] < array[smallest]) { 
        smallest = left 
      } 
     
      if (right < heapSize && array[right] < array[smallest]) { 
        smallest = right 
      } 
     
      if (smallest !== i) { 
        swap(array, i, largsmallestest); 
        heapify(array, heapSize, smallest); // 保证交换之后仍符合最大堆
      } 
    }
  }
```

从大到小排序

## minHeap()

仅仅运行 buildheap 部分，即可构建一个结构，

然后调用用 heapPop，即是弹出堆顶元素

```js
  function minHeap(array){
    buildHeap(array) 
  
    function buildHeap(array){ 
      const heapSize = array.length; 
      for (let i = (heapSize>>>1)-1; i >= 0; i--) { 
        minHeapify(array, heapSize, i); 
      } 
    }
    return array
  } 
  function minHeapify(array, heapSize, i){ 
    let left = i * 2 + 1, 
        right = i * 2 + 2, 
        smallest = i 
  
    if (left < heapSize && array[left] < array[smallest]) { 
      smallest = left 
    } 
  
    if (right < heapSize && array[right] < array[smallest]) { 
      smallest = right 
    } 
  
    if (smallest !== i) { 
      swap(array, i, smallest); 
      minHeapify(array, heapSize, smallest); // 保证交换之后仍符合最大堆
    } 
  }
```

## heapPop()

不可以直接弹出堆顶的元素，而是应该先排除他，然后重新构建

排除的方法就是和堆尾的交换, 并缩小 heapSize

```js
  this.heapPop = function(){
    const array = this
    const heapSize = array.length
    // 弹出堆顶的元素
    swap(array,0,heapSize-1)
    // 重新构建最小堆
    minHeapify(array, heapSize-1, 0) 
    // 弹出数组最后一个元素，即原堆顶
    array.pop()
  }
```

# 桶排序

# 乱序

## Math.random

一个经常会遇见的写法是使用 Math.random()：

  ```js
  var values = [1, 2, 3, 4, 5];
  
  values.sort(function(){
      return Math.random() - 0.5;
  });
  
  console.log(values)
  ```

`Math.random() - 0.5` 随机得到一个正数、负数或是 0，如果是正数则降序排列，如果是负数则升序排列，如果是 0 就不变，然后不断的升序或者降序，最终得到一个乱序的数组。

看似很美好的一个方案，实际上，效果却不尽如人意。不信我们写个 demo 测试一下：

  ```js
  var times = [0, 0, 0, 0, 0];
  
  for (var i = 0; i < 100000; i++) {
      
      let arr = [1, 2, 3, 4, 5];
      
      arr.sort(() => Math.random() - 0.5);
      
      times[arr[4]-1]++;
  
  }
  
  console.log(times)
  ```

测试原理是：将 `[1, 2, 3, 4, 5]` 乱序 10 万次，计算乱序后的数组的最后一个元素是 1、2、3、4、5 的次数分别是多少。

一次随机的结果为：

  ```
  [30636, 30906, 20456, 11743, 6259]
  ```

该结果表示 10 万次中，数组乱序后的最后一个元素是 1 的情况共有 30636 次，是 2 的情况共有 30906 次，其他依此类推。

我们会发现，最后一个元素为 5 的次数远远低于为 1 的次数，所以这个方案是有问题的。

可是我明明感觉这个方法还不错呐？初见时还有点惊艳的感觉，为什么会有问题呢？

## 插入排序

如果要追究这个问题所在，就必须了解 sort 函数的原理，然而 ECMAScript 只规定了效果，没有规定实现的方式，所以不同浏览器实现的方式还不一样。

为了解决这个问题，我们以 v8 为例，v8 在处理 sort 方法时，当目标数组长度小于 10 时，使用插入排序；反之，使用快速排序和插入排序的混合排序。

所以我们来看看 v8 的源码，因为是用 JavaScript 写的，大家也是可以看懂的。

源码地址：<https://github.com/v8/v8/blob/master/src/js/array.js>

为了简化篇幅，我们对 `[1, 2, 3]` 这个数组进行分析，数组长度为 3，此时采用的是插入排序。

其原理在于将第一个元素视为有序序列，遍历数组，将之后的元素依次插入这个构建的有序序列中。

## 具体分析

明白了插入排序的原理，我们来具体分析下 [1, 2, 3] 这个数组乱序的结果。

演示代码为：

  ```js
  var values = [1, 2, 3];
  
  values.sort(function(){
      return Math.random() - 0.5;
  });
  ```

注意此时 sort 函数底层是使用插入排序实现

我们开始逐步分析乱序的过程：

因为插入排序视第一个元素为有序的，所以数组的外层循环从 `i = 1` 开始，`arr[i]` 值为 2，此时内层循环遍历，比较 `compare(1, 2)`：

+ 因为 `Math.random() - 0.5` 的结果有 50% 的概率小于 0 ，有 50% 的概率大于 0
+ 所以有 50% 的概率数组变成 [2, 1, 3]
+ 50% 的结果不变，数组依然为 [1, 2, 3]。

假设依然是 [1, 2, 3]，我们再进行一次分析，接着遍历，`i = 2`，`arr[i]` 的值为 3，此时内层循环遍历，比较 `compare(2, 3)`：

+ 有 50% 的概率数组不变，依然是 `[1, 2, 3]`，然后遍历结束。
+ 有 50% 的概率变成 [1, 3, 2]，因为还没有找到 3 正确的位置，所以还会进行遍历，所以在这 50% 的概率中又会进行一次比较：
+ `compare(1, 3)`，有 50% 的概率不变，数组为 [1, 3, 2]，此时遍历结束
+ 有 50% 的概率发生变化，数组变成 [3, 1, 2]。

综上，在 [1, 2, 3] 中，有 50% 的概率会变成 [1, 2, 3]，有 25% 的概率会变成 [1, 3, 2]，有 25% 的概率会变成 [3, 1, 2]。

另外一种情况 [2, 1, 3] 与之分析类似，我们将最终的结果汇总成一个表格：

  | 数组          | i = 1           | i = 2         | 总计          |
  | ------------- | --------------- | ------------- | ------------- |
  | [1, 2, 3]     | 50% [1, 2, 3]   | 50% [1, 2, 3] | 25% [1, 2, 3] |
  | 25% [1, 3, 2] | 12.5% [1, 3, 2] |               |               |
  | 25% [3, 1, 2] | 12.5% [3, 1, 2] |               |               |
  | 50% [2, 1, 3] | 50% [2, 1, 3]   | 25% [2, 1, 3] |               |
  | 25% [2, 3, 1] | 12.5% [2, 3, 1] |               |               |
  | 25% [3, 2, 1] | 12.5% [3, 2, 1] |               |               |

为了验证这个推算是否准确，我们写个 demo 测试一下：

  ```js
  var times = 100000;
  var res = {};
  
  for (var i = 0; i < times; i++) {
      
      var arr = [1, 2, 3];
      arr.sort(() => Math.random() - 0.5);
      
      var key = JSON.stringify(arr);
      res[key] ? res[key]++ :  res[key] = 1;
  }
  
  // 为了方便展示，转换成百分比
  for (var key in res) {
      res[key] = res[key] / times * 100 + '%'
  }
  
  console.log(res)
  ```

这是一次随机的结果：

[![Math random 效果演示](https://github.com/mqyqingfeng/Blog/raw/master/Images/shuffle/mathRandom.png)](https://github.com/mqyqingfeng/Blog/raw/master/Images/shuffle/mathRandom.png)

我们会发现，乱序后，`3` 还在原位置 (即 [1, 2, 3] 和 [2, 1, 3]) 的概率有 50% 呢。

所以根本原因在于什么呢？其实就在于在插入排序的算法中，当待排序元素跟有序元素进行比较时，一旦确定了位置，就不会再跟位置前面的有序元素进行比较，所以就乱序的不彻底。

那么如何实现真正的乱序呢？而这就要提到经典的 Fisher–Yates 算法。

## Fisher–Yates

为什么叫 Fisher–Yates 呢？ 因为这个算法是由 Ronald Fisher 和 Frank Yates 首次提出的。

话不多说，我们直接看 JavaScript 的实现：

```js
  function shuffle(a) {
      var j, x, i;
      for (i = a.length; i; i--) {
          j = Math.floor(Math.random() * i);
          x = a[i - 1];
          a[i - 1] = a[j];
          a[j] = x;
      }
      return a;
  }
```

原理很简单，就是遍历数组元素，然后将当前元素与以后随机位置的元素进行交换，从代码中也可以看出，这样乱序的就会更加彻底。

如果利用 ES6，代码还可以简化成：

```js
  function shuffle(a) {
    const len = a.length
    for (let i = 0; i<len; i++) {
        let j = Math.floor(Math.random() * (len-i)+i);
        [a[i], a[j]] = [a[j], a[i]];
    }
    return a;
  }
```

还是再写个 demo 测试一下吧：

  ```js
  var times = 100000;
  var res = {};
  
  for (var i = 0; i < times; i++) {
      var arr = shuffle([1, 2, 3]);
  
      var key = JSON.stringify(arr);
      res[key] ? res[key]++ :  res[key] = 1;
  }
  
  // 为了方便展示，转换成百分比
  for (var key in res) {
      res[key] = res[key] / times * 100 + '%'
  }
  
  console.log(res)
  ```

这是一次随机的结果：

[![Fisher–Yates 效果演示](https://github.com/mqyqingfeng/Blog/raw/master/Images/shuffle/fisher-yates.png)](https://github.com/mqyqingfeng/Blog/raw/master/Images/shuffle/fisher-yates.png)

真正的实现了乱序的效果！

# 对比

![img](https://pic2.zhimg.com/80/v2-e3a121dea092f9ec2ef727ceab030aad_hd.jpg)

## 稳定性

假定在待排序的记录序列中，存在多个具有相同的值的元素，若经过排序，这些记录的相对次序保持不变，即在原序列中，`r[i]==r[j]`，且 `r[i]` 在 `r[j]` 之前，而在排序后的序列中，`r[i]` 仍在 `r[j]` 之前，则称这种排序算法是稳定的；否则称为不稳定的。

算法是否稳定与时间复杂度没有关系，更多的取决于算法的实现，交换元素 or 插入元素等

### 冒泡排序

冒泡排序就是把小的元素往前调或者把大的元素往后调。比较是相邻的两个元素比较，交换也发生在这两个元素之间。所以，如果两个元素相等，也不会发生交换，因此冒泡排序是稳定的

### 选择排序

选择排序是给每个位置选择当前元素最小的，比如给第一个位置选择最小的，在剩余元素里面给第二个元素选择第二小的，依次类推，直到第 n-1 个元素

举个例子，序列 5 8 5 2 9， 我们知道第一遍选择第 1 个元素 5 会和 2 交换，那么原序列中 2 个 5 的相对前后顺序就被破坏了，所以选择排序不是一个稳定的排序算法。

### 插入排序

插入排序是在一个已经有序的小序列的基础上，一次插入一个元素。当然，刚开始这个有序的小序列只有 1 个元素，就是第一个元素。比较是从有序序列的末尾开 始，也就是想要插入的元素和已经有序的最大者开始比起，如果比它大则直接插入在其后面，否则一直往前找直到找到它该插入的位置。如果碰见一个和插入元素相 等的，那么插入元素把想插入的元素放在相等元素的后面。所以，相等元素的前后顺序没有改变，从原无序序列出去的顺序就是排好序后的顺序，所以插入排序是稳 定的。

### 快速排序

快速排序有两个方向，左边的 i 下标一直往右走，当 `a[i] <= a[center_index]`，其中 `center_index` 是中枢元素的数组下标，一般取为数组第 0 个元素。而右边的 j 下标一直往左走，当 `a[j] > a[center_index]`。如果 `i` 和 `j` 都走不动了，`i <= j`, 交换 `a[i]` 和 `a[j]`，重复上面的过程，直到 `i>j`。 交换 `a[j]` 和 `a[center_index]`，完成一趟快速排序。在中枢元素和 `a[j]` 交换的时候，很有可能把前面的元素的稳定性打乱，比如序列为 ` 5 3 3 4 3 8 9 10 11`， 现在中枢元素 5 和 3(第 5 个元素，下标从 1 开始计) 交换就会把元素 3 的稳定性打乱，所以快速排序是一个不稳定的排序算法，不稳定发生在中枢元素和 a[j] 交换的时刻。

### 归并排序

归并排序是把序列递归地分成短序列，递归出口是短序列只有 1 个元素 (认为直接有序) 或者 2 个序列 (1 次比较和交换),然后把各个有序的段序列合并成一个有 序的长序列，不断合并直到原序列全部排好序。

可以发现，在 1 个或 2 个元素时，1 个元素不会交换，2 个元素如果大小相等也没有人故意交换，这不会破坏稳定 性。

那么，在短的有序序列合并的过程中，稳定是否受到破坏？没有，合并过程中我们可以保证如果两个当前元素相等时，我们把处在前面的序列的元素保存在结 果序列的前面，这样就保证了稳定性。所以，归并排序也是稳定的排序算法。

### 堆排序

大顶堆要求父节点大于等于其 2 个子节点，小顶堆要求父节点小于等于其 2 个子节点。

在一个长为 n 的序列，堆排序的过程是从第 n/2 开始和其子节点共 3 个值选择最大 (大顶堆) 或者最小 (小顶堆),这 3 个元素之间的选择当然不会破坏稳定性。但当为 n /2-1, n/2-2, ...1 这些个父节点选择元素时，就会破坏稳定性。有可能第 n/2 个父节点交换把后面一个元素交换过去了，而第 n/2-1 个父节点把后面一个相同的元素没 有交换，那么这 2 个相同的元素之间的稳定性就被破坏了。所以，堆排序不是稳定的排序算法。

### 其他

稳定：基数排序

不稳定：希尔排序、直接排序

## 快速排序相对于其他排序效率更高的原因

上面一共提到了 8 种排序的方法，在实际使用中，应用最广泛的是快速排序。快速排序相对于其他排序算法的优势在于在相同 数据量的情况下，它的运算效率最高，并且它额外所需空间最小。

我们首先从时间复杂度来判断，由于前面几种方法的时间复杂度平均情况下基本趋向于 O(n²)，因此只从时间复杂度上来看 的话，显然归并排序、堆排序和快速排序的时间复杂度最小。但是既然这几种方法的时间复杂度基本一致，并且快速排序在最 坏情况下时间的复杂度还会变为 O(n²)，那么为什么它的效率反而更高呢？

首先在对大数据量排序的时候，由于归并排序的空间复杂度为 O(n)，因此归并排序在这种情况下会需要过多的额外内存，因 此归并排序首先就被排除掉了。

接下来就剩下了堆排序和快速排序的比较。我认为堆排序相对于快速排序的效率不高的原因有两个方面。

第一个方面是对于比较操作的有效性来说。对于快速排序来说，每一次元素的比较都会确定该元素在数组中的位置，也就是在 枢纽值的左边或者右边，快速排序的每一次比较操作都是有意义的结果。而对于堆排序来说，在每一次重新调整堆的时候，我 们在迭代时，已经知道上层的节点值一定比下层的节点值大，因此当我们每次为了打乱堆结构而将最后一个元素与堆顶元素互 换时，互换后的元素一定是比下层元素小的，因此我们知道比较结果却还要在堆结构调整时去进行再一次的比较，这样的比较 是没有意义的，以此在堆排序中会产生大量的没有意义的比较操作。

第二个方面是对于缓存局部性原理的利用上来考虑的，我认为这应该是造成堆排序的效率不如快速排序的主要原因。在计算机 中利用了多级缓存的机制，来解决 cpu 计算速度与存储器数据读取速度间差距过大的问题。缓存的原理主要是基于局部性原 理，局部性原理简单来说就是，当前被访问过的数据，很有可能在一段时间内被再次访问，这被称为时间局部性。还有就是当 前访问的数据，那么它相邻的数据，也有可能在一段时间内被访问到，这被称为空间局部性。计算机缓存利用了局部性的原理 来对数据进行缓存，来尽可能少的减少磁盘的 I/O 次数，以此来提高执行效率。对于堆排序来说，它最大的问题就是它对于 空间局部性的违背，它在进行比较时，比较的并不是相邻的元素，而是与自己相隔很远的元素，这对于利用空间局部性来进行 数据缓存的计算机来说，它的很多缓存都是无效的。并且对于大数据量的排序来说，缓存的命中率就会变得很低，因此会明显 提高磁盘的 I/O 次数，并且由于堆排序的大量的无效比较，因此这样就造成了堆排序执行效率的低下。而相对来快速排序来 说，它的排序每一次都是在相邻范围内的比较，并且比较的范围越来越小，它很好的利用了局部性原理，因此它的执行效率更 高。简单来说就是在堆排序中获取一个元素的值所花费的时间比在快速排序获取一个元素的值所花费的时间要大。因此我们可 以看出，时间复杂度类似的算法，在计算机中实际执行可能会有很大的差别，因为决定算法执行效率的还有内存读取这样的其 他的因素。

相关资料可以参考： [《为什么在平均情况下快速排序比堆排序要优秀？》](https://www.zhihu.com/question/23873747) [《为什么说快速排序是性能最好的排序算法？》](https://blog.csdn.net/qq_36770641/article/details/82669788)

## 排序面试题目总结

1. 快速排序在完全无序的情况下效果最好，时间复杂度为 O(nlogn)，在有序情况下效果最差，时间复杂度为 O(n^2)。
2. 初始数据集的排列顺序对算法的性能无影响的有堆排序，直接选择排序，归并排序，基数排序。
3. 合并 m 个长度为 n 的已排序数组的时间复杂度为 O(nmlogm)。
4. 外部排序常用的算法是归并排序。
5. 数组元素基本有序的情况下，插入排序效果最好，因为这样只需要比较大小，不需要移动，时间复杂度趋近于 O(n)。
6. 如果只想得到 1000 个元素组成的序列中第 5 个最小元素之前的部分排序的序列，用堆排序方法最快。
7. 插入排序和优化后的冒泡在最优情况（有序）都只用比较 n-1 次。
8. 对长度为 n 的线性表作快速排序，在最坏情况下，比较次数为 n(n-1)/2。
9. 下标从 1 开始，在含有 n 个关键字的小根堆（堆顶元素最小）中，关键字最大的记录有可能存储在 `[n/2]+2` 位置上。 因为小根堆中最大的数一定是放在叶子节点上，堆本身是个完全二叉树，完全二叉树的叶子节点的位置大于 `[n/2]`
10. 拓扑排序的算法，每次都选择入度为 0 的结点从图中删去，并从图中删除该顶点和所有以它为起点的有向边。
11. 任何一个基于 " 比较 " 的内部排序的算法，若对 n 个元素进行排序，则在最坏情况下所需的比较次数 k 满足 2^k > n!， 时间下界为 O(nlogn)
12. m 个元素 k 路归并的归并趟数 s=logk(m)，代入数据：logk(100)≦3
13. 对 n 个记录的线性表进行快速排序为减少算法的递归深度，每次分区后，先处理较短的部分。
14. 在用邻接表表示图时，拓扑排序算法时间复杂度为 O(n+e)
