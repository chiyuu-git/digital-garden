---
{"aliases":["散列表","哈希表"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-05-23-Tue, 8:27:03 pm","date-modified":"2023-05-24-Wed, 10:38:17 am","permalink":"/programming/basic/data-structure/hash-table/","dgPassFrontmatter":true}
---


# 概述

HashTable 类

散列算法的作用是**尽可能快地**在数据结构中找到一个值。在之前的章节中，你已经知道如果要在数据结构中获得一个值（使用 get 方法），**需要遍历整个数据结构**来找到它。

如果使用散列函数，就知道值的具体位置，因此能够快速检索到该值。**（从这里可以看出以数组为基础）**

- 散列函数的作用是给定一个键值，然后返回值在表中的地址
- 举个例子，我们继续使用在前一节中使用的电子邮件地址簿。我们将要使用最常见的散列函数——“lose lose”散列函数，方法是简单地将每个键值中的每个字母的 ASCII 值相加。

  ![1554596681769](/img/user/programming/basic/data-structure/hash-table/1554596681769.png)

# 散列表的实现

## HashTable 类骨架

```js
  function HashTable() { 
    var table = []; 
  } 
```

## 基本操作

- put(key,value)：向散列表增加一个新的项（也能更新散列表）。
- remove(key)：根据键值从散列表中移除值。
- get(key)：返回根据键值检索到的特定的值。

## 散列函数

在实现这三个方法之前，要实现的第一个方法是散列函数，它是 HashTable 类中的一个**私有方法**：

```js
  var loseloseHashCode = function (key) { 
    var hash = 0;                          //{1} 
    for (var i = 0; i < key.length; i++) { //{2} 
      hash += key.charCodeAt(i);         //{3} 
    } 
    return hash % 37;                      //{4} 
  }; 
```

给定一个 key 参数，我们就能根据组成 key 的每个字符的 ASCII 码值的和得到一个数字。

所以，首先需要一个变量来存储这个总和（行{1}）。

然后，遍历 key（行{2}）并将从 ASCII 表中查到的每个字符对应的 ASCII 值加到 hash 变量中（可以使用 JavaScript 的 String 类中的 charCodeAt 方法——行{3}）。

最后，返回 hash 值。为了得到比较小的数值，我们会使用 hash 值和一个**任意数**做除法的余数（mod）。

> 散列值很可能重复，所以说是 loselose

## Put 方法

```js
  this.put = function(key, value) { 
    var position = loseloseHashCode(key); //{5} 
    console.log(position + ' - ' + key); //{6} 
    table[position] = value; //{7} 
  }; 
```

## Get 方法

```js
  this.get = function (key) { 
    return table[loseloseHashCode(key)]; 
  }; 
```

## Remove 方法

```js
  this.remove = function(key) { 
    table[loseloseHashCode(key)] = undefined; 
  }; 
```

对于 HashTable 类来说，我们不需要像 ArrayList 类一样从 table 数组中将位置也移除。由于元素分布于整个数组范围内，一些位置会没有任何元素占据，并默认为 undefined 值。

我们也不能将位置本身从数组中移除（这会改变其他元素的位置），否则，当下次需要获得或移除一个元素的时候，这个元素会不在我们用散列函数求出的位置上。

# 处理散列表中的冲突

有时候，一些键会有相同的散列值。不同的值在散列表中对应相同位置的时候，我们称其为冲突。例如，我们看看下面的代码会得到怎样的输出结果：

  ```js
  var hash = new HashTable(); 
  hash.put('Gandalf', 'gandalf@email.com'); 
  hash.put('John', 'johnsnow@email.com'); 
  hash.put('Tyrion', 'tyrion@email.com'); 
  hash.put('Aaron', 'aaron@email.com'); 
  hash.put('Donnie', 'donnie@email.com'); 
  hash.put('Ana', 'ana@email.com'); 
  hash.put('Jonathan', 'jonathan@email.com'); 
  hash.put('Jamie', 'jamie@email.com'); 
  hash.put('Sue', 'sue@email.com'); 
  hash.put('Mindy', 'mindy@email.com'); 
  hash.put('Paul', 'paul@email.com'); 
  hash.put('Nathan', 'nathan@email.com'); 
  
  /*
  19 - Gandalf 
  29 - John 
  16 - Tyrion 
  16 - Aaron 
  13 - Donnie 
  13 - Ana 
  5 - Jonathan 
  5 - Jamie 
  5 - Sue 
  32 - Mindy 
  32 - Paul 
  10 – Nathan 
  */
  ```

> 注意，Tyrion 和 Aaron 有相同的散列值（16）。Donnie 和 Ana 有相同的散列值（13），Jonathan、Jamie 和 Sue 有相同的散列值（5），Mindy 和 Paul 也有相同的散列值（32）。

这样后添加的元素就会覆盖先添加的元素，为了避免数据丢失，我们通常会使用以下几种方法:

1. 分离链接
2. 线性探查
3. 双散列法

## 分离链接

分离链接法包括为散列表的每一个位置创建一个链表并将元素存储在里面。它是解决冲突的最简单的方法，但是它在 HashTable 实例之外还需要额外的存储空间。

例如，我们在之前的测试代码中使用分离链接的话，输出结果将会是这样：

  ![1554597553412](/img/user/programming/basic/data-structure/hash-table/1554597553412.png)

在位置 5 上，将会有包含三个元素的 LinkedList 实例；在位置 13、16 和 32 上，将会有包含两个元素的 LinkedList 实例；在位置 10、19 和 29 上，将会有包含单个元素的 LinkedList 实例。

对于分离链接和线性探查来说，只需要重写三个方法: put、get 和 remove

这三个方法在每种技术实现中都是不同的。

### ValuePair 类

为了实现一个使用了分离链接的 HashTable 实例，我们需要一个新的辅助类来表示将要加入 LinkedList 实例的元素。我们管它叫 ValuePair 类（在 HashTable 类内部定义）：

```js
  var ValuePair = function(key, value){ 
    this.key = key; 
    this.value = value; 
  
    this.toString = function() { 
      return '[' + this.key + ' - ' + this.value + ']'; 
    } 
  }; 
```

这个类只会将 key 和 value 存储在一个 Object 实例中。我们也重写了 toString 方法，以便之后在浏览器控制台中输出结果。

### Put 方法

```js
  this.put = function(key, value){ 
    var position = loseloseHashCode(key); 
   
    if (table[position] == undefined) { //{1} 
      table[position] = new LinkedList(); 
    } 
    table[position].append(new ValuePair(key, value)); //{2} 
  }; 
```

在这个方法中，将验证要加入新元素的位置是否已经被占据（行{1}）。如果这个位置是第一次被加入元素，我们会在这个位置上初始化一个 LinkedList 类的实例。然后，使用 LinkedList 类的 append 方法向 LinkedList 实例中添加一个 ValuePair 实例（键和值）（行{2}）。

### Get 方法

```js
  this.get = function(key) { 
    var position = loseloseHashCode(key); 
   
    if (table[position] !== undefined){ //{3} 
   
      //遍历链表来寻找键/值 
      var current = table[position].getHead(); //{4} 
   
      while(current.next){  //{5} 
        if (current.data.key === key){ //{6} 
          return current.data.value; //{7} 
        } 
        current = current.next; //{8} 
      } 
   
      //检查元素是链表唯一的节点的情况 
      if (current.data.key === key){ //{9} 
        return current.data.value; 
      } 
    } 
    return undefined; //{10} 
  };
```

我们要做的第一个验证，是确定在特定的位置上是否有元素存在（行{3}）。

  - 如果没有，则返回一个 undefined 表示在 HashTable 实例中没有找到这个值（行{10}）。
  - 如果在这个位置上有值存在，我们知道这是一个 LinkedList 实例。现在要做的是遍历这个链表来寻找我们需要的元素。
    - 在遍历之前先要获取链表表头的引用（行{4}）
    - 然后就可以从链表的头部遍历到尾部（行{5}，current.next 将会是 null）。

Node 链表包含 next 指针和 data 属性。而 data 属性又是 ValuePair 的实例，所以它又有 value 和 key 属性。可以通过 current.data.key 来获得 Node 链表的 key 属性，并通过比较它来确定它是否就是我们要找的键（行{6}）。（这就是要使用 ValuePair 这个辅助类来存储元素的原因。我们不能简单地存储值本身，这样就不能确定哪个值对应着特定的键。）

  - 如果 key 值相同，就返回 Node 的值（行{7}）；
  - 如果不相同，就继续遍历链表，访问下一个节点（行{8}）。

如果要找的元素是链表的唯一节点，那么就不会进入 while 循环的内部。因此，需要在行{9}处理这种特殊的情况。

> 在一开始检查就好，如果是这样，就没有必要遍历了

### Remove 方法

使用分离链接法从 HashTable 实例中移除一个元素和之前在本章实现的 remove 方法有一些不同。现在使用的是链表，我们需要从链表中移除一个元素。来看看 remove 方法的实现：

```js
  this.remove = function(key){ 
    var position = loseloseHashCode(key); 
   
    if (table[position] !== undefined){ 
   
      var current = table[position].getHead(); 
      while(current.next){ 
        if (current.data.key === key){ //{11} 
          table[position].remove(current.element); //{12} 
          if (table[position].isEmpty()){ //{13} 
            table[position] = undefined; //{14} 
          } 
          return true; //{15} 
        } 
        current = current.next; 
      } 
   
      // 检查是否为第一个或最后一个元素 
      if (current.data.key === key){ //{16} 
        table[position].remove(current.element); 
        if (table[position].isEmpty()){ 
          table[position] = undefined; 
        } 
        return true; 
      } 
    } 
   
    return false; //{17} 
  }; 
```

在 remove 方法中，我们使用和 get 方法一样的步骤找到要找的元素。遍历 LinkedList 实例时，如果链表中的 current 元素就是要找的元素（行{11}），使用 remove 方法将其从链表中移除。

然后进行一步额外的验证：如果链表为空了（行{13}——链表中不再有任何元素了），就将散列表这个位置的值设为 undefined（行{14}），这样搜索一个元素或打印它的内容的时候，就可以跳过这个位置了。

最后，返回 true 表示这个元素已经被移除（行{15}）或者在最后返回 false 表示这个元素在散列表中不存在（行{17}）。

同样，需要和 get 方法一样，处理元素在第一个或最后一个的情况（行{16}）。

## 线性探查

另一种解决冲突的方法是线性探查。当想向表中某个位置加入一个新元素的时候，如果索引为 index 的位置已经被占据了，就尝试 index+1 的位置。如果 index+1 的位置也被占据了，就尝试 index+2 的位置，以此类推。

### Put 方法

```js
  this.put = function(key, value){ 
    var position = loseloseHashCode(key); // {1} 
   
    if (table[position] == undefined) { // {2} 
      table[position] = new ValuePair(key, value); // {3} 
    } else { 
      var index = ++position; // {4} 
      while (table[index] != undefined){ // {5} 
        index++; // {6} 
      } 
      table[index] = new ValuePair(key, value); // {7} 
    } 
  }; 
```

和之前一样，先获得由散列函数生成的位置（行{1}），然后验证这个位置是否有元素存在（如果这个位置被占据了，将会通过行{2}的验证）。如果没有元素存在，就在这个位置加入新元素（行{3}——一个 ValuePair 的实例）。

如果这个位置已经被占据了，需要找到下一个没有被占据的位置（position 的值是 undefined），因此我们声明一个 index 变量并赋值为 position+1（行{4}——在变量名前使用自增运算符 ++ 会先递增变量值然后再将其赋值给 index）。然后验证这个位置是否被占据（行{5}），如果被占据了，继续将 index 递增（行{6}），直到找到一个没有被占据的位置。然后要做的，就是将值分配到这个位置（行{7}）。

### Get 方法

```js
  this.get = function(key) { 
    var position = loseloseHashCode(key); 
   
    if (table[position] !== undefined){ //{8} 
      if (table[position].key === key) { //{9} 
        return table[position].value; //{10} 
      } else { 
        var index = ++position; 
        while (table[index] === undefined 
        || table[index].key !== key){ //{11} 
          index++; 
        } 
        if (table[index].key === key) { //{12} 
          return table[index].value; //{13} 
        } 
      } 
    } 
    return undefined; //{14} 
  }; 
```

要获得一个键对应的值，先要确定这个键存在（行{8}）。如果这个键不存在，说明要查找的值不在散列表中，因此可以返回 undefined（行{14}）。如果这个键存在，需要检查我们要找的值是否就是这个位置上的值（行{9}）。如果是，就返回这个值（行{10}）。

如果不是，就在散列表中的下一个位置继续查找，直到找到一个键值与我们要找的键值相同的元素（行{11}）。然后，验证一下当前项就是我们要找的项（行{12}——只是为了确认一下）并且将它的值返回（行{13}）。

我们无法确定要找的元素实际上在哪个位置，这就是使用 ValuePair 来表示 HashTable 元素的原因。

### Remove 方法

remove 方法和 get 方法基本相同，不同之处在于行{10}和{13}，它们将会由下面的代码代替：

  `table[index] = undefined; `

要移除一个元素，只需要给其赋值为 undefined，来表示这个位置不再被占据并且可以在必要时接受一个新元素。

## 更好的散列函数

```js
  var djb2HashCode = function (key) { 
    var hash = 5381; //{1} 
    for (var i = 0; i < key.length; i++) { //{2} 
      hash = hash * 33 + key.charCodeAt(i); //{3} 
    } 
    return hash % 1013; //{4} 
  }; 
```

它包括初始化一个 hash 变量并赋值为一个质数（行{1}——大多数实现都使用 5381），然后迭代参数 key（行{2}），将 hash 与 33 相乘（用来当作一个魔力数），并和当前迭代到的字符的 ASCII 码值相加（行{3}）。

最后，我们将使用相加的和与另一个随机质数（比我们认为的散列表的大小要大——在本例中，我们认为散列表的大小为 1000）相除的余数。

# 散列表和散列集合

散列表和散列映射是一样的，我们已经在本章中介绍了这种数据结构。

在一些编程语言中，还有一种叫作散列集合的实现。散列集合由一个集合构成，但是插入、移除或获取元素时，使用的是散列函数。

我们可以重用本章中实现的所有代码来实现散列集合，不同之处在于，不再添加键值对，而是只插入值而没有键。例如，可以使用散列集合来存储所有的英语单词（不包括它们的定义）。和集合相似，散列集合只存储唯一的不重复的值。

# 散列表与字典

dictionary 跟 map 其实是同一个东西，只是在不同场合叫法不同。

dictionary 的中文是字典，map 在中文是映射，也有地图的意思。查字典，查地图，都是通过某个信息，去找到另一个信息。比如通过单词的拼写找到单词的具体含义。

类比查字典过程，单词的拼写为 key, 单词的具体含义为 value。dictionary 就是通过 key，找到 value，有时也将 dictionary 说成是 key-value 结构。只要达到查找目的，都可以叫做 dictionary。具体怎么找，可以有不同实现。

比如，最简单是将 key，value 放在一起，线性排。

```text
  k1, k2, k3, k4, k4 ....
  v1, v2, v3, v4, v5 ....
```

当需要从 key 找到对应的 value 时，就从头到尾遍历过去。依次判断 k1, k2, k3, k4 是不是等于 key, 当等于的时候，就找到 key 的具体位置，从而也就找到了 value。

但这样从头到尾遍历，速度就太慢了，时间复杂度为 O(N)。N 为数据的大小。

为了快速从 key 找到 value。dictionary(或者说 map) 的通常有两种实现方式。

  1. 二叉树
  2. 哈希 (hash) 表

二叉树查找的时间复杂度为 O(logN)，哈希表的时间复杂度大致为 O(1)。二叉树也分红黑树，AVL 树等。哈希表的速度很快，很多语言内置的 dictionary 都使用哈希表来实现，但它通常会浪费一些存储空间。这部分有兴趣去看数据结构的书。

hash_map 其实就是使用 hash 表实现的 map。注意，二叉树，哈希表仅仅是 dictionary 的实现方式，不能说 hash 就等于 dictionary，实现方式可以有多种多样。

# 散列表底层

+ object set map 慢 array 底层都是散列表
+ <https://juejin.im/entry/58eb2b94ac502e006c452632>
+ <https://www.rrfed.com/2017/04/04/chrome-object/>

# FAQ

- 找到数组中的对称对
- 追踪遍历的完整路径
- 查看一个数组是否为另一个数组的子集
- 检查给定数组是否不相交


# 哈希表与索引

有好几道哈希表索引的题目, 同时也有对应的滑动窗口解法, 之前都是没做的, 需要好好总结一下

因为是索引相关的题目, 所以既可以使用哈希表记录索引, 也可以使用滑动窗口, 通过将窗口限制在索引的条件哪, 来处理

[219. 存在重复元素 II](../leetcode/219.%20存在重复元素%20II.md)