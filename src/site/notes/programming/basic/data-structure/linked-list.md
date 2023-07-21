---
{"dg-publish":true,"permalink":"/programming/basic/data-structure/linked-list/"}
---


# 概念

链表是另一个重要的线性数据结构，刚一看可能看起来像数组，但在内存分配，内部结构以及如何执行插入和删除的基本操作方面有所不同。

## 链表与数组的区别

数组是需要一块**连续的内存空间**来存储，对内存的要求比较高。 而链表却相反，它并不需要一块连续的内存空间。链表是通过指针将一组零散的内存块串联在一起。

数组可以**快速的查找**某个元素，但是在**插入和删除**时就要移动大量元素。原因就在于相邻元素的存储位置也具有邻居关系。他们的编号是 0，1，2，3，4，...，n，它们在内存中的位置也是紧挨着的，中间没有空隙，所以就无法快速添加元素。而当删除后，当中就会留出空隙，自然需要弥补。

所以我们需要这样一种数据结构： 我们反正也是要让相邻元素间留有足够余地，那干脆所有的元素都不要考虑相邻位置了，哪有空位就到哪里，只是让每个元素知道它下一个元素的位置在哪里。我们可以在第一个元素时，就知道第二个元素的位置在哪；在第二个元素时，再找到第三个元素的位置。这样，所有的元素都可以遍历而找到。

因此，为了表示每个数据元素 n 和后继元素 n+1 之间的逻辑关系，对数据元素 n 来说，除了存储本身的信息之外，还需要存储一个指示其后继的信息。我们把存储元素的域称之为 **数据域**，把存储直接后继位置的域称之为 **指针域**。指针域中存储的信息称做 **指针域链**。这两部分信息组成数据元素 n 的存储映像，称为 **结点**。

而由 n 个结点链结成一个链表，称之为 **链式存储结构**。

链表用于实现文件系统，哈希表和邻接表。

下图是链表内部结构的直观展示：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522102922774.png)

### 链表和数组的性能对比

![](/img/user/programming/basic/data-structure/linked-list/image-20230522102948257.png)

数组和链表的对比，并不能局限于时间复杂度。而且，在实际开发中，不能仅仅利用复杂度分析就决定使用哪个数据结构来存储数据。针对不同的类型项目来权衡。当然，在大前端，还是数组用的最多。

需要先通过 On 的时间复杂度访问, 再执行 O1 的插入删除操作, 所以最终数组和链表的操作时间复杂度都是一样的

# 链表的实现

## 基本操作

- InsertAtHead —— 在链表**头部**插入指定元素
- append —— 在链表**末尾**插入指定元素
- insert —— 在指定的位置插入指定的元素
- remove—— 从链表中删除指定元素
- removeAt —— 删除链表的指定元素
- getHead —— 返回链表的头部元素
- toString —— 把链表变成字符串
- indexOf—— 返回链表中的指定元素
- isEmpty —— 如果链表为空，返回 true

我们一般设计的链表有两个类。`Node` 类用来表示节点，`LinkedList` 类提供了一些辅助方法，比如说结点的增删改查，以及显示列表元素等方法。 接下来看看如何用 `js` 代码表示一个链表。

  ```js
    var Node = function(data) {
      this.data = data;
      this.next = null;
    };
    var node1 = new Node(1);
    var node2 = new Node(2);
    var node3 = new Node(3);
  
    node1.next = node2;
    node2.next = node3;
    console.log(node1.data);
    console.log(node1.next.data);
    console.log(node1.next.next.data);
  ```

`Node` 类包含两个属性：`data` 用来保存节点上的数据，`next` 用来保存指向下一个节点的链接。

## 理解指针或引用的含义

指针或者引用，他们的意思都是一样的，都是存储所指对象的内存地址。

将某个变量赋值给指针，实际上就是将某个变量的内存地址赋值给指针，或者反过来说，指针中存储了这个变量的内存地址，指向了这个变量，通过指针就能找到这个变量。

结点中的 next 指针，储存了下一个结点的内存地址

## 警惕指针丢失和内存泄露

在写链表代码的时候，尤其是我们的指针，会不断的改变，指来指去的。所以在写的时候，一定注意不要弄丢了指针。

```text
  new_node -> next = p -> next;
  p -> next = new_node;
  new_node.next = p.next
  p.next = new_node
```

新结点指向 p 指针原本指向的结点，然后 p 指针再指向新结点

## 重点留意边界条件处理

当我们向一个空链表中插入第一个结点时，就需要特殊处理了。当链表为空时，也就是链表的 head 为空，那直接赋值即可，如下：

  ```js
  if(head == null) {
      head = new_node;
  }
  ```

写链表代码时，要经常注意边界条件是否考虑到了：

  - 如果链表为空时，代码是否能正常工作？
  - 如果链表只有一个结点时，代码是否能正常工作？
  - 如果在处理头结点和尾结点时，代码是否能正常工作？

## LinkList 类骨架

```js
  function LinkedList() { 
  
    let Node = function(val){ // {1} 
      this.val = val
      this.next = null
    }
  
    let length = 0 // {2} 利用闭包模拟私有变量
    let head = null // {3} 利用闭包模拟私有变量
  
    this.append = function(data){}; 
    this.insert = function(position, data){}; 
    this.removeAt = function(position){}; 
    this.remove = function(data){}; 
    this.indexOf = function(data){}; 
    this.isEmpty = function() {}; 
    this.size = function() {}; 
    this.getHead = function(){}; 
    this.toString = function(){}; 
    this.print = function(){}; 
  } 
```

```js
class Node {
    val = null;
    next = null;

    constructor(data) {
        this.val = data;
    }

}
class LinkedList {
    head = null;
    length = 0;
}
```

## append()

看一段完整的添加节点代码：

  ```js
  this.append = function(val){
      let node = new Node(val)
      let current
      if(head === null){
        // 链表中的第一个节点
        // 让head指向该节点
        head = node
      }else{
        // 向链表的末尾添加节点
        // 循环链表找到最后的节点
        current = head
        while(current.next){
          current = current.next
          // 最后的节点 next 为 null，跳出循环
        }
        // 跳出了while循环
        // 最后一项，将其 next 赋为 node，建立链接 
        current.next = node
      }
      length++ // 更新链表长度
    }
  ```

如果头结点不存在的话，头结点等于尾结点。如果头结点存在的话，找到尾结点来扩充链表的数据

> 列表最后一个节点的下一个元素始终是 null。 我们知道它会是列表的最后一项。

```JS
    append(data) {
        if (this.head === null) {
            this.head = new Node(data)
        }
        else {
            let cur = this.head;
            while (cur.next !== null) {
                cur = cur.next;
            }
            // 跳出循环之后, cur 为尾结点, cur.next 为 null
            cur.next = new Node(data);
        }
        // 更新链表长度
        this.length++;
    }
```

## removeAt()

再来看单链表结点的删除操作。如果在 p 结点后删除一个结点，只需要关注一步即可：

  ```text
  p -> next = p -> next -> next;
  p.next = p.next.next
  // 这种图把箭头替换成点就好了
  ```

但是，当链表中只剩一个结点 head 时，也需要特殊处理才可以，如下：

  ```js
  if(head.next == null){
      head = null;
  }
  ```

```js
  this.removeAt = function(position){ 
      // 检查越界值 
      if (position > -1 && position < length){ // {1} 
        let current = head, // {2} 
            previous, // {3} 
            index = 0 // {4} 
        // 移除第一项 
        if (position === 0){ // {5} 
          head = current.next 
        } else { 
          // index++ < position
          while (index < position){ // {6} 
            previous = current     // {7} 
            current = current.next // {8} 
            index++
          } 
          // 跳出了循环，此时index==position
          // 将previous与current的下一项链接起来
          // 跳过current，等着被垃圾回收器清除，从而移除它 
          previous.next = current.next // {9} 
        } 
        length-- // {10} 
        return current.element 
      } else { 
        // 不是有效的位置，返回null
        return null // {11} 
      } 
    }
```

```js
var removeElements = function(head, val) {
    const dummyHead = new ListNode(0);
    dummyHead.next = head;
    let temp = dummyHead;
    while (temp.next !== null) {
        if (temp.next.val == val) {
            temp.next = temp.next.next;
        } else {
            temp = temp.next;
        }
    }
    return dummyHead.next;
};
```

移除最后一个元素：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522103559436.png)

移除中间的元素：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522103623120.png)

## insert()

使用这个方法可以在任意位置插入一个元素。

```js
  this.insert = function(position, val){ 
      //检查越界值 
      if (position >= 0 && position <= length){ //{1} 
        let node = new Node(val), 
            current = head, 
            previous, 
            index = 0; 
        if (position === 0){ // 在第一个位置添加 
          node.next = current //{2} 
          head = node; 
        } else { 
          while (index++ < position){ //{3} 
            previous = current 
            current = current.next 
          } 
          node.next = current //{4} 
          previous.next = node //{5} 
        } 
        this.length++ // 更新列表的长度 
        return true 
      } else { 
        return false //{6} 
      } 
    } 
```

在列表的起点添加一个元素：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522103645304.png)

在列表尾部添加一个元素：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522103657317.png)

在列表中间添加一个元素：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522103704328.png)

## toString()

toString 方法会把 LinkedList 对象转换成一个字符串

```js
    toString() {

        let current = this.head,
            string = '';

        while (current) {
            string += current.val + (current.next ? '->' : '');
            current = current.next;
        }
        return string;
    };
```

## indexOf()

indexOf 方法接收一个元素的值，如果在列表中找到它，就返回元素的位置，否则返回 -1。

```js
    this.indexOf = function(val){ 
      let current = head //{1} 
      let index = -1
      while (current) { //{2} 
        if (val === current.val) { 
          return index       //{3} 
        } 
        index++                //{4} 
        current = current.next //{5} 
      }
      return -1 
    }
```

## remove()

在实现了 indexOf() 的基础上，实现根据数据值删除节点

```js
  this.remove = function(val){ 
    let index = this.indexOf(val) 
    // 即使返回-1，也会被remove的边界限制
    return this.removeAt(index) 
  } 
```

## removeAll()

```js
    /**
     * 删除所有值为 val 的元素
     */
    removeAll(val) {
        const dummyHead = new Node(0);
        dummyHead.next = this.head;
        let cur = this.head;
        let prev = dummyHead;

        while(cur !== null) {
            if (cur.data === val) {
                // 链表引用链跳过了 cur, 即实现了删除
                prev.next = cur.next;
                cur = cur.next;
                this.length--;
            }
            else {
                prev = cur;
                cur = cur.next;
            }
        }

        this.head = dummyHead.next;
        return dummyHead.next;
    }

    removeALLRecursion(cur = this.head, val) {
        if (cur === null) {
            return cur;
        }

        cur.next = this.removeALLRecursion(cur.next, val);
        return cur.data === val ? cur.next : cur;
    }
```

## removeNthFromEnd()

```js
    removeNthFromEnd(n) {
        const dummyHead = new Node(0);
        dummyHead.next = this.head;
		
        this.removeNthFromEndRecursion(dummyHead, n);
        this.head = dummyHead.next;
        return dummyHead.next;
    }
		
    removeNthFromEndRecursion = function(cur = this.head, n) {
        // 因为是删除倒数的, 所以用递归的写法特别合适, 用迭代反而处理起来不方便
        if (cur.next === null) {
            return 1
        }
        let reverseCount = this.removeNthFromEndRecursion(cur.next, n)
		
        if (reverseCount === n) {
            cur.next = cur.next.next;
        }
		
        return ++reverseCount
    };
```

## 其他方法

```js
  this.isEmpty = function() { 
    return length === 0 
  } 
  
  this.size = function() { 
    return length 
  } 
  
  this.getHead = function(){ 
    return head 
  } 
```

head 变量是 LinkedList 类的私有变量（这意味着它不能在 LinkedList 实例外部被访问和更改，只有通过 LinkedList 实例才可以）。但是，如果我们需要在类的实现外部循环访问列表，就需要提供一种获取类的第一个元素的方法。

## 翻转链表

### 迭代

三指针法

```js
/**
 * 三指针法实现迭代翻转
 */
reverseListIteration() {
	let cur = this.head;
	let prev = null;
	let next = null;
	
	while(cur!== null) {
		next = cur.next;
		// next 指向 prev 这一步实现了翻转
		cur.next = prev;
		// 更新迭代变量
		prev = cur;
		cur = next;
	}
	
	// 更新头节点
	this.head = prev;
}
```

断链之前要保存，第一步，第二部

### 递归

递归版本稍微复杂一些，其关键在于反向工作。假设列表的其余部分已经被反转，现在我该如何反转它前面的部分？

![](/img/user/programming/basic/data-structure/linked-list/image-20230522104201584.png)

要小心的是 n 1 的下一个必须指向 Ø 。如果你忽略了这一点，你的链表中可能会产生循环。如果使用大小为 2 的链表测试代码，则可能会捕获此错误。

  ```js
    /**
     * 后序遍历 递归实现翻转
     */
    reverseListRecursion(cur = this.head) {
        if (cur.next === null) {
            this.head = cur;
            return cur;
        };

        let prev = this.reverseListRecursion(cur.next);
        cur.next.next = cur;
        cur.next = null;

        this.head = prev;
        return prev;
    }
  ```

前序遍历递归, 实际上就和迭代的逻辑是一样的

```js
    reverseListForwardRecursion(prev = null, cur = this.head) {

        const next = cur.next;
        cur.next = prev;
        prev = cur;
        cur = next;

        if (cur === null) {
            this.head = prev;
            return prev;
        }

        this.reverseListForwardRecursion(prev, cur)
    }
```

### swapPairs

![24. 两两交换链表中的节点](../leetcode/24.%20两两交换链表中的节点.md)

### reverseBetween

![92. 反转链表 II](../leetcode/92.%20反转链表%20II.md)

### reverseKGroup

![25. K 个一组翻转链表](../leetcode/25.%20K%20个一组翻转链表.md)

## 总结

[几乎刷完了力扣所有的链表题，我发现了这些东西。。。 - 知乎](https://zhuanlan.zhihu.com/p/281404491)

### 链表迭代的理解

往往涉及多个指针, prev, cur, next

想改哪一个就先改哪一个, 但是如果有循环依赖就必须抽一个 temp 了.否则很容易想得太混乱, 所以最保险的方法还是先用临时变量保存下来, 之后再简化临时变量就行

### 链表递归的理解

链表的定义具有递归性质, 因此链表的题目常可以用递归的方法求解.

所有的循环都可以写成递归, 所以递归的本质是和循环一样的, 关键就是停止的条件:

+ 循环停止的条件
+ 递归停止的条件

链表的递归关键是反向工作, 将 head 变量改成 cur 更容易理解, 直接从递归停止的函数开始一层层往上翻就可以实现了

只翻一次就够了, 只需要处理好单次调用里的逻辑就好了

链表的递归也可以理解为深度优先遍历: 根据对节点的操作时机, 可以分为前序遍历和后序遍历

+ 如果是前序遍历，那么你可以想象前面的链表都处理好了，怎么处理的不用管
+ 如果是后续遍历，那么你可以想象后面的链表都处理好了，怎么处理的不用管

前序遍历容易改写成不需要栈的递归, 不那么准确的说, 可以更容易改成 while

### 通过 dummyHead 避免特判

通过在开始前创建一个哑节点 dummyHead, dummyHead.next 指向 head, 来避免对 head 的特判

# 链表的类型

## 单链表（单向）

最简单最常用的是 **单链表**，此链表的每个结点只包含一个指针域。

我们实现的就是单向链表，其中有两个结点是比较特殊的。他们分别是第一个结点和最后一个节点。我们习惯性地把第一个结点叫做**头结点**，把最后一个结点叫做**尾结点**。头结点是用来记录链表的基地址。有了它，我们就可以遍历得到整条链表。而尾结点特殊地方它的指针不是指向下一个地方，而是指向一个空地址 NULL，表示这是链表上最后一个结点。

我们可以判断当前结点的 next 是否为空，就知道循环是否结束。

## 循环链表

将单链表中尾结点的指针由空指针指向头节点，就使整个单链表形成一个环，这种头尾相接的单链表就简称为循环链表。

![](/img/user/programming/basic/data-structure/linked-list/image-20230522103828048.png)

其实循环链表和单链表的主要差异就在于循环的判断条件上，原来是判断当前结点的 next 是否为空，现在则是判断当前结点的 next **是否等于头结点**。

那头结点又是怎么确定呢? 由私有变量唯一指定，每次遍历都是从他开始

## 双向链表

双向链表是在单链表的每个结点中，再设置一个指向其前驱结点的指针域。所以在双向链表中的结点都有**两个指针域**，一个指向直接后继，另一个指向直接前驱。

![](/img/user/programming/basic/data-structure/linked-list/image-20230522103859224.png)

从上图中可以看出来，双向链表需要额外的两个空间来存储后继结点和前驱结点的地址。所以，如果存储同样多的数据，双向链表要比单链表占用更多的内存空间。

虽然两个指针比较浪费存储空间，但是可以支持双向遍历，这样也带来了双向链表操作的灵活性。

### DoublyLinkedList 类

```js
  function DoublyLinkedList() { 
   
    let Node = function(element){ 
   
      this.element = element; 
      this.next = null; 
      this.prev = null; //新增的 
    }; 
   
    let length = 0; 
    let head = null; 
    let tail = null; //新增的 
   
    //这里是方法 
  } 
```

双向链表提供了两种迭代列表的方法：从头到尾，或者反过来。

我们也可以访问一个特定节点的**下一个或前一个元素**。在单向链表中，如果迭代列表时错过了要找的元素，就需要回到列表起点，重新开始迭代。这是双向链表的一个优点。

### insert()

向双向链表中插入一个新项跟（单向）链表非常类似。区别在于，单向链表只要控制一个 next 指针，而双向链表则要同时控制 next 和 prev（previous，前一个）这两个指针。

```js
  this.insert = function(position, element){ 
  
    //检查越界值 
    if (position >= 0 && position <= length){ 
  
      let node = new Node(element), 
          current = head, 
          previous, 
          index = 0; 
  
      if (position === 0){ //在第一个位置添加 
  
        if (!head){ //新增的 {1} 
          head = node; 
          tail = node; 
        } else { 
          node.next = current; 
          current.prev = node; //新增的 {2} 
          head = node; 
        } 
      } else  if (position === length) { //最后一项 //新增的 
  
        current = tail; // {3} 
        current.next = node; 
        node.prev = current; 
        tail = node; 
  
      } else { 
        while (index++ < position){ //{4} 
          previous = current; 
          current = current.next; 
        } 
        node.next = current; //{5} 
        previous.next = node; 
  
        current.prev = node; //新增的 
        node.prev = previous; //新增的 
      } 
  
      length++; //更新列表的长度 
  
      return true; 
  
    } else { 
      return false; 
    } 
  }; 
  
```

在双向链表头部插入一个节点：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522103952471.png)

在双向链表最后添加一个节点：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522104000733.png)

在列表中间插入一个新节点：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522104007719.png)

我们可以对 insert 和 remove 这两个方法的实现做一些改进。在结果为否的情况下，我们可以把元素插入到列表的尾部。性能也可以有所改进，比如，如果 position 大于 length/2，就最好从尾部开始迭代，而不是从头开始（这样就能迭代更少列表中的元素）

### remove()

从双向链表中移除元素跟单向链表非常类似。唯一的区别就是还需要设置前一个位置的指针。

  ```js
  this.removeAt = function(position){ 
  
    //检查越界值 
    if (position > -1 && position < length){ 
  
      let current = head, 
          previous, 
          index = 0; 
  
      //移除第一项 
      if (position === 0){ 
  
        head = current.next; // {1} 
  
        //如果只有一项，更新tail //新增的 
        if (length === 1){ // {2} 
          tail = null; 
        } else { 
          head.prev = null; // {3} 
        } 
  
      } else if (position === length-1){ //最后一项 //新增的 
  
        current = tail; // {4} 
        tail = current.prev; 
        tail.next = null; 
      } else { 
  
        while (index++ < position){ // {5} 
  
          previous = current; 
          current = current.next; 
        } 
  
        //将previous与current的下一项链接起来——跳过current 
        previous.next = current.next; // {6} 
        current.next.prev = previous; //新增的 
      } 
  
      length--; 
  
      return current.element; 
  
    } else { 
      return null; 
    } 
  };
```

移除第一个元素：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522104020986.png)

从最后一个位置移除元素：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522104027200.png)

从列表中间移除一个元素：

![](/img/user/programming/basic/data-structure/linked-list/image-20230522104032961.png)

## 双向循环链表

既然单链表可以有循环链表，那么双向链表当然也可以是循环链表。你可以停下来想想双向循环链表长什么样子。

![](/img/user/programming/basic/data-structure/linked-list/image-20230522104044553.png)
