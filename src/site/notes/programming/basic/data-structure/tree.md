---
{"dg-publish":true,"permalink":"/programming/basic/data-structure/tree/"}
---


到目前为止，本书介绍了一些顺序数据结构，介绍的第一个非顺序数据结构是散列表。在本章，我们将要学习另一种**非顺序数据结构——树**，它对于存储需要**快速查找**的数据非常有用。

# 概述

树是一种分层数据的抽象模型。现实生活中最常见的树的例子是家谱，或是公司的组织架构图，如下图所示：

![1554600282423](/img/user/programming/basic/data-structure/tree/1554600282423.png)

一个树结构包含一系列存在父子关系的节点。每个节点都有一个父节点（除了顶部的第一个节点）以及零个或多个子节点：

![1554600352555](/img/user/programming/basic/data-structure/tree/1554600352555.png)

## 术语

位于树顶部的节点叫作**根节点（11）**。它没有父节点。

树中的每个元素都叫作节点，节点分为内部节点和外部节点。

- 至少有一个**子节点**的节点称为**内部节点**（7、5、9、15、13 和 20 是内部节点）。
- 没有子元素的节点称为外部节点或**叶节点**（3、6、8、10、12、14、18 和 25 是叶节点）。

有关树的另一个术语是**子树**。子树由节点和它的后代构成。例如，节点 13、12 和 14 构成了上图中树的一棵子树。

节点的一个属性是**深度**，节点的深度取决于**自身**的祖先节点的数量。比如，节点 3 有 3 个祖先节点（5、7 和 11），它的深度为 3。

树的**高度**取决于**所有节点深度**的最大值。一棵树也可以被分解成层级。根节点在第 0 层，它的子节点在第 1 层，以此类推。上图中的树的高度为 3（最大高度已在图中表示——第 3 层）。

## 树的类型

二叉树中的节点最多只能有两个子节点：一个是左侧子节点，另一个是右侧子节点。这些定义有助于我们写出更高效的向/从树中插入、查找和删除节点的算法。二叉树在计算机科学中的应用非常广泛。

完美二叉树：每一层的子节点都是满的

### 完全二叉树

According to Wikipedia, every level, except possibly the last, is completely filled in a complete binary tree, and all nodes in the last level are as far left as possible. It can have between 1 and 2h nodes inclusive at the last level h.

只有一个节点也是完全二叉树

### 二叉搜索树

二叉搜索树（BST）是二叉树的一种，但是它只允许你在**左侧节点存储（比父节点）小的值**，在**右侧节点存储（比父节点）大（或者等于）的值**。上一节的图中就展现了一棵二叉搜索树。

对于每一棵二叉搜索树树（子树），根节点就是这些数据的**中位数** @@@

# 二叉搜索树的实现

## Binary Search Tree 类

```js
class TreeNode { //{1}
    val = null
    key = null
    left = null
    right = null

    constructor(val) {
        this.val = val;
        this.key = val;
    }
}

class BinarySearchTree {
    root = null; //{2}
}

```

下图展现了二叉搜索树数据结构的组织方式：

![1554602235414](/img/user/programming/basic/data-structure/tree/1554602235414.png)

和**链表**一样，将通过**指针**来表示节点之间的关系（术语称其为边）。

在双向链表中，每个节点包含两个指针，一个指向下一个节点，另一个指向上一个节点。对于树，使用同样的方式（也使用两个指针）。但是，一个指向左侧子节点，另一个指向右侧子节点。

因此，将声明一个 Node 类来表示树中的每个节点（行{1}）。值得注意的一个小细节是，不同于在之前的章节中将**节点本身**称作节点或项，我们将会称其为键。**键是树相关的术语中对节点的称呼**。

> 双向链表一个节点包括：前指针 数据 后指针
>
> 二叉树一个节点包括：左指针 键 右指针

我们将会遵循和 [linked-list](linked-list.md) 类中相同的模式, 这表示也将声明一个变量以控制此数据结构的第一个节点。在树中，它不再是头节点，而是**根元素**（行{2}）。

## 基本操作

- insert(key)：向树中插入一个新的键。
- search(key)：在树中查找一个键，如果节点存在，则返回 true；如果不存在，则返回 false。
- inOrderTraverse：通过**中序遍历**方式遍历所有节点。
- preOrderTraverse：通过**先序遍历**方式遍历所有节点。
- postOrderTraverse：通过**后序遍历**方式遍历所有节点。
- min：返回树中最小的值/键。
- max：返回树中最大的值/键。
- ceiling(key)：返回大于等于 key 的最小值，类似于 Math.ceiling(key) ，只是返回值必须是树中的键
- floor(key)：返回小于等于 key 的最大值
- remove(key)：从树中移除某个键。

# insert()

下面的代码是用来向树插入一个新键的算法的第一部分：

```js
    insert(key) {
        const node = new TreeNode(key);
        if (this.root === null) this.root = node;
        else this.insertNode(this.root, node);
        // 返回插入的节点
        return node;
    }
```

要向树中插入一个新的节点（或项），要经历三个步骤：

1. 创建用来表示新节点的 Node 类实例。只需要向构造函数传递我们想用来插入树的节点值，它的左指针和右指针的值会由构造函数自动设置为 null。
2. 验证这个插入操作是否为一种特殊情况。这个特殊情况就是我们要插入的节点是树的第一个节点（行{2}）。如果是，就将根节点指向新节点。
3. 将节点加在非根节点的其他位置。这种情况下，需要一个私有的**辅助函数** insertNode ，函数定义如下：

```js
    insertNode(node, newNode) {
        if (newNode.key < node.key) {
            //{4}
            if (node.left === null) {
                //{5}
                node.left = newNode //{6}
            } else {
                this.insertNode(node.left, newNode) //{7}
            }
        } else {
            if (node.right === null) {
                //{8}
                node.right = newNode //{9}
            } else {
                this.insertNode(node.right, newNode) //{10}
            }
        }
    }
```

如果树非空，需要找到插入新节点的位置。因此，在调用 insertNode 方法时要通过参数传入**树的根节点和要插入的节点。**

> 正如链表必须从头部开始遍历，树必须要从根节点开始递归

如果新节点的键小于当前节点的键（初始时，当前节点就是根节点）（行{4}）那么需要检查当前节点的左侧子节点。

- 如果它没有左侧子节点（行{5}），就在那里插入新的节点。
- 如果有左侧子节点，需要通过**递归**调用 insertNode 方法（行{7}）继续找到树的下一层。
- 下次将要比较的节点将会是当前节点的左侧子节点。

如果节点的键比当前节点的键大，那么需要检查当前节点的右侧子节点。

- 如果当前节点没有右侧子节点（行{8}），就在那里插入新的节点（行{9}）。
- 如果有右侧子节点，同样需要递归调用 insertNode 方法
- 但是要用来和新节点比较的节点将会是右侧子节点。

## 示例

让我们通过一个例子来更好地理解这个过程。

首先插入第一个节点，这种情况下，树中有一个单独的节点，根指针将会指向它。源代码的行{2}将会执行。

```js
var tree = new BinarySearchTree();
```

接着输入：

```js
tree.insert(11);
tree.insert(7);
tree.insert(15);
tree.insert(5);
tree.insert(3);
tree.insert(9);
tree.insert(8);
tree.insert(10);
tree.insert(13);
tree.insert(12);
tree.insert(14);
tree.insert(20);
tree.insert(18);
tree.insert(25);
```

会创建如下结构：

![1554603298906](/img/user/programming/basic/data-structure/tree/1554603298906.png)

现在我们插入一个键值为 6 的节点：`tree.insert(6)`

1. 树不是空的，insertNode 方法将会被调用：`insertNode（root, key[6]）`
2. 算法将会检测行{4}（`key[6] < root[11]` 为真），并继续检测行{5}（`node.left[7]` 不是 null），然后将到达行{7}并调用 `insertNode(node.left[7], key[6])`
3. 将再次进入 insertNode 方法内部，但是使用了不同的参数。它会再次检测行{4}（`key[6] < node[7]` 为真），然后再检测行{5}（`node.left[5]` 不是 null），接着到达行{7}，调用 `insertNode（node.left[5], key[6])`
4. 将再一次进入 insertNode 方法内部。它会再次检测行{4}（`key[6] < node[5]` 为假），然后到达行{8}（node.right 是 null——节点 5 没有任何右侧的子节点），然后将会执行行{9}，在节点 5 的右侧子节点位置插入键 6。
5. 然后，方法调用会依次出栈，代码执行过程结束。

![1554603493487](/img/user/programming/basic/data-structure/tree/1554603493487.png)

# 深度优先遍历

遍历一棵树是指访问树的每个节点并对它们进行某种操作的过程。但是我们应该怎么去做呢？

应该从树的顶端还是底端开始呢？从左开始还是从右开始呢？

访问树的所有节点有三种方式：中序、先序和后序。

## 中序遍历

中序遍历是一种以**上行顺序**访问 BST 所有节点的遍历方式, 也就是以从**最小到最大**的顺序访问所有节点.我们来看它的实现：

```js
this.inOrderTraverse = function (callback) {
  inOrderTraverseNode(root, callback); //{1}
};
```

inOrderTraverse 方法接收一个回调函数作为参数。回调函数用来定义我们**对遍历到的每个节点进行的操作**

> 这也叫作访问者模式，要了解更多关于访问者模式的信息，请参考 http://en.wikipedia.org/wiki/Visitor_pattern）。

由于我们在 BST 中最常实现的算法是递归，这里使用了一个私有的辅助函数，来接收一个节点和对应的回调函数作为参数（行{1}）。

```js
var inOrderTraverseNode = function (node, callback) {
  if (node !== null) {
    //{2}
    inOrderTraverseNode(node.left, callback); //{3}
    callback(node.key); //{4}
    inOrderTraverseNode(node.right, callback); //{5}
  }
};
```

要通过中序遍历的方法遍历一棵树，首先要检查以参数形式传入的节点是否为 null

> 这就是停止递归继续执行的判断条件——行{2}——递归算法的基本条件
> 同样的从根节点开始递归

然后，递归调用相同的函数来访问左侧子节点（行{3}）。接着对这个节点进行一些操作（callback），然后再访问右侧子节点（行{5}）。

我们试着在之前展示的树上执行下面的方法：

```js
function printNode(value) {
  //{6}
  console.log(value);
}
tree.inOrderTraverse(printNode); //{7}
```

3 5 6 7 8 9 10 11 12 13 14 15 18 20 25 （每个数字将会输出在不同的行）

下图展示了访问的路径：

![1554605385643](/img/user/programming/basic/data-structure/tree/1554605385643.png)

## 先序遍历

先序遍历是以**父节点优先于后代节点**的顺序访问每个节点的

```js
this.preOrderTraverse = function (callback) {
  preOrderTraverseNode(root, callback);
};
```

```js
var preOrderTraverseNode = function (node, callback) {
  if (node !== null) {
    callback(node.key); //{1}
    preOrderTraverseNode(node.left, callback); //{2}
    preOrderTraverseNode(node.right, callback); //{3}
  }
};
```

先序遍历和中序遍历的不同点是，先序遍历会先访问节点本身（行{1}），然后再访问它的左侧子节点（行{2}），最后是右侧子节点（行{3}），而中序遍历的执行顺序是：{2}、{1}和{3}。

下面的图描绘了 preOrderTraverse 方法的访问路径：

![1554605791244](/img/user/programming/basic/data-structure/tree/1554605791244.png)

## 后序遍历

后序遍历则是**先访问节点的后代节点，再访问节点本身**。后序遍历的一种应用是计算一个目录和它的子目录中所有文件所占空间的大小。

```js
this.postOrderTraverse = function (callback) {
  postOrderTraverseNode(root, callback);
};
```

```js
var postOrderTraverseNode = function (node, callback) {
  if (node !== null) {
    postOrderTraverseNode(node.left, callback); //{1}
    postOrderTraverseNode(node.right, callback); //{2}
    callback(node.key); //{3}
  }
};
```

这个例子中，后序遍历会先访问左侧子节点（行{1}），然后是右侧子节点（行{2}），最后是父节点本身（行{3}）。

你会发现，中序、先序和后序遍历的实现方式是很相似的，唯一不同的是行{1}、{2}和{3}的执行顺序。

下面的图描绘了 postOrderTraverse 方法的访问路径：

![1554605962009](/img/user/programming/basic/data-structure/tree/1554605962009.png)

## 总结

前中后指的是 callback 的执行位置

中序遍历，根节点，在中间出现

中序遍历的一种应用就是对树进行**排序**操作

先序遍历，根节点，最先出现

先序遍历的一种应用是打印一个结构化的文档。

后序遍历，根节点，最后出现

# 广度优先遍历

又叫层次遍历

```js
// 层次遍历、广度优先搜索
var levelOrder = function (root) {
  if (root === null) return "";
  const queue = [root];
  const res = [];
  while (queue.length > 0) {
    const node = queue.shift();
    res.push(node.key);

    if (node.left != null) queue.push(node.left);
    // null 可以用于辨识树的结构，连续的两个null，即是一个叶节点
    else res.push(null); 

    if (node.right != null) queue.push(node.right);
    else res.push(null);
  }
  return res;
};
```

往队列添加所有后继节点

层次遍历其实也可以有先后之分的, 因为数组的性能原因, shift 操作是比较好时的, 最好是能通过 push 和 pop 来遍历所有的后代节点.

但是这样的话就会后进的先遍历, 相当于是左右节点互换了一下位置? 这样的话再让 left 和 right 的顺序换一换就可以正常顺序展示了, 性能更好

```js
var countNodes = function(node) {
    const queue = [node];
    let count = 0;

    while (queue.length > 0) {
        const n = queue.pop();

        if (n !== null) {
            console.log('n: ', n.key);
            count++;
			// left 和 right 顺序调换, 以使用 pop 方法遍历, 相当于是用栈
            queue.push(n.right);
            queue.push(n.left);
        }
    }

    return count;
};
```

# depth()

```js
// this.depth = maxDepth(root) 有没有保存闭包的区别
this.depth = function () {
  return maxDepth(root);
};
function maxDepth(node) {
  if (node === null) return 0;
  const leftDepth = maxDepth(node.left);
  const rightDepth = maxDepth(node.right);
  return Math.max(leftDepth, rightDepth) + 1;
}
```

深度优先，标记深度

https://leetcode.cn/problems/add-one-row-to-tree/submissions/

# print()

```js
// 本质：标记深度的 前中后序遍历
this.print = function () {
  const level = [];
  traverse(root, 0, level);
  return level;
};
function traverse(node, depth, level) {
  // 新的一层，新建该层的数组
  if (depth > level.length - 1) level.push([]);

  if (node === null) {
    level[depth].push(node);
  } else {
    level[depth].push(node.key);
    traverse(node.left, depth + 1, level);
    traverse(node.right, depth + 1, level);
  }
}
```

深度优先，标记深度

# serialize()

[297. 二叉树的序列化与反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)

## 题目

序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

```
示例:
你可以将以下二叉树：
	 1
  / \
  2   3
     / \
    4   5
// 序列化为 "[1,2,3,null,null,4,5]"
```

提示: 这与 LeetCode 目前使用的方式一致，详情请参阅 LeetCode 序列化二叉树的格式。你并非必须采取这种方式，你也可以采用其他的方法解决这个问题。

说明: 不要使用类的成员 / 全局 / 静态变量来存储状态，你的序列化和反序列化算法应该是无状态的。

## 序列化

### 深度优先 - 先序遍历

![image.png](/img/user/programming/basic/data-structure/tree/22aa4f1f4325669c02d898729d72d5cd56cb47ea00d82528d4df15239ed46c35-image.png)

```js
var serialize = function (node, res = []) {
  if (node !== null) {
    res.push(node.val); //{1}
    serialize(node.left, res); //{2}
    serialize(node.right, res); //{3}
  } else {
    res.push(null);
  }
  // 序列化成字符串
  return res.toString();
};
```

字符串形式 null 被序列化成空位，更加节省空间

### 广度优先 - 层次遍历

```js
var serialize = function(root) {
  if(root===null) return JSON.stringify([null]
  const queue = [root]
  const res = []
  while(queue.length>0){
    const node = queue.shift()
    res.push(node.val)
    if(node.left!=null) queue.push(node.left)
    else res.push(null)

    if(node.right!=null) queue.push(node.right)
    else res.push(null)
  }
  // 序列化成JSON
  return JSON.stringify(res)
}
```

与层次遍历的区别在于叶节点的 null 也需要 push，用于标记树的结构

## 反序列化

### 解析 JSON

```js
var deserialize = function (data) {
  const serialize = JSON.parse(data);
  return recursion(serialize);
};
function recursion(arr) {
  if (arr[0] === null) return arr.shift();

  const root = new Node(arr.shift());
  root.left = recursion(arr);
  root.right = recursion(arr);

  return root;
}
```

### 解析字符串

`const serialize = data.split(',')`

null 被序列化为空 > `["2", "1", "", "", "4", "3", "", "", …]`

JSON 则是 `[1,2,null,null,3,4,null,null,5,null,null]`

```js
var deserialize = function (data) {
  const serialize = data.split(",");
  const rt = recursion(serialize);
  return rt;
  function recursion(arr) {
    if (arr[0] === "") {
      arr.shift();
      return null;
    }

    const root = new Node(arr.shift());
    root.left = recursion(arr);
    root.right = recursion(arr);

    return root;
  }
};
```

# search()

搜索树中的值

在树中，有三种经常执行的搜索类型：

- 搜索最小值
- 搜索最大值
- 搜索特定的值

## 搜索最小值和最大值

我们使用下面的树作为示例：

![1554606164872](/img/user/programming/basic/data-structure/tree/1554606164872.png)

如果你看一眼树最后一层最左侧的节点，会发现它的值为 3，这是这棵树中最小的键。

如果你再看一眼树最右端的节点（同样是树的最后一层），会发现它的值为 25，这是这棵树中最大的键

这种特点在我们实现搜索树节点的最小值和最大值的方法时能给予我们很大的帮助。

### 搜索最小值

首先，我们来看寻找树的最小键的方法：

```js
this.min = function () {
  return minNode(root); //{1}
};
```

```js
var minNode = function (node) {
  if (node) {
    while (node && node.left !== null) {
      //{2}
      node = node.left; //{3}
    }
    // 跳出while循环，到底最底层、最左处的节点
    return node.key;
  }
  return null; //{4}
};
```

minNode 方法允许我们**从树中任意一个节点开始寻找最小的键**。我们可以使用它来找到一棵树或它的子树中最小的键。因此，我们在调用 minNode 方法的时候传入树的根节点（行{1}），因为我们想要找到整棵树的最小键。

### 搜索最大值

```js
this.max = function () {
  return maxNode(root);
};

var maxNode = function (node) {
  if (node) {
    while (node && node.right !== null) {
      //{5}
      node = node.right;
    }
    return node.key;
  }
  return null;
};
```

要找到最大的键，我们要沿着树的右边进行遍历（行{5}）直到找到最右端的节点。

因此，对于寻找最小值，总是沿着树的左边；而对于寻找最大值，总是沿着树的右边。

## 搜索特定值

在之前的章节中，我们同样实现了 find、search 或 get 方法来查找数据结构中的一个特定的值（和之前章节中实现的 has 方法相似）。我们将同样在 BST 中实现搜索的方法，来看它的实现：

```js
this.search = function (key) {
  return searchNode(root, key); //{1}
};
```

```js
var searchNode = function (node, key) {
  if (node === null) {
    //{2}
    return false;
  }
  if (key < node.key) {
    //{3}
    return searchNode(node.left, key); //{4}
  } else if (key > node.key) {
    //{5}
    return searchNode(node.right, key); //{6}
  } else {
    return true; //{7}
  }
};
```

我们要做的第一件事，是声明 search 方法。和 BST 中声明的其他方法的模式相同，我们将会使用一个辅助函数 searchNode

searchNode 方法可以用来寻找一棵树或它的任意子树中的一个特定的值。这也是为什么在行{1}中调用它的时候传入树的根节点作为参数。

在开始算法之前，先要验证作为参数传入的 node 是否合法（不是 null）。如果是 null 的话，说明要找的键没有找到，返回 false。

如果传入的节点不是 null，需要继续验证。

- 如果要找的键比当前的节点小（行{3}），那么继续在左侧的子树上搜索（行{4}）
- 如果要找的键比当前的节点大，那么就从右侧子节点开始继续搜索（行{6}）
- 否则就说明要找的键和当前节点的键相等，就返回 true 来表示找到了这个键（行{7}）

## ceiling(key) 和 floor(key)

`ceiling(key)` 函数：返回大于等于 `key` 的最小元素，如果不存在，返回空。

`floor(key)` 函数：返回小于等于 `key` 的最大元素，如果不存在，返回空。

floor 分析如下，在查找过程中，分 3 种情况：

1. key 小于当前结点的键，所以对 key 向下取整的结果肯定会在左子树， 所以向左儿子递归处理
2. key 等于当前结点的键， 也符合 floor 的定义， 所以直接返回该键
3. key 大于当前结点的键，这种情况只能先排除左子树，在此基础上有两种可能：floor 值就是当前结点的键，或者 floor 在当前结点的右子树中， 但由于条件不足无法立即给出判断，所以只能继续向右子树递归 floor 方法，并取得递归的返回值，判断递归返回的结果是否为 null
   - 如果递归返回 null,说明右子树没有 floor 值，所以 floor 值就是当前结点的键，
   - 如果递归不为 null,说明右子树还有比当前结点键更大的 floor 值，所以返回递归后的非 null 的 floor 值

```js
this.floor = function (key) {
  return floorNode(root, key);
};
function floorNode(node, key) {
  if (node === null) return null;
  if (key < node.key) {
    // floor值肯定在左子树
    return floorNode(node.left, key);
  } else if (key === node.key) {
    return node.key;
  } else {
    // key>node.key
    // 有两种可能：floor值是当前结点或在右子树
    const floor = floorNode(node.right, key);
    if (floor === null) return node.key;
    else return floor;
  }
}
```

```js
// ceiling
this.ceiling = function (key) {
  return ceilingNode(root, key);
};
function ceilingNode(node, key) {
  if (node === null) return false;
  if (key > node.key) {
    // ceiling值肯定在右子树
    return ceilingNode(node.right, key);
  } else if (key === node.key) {
    return node.key;
  } else {
    // key<node.key
    // 有两种可能：ceiling值是当前节点或者是左子树
    const ceiling = ceilingNode(node.left, key);
    if (ceiling === null) return node.key;
    else return ceiling;
  }
}
```

# remove()

```js
this.remove = function (key) {
  root = removeNode(root, key); //{1}
};
```

这个方法接收要移除的键并且它调用了 `removeNode` 方法，传入 root 和要移除的键作为参数（行{1}）。我要提醒大家的一件非常重要的事情是，**root 被赋值为 removeNode 方法的返回值**。我们稍后会明白其中的原因。

removeNode 方法的复杂之处在于我们要处理不同的运行场景，当然也包括它同样是通过递归来实现的。

我们来看 removeNode 方法的实现：

```js
var removeNode = function (node, key) {
  if (node === null) {
    //{2}
    return null;
  }
  if (key < node.key) {
    //{3}
    node.left = removeNode(node.left, key); //{4}
    return node; //{5}
  } else if (key > node.key) {
    //{6}
    node.right = removeNode(node.right, key); //{7}
    return node; //{8}
  } else {
    //键等于node.key

    //第一种情况——一个叶节点
    if (node.left === null && node.right === null) {
      //{9}
      node = null; //{10}
      return node; //{11}
    }

    //第二种情况——一个只有一个子节点的节点
    if (node.left === null) {
      //{12}
      node = node.right; //{13}
      return node; //{14}
    } else if (node.right === null) {
      //{15}
      node = node.left; //{16}
      return node; //{17}
    }

    //第三种情况——一个有两个子节点的节点
    var aux = findMinNode(node.right); //{18}
    node.key = aux.key; //{19}
    node.right = removeNode(node.right, aux.key); //{20}
    return node; //{21}
  }
};
```

我们来看行{2}，如果正在检测的节点是 null，那么说明键不存在于树中，所以返回 null。

然后，我们要做的第一件事，就是在树中找到要移除的节点。因此，

- 如果要找的键比当前节点的值小（行{3}），就沿着树的左边找到下一个节点（行{4}）。
- 如果要找的键比当前节点的值大（行{6}），那么就沿着树的右边找到下一个节点（行{7}）。

如果我们找到了要找的键（键和 node.key 相等），就需要处理三种不同的情况。

findMinNode 方法如下：

```js
function findMinNode(node) {
  while (node && node.left !== null) {
    node = node.left;
  }
  return node;
}
```

## 移除一个叶节点

第一种情况是该节点是一个没有左侧或右侧子节点的叶节点——行{9}。在这种情况下，我们要做的就是给这个节点赋予 null 值来移除它（行{9}）。但是当学习了链表的实现之后，我们知道仅仅赋一个 null 值是不够的，还需要处理指针。在这里，这个节点没有任何子节点，但是它有一个父节点，需要通过返回 null 来将对应的父节点指针赋予 null 值（行{11}）

现在节点的值已经是 null 了，父节点指向它的指针也会接收到这个值，这也是我们要在函数中返回节点的值的原因。父节点总是会接收到函数的返回值。另一种可行的办法是将父节点和节点本身都作为参数传入方法内部

如果回头来看方法的第一行代码，会发现我们在行{4}和行{7}更新了节点左右指针的值，同样也在行{5}和行{8}返回了更新后的节点

下图展现了移除一个叶节点的过程

![1554617676771](/img/user/programming/basic/data-structure/tree/1554617676771.png)

## 移除有一个左侧或右侧子节点的节点

现在我们来看第二种情况，移除有一个左侧子节点或右侧子节点的节点。这种情况下，需要跳过这个节点，直接将父节点指向它的指针指向子节点

如果这个节点没有左侧子节点（行{12}），也就是说它有一个右侧子节点。那我们把**父节点**对它的引用改为对它右侧子节点的引用（行{13}）通过返回更新后的节点（行{14}）

如果这个节点没有右侧子节点，也是一样——把**父节点**对它的引用改为对它左侧子节点的引用（行{16}）通过返回更新后的值（行{17}）

下图展现了移除只有一个左侧子节点或右侧子节点的节点的过程：

![1554617885924](/img/user/programming/basic/data-structure/tree/1554617885924.png)

## 移除有两个子节点的节点

现在是第三种情况，也是最复杂的情况，那就是要移除的节点有两个子节点——左侧子节点和右侧子节点。要移除有两个子节点的节点，需要执行四个步骤。

1. 当找到了需要移除的节点后，需要找到它**右边子树中最小的节点**（它的继承者——行{18}）。
2. 然后，用它右侧子树中最小节点的键去更新这个节点的值（行{19}）。通过这一步，我们改变了这个节点的键，也就是说它被移除了。
3. 但是，这样在树中就有两个拥有相同键的节点了，这是不行的。要继续把右侧子树中的最小节点移除，毕竟它已经被移至要移除的节点的位置了（行{20}）。
4. 最后，向它的父节点返回更新后节点的引用（行{21}）。

findMinNode 方法的实现和 min 方法的实现方式是一样的。唯一不同之处在于，在 min 方法中只返回键，而在 findMinNode 中返回了节点。

下图展现了移除有两个子节点的节点的过程：

![1554618074255](/img/user/programming/basic/data-structure/tree/1554618074255.png)

# 自平衡树

BST 存在一个问题：取决于你添加的节点数，树的一条边可能会非常深；也就是说，树的一条分支会有很多层，而其他的分支却只有几层。

这会在需要在某条边上添加、移除和搜索某个节点时引起一些性能问题。

```
            6
           /
          5
         /
        4
       /
      3
     /
    2
   /
  1
```

在上面这棵二叉搜索树上查找一个元素需要花费 **线性**时间复杂度，这跟在链表中搜索的速度是一样的。越是有序的数组这个问题越严重，现在我们来比较一下下面这棵平衡二叉搜索树。

```
          4
        /   \
       2     6
      / \   /
     1   3  5
```

假设这棵树上节点总数为 n，一个平衡树能把高度维持在 h=logn。因此这棵树上支持在 O(h)=O(logn) 时间内完成 插入，搜索，删除 操作。

为了解决这个问题，有一种树叫作 Adelson-Velskii-Landi 树（AVL 树）。AVL 树是一种自平衡二叉搜索树，意思是任何一个节点左右两侧子树的高度之差最多为 1。也就是说这种树会在添加或移除节点时尽量试着成为一棵完全树。

AVL 树是一种自平衡树。添加或移除节点时，AVL 树会尝试自平衡。任意一个节点（不论深度）的左子树和右子树高度最多相差 1。添加或移除节点时，AVL 树会尽可能尝试转换为完全树。

## 在 AVL 树插入节点

在 AVL 树中插入或移除节点和 BST 完全相同。然而，AVL 树的不同之处在于我们需要**检验它的平衡因子**，如果有需要，则将其逻辑应用于树的自平衡。

```js
var insertNode = function (node, element) {
  if (node === null) {
    node = new Node(element);
  } else if (element < node.key) {
    node.left = insertNode(node.left, element);

    if (node.left !== null) {
      // 确认是否需要平衡 {1}
    }
  } else if (element > node.key) {
    node.right = insertNode(node.right, element);

    if (node.right !== null) {
      // 确认是否需要平衡 {2}
    }
  }
  return node;
};
```

## 计算平衡因子

在 AVL 树中，需要对每个节点计算右子树高度（hr）和左子树高度（hl）的差值，该值 （hr－hl）应为 0、1 或 -1。如果结果不是这三个值之一，则需要平衡该 AVL 树。这就是平衡因子的概念

注意：每个节点的平衡因子与子节点的平衡因子无关

下图举例说明了一些树的**平衡因子**（所有的树都是平衡的）：

![1554618444522](/img/user/programming/basic/data-structure/tree/1554618444522.png)

计算**节点高度**的代码如下：

```js
var heightNode = function (node) {
  if (node === null) {
    return -1;
  } else {
    return Math.max(heightNode(node.left), heightNode(node.right)) + 1;
  }
};
```

平衡因子：右节点高度 - 左节点高度，而不是 右平衡因子 - 左平衡因子

因此，向左子树插入新节点时，需要计算其高度；如果高度差大于 1（即不为 -1、0 和 1 之一），就需要平衡左子树。代码如下：

```js
// 替换insertNode方法的行{1}
if (heightNode(node.left) - heightNode(node.right) > 1) {
  // 旋转 {3}
}
```

向右子树插入新节点时，应用同样的逻辑，代码如下：

```js
// 替换insertNode方法的行{2}
if (heightNode(node.right) - heightNode(node.left) > 1) {
  // 旋转 {4}
}
```

## AVL 旋转

向 AVL 树插入节点时，可以执行单旋转或双旋转两种平衡操作，分别对应四种场景。

右右（RR）：向左的单旋转

左左（LL）：向右的单旋转

左右（LR）：向右的双旋转

右左（RL）：向左的双旋转

### 右右（RR）：向左的单旋转

![1554620603297](/img/user/programming/basic/data-structure/tree/1554620603297.png)

假设向 AVL 树插入节点 90，这会造成树失衡（节点 50-Y 高度为 -2），因此需要恢复树的平衡。因为是右节点的右节点导致的失衡，所以称之为 RR，所执行的操作是左旋转

与平衡操作相关的节点有三个（X、Y、Z）

操作的主要目标是节点 Y：

- 对节点 Y 绕着 X 进行左旋转

Y 旋转到 X 的左子树上

- Z 被 Y 霸占了自己原本的位置，所以需要重新插入
- Z 比 X 小，比 Y 大，应该插入到 Y 的右子树

```js
function rotationRR(node) {
  const temp = node.right; // {1} temp = X , 保存X
  node.right = temp.left; // {2} Y.right = Z , 断开X与Y，同时修正Z的位置
  temp.left = node; // {3} X.left = Y , 修正Y的位置
  return temp;
}
```

### 左左（LL）：向右的单旋转

![1554635266775](/img/user/programming/basic/data-structure/tree/1554635266775.png)

假设向 AVL 树插入节点 5，这会造成树失衡（节点 50-Y 平衡因子为 +2），需要恢复树的平衡。因为是左节点的左节点导致的失衡，所以称之为 LL，所执行的操作是右旋转

与平衡操作相关的节点有三个（X、Y、Z）

操作的主要目标是节点 Y：

- 对节点 Y 绕着 X 进行右旋转
- Y 旋转到 X 的右子树上
- Z 被 Y 霸占了自己原本的位置，所以需要重新插入
- Z 比 X 大，比 Y 小，应该插入到 Y 的左子树

```js
function rotationLL(node) {
  var tmp = node.left; // {1} temp = X , 保存X
  node.left = tmp.right; // {2} Y.left = Z , 断开X与Y，同时修正Z的位置
  tmp.right = node; // {3} X.right = Y , 修正Y的位置
  return tmp;
}
```

### 左右（LR）：先左旋转再右旋转

![1554635387919](/img/user/programming/basic/data-structure/tree/1554635387919.png)

假设向 AVL 树插入节点 35，这会造成树失衡（节点 50-Y 平衡因子为 +2），需要恢复树的平衡。因为是左节点的右节点导致的失衡，所以称之为 LR，所执行的操作是先左旋转再右旋转

把先把目光聚焦在 Z 和 X：

- 对节点 Z 绕着 X 进行左旋转
- Z 旋转到 X 的左子树上
- 被 Y 霸占的子节点 35，所以需要重新插入
- 子节点 35 比 X 小，比 Y 大，应该插入到 Y 的右子树

以上的步骤就是一次 RR 旋转，旋转完之后是这样的

<img src='https://raw.githubusercontent.com/chiyuu-git/chiyuu-pic/master/IMG_20190723_215933.png' width='400px'>

此时把目光聚焦在 Y 和 X：

- 对节点 Y 绕着节点 X 进行右旋转
- Y 被旋转到 X 的右子树上

> 如果 X 存在右节点，对该节点重新插入即可

以上步骤就是一个 LL 旋转，这样一个 LR 二叉树就被平衡了

```js
function rotationLR(node) {
  node.left = rotationRR(node.left);
  return rotationLL(node);
}
```

### 右左（RL）：向左的双旋转

![1554635648688](/img/user/programming/basic/data-structure/tree/1554635648688.png)

设向 AVL 树插入节点 75，这会造成树失衡（节点 70 Y 高度为 -2），需要恢复树的平衡。因为是右节点的左节点导致的失衡，所以称之为 RL，所执行的操作是先右旋转再左旋转

基本上，就是先做一次 LL 旋转，再做一次 RR 旋转。

```js
function rotationRL(node) {
  node.right = rotationLL(node.right);
  return rotationRR(node);
}
```

## 辨别类型

出现不平衡的那个节点开始查看，那个节点就是节点 Y

然后往不平衡的方向走，走两步就决定错误的类型

<img src='https://raw.githubusercontent.com/chiyuu-git/chiyuu-pic/master/IMG_20190724_162223_1.png' width='400px'>

## 完成 insertNode 方法

在实际使用中，我们总是使用自平衡二叉树，所以需要对之前实现的 insertNode 方法进行替换

向左子树插入新节点，且节点的值小于其左子节点时，应进行 LL 旋转。否则，进行 LR 旋转。该过程的代码如下：

```js
// 替换insertNode方法的行{1}
if (heightNode(node.left) - heightNode(node.right) > 1) {
  // 旋转 {3}
  if (element < node.left.key) {
    node = rotationLL(node);
  } else {
    node = rotationLR(node);
  }
}
```

向右子树插入新节点，且节点的值大于其右子节点时，应进行 RR 旋转。否则，进行 RL 旋转。该过程的代码如下：

```js
// 替换insertNode方法的行{2}
if (heightNode(node.right) - heightNode(node.left) > 1) {
  // 旋转 {4}
  if (element > node.right.key) {
    node = rotationRR(node);
  } else {
    node = rotationRL(node);
  }
}
```

### 完整代码

```js
this.insert = function (key) {
  const newNode = new Node(key); //{1}
  if (root === null) {
    //{2}
    root = newNode;
  } else {
    // 接受顶层的变化
    root = insertNode(root, newNode); //{3}
  }
};
function insertNode(node, newNode) {
  if (node === null) {
    node = newNode;
    return node;
  }
  if (newNode.key < node.key) {
    //{4}
    node.left = insertNode(node.left, newNode); //{7}
    // 确认是否需要平衡 {1}
    if (heightNode(node.left) - heightNode(node.right) > 1) {
      // 传入需要平衡的节点
      if (newNode.key < node.left.key) node = rotationLL(node);
      else node = rotationLR(node);
    }
  } else {
    node.right = insertNode(node.right, newNode); //{10}
    // 确认是否需要平衡 {2}
    if (heightNode(node.right) - heightNode(node.left) > 1) {
      // 传入需要平衡的节点
      if (newNode.key > node.right.key) node = rotationRR(node);
      else node = rotationRL(node);
    }
  }
  // 需要返回平衡后的节点，所以和一开始的实现不太一样
  return node;
}
function heightNode(node) {
  if (node === null) {
    return -1;
  } else {
    return Math.max(heightNode(node.left), heightNode(node.right)) + 1;
  }
}
```

### 测试

```js
var tree = new BinarySearchTree();
tree.insert(7);
tree.insert(15);
tree.insert(5);
tree.insert(3);
tree.insert(9);
tree.insert(8); // LL
tree.insert(10);
tree.insert(13); // RL
tree.insert(12); // LL
tree.insert(14); // RR
tree.insert(20);
tree.insert(18); // RR
tree.insert(25);
tree.insert(11);
```

## 完成 Remove 方法

# 红黑树

尽管 AVL 树是自平衡的，其插入或移除节点的性能并不总是最好的。更好的选择是红黑树。

红黑树可以高效有序地遍历其节点（http://goo.gl/OxED8K）。本书不打算讲解红黑树，但也提供了它的源代码。

# 哈夫曼树

# 总结

## 用递归解决问题

在前面的章节中，我们已经介绍了如何利用递归求解树的遍历。 递归是解决树的相关问题最有效和最常用的方法之一。

我们知道，树可以以递归的方式定义为一个节点（根节点），它包括一个值和一个指向其他节点指针的列表。 递归是树的特性之一。 因此，许多树问题可以通过递归的方式来解决。 对于每个递归层级，我们只能关注单个节点内的问题，并通过递归调用函数来解决其子节点问题。

通常，我们可以通过 “自顶向下” 或 “自底向上” 的递归来解决树问题。

## “自顶向下” 的解决方案

“自顶向下” 意味着在每个递归层级，我们将首先访问节点来计算一些值，并在递归调用函数时将这些值传递到子节点。 所以 “自顶向下” 的解决方案可以被认为是一种**前序遍历**。 具体来说，递归函数 `top_down(root, params)` 的原理是这样的：

```
return specific value for null node
update the answer if needed                      // anwer <-- params
left_ans = top_down(root.left, left_params)      // left_params <-- root.val, params
right_ans = top_down(root.right, right_params)   // right_params <-- root.val, params
return the answer if needed                      // answer <-- left_ans, right_ans
```

### 示例

思考这样一个问题：给定一个二叉树，请寻找它的最大深度。

我们知道根节点的深度是 `0`。 对于每个节点，如果我们知道某节点的深度，那我们将知道它子节点的深度。 因此，在调用递归函数的时候，将节点的深度传递为一个参数，那么所有的节点都知道它们自身的深度。 而对于叶节点，我们可以通过更新深度从而获取最终答案。 这里是递归函数 `maximum_depth(root, depth)` 的伪代码：

  ```
  return if root is null
  if root is a leaf node:
       answer = max(answer, depth)         // update the answer if needed
  maximum_depth(root.left, depth + 1)      // call the function recursively for left child
  maximum_depth(root.right, depth + 1)     // call the function recursively for right child
  ```

### 示例

让我们继续讨论前面关于树的最大深度的问题，但是使用不同的思维方式：对于树的单个节点，以节点自身为根的子树的最大深度 `x` 是多少？

如果我们知道一个根节点，以其**左**子节点为根的最大深度为 `left` 和以其**右**子节点为根的最大深度为 `right`，我们是否可以回答前面的问题？ 当然可以，我们可以选择它们之间的最大值，再加上 1 来获得根节点所在的子树的最大深度。 那就是 `x = max（left，right）+ 1`。

这意味着对于每一个节点来说，我们都可以在解决它子节点的问题之后得到答案。 因此，我们可以使用“自底向上“的方法。下面是递归函数 `maximum_depth(root)` 的伪代码：

  ```
  return 0 if root is null                 // return 0 for null node
  left_depth = maximum_depth(root.left)
  right_depth = maximum_depth(root.right)
  return max(left_depth, right_depth) + 1  // return depth of the subtree rooted at root
  ```

## “自底向上” 的解决方案

“自底向上” 是另一种递归方法。 在每个递归层次上，我们首先对所有子节点递归地调用函数，然后根据返回值和根节点本身的值得到答案。 这个过程可以看作是**后序遍历**的一种。 通常， “自底向上” 的递归函数 `bottom_up(root)` 为如下所示：

```
return specific value for null node
left_ans = bottom_up(root.left)       // call function recursively for left child
right_ans = bottom_up(root.right)     // call function recursively for right child
return answers                        // answer <-- left_ans, right_ans, root.val
```

了解递归并利用递归解决问题并不容易。

当遇到树问题时，请先思考一下两个问题：

1. 你能确定一些参数，从该节点自身解决出发寻找答案吗？
2. 你可以使用这些参数和节点本身的值来决定什么应该是传递给它子节点的参数吗？

如果答案都是肯定的，那么请尝试使用 “`自顶向下`” 的递归来解决此问题。

或者你可以这样思考：对于树中的任意一个节点，如果你知道它子节点的答案，你能计算出该节点的答案吗？ 如果答案是肯定的，那么 “`自底向上`” 的递归可能是一个不错的解决方法。

# 常问的树面试问题

找到一个二叉树的高度

找到一个二叉搜索树中第 k 个最大值

找到距离根部“k”个距离的节点

找到一个二叉树中给定节点的祖先（ancestors）

# 二叉树变换

## [226. 翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

### 题目

+ 翻转一棵二叉树

  ```
       4
     /   \
    2     7
   / \   / \
  1   3 6   9
  
       4
     /   \
    7     2
   / \   / \
  9   6 3   1
  ```

### 递归法

+ 让左等于右，右等于左，然后递归左右

  ```js
  var invertTree = function(node) {
    if(node==null) return null
    let left = node.left,
        right = node.right
    node.left = right
    node.right = left
    invertTree(node.left)
    invertTree(node.right)
    return node
  }
  ```

### 迭代法

+ 其实都一样，保存一个 root 的引用即可

  ```js
  var invertTree = function(root) {
    if(root==null) return null
    const stack = [root]
    while(stack.length>0){
      const cur = stack.pop()
      let left = cur.left,
          right = cur.right
      cur.left = right
      cur.right = left
      if(cur.left) stack.push(cur.left)
      if(cur.right) stack.push(cur.right)
    }
    return root
  }
  ```

## [116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

### 题目

- 给定一个完美二叉树，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

  ```js
  struct Node {
    int val;
    Node *left;
    Node *right;
    Node *next;
  }
  ```

- 填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。
- 初始状态下，所有 next 指针都被设置为 NULL。

  ![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/15/116_sample.png)

### 分析

- 第一个想到层次遍历，但是层次遍历没办法知道深度，也就是无法确定下一个是不是 NULL
- 还是使用深度优先，递归传递深度

### 实现

- 层次遍历打印，最后在遍历 level 进行拼接

  ```js
  const {BST} = require('./utils/structure/BST')
  const bst = new BST()
  bst.insert(1)
  bst.insert(2)
  bst.insert(3)
  bst.insert(4)
  bst.insert(5)
  bst.insert(6)
  bst.insert(7)
  
  
  var connect = function(root) {
    const level = []
    preorder(root,0,level)
    level.forEach((subQueue) => {
      subQueue.forEach((node,index,subQueue) => {
        if(node!==null) node.next = subQueue[index+1] || null
      })
    })
    return root
  
    function preorder(node,depth,level){
      if(depth>level.length-1) level.push([])
  
      if (node === null) {
        level[depth].push(node)
      } else {
        level[depth].push(node)
        preorder(node.left, depth + 1, level)
        preorder(node.right, depth + 1, level)
      }
    }
  }
  console.log(connect(bst.root()))
  ```

- 因为是完全二叉树，可以在队列里直接指定

### 递归法

- 因为是完全二叉树，每个节点都是满的，所以在父节点处就可以处理完：
  - 左子节点的 next 是右子节点
  - 如果父节点的 next 不为 null，则右节点的 next 等于父节点 next 的左子节点

  ```c++
  var connect = function(root) {
    // 完全二叉树，左边没有，右边也没有
    if(root==null || root.left==null) return root
    root.left.next = root.right
    if(root.next !=null){
      root.right.next = root.next.left
    }
    connect(root.left)
    connect(root.right)
  }
  ```

## [117. 填充每个节点的下一个右侧节点指针 II](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/)

### 题目

- 给定一个二叉树

  ```
  struct Node {
    int val;
    Node *left;
    Node *right;
    Node *next;
  }
  ```

- 填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。
- 初始状态下，所有 next 指针都被设置为 NULL

  ![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/15/117_sample.png)

### 递归法

- 不是完全二叉树了，甚至不是平衡二叉树

  ```js
  var connect = function (root) {
    if (root == null) return root
  
    if (root.left != null) {
      if (root.right != null) {
        // 若右子树不为空，则左子树的 next 即为右子树
        root.left.next = root.right
      } else {
        // 若右子树为空，则左子树的 next 由根节点的 next 得出，next为根节点的父节点的右子树
        root.left.next = nextNode(root.next)
      }
    }
    if (root.right != null) {
      // 右子树的 next 由根节点的 next 得出
      root.right.next = nextNode(root.next)
    }
    // 先确保 root.right 下的节点的已完全连接，因 root.left 下的节点的连接
    // 需要 root.next 下的节点的信息，root.next肯定是祖先的右子树
    // 若 root.next 下的节点未完全连
    // 接（即先对 root.left 递归），则 root.next 下的信息链不完整，将
    // 返回错误的信息。可能出现的错误情况如下图所示。此时，底层最左边节点将无
    // 法获得正确的 next 信息：
    //                  o root
    //                 / \
    //     root.left  o —— o  root.right
    //               /    / \
    //              o —— o   o
    //             /        / \
    //            o        o   o
    connect(root.right)
    connect(root.left)
    return root
  
    function nextNode(node) {
      while (node != null) {
        if (node.left != null) return node.left
        if (node.right != null) return node.right
        node = node.next
      }
      return null
    }
  }
  ```

### 队列

- 一次读取一整行的 node

  ```js
  var connect = function(root) {
    if(root == null)return root;
    const queue = [root]
    while(queue.length>0){
      let cur = null
      // 一次读取一整行的node，静态化
      const size = queue.length
      for(let i=0;i<size;i++){
        const temp = queue.shift()
        if(cur !=null) cur.next = temp
        cur = temp
        // 这里改变了队列的长度，所以前面必须静态化
        if(temp.left!=null) queue.push(temp.left)
        if(temp.right!=null) queue.push(temp.right)
      }
    }
    return root
  }
  ```

## [538. 把二叉搜索树转换为累加树](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/)

### 题目

- 给定一个二叉搜索树（Binary Search Tree），把它转换成为累加树（Greater Tree)，使得每个节点的值是原来的节点值加上所有大于它的节点值之和。

  ```
  输入: 二叉搜索树:
                5
              /   \
             2     13
  
  输出: 转换为累加树:
               18
              /   \
            20     13
  ```

### 中序遍历 - 改

- 根节点加上右节点，左节点加上中节点

  ```js
  var convertBST = function(root) {
    if(root===null) return null
    inorder(root)
    return root
    function inorder(node){
      if(node===null) return
      inorder(node.right)
      node.val += node.right.val
      inroder(node.left)
    }
  }
  ```

- 想法错了，中节点也要考虑右节点的左节点的值
- 修正：用一个私有变量保存累加值

  ```js
  var convertBST = function(root) {
    if(root===null) return null
    let sum = 0
    inorder(root)
    return root
    function inorder(node){
      if(node===null) return
      inorder(node.right)
      node.val +=sum
      sum = node.val
      inorder(node.left)
    }
  }
  ```

# 遍历

## 二叉树相等

![100. 相同的树](../leetcode/100.%20相同的树.md)

## 对称二叉树

### 题目

- 给定一个二叉树，检查它是否是镜像对称的。
- 例如，二叉树 `[1,2,2,3,4,4,3]` 是对称的。

  ```
      1
     / \
    2   2
   / \ / \
  3  4 4  3
  ```

- 但是下面这个 `[1,2,2,null,3,null,3]` 则不是镜像对称的:

  ```
      1
     / \
    2   2
     \   \
     3    3
  ```

### 递归法

- 判断一棵二叉树本身是否是镜像对称的，这个问题可以转化为：二叉树的左子树与右子树是否是镜像对称的。
- 问题一经转化，就有一种似曾相识的感觉，上一篇文章刚分析过判断两棵二叉树是否相等的问题，而这道题只不过是把“相等”换为“对称”，方法其实是一样的！
- 因为是对称，所以是左节点和右节点比较

  ```js
  var isSymmetric = function(root) {
  	return isMirror(root,root)
    function isMirror(m,n){
      if(m === null && m === null) return true
      if(m === null || n === null) return false // 其中一个为空
      return (m.val === n.val)
        &&isMirror(m.left,n.right) 
        && isMirror(m.right,n.left)
    }
  }
  ```

## [617. 合并二叉树](https://leetcode-cn.com/problems/merge-two-binary-trees/)

### 题目

- 给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。

  你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则不为 NULL 的节点将直接作为新二叉树的节点。

  ```
  输入: 
  	Tree 1                     Tree 2                  
            1                         2                             
           / \                       / \                            
          3   2                     1   3                        
         /                           \   \                      
        5                             4   7                  
  输出: 
  合并后的树:
  	     3
  	    / \
  	   4   5
  	  / \   \ 
  	 5   4   7
  ```

### 分析

- 同时遍历两棵树

  ```js
  function TreeNode(val){
    this.val = val
    this.left = this.right = null
  }
  var mergeTrees = function(t1, t2) {
    if(t1===null) return t2
    if(t2===null) return t1
    return dfs(t1,t2)
    function dfs(node1,node2){
      if(node1===null) return node2
      if(node2===null) return node1
      const node = new TreeNode()
      node.val = node1.val + node2.val
      node.left = dfs(node1.left,node2.left)
      node.right = dfs(node1.right,node2.right)
      return node
    }
  }
  ```

## 根据遍历确定树

知道前序中序、中序后序遍历序列，那么可以唯一确定一棵二叉树，但是知道前序后序遍历序列就不一定能唯一确定一棵二叉树。

链接：https://www.nowcoder.com/questionTerminal/9fb40807179a4fe2bcd12abcaabda891?toCommentId=45662

简单解法： 先序序列得到 根节点为 A，根节点为 A，再根据中序序列得到，B 在 A 的右子树中，此时可以推出：后序序列最后两个是 BA。

再看，A 的左子树先序序列为 DCEFGH，得到左子树的根为 D，所以后序序列的倒数第三个是 D，得到 DBA，只有 D 符合。

## [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

### 题目

- 根据一棵树的前序遍历与中序遍历构造二叉树。
- 注意：你可以假设树中没有重复的元素。

  ```
  前序遍历 preorder = [3,9,20,15,7]
  中序遍历 inorder =  [9,3,15,20,7]
  返回如下的二叉树：
      3
     / \
    9  20
      /  \
     15   7
  ```

### 分析

- 首先，preorder 中的第一个元素一定是树的根，这个根又将 inorder 序列分成了左右两棵子树。现在我们只需要将先序遍历的数组中删除根元素，然后重复上面的过程处理左右两棵子树。

### 实现

```java
  function TreeNode(val) {
    this.val = val;
    this.left = this.right = null;
  }
  var buildTree = function(preorder,inorder) {
    let preIndex = 0
    const map = {}
    for (const [index,val] of inorder.entries()) {
      map[val] = index
    }
    return helper(0,inorder.length)
  
    function helper(left,right){
      if(left===right) return null
  
      // 根据前序遍历获得根节点
      const rootVal = preorder[preIndex]
      const root = new TreeNode(rootVal)
  
      // 根据中序遍历分为左右树
      const index = map[rootVal]
      preIndex++
      root.left = helper(left,index)
      root.right = helper(index+1,right)
      return root
    }
  }
  ```

## [106. 从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

### 实现

```js
  function TreeNode(val) {
    this.val = val;
    this.left = this.right = null;
  }
  var buildTree = function(inorder,postorder) {
    let postIndex = postorder.length-1
    const map = {}
    for (const [index,val] of inorder.entries()) {
      map[val] = index
    }
    return helper(0,inorder.length)
  
    function helper(left,right){
      if(left===right) return null
  
      // 根据后序遍历获得根节点
      const rootVal = postorder[postIndex]
      const root = new TreeNode(rootVal)
  
      // 根据中序遍历分为左右树
      const index = map[rootVal]
      postIndex--
      root.right = helper(index+1,right)
      root.left = helper(left,index)
      return root
    }
  }
  ```

## [112. 路径总和](https://leetcode-cn.com/problems/path-sum/)

### 题目

- 给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。
- 说明: 叶子节点是指没有子节点的节点。
- 示例:
  给定如下二叉树，以及目标和 sum = 22，

  ```
            5
           / \
          4   8
         /   / \
        11  13  4
       /  \      \
      7    2      1
  ```

- 返回 true, 因为存在目标和为 22 的根节点到叶子节点的路径 5->4->11->2。

### 前序遍历

- 最直接的方法就是利用递归，遍历整棵树：如果当前节点不是叶子，对它的所有孩子节点，递归调用 hasPathSum 函数，其中 sum 值减去当前节点的权值；如果当前节点是叶子，检查 sum 值是否为 0，也就是是否找到了给定的目标和。

  ```js
  var hasPathSum = function(node, sum) {
    if(node===null) return false
  
    sum -= node.val
    if(node.left===null && node.right===null){
      return sum===0
    }
    return  hasPathSum(node.left,sum) || hasPathSum(node.right,sum)
  }
  ```

**复杂度分析**

- 时间复杂度：我们访问每个节点一次，时间复杂度为 O(N) ，其中 N 是节点个数。
- 空间复杂度：最坏情况下，整棵树是非平衡的，例如每个节点都只有一个孩子，递归会调用 N 次（树的高度），因此栈的空间开销是 O(N)。但在最好情况下，树是完全平衡的，高度只有 log(N)，因此在这种情况下空间复杂度只有 O(log(N))

### 迭代

- 我们可以用栈将递归转成迭代的形式。深度优先搜索在除了最坏情况下都比广度优先搜索更快。最坏情况是指满足目标和的 root->leaf 路径是最后被考虑的，这种情况下深度优先搜索和广度优先搜索代价是相通的。
- 利用深度优先策略访问每个节点，同时更新剩余的目标和。
- 所以我们从包含根节点的栈开始模拟，剩余目标和为 `sum - root.val`
- 然后开始迭代：
  - 弹出当前元素，如果当前剩余目标和为 0 并且在叶子节点上返回 True
  - 如果剩余和不为零并且还处在非叶子节点上，将当前节点的所有孩子以及对应的剩余和压入栈中。

```java
  var hasPathSum = function(root, sum) {
    if(root===null) return false
    const nodeStack = new Stack()
    const sumStack = new Stack()
    nodeStack.push(root)
    sumStack.push(sum-root.val)
  
    while(!nodeStack.isEmpty()){
      const node = nodeStack.pop()
      const curSum = sumStack.pop()
      if (node.right == null && node.left == null && curSum === 0){
        // 和递归时不同，不能返回curSum===0，这就是递归和迭代的区别
        return true
      }
      if (node.right != null) {
        nodeStack.push(node.right);
        sumStack.push(curSum - node.right.val)
      }
      if (node.left != null) {
        nodeStack.push(node.left);
        sumStack.push(curSum - node.left.val)
      }
    }
    return false
  }
  ```

## [113. 路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)

### 题目

+ 给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。

  ```
  示例:
  给定如下二叉树，以及目标和 sum = 22
                5
               / \
              4   8
             /   / \
            11  13  4
           /  \    / \
          7    2  5   1
  [
     [5,4,11,2],
     [5,8,4,5]
  ]
  ```

### 递归

+ 前序遍历，直到叶节点

  ```js
  var pathSum = function(root, sum) {
    if(root===null) return 0
    const res = []
    dfs(root,sum)
    return res
    function dfs(node,sum,path=[]){
      if(node===null) return
      path.push(node.val)
      // 保证是叶节点
      if(sum===node.val && node.left==null && node.right==null) res.push([...path])
      dfs(node.left,sum-node.val,path)
      dfs(node.right,sum-node.val,path)
      path.pop()
    }
  }
  const {BST} = require('./utils/structure/BST')
  const bst = new BST()
  bst.insert(1)
  bst.insert(2)
  
  console.log(pathSum(bst.root(),1))
  ```

### 迭代

+ 用栈代替递归调用栈，参考 I

## [437. 路径总和 III](https://leetcode-cn.com/problems/path-sum-iii/)

### 题目

+ 给定一个二叉树，它的每个结点都存放着一个整数值。
+ 找出路径和等于给定数值的路径总数。
+ 路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。
+ 二叉树不超过 1000 个节点，且节点数值范围是 [-1000000,1000000] 的整数。

  ```
  root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8
  
        10
       /  \
      5   -3
     / \    \
    3   2   11
   / \   \
  3  -2   1
  
  返回 3。和等于 8 的路径有:
  
  1.  5 -> 3
  2.  5 -> 2 -> 1
  3.  -3 -> 11
  ```

### 双重递归

```js
  var pathSum = function(root, sum) {
    if(root===null) return 0
    let path = 0 
    dfs(root,sum)
    return path
    // 前序遍历每个节点，对每个节点调用acc函数
    function dfs(node,sum){
      if(node===null) return 
      acc(node,sum,0)
      dfs(node.left,sum)
      dfs(node.right,sum)
    }
    // 计算以当前节点为起点的路径是否符合题意
    function acc(node,sum,cur){
      if(node===null) return
  
      cur += node.val
      if(cur===sum) return path++
      
      acc(node.left,sum,cur)
      acc(node.right,sum,cur)
    }
  }
```

### 从叶节点开始

+ 显然，这种双递归的写法，有很多路径的重复计算
+ 这种计数问题，需要不重不漏，双递归通过不同起点保证不重，每个起点完整的搜索保证不漏
+ 换一种思路: 每次遍历到一个结点的时候，将其纳入路径当中，检查包含这个点时，会不会产生一个合法路径。由于之前这个点压根没出现，而现在必须包含，因此产生的方案是一定不重的： 包含本根结点的 && 不包含本根结点的
+ 利用了二叉树的一个特性，向下走有两条路，而从下向上走只有一条路（正是只有一条路这一点，免去了一个递归寻路的过程，只需沿着数组向前看就行）

  ```js
  var pathSum = function(root, sum) {
    if(root===null) return 0
    let path = 0,
        stack = [] 
    dfs(root,sum)
    return path
  
    function dfs(node,sum){
      if(node===null) return
      stack.push(node.val)
      let cur = 0
      for(let i = stack.length - 1; i >= 0; i--){
        cur += stack[i]
        if(cur === sum) path++
      }
      dfs(node.left,sum)
      dfs(node.right,sum)
      stack.pop()
    }
  }
  ```

## [236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

### 题目

+ 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。
+ 百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”
+ 例如，给定如下二叉树: root = [3,5,1,6,2,0,8,null,null,7,4]

  ```
  示例 1:
  
  let root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
  输出: 3
  解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
  ```

### 递归

+ 这种方法非常直观。先深度遍历改树。当你遇到节点 p 或 q 时，返回一些布尔标记。该标志有助于确定是否在任何路径中找到了所需的节点。在子树的递归中，如果该子树找到了目标节点，就标记为真
+ 算法：

  1. 从根节点开始遍历树。
  2. 如果当前节点本身是 p 或 q 中的一个，我们会将变量 mid 标记为 true，并继续搜索左右分支中的另一个节点。
  3. 如果左分支或右分支中的任何一个返回 true，则表示在下面找到了两个节点中的一个。
  4. 如果在遍历的任何点上，左、右或中三个标志中的任意两个变为 true，这意味着我们找到了节点 p 和 q 的最近公共祖先。

  ![img](https://pic.leetcode-cn.com/Figures/236/236_LCA_Binary_10.png)

+ ```java
  const {BST} = require('./utils/structure/BST')
  const bst = new BST()
  let p = bst.insert(1)
  bst.insert(2)
  let q = bst.insert(3)
  bst.insert(4)
  bst.insert(5)
  bst.insert(6)
  bst.insert(7)
  
  var lowestCommonAncestor = function(root, p, q) {
    let res = null
    recursion(root,p,q)
    return res
    function recursion(root,p,q){
      if(root===null) return false
  
      let mid= (root===p || root === q)? 1 : 0
      let left = recursion(root.left,p,q)? 1 : 0
      let right = recursion(root.right,p,q)? 1 : 0
    
      if(mid + left + right >= 2) res = root
      return mid + left + right > 0
    }
  }
  
  console.log(lowestCommonAncestor(bst.root(),p,q))
  ```
+ 问题在于已经找到了最近的公共祖先之后，还有很多多余的遍历，无法中断遍历

**复杂度分析**

+ 时间复杂度：O(N)，N 是二叉树中的节点数，最坏情况下，我们需要访问二叉树的所有节点。
+ 空间复杂度：O(N)，这是因为递归堆栈使用的最大空间位 N,斜二叉树的高度可以是 N。

### 迭代

+ 在前面的方法中，我们在回溯过程中遇到 LCA。我们可以摆脱回溯过程本身。在这种方法中，我们总是有一个指向可能 LCA 的指针，当我们找到两个节点时，我们返回指针作为答案。
+ 算法：

  1. 从根节点开始。
  2. 将 ` (root, root_state) ` 放在堆栈上。`root_state` 定义要遍历该节点的一个子节点还是两个子节点。
  3. 当堆栈不为空时，查看堆栈的顶部元素，该元素表示为 ` (parent_node, parent_state)`。
  4. 在遍历 `parent_node` 的任何子节点之前，我们检查 `parent_node` 本身是否是 p 或 q 中的一个。
  5. 当我们第一次找到 `p` 或 `q` 的时候，设置一个布尔标记，名为 `one_node_found` 为 true 。还可以通过在变量 `LCA_index` 中记录堆栈的顶部索引来跟踪最近的公共祖先。因为堆栈的所有当前元素都是我们刚刚发现的节点的祖先。
  6. 第二次 `parent_node == p or parent_node == q` 意味着我们找到了两个节点，我们可以返回 `LCA node`。
  7. 每当我们访问 `parent_node` 的子节点时，我们将 `(parent_node, updated_parent_state) ` 推到堆栈上。我们更新父级的状态为子级/分支已被访问/处理，并且相应地更改状态。
  8. 当状态变为 `BOTH_DONE` 时，最终会从堆栈中弹出一个节点，这意味着左、右子树都被推到堆栈上并进行处理。
     1. 如果 `one_node_found` 是 true 的，那么我们需要检查被弹出的顶部节点是否可能是找到的节点的祖先之一。
     2. 在这种情况下，我们需要将 `LCA_index` 减少一个。因为其中一位祖先被弹出了。 当同时找到 `p` 和 `q` 时，LCA_index 将指向堆栈中包含 `p` 和 `q ` 之间所有公共祖先的索引。并且 `LCA_index` 元素具有 `p` 和 `q` 之间的最近公共祖先。

+ ```java
  import javafx.util.*;
  
  class Solution {
  
    // Three static flags to keep track of post-order traversal.
  
    // Both left and right traversal pending for a node.
    // Indicates the nodes children are yet to be traversed.
    private static int BOTH_PENDING = 2;
  
    // Left traversal done.
    private static int LEFT_DONE = 1;
  
    // Both left and right traversal done for a node.
    // Indicates the node can be popped off the stack.
    private static int BOTH_DONE = 0;
  
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
  
      Stack<Pair<TreeNode, Integer>> stack = new Stack<Pair<TreeNode, Integer>>();
  
      // Initialize the stack with the root node.
      stack.push(new Pair<TreeNode, Integer>(root, Solution.BOTH_PENDING));
  
      // This flag is set when either one of p or q is found.
      boolean one_node_found = false;
  
      // This is used to keep track of the LCA.
      TreeNode LCA = null;
  
      // Child node
      TreeNode child_node = null;
  
      // We do a post order traversal of the binary tree using stack
      while (!stack.isEmpty()) {
  
        Pair<TreeNode, Integer> top = stack.peek();
        TreeNode parent_node = top.getKey();
        int parent_state = top.getValue();
  
        // If the parent_state is not equal to BOTH_DONE,
        // this means the parent_node can't be popped off yet.
        if (parent_state != Solution.BOTH_DONE) {
  
          // If both child traversals are pending
          if (parent_state == Solution.BOTH_PENDING) {
  
            // Check if the current parent_node is either p or q.
            if (parent_node == p || parent_node == q) {
  
              // If one_node_found was set already, this means we have found
              // both the nodes.
              if (one_node_found) {
                return LCA;
              } else {
                // Otherwise, set one_node_found to True,
                // to mark one of p and q is found.
                one_node_found = true;
  
                // Save the current top element of stack as the LCA.
                LCA = stack.peek().getKey();
              }
            }
  
            // If both pending, traverse the left child first
            child_node = parent_node.left;
          } else {
            // traverse right child
            child_node = parent_node.right;
          }
  
          // Update the node state at the top of the stack
          // Since we have visited one more child.
          stack.pop();
          stack.push(new Pair<TreeNode, Integer>(parent_node, parent_state - 1));
  
          // Add the child node to the stack for traversal.
          if (child_node != null) {
            stack.push(new Pair<TreeNode, Integer>(child_node, Solution.BOTH_PENDING));
          }
        } else {
  
          // If the parent_state of the node is both done,
          // the top node could be popped off the stack.
          // Update the LCA node to be the next top node.
          if (LCA == stack.pop().getKey() && one_node_found) {
            LCA = stack.peek().getKey();
          }
  
        }
      }
      return null;
    }
  }
  ```

  ```js
  var lowestCommonAncestor = function(root, p, q) {
    const BOTH_PENDING = 2,
          LEFT_DONE = 1,
          BOTH_DONE = 0
    const stack = new Stack()
    // Initialize the stack with the root node.
    stack.push([root,BOTH_PENDING])
    let oneNodeFound = false, // This flag is set when either one of p or q is found.
        LCA = null, // This is used to keep track of the LCA.
        childNode = null
    // We do a post order traversal of the binary tree using stack
    while(!stack.isEmpty()){
      const top = stack.pop()
      const parentNode = top[0],
            parentState = top[1]
    }
  }
  ```

## [543. 二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

### 题目

+ 给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过根结点。

  ```
            1
           / \
          2   3
         / \     
        4   5    
  返回 3, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。
  注意：两结点之间的路径长度是以它们之间边的数目表示。
  ```

### 分析

+ 等于左边的深度，加上右子树的深度，没有说是平衡树，不一定经过根节点
+ 遍历每一个节点，经过该节点的最大直径，就是该节点左子树的深度加上右子树的深度

  ```js
  var diameterOfBinaryTree = function(root) {
    if(root===null) return 0
    let max = 0
    dfs(root)
    return max
    
    function dfs(node){
      if(node===null) return 0
      const leftDepth = dfs(node.left),
            rightDepth = dfs(node.right)
      const depth = Math.max(leftDepth,rightDepth)+1
      max = Math.max(leftDepth+rightDepth,max)
      return depth
    }
  }
  ```

# 总结

- 树的前序后序中序其实都是深度优先遍历的一种，深度优先遍历可以通过栈的形式从递归转换为迭代
- 树的层次遍历就是广度优先遍历，通过队列实现
- 因为 JS 没有自带的工具库，实现迭代所需的栈结构

# 字典树

- 字典树，也叫“前缀树”，是一种树形结构，在解决字符串相关问题中非常高效。其提供非常快速的检索功能，常用于搜索字典中的单词，为搜索引擎提供自动搜索建议，甚至能用于 IP 路由选择。
- 下面展示了“top”“thus”和“their”这三个词是如何存储在字典树中的：
- 这些单词以从上到下的方式存储，其中绿色节点“p”，“s”和“r”分别表示“top”，“thus”和“their”的末尾

**常见的字典树面试问题：**

- 计算字典树中的总字数
- 打印存储在字典树中的所有单词
- 使用字典树对数组的元素进行排序
- 使用字典树从字典中形成单词
- 构建一个 T9 字典
