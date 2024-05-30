---
{"aliases":[],"tags":[],"review-dates":["2019-05-03","2019-07-19","2019-09-09"],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:06:52 pm","date-modified":"2023-06-18-Sun, 10:10:34 am","permalink":"/programming/basic/algorithm/data-structure-1/","dgPassFrontmatter":true}
---


https://github.com/EmilyYoung71415/StructuresandAlgorithms-Code

# 数组

数组是一种最简单和最广泛使用的数据结构，其它数据结构比如堆栈和队列都源自数组。

下图是一个大小为 4 的简单数组，包含几个元素（1,2,3 和 4）。

![img](/img/user/programming/basic/algorithm/data-structure-1/v2-7db1817e47dcc0a3ff15881f64e1b298_hd.jpg)

每个数据元素会被分配一个正的数值，叫作“索引”，它对应该元素在数组中的位置。大部分编程语言都将初始索引定义为 0.

## 种类

一维数组

二维数组

多维数组

## 基本操作

Insert——在给定索引位置插入一个元素

Get——返回给定索引位置的元素

Delete——删除给定索引位置的元素

Size——获取数组内所有元素的总数

## 常问的数组面试问题：

找到数组中第二小的元素

找到数组中第一个没有重复的整数

合并两个分类数组

重新排列数组中的正值和负值

# 队列

与栈类似，队列是另一种线性数据结构，以顺序方式存储元素。只允许在表的前端（front）进行删除操作，在表的后端（end）进行插入操作，和栈一样，队列是一种操作受限制的线性表。进行插入操作的端称为**队尾**，进行删除操作的端称为**队首**。

堆栈和队列之间唯一的显着区别是，队列不是使用 LIFO 方法，而是应用 FIFO 方法，这是 First in First Out（先入先出）的缩写。

队列的完美现实例子：一列人在售票亭等候。如果有新人来，他们是从末尾加入队列，而不是在开头——站在前面的人将先买到票然后离开队列。

![1553148348125](/img/user/programming/basic/algorithm/data-structure-1/1553148348125.png)

## 普通队列

从数据存储的角度看，实现队列有两种方式，一种是以数组做基础，一种是以链表做基础，数组是最简单的实现方式，本文以基础的数组来实现队列。

我们定义以下几个队列的方法：

+ enqueue 从队尾添加一个元素（新来一个办业务的人，排在了队尾）
+ dequeue 从队首删除一个元素（队伍最前面的人，办完了业务，离开了）
+ head 返回队首的元素（后边的人好奇看一下，队伍最前面的人是谁）
+ tail 返回队尾的元素（前边的人好奇看一下，队伍最后面的人是谁）
+ size 返回队列的大小（营业员数一下队伍有多少人）
+ isEmpty 返回队列是否为空（营业员查看当前是不是有人在排队）
+ clear 清空队列（此窗口暂停营业，大家撤了吧）

然后我们利用 es6 的 class 的实现以上的方法

```js
class Queue {
  constructor() {
    this.items = []; // 存储数据
  }
  enqueue(item) { // 向队尾添加一个元素
    this.items.push(item);
  }
  dequeue() { // 删除队首的一个元素
    return this.items.shift();
  }
  head() { // 返回队首的元素
    return this.items[0];
  }
  tail() { // 返回队尾的元素
    return this.items[this.items.length - 1];
  }
  size() { // 返回队列的元素
    return this.items.length;
  }
  isEmpty() { // 返回队列是否为空
    return this.items.length === 0;
  }
  clear() { // 清空队列
    this.items = [];
  }
}
```

## 优先队列

优先队列。元素的添加和移除是基于优先级的。

>  一个现实的例子就是机场登机的顺序。头等舱和商务舱乘客的优先级要高于经济舱乘客。在有些国家，老年人和孕妇（或带小孩的妇女）登机时也享有高于其他乘客的优先级。

实现一个优先队列，有两种选项：

+ 设置优先级，然后在**正确的位置添加元素**，可以使用**默认的出列操作**
+ 或者用**默认的入列操作**添加元素，然后按照**优先级移除元素**。

我们在这里实现的优先队列称为最小优先队列，因为优先级的值较小的元素被放置在队列最前面（1 代表更高的优先级）。最大优先队列则与之相反，把优先级的值较大的元素放置在队列最前面。

> 想想生活中排队取号，，优先级 priority 数值越小，排在前面，优先级高。

```js
function PriorityQueue() { 
  let items = []; 
  // 新建优先级对象的工厂函数
  function QueueElement (element, priority){ // {1} 
    this.element = element; 
    this.priority = priority; 
  } 
 
  this.enqueue = function(element, priority){ 
    let queueElement = new QueueElement(element, priority); 
 
    let added = false; 
    for (let i=0; i<items.length; i++){ 
      
      if (queueElement.priority < items[i].priority){ // {2} 
        // 找到一个优先级比要添加的元素小的，排在他的后面
        items.splice(i,0,queueElement); // {3} 
        added = true; 
        break; // {4} 
      } 
    } 
    if (!added){ 
      // 循环之后 added仍为 false ，说明优先级是最低的，排在队尾
      items.push(queueElement); //{5} 
    } 
  }; 
 
  this.print = function(){ 
    for (let i=0; i<items.length; i++){ 
      console.log(`${items[i].element} - ${items[i].priority}`); 
    } 
  }; 
  //其他方法和默认的Queue实现相同 
} 

let priorityQueue = new PriorityQueue(); 
priorityQueue.enqueue("John", 2); 
priorityQueue.enqueue("Jack", 1); 
priorityQueue.enqueue("Camila", 1); 
priorityQueue.print(); 
```

![1554538303840](/img/user/programming/basic/algorithm/data-structure-1/1554538303840.png)

## 循环队列

循环队列是一种线性数据结构，其操作表现基于 FIFO（先进先出）原则并且队尾被连接在队首之后以形成一个循环。它也被称为“环形缓冲器”。 循环队列的一个好处是我们可以**利用这个队列之前用过的空间**。在一个普通队列里，一旦一个队列满了，我们就不能插入下一个元素，即使在队列前面仍有空间。但是使用循环队列，我们能使用这些空间去存储新的值。 你的实现应该支持如下操作：

+ circularQueue(k): 构造器，设置队列长度为 k 。
+ Front: 从队首获取元素。如果队列为空，返回 -1 。
+ Rear: 获取队尾元素。如果队列为空，返回 -1 。
+ enQueue(value): 向循环队列插入一个元素。如果成功插入则返回真。
+ deQueue(): 从循环队列中删除一个元素。如果成功删除则返回真。
+ isEmpty(): 检查循环队列是否为空。
+ isFull(): 检查循环队列是否已满。

### isEmpty()

想要实现上面的功能，其实主要就是判断循环队列是否为空 `isEmpty`，是否已满 `isFull`，这里我们使用两个指针来表示队首（`head`）和队尾的指针（`tail`）。

front 指针始终指向队首的元素，并且始终初始化为 0，因为：

+ 如果初始化为 -1，需要在首次插入元素后更新 front，没有必要
+ 正常一个空队列开始插入元素，front 肯定是从 0 开始的

rear 指针有两种选择：

1. rear 始终指向队尾的元素，这样 rear 应该初始化为 -1，需要考虑：
    1. enQueue 插入先设置 rear++，首次时插入到 0
    2. 如何判断为空？(rear + 1) % size === front 即为空
    3. 如何判断为满？(rear + 1) % size === front 即为满
    4. 空满的判断是一样的，不好处理
2. rear 始终指向下一个插入元素的位置，这样 rear 应该初始化为 0，需要考虑
    1. enQueue 首次时插入到 0，插入后再设置 rear++
    2. 如何判断为空？rear === front 即为空，下一个插入的位置为队首的位置，说明是空的
    3. 如何判断为满？rear % k === front 即为满
    4. size 是 1 base 的，rear 是 0 base 的，但是因为 rear 始终指向下一个插入的位置，所以当队列为满时 rear === k
    5. rear + 1 是为了首次 enQueue 判断 0 % 任何数都为 0
    6. 所以 size 也要等于 k + 1
    7. 所以 (rear + 1) % (k + 1) === front 即为满

```js
class circleQueue{
    constructor(k){
        this.size = k + 1
        this.front = 0
        this.rear = 0
        this.data = []
    }

    isEmpty(){
        return this.front === this.rear
    }
    isFull(){
        return (this.rear + 1 ) % this.size === this.front
    }
}
```

### enQueue()

```js
class MyCircularQueue {
    constructor(k){
        this.size = k + 1
        this.front = 0
        this.rear = 0
        this.data = new Array(k);
    }

    isEmpty(){
        return this.front === this.rear
    }
    isFull(){
        return (this.rear + 1 ) % this.size === this.front
    }

    enQueue(value) {
        if (this.isFull()) return false
        this.data[this.rear] = value
        this.rear = (this.rear + 1) % this.size
        return true
    }

    deQueue() {
        if (this.isEmpty()) return false;

        // 头指针往下移动一格就是删除了，不用真的 pop 元素
        this.front = (this.front + 1) % this.size
        return true
    }

    head(){
        return this.isEmpty() ? -1 : this.data[this.front]
    }

    tail(){
        return this.isEmpty() ? -1: this.data[(this.rear - 1 + this.size) % this.size]
    }
}
```

### 实例

循环队列的一个例子就是击鼓传花游戏（Hot Potato）。

>  在这个游戏中，孩子们围成一个圆圈，把花尽快地传递给旁边的人。某一时刻传花停止，这个时候花在谁手里，谁就退出圆圈结束游戏。重复这个过程，直到只剩一个孩子（胜者）。

```js
function hotPotato (nameList, num){ 
 
  let queue = new Queue(); // {1} 
 
  for (let i=0; i<nameList.length; i++){ 
    queue.enqueue(nameList[i]); // {2} 
  } 
 
  let eliminated = ''; 
  while (queue.size() > 1){ 
    for (let i=0; i<num; i++){ 
      // 将队首的放入队尾，执行num次
      queue.enqueue(queue.dequeue()); // {3} 
    } 
    // 一轮之后(num次),此时在队尾的人被淘汰
    eliminated = queue.dequeue();// {4} 
    console.log(eliminated + '在击鼓传花游戏中被淘汰。'); 
  } 
  // 队伍的长度等于1，游戏结束
  return queue.dequeue();// {5} 
} 
 
let names = ['Jack','John','Camila','Ingrid','Carl'];
let winner = hotPotato(names, 7);
console.log('The winner is: ' + winner); 
```

## 面试题

设循环对列的容量为 50，从 0 到 49，经过入队和出队之后，有 front=11.rear=29

## 队列的应用

### 约瑟夫环问题

+ 有一个数组存放了 100 个数据 0-99，要求每隔两个数删除一个数，到末尾时再循环至开头继续进行，求最后一个被删除的数字。

  > 比如说：有十个数字：0，1，2，3，4，5，6，7，8，9，每隔两个数删除一个数，就是 2 5 8 删除，如果只是从 0 到 99 每两个数删除一个数，其实挺简单的，但是我们还得考虑到末尾的时候还有再重头开始，还得考虑删除掉的元素从数组中删除。那我们如果队列的话，就比较简单了

+ 思路分析

  1. 先将这 100 个数据放入队列，用 while 循环，终止的条件是队列里只有一个元素。
  2. 定义 index 变量从 0 开始计数，从队列头部删除一个元素，index + 1
  3. 如果 index%3 === 0 ,说明这个元素需要被移除队列，否则的话就把它移动到队列的尾部
  4. 经过 while 循环后，不断的有元素出队列，最后队伍中只会剩下一个被删除的元素

  ```js
  var arr = []; 
  // 准备0-99  100个数据
  for (var i = 0; i < 100; i++) {
    arr.push(i);
  }
  // 每隔两个数删除一个数
  function delRang(arr) {
    var queue = new Queue(); // 调用之前实现Queue类
    var len = arr.length;
    for (var i = 0; i < len; i++) {
      queue.enqueue(i); // 将数据存入队列
    }
    var index = 0;
    while (queue.size() !== 1) { // 循环判断队列里大小否为还剩下1个
      var item = queue.dequeue(); // 出队一个元素，根据当前的index来判断是否需要移除
      index += 1;
      if (index % 3 !== 0) {
        queue.enqueue(item); // 不是的话，则添加到队尾，继续循环
      }
    }
    console.log(queue.head()); // 90
    return queue.head(); // 返回最后一个元素
  }
  
  delRang(arr);
  ```

### 打印杨辉三角

+ ![img](https://gss2.bdstatic.com/9fo3dSag_xI4khGkpoWK1HF6hhy/baike/c0%3Dbaike92%2C5%2C5%2C92%2C30/sign=b15f585fc3ea15ce55e3e85bd7695196/7e3e6709c93d70cf89a39cd7f2dcd100bba12b8a.jpg)
+ 思路分析

  1. 杨辉三角中的每一行，都依赖于上一行，假设现在队列里已经存储了第 n-1 行的数据，那么输出第 n 行时，只需要将队列里的数据依次出队列，进行计算得到下一行的数值并将计算所得存储到队列中
  2. 然后我们需要两层 for 循环，将 n-1 行和 n 行的数据分开打印；有上图可以得出规律，n 行只有 n 个数，所以我们就可以使用 for 循环控制 enqueue 的次数，n 次结束后，队列里存储的就是计算好的第 n+1 行的数据

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <title>打印杨辉三角</title>
    </head>
    <body>
      <script src="./queue.js"></script>
      <script>
        // 杨辉三角
        {
          function yangHui(n) {
            var queue = new Queue();
            queue.enqueue(1); // 先在队列中存储第一行的数据
            for (var i = 1; i <= n; i++) { // 第一层循环控制层数
              var line = "";
              var pre = 0;
              for (var j = n; j > i; j--) { // 打印空格
                document.write("&nbsp;");
              }
              for (var j = 0; j < i; j++) { // 第二层控制当前层的数据
                var item = queue.dequeue();
                var value = item + pre; // 计算下一行的值
                pre = item;
                line += item + " ";
                queue.enqueue(value);
              }
              queue.enqueue(1); // 将每层的最后一个数值 1 存入队列中
              document.write(line + "<br />");
            }
          }
          yangHui(10);
        }
      </script>
    </body>
  </html>
  ```

### 常问的队列面试问题：

- 使用队列来实现堆栈
- 颠倒队列中前 k 个元素的顺序
- 使用队列生成从 1 到 n 的二进制数
- 使用队列的例子还有很多，比如逐层打印一颗树上的节点，还有消息通讯使用的 socket，当大量客户端向服务端发起连接，而服务端拥挤时，就会形成队列，先来的先处理，后来的后处理，当队列满时，新来的请求直接抛弃掉。
- 数据结构在系统设计中的应用非常广泛，只是我们水平达不到那个级别，知道的太少，但如果能理解并掌握这些数据结构，那么就有机会在工作中使用它们并解决一些具体的问题，当我们手里除了锤子还有电锯时，那么我们的眼里就不只是钉子，解决问题的思路也会更加开阔。

# 队列与栈

## 栈实现队列

+ 栈是先进后出，队列是先进先出，但可以用两个栈来模拟一个队列的功能，来实现队列中主要的 enqueue，dequeue， head 方法。

**思路分析**

+ 我们所学的每一种数据结构，本质上都是对数据如何存储和使用的研究，这就必然涉及到增删改查，那么考虑实现这些方法时，我们优先考虑如何实现数据的增加，只有存在了数据，才能够后续的操作。
+ 所以如何实现队列中的增加数据方法 enqueue 呢？
  + 给两个栈分别命名为 stack1，stack2。那有了这两个栈以后，可以选取其中一个来存储数据，比如说 stack1，那么队列的 enqueue 方法就很容易了，直接利用栈的 push 方法就能够添加数据。
+ 接下来考虑队列中的删除 dequeue 方法
  + 首先要注意下 dequeue 方法是删除队列中的头部元素，而此时队首是在 stack1 栈底的，目前来说还取不到。
  + 这个时候 stack2 该上场了，可以把 stack1 中的元素都依次移除并压入 stack2 中，这样的话，stack2 的栈顶就变成了队首，不就可以利用 stack2 的 pop 方法来移除元素了嘛。
+ 那队列的 head 方法呢
  + 执行完 stack2 的 pop 方法后，还需要把数据再移回 stack1 里吗？
  + 其实不需要了，因为此时队首正好是 stack2 的栈顶，而队列的 head 方法就可以利用栈的 top 方法来实现了。
  + 如果 stack2 是空的怎么办？那 stack1 的元素都移除到 stack2 就可以了。
  + 如果 stack1 也是空的呢，那就说明队列中没有元素了，此时返回 null 就可以了。
+ 注意到了吗，这里又用到了**分而治之** 的思想，还记得之前在哪里用过吗？对，就是在给栈添加获取最小值方法的时候用过，当时也是用了两个栈来实现。
+ 这里的话 enqueue 始终都操作 stack1，dequeue 和 head 方法始终都操作 stack2。

  ```js
  {
    class StackQueue {
      constructor() {
        this.stack1 = new Stack();
        this.stack2 = new Stack();
      }
      // 初始化stack，伪造私有方法
      _initStack() {
        if (this.stack1.isEmpty() && this.stack2.isEmpty()) {
          return null; // 如果两个栈都是空的，那么队列中就没有元素
        }
        if (this.stack2.isEmpty()) {
          // 如果stack2是空的，那么此时stack1一定不为空
          while (!this.stack1.isEmpty()) {
            this.stack2.push(this.stack1.pop()); // 把stack1的元素移除到stack2中
          }
        }// 如果stack2不是空的，那就什么都不做，队列无法访问队尾的数据，
      }
      // 向队尾添加一个元素
      enqueue(item) {
        this.stack1.push(item); // 把数据存入到stack1中
      }
      // 删除队首的一个元素
      dequeue() {
        this._initStack();
        return this.stack2.pop();
      }
      // 返回队首的元素
      head() {
        this._initStack();
        return this.stack2.top();
      }
    }
    var stackQueue = new StackQueue();
    stackQueue.enqueue(1);
    stackQueue.enqueue(4);
    stackQueue.enqueue(8);
    console.log(stackQueue.head()); // 1
    stackQueue.dequeue();
    stackQueue.enqueue(9);
    console.log(stackQueue.head()); // 4
    stackQueue.dequeue();
    console.log(stackQueue.head()); // 8
    console.log(stackQueue.dequeue()); // 8
    console.log(stackQueue.dequeue()); // 9
  }
  ```

## 队列实现栈

+ 队列是先进先出，栈是先进后出，(不断重复这两个知识点) 但可以用两个队列来模拟一个栈的功能，来实现栈中主要的 push，pop， top 方法。
+ 你可能会想到利用上边的套路来实现这个需求，但是最后的结果你会发现是不正确的。因为 把 stack1 的元素移除到 stack2 中，此时的两个栈中的数据就首尾交换了，而如果此处换成队列 this.queue2.enqueue(this.queue1. dequeue())，
+ 你会发现由于队列的特性，此时的两个队列还是一样的，首尾并没有交换。

**思路分析**

+ 和上边一样，我们先考虑如何实现栈的存储数据 push 方法:
  + 给两个队列分别命名为 queue1，queue2。实现 push 方法时，利用队列的 enqueue 方法，如果两个队列都为空，那么默认向 queue1 里添加数据；如果有一个不为空，那么就向这个不为空的队列里添加数据。
+ top 方法就简单了:
  + 利用队列的 tail 方法，两个队列要么都为空，要么有一个不为空，那么返回不为空队列的尾部元素就是栈顶元素了
+ 接下来思考比较复杂的 pop 方法:
  + pop 方法删除的是栈顶，但此时栈顶元素是队列的尾部元素，而队尾元素是不能删除的。
  + 但每次执行 pop 时，可以将不为空的 a 队列里的元素循环删除并放入到另一个 b 队列中，直到 a 队列中只剩下一个元素，此时 a 队列的这个元素就是队列的尾部元素，也就是栈顶元素了，那 pop 方法就简单了，利用 a 队列的 dequeue 方法就可以了。
+ 在具体的实现中，需要额外定义两个变量，dataQueue 和 emptyQueue:
  + dataQueue 始终指向那个不为空的队列
  + emptyQueue 始终指向那个为空的队列

  ```js
  {
    class QueueStack {
      constructor() {
        this.queue1 = new Queue();
        this.queue2 = new Queue();
        this.dataQueue = null; // 存放数据的队列
        this.emptyQueue = null; // 存放备份数据的队列
      }
      // 初始化队列数据，模拟私有方法 确认哪个队列存放数据，哪个队列做备份 
      _initQueue() {
        if (this.queue1.isEmpty()) {
          this.dataQueue = this.queue2;
          this.emptyQueue = this.queue1;
        } else {
          // 都为空的话 默认是 队列1
          this.dataQueue = this.queue1;
          this.emptyQueue = this.queue2;
        }
      }
      // 往栈里压入一个元素
      push(item) {
        this._initQueue();
        this.dataQueue.enqueue(item);
      }
      // 返回栈顶的元素
      top() {
        this._initQueue();
        return this.dataQueue.tail();
      }
      // 把栈顶的元素移除
      pop() {
        this._initQueue();
        while (this.dataQueue.size() > 1) {
          // 利用备份队列转移数据，
          this.emptyQueue.enqueue(this.dataQueue.dequeue()); // 数据队列和备份队列交换了身份
        }
        return this.dataQueue.dequeue(); // 移除数据队列的头部元素
      }
    }
    var queueStack = new QueueStack();
    queueStack.push(1);
    queueStack.push(2);
    queueStack.push(4);
    console.log(queueStack.top()); // 栈顶是 4
    console.log(queueStack.pop()); // 移除 4
    queueStack.push(5);
    console.log(queueStack.top()); // 栈顶变成 5
    queueStack.push(6);
    console.log(queueStack.pop()); // 移除 6
    console.log(queueStack.pop()); // 移除5
    console.log(queueStack.top()); // 栈顶是 2
  }
  ```

# 集合

## 概述

+ 集合，这是一种不允许值重复的顺序数据结构。 数组、栈、队列、链表都是顺序数据结构
+ 集合是由一组无序且唯一（即不能重复）的项组成的。这个数据结构使用了与有限集合相同的**数学概念**，但应用在计算机科学的数据结构中。
+ 在数学中，集合也有**并集**、**交集**、**差集**等基本操作。在这一章中我们也会介绍这些操作。

## 集合的实现

### Set 类骨架

+ ```js
  function Set() { 
    let items = {}; 
  } 
  ```
+ 有一个非常重要的细节，我们使用对象而不是数组来表示集合（items）。但也可以用数组实现。在这里我们用对象来实现，稍微有点儿不一样，也学习一下实现相似数据结构的新方法。
+ 同时，JavaScript 的对象不允许一个键指向两个不同的属性，也保证了集合里的元素都是唯一的。
+ 接下来，需要声明一些集合可用的方法（我们会尝试模拟与 ECMAScript 6 实现相同的 Set 类）。

### 基本操作

+ add(value)：向集合添加一个新的项。

  delete(value)：从集合移除一个值。

  has(value)：如果值在集合中，返回 true，否则返回 false。

  clear()：移除集合中的所有项。

  size()：返回集合所包含元素的数量。与数组的 length 属性类似。

  values()：返回一个包含集合中所有值的数组。

### has()

+ 首先要实现的是 has() 方法。这是因为它会被 add、remove 等其他方法调用。下面看看它的实现：

  ```js
  this.has = function(value){ 
    return value in items; 
  }; 
  ```

+ 但这个方法还有更好的实现方式，如下：

  ```js
  this.has = function(value){ 
    return items.hasOwnProperty(value); 
  }; 
  ```

### add()

+ ```js
  this.add = function(value){ 
    // 检查value是否存在于集合中
    if (!this.has(value)){ 
      // 如果不存在
      items[value] = value; //{1} 
      return true; 
    } 
    return false; 
  }; 
  
  ```
+ 返回 true，表示添加了这个值。如果集合中已经有这个值，就返回 false，表示没有添加它。
+ 添加一个值的时候，把它同时作为键和值保存，因为这样有利于查找这个值。

### remove() 和 clear()

+ ```js
  this.remove = function(value){ 
    if (this.has(value)){ 
      delete items[value]; //{2} 
      return true; 
    } 
    return false; 
  }; 
  ```
+ ```js
  this.clear = function(){ 
    items = {}; // {3} 
  }; 
  
  ```

### size()

+ 下一个要实现的是 size 方法（返回集合中有多少项）。这个方法有三种实现方式。
+ 第一种方法是使用一个 length 变量，每当使用 add 或 remove 方法时控制它，就像在上一章中使用 LinkedList 类一样。
+ 第二种方法，使用 JavaScript 内建的 Object 类的一个内建函数（ECMAScript 5 以上版本）：

  ```js
  this.size = function(){ 
    return Object.keys(items).length; //{4} 
  }; 
  ```

+ JavaScript 的 Object 类有一个 keys 方法，它返回一个包含给定对象所有属性的数组。在这种情况下，可以使用这个数组的 length 属性（行{4}）来返回 items 对象的属性个数。以上代码只能在现代浏览器中运行（比如 IE9 以上版本、Firefox 4 以上版本、Chrome 5 以上版本、Opera 12 以上版本、Safari 5 以上版本，等等）。
+ 第三种方法是手动提取 items 对象的每一个属性，记录属性的个数并返回这个数字。这个方法可以在任何浏览器上运行，和之前的代码是等价的：

  ```js
  this.sizeLegacy = function(){ 
    let count = 0; 
    for(let key in items) { //{5} 
      if(items.hasOwnProperty(key)) //{6} 
      ++count; //{7} 
    } 
    return count; 
  }; 
  ```

  > 不能简单地使用 for-in 语句遍历 items 对象的属性，并递增 count 变量的值。还需要使用 hasOwnProperty 方法（以验证 items 对象具有该属性），因为对象的原型包含了额外的属性（属性既有继承自 JavaScript 的 Object 类的，也有属于对象自身，未用于数据结构的）。

### values()

+ values 方法也应用了相同的逻辑，提取 items 对象的所有属性，以数组的形式返回：

  ```js
  this.values = function(){ 
    let values = []; 
    for (let i=0, keys=Object.keys(items); i<keys.length; i++) { 
      values.push(items[keys[i]]); 
    }  
    return values; 
  }; 
  ```

+ ```js
  this.valuesLegacy = function(){ 
    let values = []; 
    for(let key in items) { //{7} 
      if(items.hasOwnProperty(key)) { //{8} 
        values.push(items[key]); 
      } 
    } 
    return values; 
  }; 
  ```

## 集合操作

+ 并集：对于给定的两个集合，返回一个包含两个集合中所有元素的新集合。
+ 交集：对于给定的两个集合，返回一个包含两个集合中共有元素的新集合。
+ 差集：对于给定的两个集合，返回一个包含所有存在于第一个集合且不存在于第二个集

  合的元素的新集合。

+ 子集：验证一个给定集合是否是另一集合的子集。

### 并集

+ 并集的数学概念是集合 A 和集合 B 的并集，表示为： $A∪B $
+ 该集合定义如下： $A∪B = { x | x ∈ A∨x ∈ B } $
+ ```js
  this.union = function(otherSet){ 
    let unionSet = new Set(); //{1} 
   
    let values = this.values(); //{2} 
    for (let i=0; i<values.length; i++){ 
      unionSet.add(values[i]); 
    } 
   
    values = otherSet.values(); //{3} 
    for (let i=0; i<values.length; i++){ 
      unionSet.add(values[i]); 
    } 
   
    return unionSet; 
  }; 
  ```
+ 首先需要创建一个新的集合，代表两个集合的并集（行{1}）。接下来，获取第一个集合（当前的 Set 类实例）所有的值（values），遍历并全部添加到代表并集的集合中（行{2}）。然后对第二个集合做同样的事（行{3}）。最后返回结果。
+ **由于对象属性的唯一性，同样的元素会被覆盖，就做到了并集的效果**

### 交集

+ 交集的数学概念是集合 A 和集合 B 的交集，表示为： $A∩B $
+ 该集合定义如下： $A∩B = { x | x ∈ A∧x ∈ B } $
+ ```js
  this.intersection = function(otherSet){ 
    let intersectionSet = new Set(); //{1} 
   
    let values = this.values(); 
    for (let i=0; i<values.length; i++){ //{2} 
      if (otherSet.has(values[i])){    //{3} 
        intersectionSet.add(values[i]); //{4} 
      } 
    } 
   
    return intersectionSet; 
  } 
  ```
+ intersection 方法需要找到当前 Set 实例中，所有也存在于给定 Set 实例中的元素。首先创建一个新的 Set 实例，这样就能用它返回共有的元素（行{1}）。接下来，遍历当前 Set 实例所有的值（行{2}），验证它们是否也存在于 otherSet 实例（行{3}）之中。可以用这一章前面实现的 has 方法来验证元素是否存在于 Set 实例中。然后，如果这个值也存在于另一个 Set 实例中，就将其添加到创建的 intersectionSet 变量中（行{4}），最后返回它。

### 差集

+ 差集的数学概念是集合 A 和集合 B 的差集，表示为：A-B，定义如下：$AB = { x | x ∈ A ∧ x  B } $
+ ```js
  this.difference = function(otherSet){ 
    let differenceSet = new Set(); //{1} 
   
    let values = this.values(); 
    for (let i=0; i<values.length; i++){ //{2} 
      if (!otherSet.has(values[i])){   //{3} 
        differenceSet.add(values[i]); //{4} 
      } 
    } 
   
    return differenceSet; 
  }; 
  ```
+ intersection 方法会得到所有同时存在于两个集合中的值。而 difference 方法会得到所有存在于集合 A 但不存在于 B 的值。因此这两个方法在实现上唯一的区别就是行{3}。只获取不存在于 otherSet 实例中的值，而不是也存在于其中的值。行{1}、{2}和{4}是完全相同的。

### 子集

+ 集的数学概念是集合 A 是集合 B 的子集（或集合 B 包含了 A），表示为： $A⊆B $
+ 该集合定义如下： $∀x { x ∈ A → x ∈ B } $
+ ```js
  this.subset = function(otherSet){ 
   
    if (this.size() > otherSet.size()){ //{1} 
      return false; 
    } else { 
      let values = this.values(); 
      for (let i=0; i<values.length; i++){ //{2} 
        if (!otherSet.has(values[i])){ //{3} 
          return false; //{4} 
        } 
      } 
      return true; //{5} 
    } 
  }; 
  ```
+ 首先需要验证的是当前 Set 实例的大小。如果当前实例中的元素比 otherSet 实例更多，它就不是一个子集（行{1}）。子集的元素个数需要小于或等于要比较的集合。
+ 接下来要遍历集合中的所有元素（行{2}），验证这些元素也存在于 otherSet 中（行{3}）。如果有任何元素不存在于 otherSet 中，就意味着它不是一个子集，返回 false（行{4}）。如果所有元素都存在于 otherSet 中，行{4}就不会被执行，那么就返回 true（行{5}）。

## ES6——Set 类

+ ECMAScript 2015 新增了 Set 类。我们可以基于 ES6 的 Set 开发我们的 Set 类。
+ 和我们的 Set 不同，ES6 的 Set 的 values 方法返回 Iterator（第 2 章提到过），而不是值构成的数组。另一个区别是，我们实现的 size 方法返回 set 中存储的值的个数，而 ES6 的 Set 则有一个 size 属性。
+ 可以用 delete 方法删除 set 中的元素： `set.delete(1); `
+ clear 方法会重置 set 数据结构，这跟我们实现的功能一样。

### 模拟并集操作

+ ```js
  let unionAb = new Set(); //{1} 
  for (let x of setA) unionAb.add(x); //{2} 
  for (let x of setB) unionAb.add(x); //{3} 
  ```
+ 我们可以创建一个新的集合，用来添加两个集合中所有的元素（行{1}）。迭代这两个集合（行{2}、行{3}），把所有元素都添加到并集的集合中。代码如下：

### 模拟交集操作

+ ```js
  let intersection = function(setA, setB) { 
    let intersectionSet = new Set(); 
    for (let x of setA) { 
      if (setB.has(x)) { //{1} 
        intersectionSet.add(x); 
      } 
    } 
    return intersectionSet; 
  }; 
  let intersectionAB = intersection(setA, setB); 
  ```
+ 交集可以用更简单的语法实现，代码如下：

  ```js
   intersectionAb = new Set([x for (x of setA) if (setB.has(x))]); 
  ```

### 模拟差集操作

+ ```js
  let difference = function(setA, setB) { 
    let differenceSet = new Set(); 
    for (let x of setA) { 
      if (!setB.has(x)) { //{1} 
        differenceSet.add(x); 
      } 
    } 
    return differenceSet; 
  }; 
  let differenceAB = difference(setA, setB); 
  ```
+ intersection 函数和 difference 函数只有行{1}不同，因为差集中只添加 setA 有而 setB 没有的元素。
+ 差集也可以用更简单的语法实现，代码如下：

  ```js
  differenceAB = new Set([x for (x of setA) if (!setB.has(x))]); 
  ```

  > 目前只有 Firefox 支持简化的语法，但在所有支持 ES6 的现代浏览器中都可以执行 difference 函数。

# 字典

+ 在上一章中，我们学习了集合。本章我们会继续学习使用字典和散列表来**存储唯一值（不重复的值）的数据结构。**
+ 集合、字典和散列表可以存储不重复的值。在集合中，我们感兴趣的是每个值本身，并把它当作主要元素。在字典中，我们用**[键，值]**的形式来存储数据。在散列表中也是一样（也是以**[键，值]**的形式来存储数据）。但是两种数据结构的实现方式略有不同，本章中将会介绍。

## 概述

+ 你已经知道，集合表示一组互不相同的元素（不重复的元素）。在字典中，存储的是 [键，值] 对，其中键名是用来查询特定元素的。字典和集合很相似，**集合以 [值，值] 的形式存储元素**，字典则是以 [键，值] 的形式来存储元素。字典也称作**映射**。
+ 与 Set 类相似，ECMAScript 6 同样包含了一个 Map 类的实现，即我们所说的字典。

## 字典的实现

### Dictionary 类骨架

+ ```js
  function Dictionary() { 
    var items = {}; // 私有对象，外部无法操作，只能使用我们暴露的方法
  } 
  ```

### 基本操作

+ set(key,value)：向字典中添加新元素。
+ delete(key)：通过使用键值来从字典中移除键值对应的数据值。
+ has(key)：如果某个键值存在于这个字典中，则返回 true，反之则返回 false。
+ get(key)：通过键值查找特定的数值并返回。
+ clear()：将这个字典中的所有元素全部删除。
+ size()：返回字典所包含元素的数量。与数组的 length 属性类似。
+ keys()：将字典所包含的所有键名以数组形式返回。
+ values()：将字典所包含的所有数值以数组形式返回。

### Has 和 Set 方法

+ 我们首先来实现 has(key) 方法。之所以要先实现这个方法，是因为它会被 set 和 remove 等其他方法调用。我们可以通过如下代码来实现：

  ```js
  this.has = function(key) { 
    return key in items; 
  }; 
  
  this.has = function(key){
    return items.hasOwnProperty(key)
  }
  ```

  ```js
  this.set = function(key, value) { 
    items[key] = value; //{1} 
  }; 
  ```

### Delete 方法

+ ```js
  this.delete= function(key) { 
    if (this.has(key)) { 
      delete items[key]; 
      return true; 
    } 
    return false; 
  }; 
  ```

### Get 和 Values 方法

+ ```js
  this.get = function(key) { 
    return this.has(key) ? items[key] : undefined; 
  }; 
  ```
+ ```js
  this.values = function() { 
    var values = []; 
    for (var k in items) { //{1} 
      if (this.has(k)) { 
        values.push(items[k]); //{2} 
      } 
    } 
    return values; 
  }; 
  ```

### clear、size、keys 和 getItems 方法

+ ```js
  this.keys = function() { 
    return Object.keys(items); 
  }; 
  ```
+ ```js
  this.getItems = function() { 
    return items; 
  } 
  ```

## ES6——Map 类

- 和我们的 Dictionary 类不同，ES6 的 Map 类的 values 方法和 keys 方法都返回 Iterator（第 2 章提到过），而不是值或键构成的数组。另一个区别是，我们实现的 size 方法返回字典中存储的值的个数，而 ES6 的 Map 类则有一个 size 属性。

## ES6——Weak Map 类和 WeakSet 类

除了 Set 和 Map 这两种新的数据结构，ES6 还增加了它们的弱化版本，WeakSet 和 WeakMap。

基本上，Map 和 Set 与其弱化版本之间仅有的区别是：

  - WeakSet 或 WeakMap 类没有 entries、keys 和 values 等方法；
  - 只能用对象作为键。

创建和使用这两个类主要是为了性能。WeakSet 和 WeakMap 是弱化的（用对象作为键），没有强引用的键。这使得 JavaScript 的垃圾回收器可以从中清除整个入口。

另一个优点是，必须用键才可以取出值。这些类没有 entries、keys 和 values 等迭代器方法，因此，除非你知道键，否则没有办法取出值。这印证了我们在第 3 章的做法，即使用 WeakMap 类封装 ES6 类的私有属性。
