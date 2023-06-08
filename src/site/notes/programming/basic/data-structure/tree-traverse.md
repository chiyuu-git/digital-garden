---
{"dg-publish":true,"permalink":"/programming/basic/data-structure/tree-traverse/"}
---


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

![1554605385643](/img/user/programming/basic/data-structure/tree-traverse/1554605385643.png)

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

![1554605791244](/img/user/programming/basic/data-structure/tree-traverse/1554605791244.png)

### N 叉树的前序遍历

```js
var preorder = function(root) {
    const res = [];
    helper(root, res);
    return res;
}

const helper = (root, res) => {
    if (root === null) {
        return;
    }
    res.push(root.val);
    for (const ch of root.children) {
        helper(ch, res);
    }
};
```

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

![1554605962009](/img/user/programming/basic/data-structure/tree-traverse/1554605962009.png)

## 总结

前中后指的是 callback 的执行位置

中序遍历，根节点，在中间出现

中序遍历的一种应用就是对树进行**排序**操作

先序遍历，根节点，最先出现

先序遍历的一种应用是打印一个结构化的文档。

后序遍历，根节点，最后出现

1. 确定终止条件
2. 确定单层递归的逻辑
3. 需要用到什么参数就补充什么参数, 再确定返回值即可

## 前中后序遍历的迭代形式

本质上来说, 递归形式是利用了系统的栈, 改成迭代形式的话, 只要我们自己实现了和系统一样的栈就可以了. 其他逻辑都是相同的

遍历的节点和处理的节点是两个不同的逻辑, 在前序遍历遍历中, 遍历的节点和处理的节点是同一个, 所以处理起来会比较容易.

但是中序遍历和后序遍历中, 要区别开来, 实现就有难度了

### 先序遍历的迭代形式

相对的, 下面这种做法其实是先序遍历的迭代形式:

因为数组的性能原因, shift 操作是比较耗时的, 最好是能通过 push 和 pop 来遍历所有的后代节点.

但是这样的话就会后进的先遍历, 相当于是左右节点互换了一下位置? 这样的话再让 left 和 right 的顺序换一换就可以正常顺序展示了, 性能更好

```js
var countNodes = function(node) {
    const stack = [node];
    let count = 0;

    while (stack.length > 0) {
        const n = stack.pop();

        if (n !== null) {
            console.log('n: ', n.key);
            count++;
			// left 和 right 顺序调换, 以使用 pop 方法遍历, 相当于是用栈
            stack.push(n.right);
            stack.push(n.left);
        }
    }

    return count;
};
```

### 中序遍历的迭代形式

因为访问的顺序和处理的顺序是不一样的, 我们需要一个指针来记录当前需要处理的节点

栈用来模拟遍历的顺序

查看动图: [二叉树的中序遍历 - 二叉树的中序遍历 - 力扣（LeetCode）](https://leetcode.cn/problems/binary-tree-inorder-traversal/solution/er-cha-shu-de-zhong-xu-bian-li-by-leetcode-solutio/)

```js
var inorderTraversal = function(root) {
    const res = [];
    const stack = [];
    let cur = root;
    while (cur || stack.length) {
        // 一直访问左子节点, 直到左子节点为空, 就到达了需要处理的元素
        while (cur) {
            stack.push(cur);
            cur = cur.left;
        }
        cur = stack.pop();
        res.push(cur.val);
        // 然后访问右子节点, 如果不为空那么下一轮遍历中就会继续访问其左子节点
        // 若为空, 则再次弹出 stack 中元素进行处理, 此时即是: 左子节点处理完了, 右子节点为空, 那么就回去处理父节点了
        cur = cur.right;
    }
    return res;
};
```

### 后序遍历的迭代形式

```js
var postorderTraversal = function(root) {
    const res = [];
    const stack = [];
    let cur = root;
    // 用于标记右子树已经访问过了, 这样才能从避免无限迭代右子树
    let prev = null;
    while (cur || stack.length) {
        while (cur !== null) {
            stack.push(cur);
            cur = cur.left;
        }
        cur = stack.pop();
        if (cur.right === null || cur.right === prev) {
            res.push(cur.val);
            prev = cur;
            // next loop will pop
            cur = null;
        }
        else {
            stack.push(cur);
            cur = cur.right;
        }
    }

    return res;
};
```

#### 后序遍历 N 叉树

```js
var postorder = function(root) {
    const res = [];
    if (root == null) {
        return res;
    }
    const map = new Map();
    const stack = [];
    let node = root;
    while (stack.length || node) {
        while (node) {
            stack.push(node);
            const children = node.children;
            if (children && children.length > 0) {
                map.set(node, 0);
                node = children[0];
            } else {
                node = null;
            }
        }
        node = stack[stack.length - 1];
        const index = (map.get(node) || 0) + 1;
        const children = node.children;
        if (children && children.length > index) {
            map.set(node, index);
            node = children[index];
        } else {
            res.push(node.val);
            stack.pop();
            map.delete(node);
            node = null;
        }
    }
    return res;
};
```

## Morris 遍历

不是一定要掌握的

无论是递归还是迭代形式, 因为有系统栈的存在, 所以空间复杂度都是 On

在 mirros 遍历中不使用栈, 而是使用空闲指针来保存回溯的线索, 从而达到 O(1) 的空间复杂度

貌似和二叉树的线索化是相同的, link 一下 线索二叉树

当他的 mostRight 指向 null 时，就是第一次访问，当他的 mostRight 指向 cur 时，就是第二次访问。

[神级遍历——morris - 知乎](https://zhuanlan.zhihu.com/p/101321696)

[【数据结构与算法】Morris遍历详解 - 掘金](https://juejin.cn/post/7021341254457753631)

[经典算法小评(2)——Morris树遍历算法](https://ghh3809.github.io/2018/08/06/morris-traversal/)

[Morris Traversal方法遍历二叉树（非递归，不用栈，O(1)空间） - AnnieKim - 博客园](https://www.cnblogs.com/anniekim/archive/2013/06/15/morristraversal.html)

[Morris遍历（值得学习的二叉树遍历方法）\_Lazy mode的博客-CSDN博客](https://blog.csdn.net/Codeoh/article/details/108738591)

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

往队列添加所有后继节点, 要实现真正的层次遍历, 必须使用 shift 和 pop 的组合, 这样才是一层层的遍历.

# DFS Vs BFS

- 树的前序后序中序其实都是深度优先遍历的一种，深度优先遍历可以通过栈的形式从递归转换为迭代
- 树的层次遍历就是广度优先遍历，通过队列实现
- 因为 JS 没有自带的工具库，实现迭代所需的栈结构

DFS 可以传参数, 利用函数的调用栈节省空间复杂度, 如果是 sum 类型的, 可以 return 0,

BFS 不能传参数, 只能传队列, 一个存储节点正常走遍历, 另一个存储节点对应题意所需的信息, 当然可以用一个对象把节点和信息都囊括在一个队列当中

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

## 199. 二叉树的右视图

[199. 二叉树的右视图](../leetcode/199.%20二叉树的右视图.md)

## 116. 填充每个节点的下一个右侧节点指针

[116. 填充每个节点的下一个右侧节点指针](../leetcode/116.%20填充每个节点的下一个右侧节点指针.md)

## 117. 填充每个节点的下一个右侧节点指针 II

![117. 填充每个节点的下一个右侧节点指针 II](tree-traverse/117. 填充每个节点的下一个右侧节点指针 II.md)

# print()

## DFS

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

## BFS

层次遍历如何实现 print 函数呢? 层次遍历如何标记深度呢?

```js
var levelOrder = function(root) {
    const ret = [];
    if (!root) {
        return ret;
    }

    const q = [];
    q.push(root);
    while (q.length !== 0) {
        const currentLevelSize = q.length;
        ret.push([]);
		// 每次遍历一层的节点
        for (let i = 1; i <= currentLevelSize; ++i) {
            const node = q.shift();
            ret[ret.length - 1].push(node.val);
            if (node.left) q.push(node.left);
            if (node.right) q.push(node.right);
        }
    }
        
    return ret;
};
```

## printPath

![257. 二叉树的所有路径](tree-traverse/257. 二叉树的所有路径.md)

# serialize()

[297. 二叉树的序列化与反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)

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

![image.png](/img/user/programming/basic/data-structure/tree-traverse/22aa4f1f4325669c02d898729d72d5cd56cb47ea00d82528d4df15239ed46c35-image.png)

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

# 基础

## 二叉树相等

![100. 相同的树](tree-traverse/100. 相同的树.md)

## 对称二叉树

![101. 对称二叉树](tree-traverse/101. 对称二叉树.md)

## 翻转二叉树

![226. 翻转二叉树](tree-traverse/226. 翻转二叉树.md)

## 617. 合并二叉树

![617. 合并二叉树](tree-traverse/617. 合并二叉树.md)

## 543. 二叉树的直径

[543. 二叉树的直径 - 力扣（LeetCode）](https://leetcode.cn/problems/diameter-of-binary-tree/)

# 进阶

## 508. 出现次数最多的子树元素和

[508. 出现次数最多的子树元素和](../leetcode/508.%20出现次数最多的子树元素和.md)

# 遍历路径

更一般的特性是由父节点传递信息给子节点, 这样就可以和 depth 类型统一起来了

大一统理论 / 万有理论

## 257. 二叉树的所有路径

[257. 二叉树的所有路径](../leetcode/257.%20二叉树的所有路径.md)

## 112. 路径总和

[112. 路径总和](../leetcode/112.%20路径总和.md)

## 113. 路径总和 II

[113. 路径总和 II](../leetcode/113.%20路径总和%20II.md)

## 437. 路径总和 III

[437. 路径总和 III](../leetcode/437.%20路径总和%20III.md)

## 129. 求根节点到叶节点数字之和

[129. 求根节点到叶节点数字之和](../leetcode/129.%20求根节点到叶节点数字之和.md)

# 根据遍历确定树

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
