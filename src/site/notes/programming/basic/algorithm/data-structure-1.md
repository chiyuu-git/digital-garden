---
{"dg-publish":true,"permalink":"/programming/basic/algorithm/data-structure-1/"}
---


https://github.com/EmilyYoung71415/StructuresandAlgorithms-Code

5/3

7/19

9/9

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

# 栈

## 概念

又名“堆栈”，和堆一点关系都没有，是两种完全不同的数据结构

> 我们都熟悉很有名的撤销（Undo）选项，它几乎存在每个应用程序中。有没有想过它是如何工作的？其思路就是，按照最后的状态排列在先的顺序将工作的先前状态（限于特定数字）存储在内存中。这只用数组是无法实现的，因此堆栈就有了用武之地。

可以把堆栈看作一堆垂直排列的书籍。为了获得位于中间位置的书，你需要拿掉放在它上面的所有书籍。这就是 LIFO（后进先出）方法的工作原理。

栈顶允许操作，栈底不允许操作

这是一个包含三个数据元素（1,2 和 3）的堆栈图像，其中 3 位于顶部，首先把它删除：

![1552868500937](/img/user/programming/basic/algorithm/data-structure-1/1552868500937.png)

## 栈的实现

从数据存储的角度看，实现栈有两种方式，一种是以数组做基础，一种是以链表做基础，数组是最简单的实现方式，本文以基础的数组来实现栈。

栈的基本操作包括创建栈、销毁栈、出栈、入栈、获取栈顶元素、获取栈的大小、清空栈。

我们定义以下几个栈的方法：

+ push 添加一个元素到栈顶
+ pop 弹出栈顶元素
+ top/peek 返回栈顶元素
+ isEmpty 判断栈是否为空
+ size 返回栈里元素的个数
+ clear 清空栈

然后我们利用 es6 的 class 的实现以上的方法

```js
class Stack {
  constructor() {
    this.items = []; // 使用数组存储数据
  }
  push(item) {
    this.items.push(item); // 往栈里压入一个元素
  }
  pop() {
    // 如果不为空
    if(!this.isEmpty()) return this.items.pop(); // 把栈顶的元素移除
  }
  top() {
    return this.items[this.items.length - 1]; // 返回栈顶的元素
  }
  isEmpty() {
    return this.items.length === 0; //返回栈是否为空
  }
  size() {
    return this.items.length; // 返回栈的大小
  }
  clear() {
    this.items = []; // 清空栈
  }
  toString(){
    return this.items.reduce(function(str,val){
      return str + val
    },'')
  }
}
```

上面的方法是否仅仅是对数组做了一层封装?

给你一个数组，你可以通过索引操作任意一个元素，但是给你一个栈，你能操作任意元素么？栈提供的方法只允许你操作栈顶的元素，也就是**数组的最后一个元素**，这种限制其实提供给我们一种思考问题的方式，这个方式也就是栈的特性，后进先出。

既然**栈的底层实现其实就是数组**，栈能做的事情，数组一样可以做啊，为什么弄出一个栈来，是不是多此一举？封装是为了更好的利用，站在栈的肩膀上思考问题显然要比站在数组的肩膀上思考问题更方便，后面的练习题你将有所体会。

## 栈的应用

### 判断括号是否匹配

```js
()ss()ss(sss(ss)(ss)ss) 合法
()ss()ss(sss(ss)(ss)ss)) 不合法
```

括号有嵌套关系，也有并列关系，如果我们用数组或者对象的方法也能解决，今天我们试着用栈来解决这个问题。

1. 遍历字符串
2. 如果是左括号，就压入栈中
3. 如果是右括号，判断栈是否为空

   + 如果不为空，则把栈顶元素移除 (也就是在栈中存放的左括号)，这对括号就抵消了；
   + 如果为空，就说明缺少左括号，返回 false

4. 循环结束后，看栈的大小是否为 0，如果不为 0，就说明没有成对出现，为 0，就说明全部抵消了。

```js
{
  function isDouuble(str) {
    const stack = new Stack();
    const len = str.length;
    for (let i = 0; i < len; i++) {
      const item = str[i];
      if (str[i] === "(") {
        stack.push(item); // 入栈
      } else if (item === ")") {
        if (stack.isEmpty()) {
          return false;
        } else {
          stack.pop(); // 出栈
        }
      }
    }
    return stack.size() === 0;
  }
  console.log(isDouuble("()ss()ss(sss(ss)(ss)ss)")); // true
  console.log(isDouuble("()ss()ss(sss(ss)(ss)ss)(")); // false
  console.log(isDouuble("()ss()ss(sss(ss)(ss)ss))")); // false
  console.log(isDouuble("()ss()ss(sss(ss)(ss)ss))(")); // false
}
```

### [20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

> 给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串，判断字符串是否有效。
>
> 有效字符串需满足：
>
> 1. 左括号必须用相同类型的右括号闭合。
> 2. 左括号必须以正确的顺序闭合。
>
> 注意空字符串可被认为是有效字符串。

```js
var isValid = function (s) {
  function Stack() {
    var items = []
    this.push = function (item){
      console.log(item)
      items.push(item)
    }
    this.pop = function (){
      return items.pop()
    }
    this.isEmpty = function(){
      return items.length === 0 
    }
  }

  let stack = new Stack()
  // 观察结构可以发现，即是括号的类型不同，只有匹配的情况下才可以成功pop
  let map = {
    '[':']',
    '(':')',
    '{':'}',
  }
  for (let i = 0; i < s.length; i++) {
    // 左边的执行push操作，判断是否相等
    if(/\(|\[|\{/.test(s[i])){
      stack.push(s[i])
    }else{
    // 右边的执行pop操作
      let pop = stack.pop()
      console.log(map[pop],s[i])
      if(map[pop] !== s[i]){
        return false
      }
    }
  }

  // 循环执行完毕，栈为空，return true
	return stack.isEmpty()
}
```

### [844. 比较含退格的字符串](https://leetcode-cn.com/problems/backspace-string-compare/)

### [682. 棒球比赛](https://leetcode-cn.com/problems/baseball-game/)

## 实现一个 Min 方法的栈

### 双栈

实现一个栈，除了常见的 push，pop 方法以外，提供一个 min 方法，返回栈里最小的元素，且时间复杂度为 $O(1)$

可以利用两个栈来实现，一个栈用来存储数据，一个栈用来存储栈里最小的数据；利用编程中分而治之的思想，就是分开想分开处理

1. 定义两个栈，dataStack 和 minStack;
2. 对于 dataStack 栈来说，正常的 psuh,pop 实现就好；
3. 对于 minStatck 栈来说，它是要存储栈里最小的值，所以当 minStack 为空的时候，那么 push 进来的数据就是最小的；如果不为空，此时 minStack 栈顶的元素就是最小的，如果 push 进来的元素比栈顶的元素还小，直接 push 进来就行，这样 minStack 栈的栈顶始终都是栈里的最小值。

```js
{
  class MinStack {
    constructor() {
      this.dataStack = new Stack(); // 普通的栈
      this.minStack = new Stack(); // 存储最小值的栈
    }
    // push 和 pop 两个栈都要操作，保持大小统一
    push(item) {
      this.dataStack.push(item); // 常规操作
      if (this.minStack.isEmpty() || item < this.minStack.top()) {
        this.minStack.push(item); // 保证minStack栈顶是最小的值
      } else {
        this.minStack.push(this.minStack.top()); 
        // 重复push最小值，保持两个栈的大小一样且顶部是最小的
      }
    }
    pop() {
      this.minStack.pop(); // 保持大小统一
      return this.dataStack.pop(); // 返回真实的数字
    }
    min() {
      return this.minStack.top(); // 返回最小的数字
    }
  }

  const minstack = new MinStack();
  minstack.push(3);
  minstack.push(2);
  minstack.push(6);
  minstack.push(8);
  console.log(minstack.min()); // 2
  console.log(minstack.pop()); // 8
  minstack.push(1);
  console.log(minstack.min()); // 1
}
```

两个栈同步：为了保证可以 pop 掉最小的元素，当 dataStack pop 掉了最小的元素，如果 minStack 没有同步大小，可能最小的元素还在栈内

#### 单栈

每次 push 两个值，先 push 新值，再 push 最小值

```js
var MinStack = function() {
  this.stack = []
};
MinStack.prototype.push = function(x) {
  if(this.stack.length===0){
      this.stack.push(x)
      this.stack.push(x)
  }else{
    const min = this.getMin()
    this.stack.push(x)
    if(min<x) this.stack.push(min)
    else this.stack.push(x)
  }
};
MinStack.prototype.pop = function() {
  this.stack.pop()
  return this.stack.pop()
};
MinStack.prototype.top = function() {
    
  return this.stack[this.stack.length-2]
};
MinStack.prototype.getMin = function() {
  return this.stack[this.stack.length-1]
};
```

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

# 链表

- 链表是另一个重要的线性数据结构，刚一看可能看起来像数组，但在内存分配，内部结构以及如何执行插入和删除的基本操作方面有所不同。

## 链表与数组的区别

+ 数组是需要一块**连续的内存空间**来存储，对内存的要求比较高。 而链表却相反，它并不需要一块连续的内存空间。链表是通过指针将一组零散的内存块串联在一起。
+ 数组可以**快速的查找**某个元素，但是在**插入和删除**时就要移动大量元素。原因就在于相邻元素的存储位置也具有邻居关系。他们的编号是 0，1，2，3，4，...，n，它们在内存中的位置也是紧挨着的，中间没有空隙，所以就无法快速添加元素。而当删除后，当中就会留出空隙，自然需要弥补。
+ 所以我们需要这样一种数据结构： 我们反正也是要让相邻元素间留有足够余地，那干脆所有的元素都不要考虑相邻位置了，哪有空位就到哪里，只是让每个元素知道它下一个元素的位置在哪里。我们可以在第一个元素时，就知道第二个元素的位置在哪；在第二个元素时，再找到第三个元素的位置。这样，所有的元素都可以遍历而找到。
+ 因此，为了表示每个数据元素 n 和后继元素 n+1 之间的逻辑关系，对数据元素 n 来说，除了存储本身的信息之外，还需要存储一个指示其后继的信息。我们把存储元素的域称之为 **数据域**，把存储直接后继位置的域称之为 **指针域**。指针域中存储的信息称做 **指针域链**。这两部分信息组成数据元素 n 的存储映像，称为 **结点**。
+ 而由 n 个结点链结成一个链表，称之为 **链式存储结构**。

- 链表用于实现文件系统，哈希表和邻接表。
- 下图是链表内部结构的直观展示：
- ![1552868748866](/img/user/programming/basic/algorithm/data-structure-1/1552868748866.png)

### 链表和数组的性能对比

- ![1553141140317](/img/user/programming/basic/algorithm/data-structure-1/1553141140317.png)
- 数组和链表的对比，并不能局限于时间复杂度。而且，在实际开发中，不能仅仅利用复杂度分析就决定使用哪个数据结构来存储数据。针对不同的类型项目来权衡。当然，在大前端，还是数组用的最多。

## 链表的实现

### 基本操作

- InsertAtHead —— 在链表**头部**插入指定元素
- append —— 在链表**末尾**插入指定元素
- insert —— 在指定的位置插入指定的元素
- remove—— 从链表中删除指定元素
- removeAt —— 删除链表的指定元素
- getHead —— 返回链表的头部元素
- toString —— 把链表变成字符串
- indexOf—— 返回链表中的指定元素
- isEmpty —— 如果链表为空，返回 true

### 链表的实现

- 我们一般设计的链表有两个类。`Node` 类用来表示节点，`LinkedList` 类提供了一些辅助方法，比如说结点的增删改查，以及显示列表元素等方法。 接下来看看如何用 `js` 代码表示一个链表。

  ```js
  {
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
  }
  ```

- `Node` 类包含两个属性：`data` 用来保存节点上的数据，`next` 用来保存指向下一个节点的链接。

**理解指针或引用的含义**

- 指针或者引用，他们的意思都是一样的，都是存储所指对象的内存地址。
- 将某个变量赋值给指针，实际上就是将某个变量的内存地址赋值给指针，或者反过来说，指针中存储了这个变量的内存地址，指向了这个变量，通过指针就能找到这个变量。
- 结点中的 next 指针，储存了下一个结点的内存地址

**警惕指针丢失和内存泄露**

- 在写链表代码的时候，尤其是我们的指针，会不断的改变，指来指去的。所以在写的时候，一定注意不要弄丢了指针。
- ```js
  new_node -> next = p -> next;
  p -> next = new_node;
  new_node.next = p.next
  p.next = new_node
  ```
- 新结点指向 p 指针原本指向的结点，然后 p 指针再指向新结点

**重点留意边界条件处理**

- 当我们向一个空链表中插入第一个结点时，就需要特殊处理了。当链表为空时，也就是链表的 head 为空，那直接赋值即可，如下：

  ```js
  if(head == null) {
      head = new_node;
  }
  ```

+ 写链表代码时，要经常注意边界条件是否考虑到了：

  - 如果链表为空时，代码是否能正常工作？
  - 如果链表只有一个结点时，代码是否能正常工作？
  - 如果在处理头结点和尾结点时，代码是否能正常工作？

#### LinkList 类骨架

+ ```js
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

#### append()

- 看一段完整的添加节点代码：

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

- 如果头结点不存在的话，头结点等于尾结点。如果头结点存在的话，找到尾结点来扩充链表的数据

  > 列表最后一个节点的下一个元素始终是 null。 我们知道它会是列表的最后一项。

#### removeAt()

- 再来看单链表结点的删除操作。如果在 p 结点后删除一个结点，只需要关注一步即可：

  ```js
  p -> next = p -> next -> next;
  p.next = p.next.next
  // 这种图把箭头替换成点就好了
  ```

- 但是，当链表中只剩一个结点 head 时，也需要特殊处理才可以，如下：

  ```js
  if(head -> next == null){
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

- 移除最后一个元素：

  ![1554548880185](/img/user/programming/basic/algorithm/data-structure-1/1554548880185.png)

- 移除中间的元素：

  ![1554548919162](/img/user/programming/basic/algorithm/data-structure-1/1554548919162.png)

#### insert()

+ 使用这个方法可以在任意位置插入一个元素。

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

+ 在列表的起点添加一个元素：

  ![1554549239075](/img/user/programming/basic/algorithm/data-structure-1/1554549239075.png)

+ 在列表尾部添加一个元素：

  ![1554549327438](/img/user/programming/basic/algorithm/data-structure-1/1554549327438.png)

+ 在列表中间添加一个元素：

  ![1554549369128](/img/user/programming/basic/algorithm/data-structure-1/1554549369128.png)

#### toString()

+ toString 方法会把 LinkedList 对象转换成一个字符串

  ```js
  this.toString = function(){ 
   
    let current = head, //{1} 
    string = '';    //{2} 
   
    while (current) {   //{3} 
      string +=current.data +(current.next ? '->' : '');//{4} 
      current = current.next;          //{5} 
    } 
    return string;              //{6} 
  }; 
  
  ```

#### indexOf()

+ indexOf 方法接收一个元素的值，如果在列表中找到它，就返回元素的位置，否则返回 -1。

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

#### remove()

+ 在实现了 indexOf() 的基础上，实现根据数据值删除节点

  ```js
  this.remove = function(val){ 
    let index = this.indexOf(val) 
    // 即使返回-1，也会被remove的边界限制
    return this.removeAt(index) 
  } 
  ```

#### 其他方法

+ ```js
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
+ head 变量是 LinkedList 类的私有变量（这意味着它不能在 LinkedList 实例外部被访问和更改，只有通过 LinkedList 实例才可以）。但是，如果我们需要在类的实现外部循环访问列表，就需要提供一种获取类的第一个元素的方法。

### 单链表（单向）

- 最简单最常用的是 **单链表**，此链表的每个结点只包含一个指针域。
- 我们实现的就是单向链表，其中有两个结点是比较特殊的。他们分别是第一个结点和最后一个节点。我们习惯性地把第一个结点叫做**头结点**，把最后一个结点叫做**尾结点**。头结点是用来记录链表的基地址。有了它，我们就可以遍历得到整条链表。而尾结点特殊地方它的指针不是指向下一个地方，而是指向一个空地址 NULL，表示这是链表上最后一个结点。
- 我们可以判断当前结点的 next 是否为空，就知道循环是否结束。

### 循环链表

+ 将单链表中尾结点的指针由空指针指向头节点，就使整个单链表形成一个环，这种头尾相接的单链表就简称为循环链表。
+ ![1553140938179](/img/user/programming/basic/algorithm/data-structure-1/1553140938179.png)
+ 其实循环链表和单链表的主要差异就在于循环的判断条件上，原来是判断当前结点的 next 是否为空，现在则是判断当前结点的 next **是否等于头结点**。
+ 那头结点又是怎么确定呢？又私有变量唯一指定，每次遍历都是从他开始

### 双向链表

+ 双向链表是在单链表的每个结点中，再设置一个指向其前驱结点的指针域。所以在双向链表中的结点都有**两个指针域**，一个指向直接后继，另一个指向直接前驱。
+ ![1553141060427](/img/user/programming/basic/algorithm/data-structure-1/1553141060427.png)
+ 从上图中可以看出来，双向链表需要额外的两个空间来存储后继结点和前驱结点的地址。所以，如果存储同样多的数据，双向链表要比单链表占用更多的内存空间。
+ 虽然两个指针比较浪费存储空间，但是可以支持双向遍历，这样也带来了双向链表操作的灵活性。

#### DoublyLinkedList 类

+ ```js
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
+ 双向链表提供了两种迭代列表的方法：从头到尾，或者反过来。
+ 我们也可以访问一个特定节点的**下一个或前一个元素**。在单向链表中，如果迭代列表时错过了要找的元素，就需要回到列表起点，重新开始迭代。这是双向链表的一个优点。

#### insert()

+ 向双向链表中插入一个新项跟（单向）链表非常类似。区别在于，单向链表只要控制一个 next 指针，而双向链表则要同时控制 next 和 prev（previous，前一个）这两个指针。
+ ```js
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
+ 在双向链表头部插入一个节点：

  ![1554553356596](/img/user/programming/basic/algorithm/data-structure-1/1554553356596.png)

+ 在双向链表最后添加一个节点：

  ![1554553405130](/img/user/programming/basic/algorithm/data-structure-1/1554553405130.png)

+ 在列表中间插入一个新节点：

  ![1554553449998](/img/user/programming/basic/algorithm/data-structure-1/1554553449998.png)

+ 我们可以对 insert 和 remove 这两个方法的实现做一些改进。在结果为否的情况下，我们可以把元素插入到列表的尾部。性能也可以有所改进，比如，如果 position 大于 length/2，就最好从尾部开始迭代，而不是从头开始（这样就能迭代更少列表中的元素）

#### remove()

+ 从双向链表中移除元素跟单向链表非常类似。唯一的区别就是还需要设置前一个位置的指针。

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

+ 移除第一个元素：

  ![1554553605588](/img/user/programming/basic/algorithm/data-structure-1/1554553605588.png)

+ 从最后一个位置移除元素：

  ![1554553626722](/img/user/programming/basic/algorithm/data-structure-1/1554553626722.png)

+ 从列表中间移除一个元素：

  ![1554553653606](/img/user/programming/basic/algorithm/data-structure-1/1554553653606.png)

### 双向循环链表

- 既然单链表可以有循环链表，那么双向链表当然也可以是循环链表。你可以停下来想想双向循环链表长什么样子。
- ![1553141097990](/img/user/programming/basic/algorithm/data-structure-1/1553141097990.png)

## 常问的链表面试问题：

### 翻转列表

#### 迭代

+ 三指针法

  ```js
  //三指针法
  let reverseList = head => {
    let curt = head;
    let prev = null;
    let next = null;
  
    while (curt) {
      // 保存当前结点的下个节点，next→node(2)
      next = curt.next;
      // 将当前结点的下一个节点指向“上一个节点”，这一步实现了反转
      // 将当前节点，指向了上一轮循环中的prev
      curt.next = prev;
      // 将当前结点设置为“上一个节点”
      prev = curt;
      // 将保存的下一个节点设置为头结点
      curt = next;
    }
    return prev; // 此时为头节点
  };
  ```

+ 断链之前要保存，第一步，第二部

#### 递归

+ 递归版本稍微复杂一些，其关键在于反向工作。假设列表的其余部分已经被反转，现在我该如何反转它前面的部分？

  ![1565253793542](/img/user/programming/basic/algorithm/data-structure-1/1565253793542.png)

+ 要小心的是 n 1 的下一个必须指向 Ø 。如果你忽略了这一点，你的链表中可能会产生循环。如果使用大小为 2 的链表测试代码，则可能会捕获此错误。

  ```js
  var reverseList = function(head) {
  	if(head==null || head.next==null) return head
    let prev = reverseList(head.next)
    head.next.next = head
    head.next = null
    return prev // 递归传递反转后的头结点
  }
  ```

- 检测链表中的循环
- 返回链表中倒数第 n 个节点
- 移除链表中的重复值

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

- 除了 Set 和 Map 这两种新的数据结构，ES6 还增加了它们的弱化版本，WeakSet 和 WeakMap。
- 基本上，Map 和 Set 与其弱化版本之间仅有的区别是：
  - WeakSet 或 WeakMap 类没有 entries、keys 和 values 等方法；
  - 只能用对象作为键。
- 创建和使用这两个类主要是为了性能。WeakSet 和 WeakMap 是弱化的（用对象作为键），没有强引用的键。这使得 JavaScript 的垃圾回收器可以从中清除整个入口。
- 另一个优点是，必须用键才可以取出值。这些类没有 entries、keys 和 values 等迭代器方法，因此，除非你知道键，否则没有办法取出值。这印证了我们在第 3 章的做法，即使用 WeakMap 类封装 ES6 类的私有属性。

# 散列表

## 概述

- HashTable 类
- 散列算法的作用是**尽可能快地**在数据结构中找到一个值。在之前的章节中，你已经知道如果要在数据结构中获得一个值（使用 get 方法），**需要遍历整个数据结构**来找到它。
- 如果使用散列函数，就知道值的具体位置，因此能够快速检索到该值。**（从这里可以看出以数组为基础）**
- **散列函数的作用是给定一个键值，然后返回值在表中的地址。**
- 举个例子，我们继续使用在前一节中使用的电子邮件地址簿。我们将要使用最常见的散列函数——“lose lose”散列函数，方法是简单地将每个键值中的每个字母的 ASCII 值相加。

  ![1554596681769](/img/user/programming/basic/algorithm/data-structure-1/1554596681769.png)

## 散列表的实现

### HashTable 类骨架

- ```js
  function HashTable() { 
    var table = []; 
  } 
  ```

### 基本操作

- put(key,value)：向散列表增加一个新的项（也能更新散列表）。
- remove(key)：根据键值从散列表中移除值。
- get(key)：返回根据键值检索到的特定的值。

### 散列函数

- 在实现这三个方法之前，要实现的第一个方法是散列函数，它是 HashTable 类中的一个**私有方法**：

  ```js
  var loseloseHashCode = function (key) { 
    var hash = 0;                          //{1} 
    for (var i = 0; i < key.length; i++) { //{2} 
      hash += key.charCodeAt(i);         //{3} 
    } 
    return hash % 37;                      //{4} 
  }; 
  ```

- 给定一个 key 参数，我们就能根据组成 key 的每个字符的 ASCII 码值的和得到一个数字。
- 所以，首先需要一个变量来存储这个总和（行{1}）。
- 然后，遍历 key（行{2}）并将从 ASCII 表中查到的每个字符对应的 ASCII 值加到 hash 变量中（可以使用 JavaScript 的 String 类中的 charCodeAt 方法——行{3}）。
- 最后，返回 hash 值。为了得到比较小的数值，我们会使用 hash 值和一个**任意数**做除法的余数（mod）。

  > 散列值很可能重复，所以说是 loselose

### Put 方法

- ```js
  this.put = function(key, value) { 
    var position = loseloseHashCode(key); //{5} 
    console.log(position + ' - ' + key); //{6} 
    table[position] = value; //{7} 
  }; 
  ```

### Get 方法

- ```js
  this.get = function (key) { 
    return table[loseloseHashCode(key)]; 
  }; 
  ```

### Remove 方法

- ```js
  this.remove = function(key) { 
    table[loseloseHashCode(key)] = undefined; 
  }; 
  ```
- 对于 HashTable 类来说，我们不需要像 ArrayList 类一样从 table 数组中将位置也移除。由于元素分布于整个数组范围内，一些位置会没有任何元素占据，并默认为 undefined 值。
- 我们也不能将位置本身从数组中移除（这会改变其他元素的位置），否则，当下次需要获得或移除一个元素的时候，这个元素会不在我们用散列函数求出的位置上。

## 处理散列表中的冲突

- 有时候，一些键会有相同的散列值。不同的值在散列表中对应相同位置的时候，我们称其为冲突。例如，我们看看下面的代码会得到怎样的输出结果：

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

- 这样后添加的元素就会覆盖先添加的元素，为了避免数据丢失，我们通常会使用以下几种方法：**分离链接、线**
  **性探查和双散列法**。

### 分离链接

- 分离链接法包括为散列表的每一个位置创建一个链表并将元素存储在里面。它是解决冲突的最简单的方法，但是它在 HashTable 实例之外还需要额外的存储空间。
- 例如，我们在之前的测试代码中使用分离链接的话，输出结果将会是这样：

  ![1554597553412](/img/user/programming/basic/algorithm/data-structure-1/1554597553412.png)

- 在位置 5 上，将会有包含三个元素的 LinkedList 实例；在位置 13、16 和 32 上，将会有包含两个元素的 LinkedList 实例；在位置 10、19 和 29 上，将会有包含单个元素的 LinkedList 实例。
- 对于分离链接和线性探查来说，只需要重写三个方法**：put、get 和 remove**。这三个方法在每种技术实现中都是不同的。

#### ValuePair 类

- 为了实现一个使用了分离链接的 HashTable 实例，我们需要一个新的辅助类来表示将要加入 LinkedList 实例的元素。我们管它叫 ValuePair 类（在 HashTable 类内部定义）：
- ```js
  var ValuePair = function(key, value){ 
    this.key = key; 
    this.value = value; 
  
    this.toString = function() { 
      return '[' + this.key + ' - ' + this.value + ']'; 
    } 
  }; 
  ```
- 这个类只会将 key 和 value 存储在一个 Object 实例中。我们也重写了 toString 方法，以便之后在浏览器控制台中输出结果。

#### Put 方法

- ```js
  this.put = function(key, value){ 
    var position = loseloseHashCode(key); 
   
    if (table[position] == undefined) { //{1} 
      table[position] = new LinkedList(); 
    } 
    table[position].append(new ValuePair(key, value)); //{2} 
  }; 
  ```
- 在这个方法中，将验证要加入新元素的位置是否已经被占据（行{1}）。如果这个位置是第一次被加入元素，我们会在这个位置上初始化一个 LinkedList 类的实例。然后，使用 LinkedList 类的 append 方法向 LinkedList 实例中添加一个 ValuePair 实例（键和值）（行{2}）。

#### Get 方法

- ```js
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
- 我们要做的第一个验证，是确定在特定的位置上是否有元素存在（行{3}）。
  - 如果没有，则返回一个 undefined 表示在 HashTable 实例中没有找到这个值（行{10}）。
  - 如果在这个位置上有值存在，我们知道这是一个 LinkedList 实例。现在要做的是遍历这个链表来寻找我们需要的元素。
    - 在遍历之前先要获取链表表头的引用（行{4}）
    - 然后就可以从链表的头部遍历到尾部（行{5}，current.next 将会是 null）。
- Node 链表包含 next 指针和 data 属性。而 data 属性又是 ValuePair 的实例，所以它又有 value 和 key 属性。可以通过 current.data.key 来获得 Node 链表的 key 属性，并通过比较它来确定它是否就是我们要找的键（行{6}）。（这就是要使用 ValuePair 这个辅助类来存储元素的原因。我们不能简单地存储值本身，这样就不能确定哪个值对应着特定的键。）
  - 如果 key 值相同，就返回 Node 的值（行{7}）；
  - 如果不相同，就继续遍历链表，访问下一个节点（行{8}）。
- 如果要找的元素是链表的唯一节点，那么就不会进入 while 循环的内部。因此，需要在行{9}处理这种特殊的情况。
- > 在一开始检查就好，如果是这样，就没有必要遍历了

#### Remove 方法

- 使用分离链接法从 HashTable 实例中移除一个元素和之前在本章实现的 remove 方法有一些不同。现在使用的是链表，我们需要从链表中移除一个元素。来看看 remove 方法的实现：

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

- 在 remove 方法中，我们使用和 get 方法一样的步骤找到要找的元素。遍历 LinkedList 实例时，如果链表中的 current 元素就是要找的元素（行{11}），使用 remove 方法将其从链表中移除。
- 然后进行一步额外的验证：如果链表为空了（行{13}——链表中不再有任何元素了），就将散列表这个位置的值设为 undefined（行{14}），这样搜索一个元素或打印它的内容的时候，就可以跳过这个位置了。
- 最后，返回 true 表示这个元素已经被移除（行{15}）或者在最后返回 false 表示这个元素在散列表中不存在（行{17}）。
- 同样，需要和 get 方法一样，处理元素在第一个或最后一个的情况（行{16}）。

### 线性探查

- 另一种解决冲突的方法是线性探查。当想向表中某个位置加入一个新元素的时候，如果索引为 index 的位置已经被占据了，就尝试 index+1 的位置。如果 index+1 的位置也被占据了，就尝试 index+2 的位置，以此类推。

#### Put 方法

- ```js
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
- 和之前一样，先获得由散列函数生成的位置（行{1}），然后验证这个位置是否有元素存在（如果这个位置被占据了，将会通过行{2}的验证）。如果没有元素存在，就在这个位置加入新元素（行{3}——一个 ValuePair 的实例）。
- 如果这个位置已经被占据了，需要找到下一个没有被占据的位置（position 的值是 undefined），因此我们声明一个 index 变量并赋值为 position+1（行{4}——在变量名前使用自增运算符 ++ 会先递增变量值然后再将其赋值给 index）。然后验证这个位置是否被占据（行{5}），如果被占据了，继续将 index 递增（行{6}），直到找到一个没有被占据的位置。然后要做的，就是将值分配到这个位置（行{7}）。

#### Get 方法

- ```js
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
- 要获得一个键对应的值，先要确定这个键存在（行{8}）。如果这个键不存在，说明要查找的值不在散列表中，因此可以返回 undefined（行{14}）。如果这个键存在，需要检查我们要找的值是否就是这个位置上的值（行{9}）。如果是，就返回这个值（行{10}）。
- 如果不是，就在散列表中的下一个位置继续查找，直到找到一个键值与我们要找的键值相同的元素（行{11}）。然后，验证一下当前项就是我们要找的项（行{12}——只是为了确认一下）并且将它的值返回（行{13}）。
- 我们无法确定要找的元素实际上在哪个位置，这就是使用 ValuePair 来表示 HashTable 元素的原因。

#### Remove 方法

- remove 方法和 get 方法基本相同，不同之处在于行{10}和{13}，它们将会由下面的代码代替：
  `table[index] = undefined; `
- 要移除一个元素，只需要给其赋值为 undefined，来表示这个位置不再被占据并且可以在必要时接受一个新元素。

### 更好的散列函数

- ```js
  var djb2HashCode = function (key) { 
    var hash = 5381; //{1} 
    for (var i = 0; i < key.length; i++) { //{2} 
      hash = hash * 33 + key.charCodeAt(i); //{3} 
    } 
    return hash % 1013; //{4} 
  }; 
  ```
- 它包括初始化一个 hash 变量并赋值为一个质数（行{1}——大多数实现都使用 5381），然后迭代参数 key（行{2}），将 hash 与 33 相乘（用来当作一个魔力数），并和当前迭代到的字符的 ASCII 码值相加（行{3}）。
- 最后，我们将使用相加的和与另一个随机质数（比我们认为的散列表的大小要大——在本例中，我们认为散列表的大小为 1000）相除的余数。

## 散列表和散列集合

- 散列表和散列映射是一样的，我们已经在本章中介绍了这种数据结构。
- 在一些编程语言中，还有一种叫作散列集合的实现。散列集合由一个集合构成，但是插入、移除或获取元素时，使用的是散列函数。
- 我们可以重用本章中实现的所有代码来实现散列集合，不同之处在于，不再添加键值对，而是只插入值而没有键。例如，可以使用散列集合来存储所有的英语单词（不包括它们的定义）。和集合相似，散列集合只存储唯一的不重复的值。

## 常问的哈希面试问题：

- 找到数组中的对称对
- 追踪遍历的完整路径
- 查看一个数组是否为另一个数组的子集
- 检查给定数组是否不相交

## 散列表与字典

+ dictionary 跟 map 其实是同一个东西，只是在不同场合叫法不同。
+ dictionary 的中文是字典，map 在中文是映射，也有地图的意思。查字典，查地图，都是通过某个信息，去找到另一个信息。比如通过单词的拼写找到单词的具体含义。
+ 类比查字典过程，单词的拼写为 key, 单词的具体含义为 value。dictionary 就是通过 key，找到 value，有时也将 dictionary 说成是 key-value 结构。只要达到查找目的，都可以叫做 dictionary。具体怎么找，可以有不同实现。
+ 比如，最简单是将 key，value 放在一起，线性排。

  ```text
  k1, k2, k3, k4, k4 ....
  v1, v2, v3, v4, v5 ....
  ```

+ 当需要从 key 找到对应的 value 时，就从头到尾遍历过去。依次判断 k1, k2, k3, k4 是不是等于 key, 当等于的时候，就找到 key 的具体位置，从而也就找到了 value。
+ 但这样从头到尾遍历，速度就太慢了，时间复杂度为 O(N)。N 为数据的大小。
+ 为了快速从 key 找到 value。dictionary(或者说 map) 的通常有两种实现方式。

  1. 二叉树
  2. 哈希 (hash) 表

+ 二叉树查找的时间复杂度为 O(logN)，哈希表的时间复杂度大致为 O(1)。二叉树也分红黑树，AVL 树等。哈希表的速度很快，很多语言内置的 dictionary 都使用哈希表来实现，但它通常会浪费一些存储空间。这部分有兴趣去看数据结构的书。
+ hash_map 其实就是使用 hash 表实现的 map。注意，二叉树，哈希表仅仅是 dictionary 的实现方式，不能说 hash 就等于 dictionary，实现方式可以有多种多样。

## 散列表底层

+ object set map 慢 array 底层都是散列表
+ <https://juejin.im/entry/58eb2b94ac502e006c452632>
+ <https://www.rrfed.com/2017/04/04/chrome-object/>
