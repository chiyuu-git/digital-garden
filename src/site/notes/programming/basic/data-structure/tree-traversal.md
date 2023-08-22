---
{"dg-publish":true,"permalink":"/programming/basic/data-structure/tree-traversal/"}
---


# 深度优先遍历

遍历一棵树是指访问树的每个节点并对它们进行某种操作的过程。但是我们应该怎么去做呢？

应该从树的顶端还是底端开始呢？从左开始还是从右开始呢？

访问树的所有节点有三种方式：中序、先序 (前序) 和后序。

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

![1554605385643](/img/user/programming/basic/data-structure/tree-traversal/1554605385643.png)

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

![1554605791244](/img/user/programming/basic/data-structure/tree-traversal/1554605791244.png)

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

![1554605962009](/img/user/programming/basic/data-structure/tree-traversal/1554605962009.png)

## 前中后序递归总结

前中后指的是 callback 的执行位置

中序遍历，根节点，在中间出现

中序遍历的一种应用就是对 BTS 进行**排序**操作

先序遍历，根节点，最先出现

先序遍历的一种应用是打印一个结构化的文档。

后序遍历，根节点，最后出现

递归的一般思路:

1. 确定终止条件
2. 确定单层递归的逻辑
3. 需要用到什么参数就补充什么参数, 再确定返回值即可

## 前中后序遍历的迭代形式 @@@

本质上来说, 递归形式是利用了系统的栈, 改成迭代形式的话, 只要我们自己实现了和系统一样的栈就可以了. 其他逻辑都是相同的

遍历的节点和处理的节点是两个不同的逻辑, 在前序遍历中, 遍历的节点和处理的节点是同一个, 所以处理起来会比较容易.

但是中序遍历和后序遍历中, 要区别开来, 实现就有难度了

掌握前序和中序的迭代形式即可, 后序不用太在意, 实在不行就用前序 reverse 处理

迭代形式的一个好处是可以提前结束迭代, 不用递归完全部的节点

### 先序遍历的迭代形式

因为数组的性能原因, shift 操作是比较耗时的, 最好是能通过 push 和 pop 来遍历所有的后代节点.

但是这样的话就会后进的先遍历, 相当于是左右节点互换了一下位置? 这样的话再让 left 和 right 的顺序换一换就可以正常顺序展示了, 性能更好

下面这种做法其实是先序遍历的迭代形式, 但是长得与层次遍历非常像

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

因为访问的顺序和处理的顺序是不一样的

我们需要一个额外的指针来记录当前的节点, 该指针代表着遍历的顺序

栈表示的是要处理的节点的顺序

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
		// 因为 cur 会一直找到子树最左叶子节点, 所以 cur 一定会走如下的流程:
		// 1. cur 是最左叶子节点, 处理完毕
		// 2. 最左叶子节点的 cur.right 一定为 null, cur 回退到 mid, 左中都处理完了
		// 3. 然后 中.right 如果为空, 那么将 左中是为一个整体, 这两组成的最左子树已经遍历完了, 再次回退到上一个 mid
		// 4. 如果 中.right 不为空, 那么就对右子树重复 1 - 4 的流程
        cur = cur.right;
    }
    return res;
};
```

也可以用 prev 思路, 其实更好理解一点, cur.left === prev, 那么就该访问 right 了

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
			// 右子节点为 null, cur 一定是左叶子节点, 直接处理
			// 右子节点为 prev, 右子树也已经处理完了, 避免重复递归
            res.push(cur.val);
            prev = cur;
            // next loop will pop
            cur = null;
        }
        else {
			// 右子树没有处理, 再次入栈
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

[【数据结构与算法】Morris遍历详解 - 掘金](https://juejin.cn/post/7021341254457753631)

### 前驱节点

指的是前中后序任意一种遍历顺序中, 当前节点的前一个遍历的节点.

对于中序遍历来说, 左中右, 左就是中的前躯节点.

对于一个复杂的二叉树来说, 中的前躯节点是左子树的最右子节点

morris 遍历的最大特征就是通过找到前躯节点, 让前驱节点的空闲指针指向后驱节点, 以此来省略调用栈带来的空间复杂度.

但是寻找前驱节点的过程会造成时间复杂度的上升, 但是总体还是 On 复杂度

### 中序遍历

我们在中序遍历的时候，一定先遍历左子树，然后遍历当前节点，最后遍历右子树。

在常规方法中，我们用递归回溯或者是栈来保证遍历完左子树可以再回到当前节点，但这需要我们付出额外的空间代价。

我们需要用一种巧妙地方法可以在 O(1) 的空间下，**遍历完左子树可以再回到当前节点**。我们希望当前的节点在遍历完当前点的前驱之后被遍历，我们可以考虑修改它的前驱节点的 right 指针。当前节点的前驱节点的 right 指针可能本来就指向当前节点（前驱是当前节点的父节点），也可能是当前节点左子树最右下的节点。如果是后者，我们希望遍历完这个前驱节点之后再回到当前节点，可以将它的 right 指针指向当前节点。

Morris 中序遍历的一个重要步骤就是寻找当前节点的前驱节点，并且 Morris 中序遍历寻找下一个点始终是通过转移到 right 指针指向的位置来完成的。

+ 如果当前节点没有左子树，则遍历这个点，然后跳转到当前节点的右子树。
+ 如果当前节点有左子树，那么它的前驱节点一定在左子树上，我们可以在左子树上一直向右行走，找到当前点的前驱节点。
  + 如果前驱节点没有右子树，就将前驱节点的 right 指针指向当前节点。这一步是为了在遍历完前驱节点后能找到前驱节点的后继，也就是当前节点。
  + 如果前驱节点的右子树为当前节点，说明前驱节点已经被遍历过并被修改了 right 指针，这个时候我们重新将前驱的右孩子设置为空，遍历当前的点，然后跳转到当前节点的右子树。

因此我们可以得到这样的代码框架:

```js
var morris = function(root) {
    let cur = root
    // 用于记录前驱节点, 想要与 previous 区分开来, 用的 front
    let front = null;
    while (cur !== null) {
        if (cur.left === null) {
			// 遍历 cur, 遍历的是叶子节点, 并且让 cur 回到后驱节点去了
            console.log(cur.val);
            cur = cur.right;
            continue;
        }
        front = cur.left;
        // 找到前驱节点
        while (front.right !== null && front.right !== cur) {
            front = front.right;
        }
        if (front.right === null) {
            // 绑定后驱节点
            front.right = cur;
            cur = cur.left;
        } else {
            front.right = null;
            // 遍历 cur, 遍历的是左中右的中, 同时让 cur 继续处理右子树
            console.log(cur.val);
            cur = cur.right;
        }
    }
    return answer;
};
```

```js
var findMode = function(root) {
    let base = 0, count = 0, maxCount = 0;
    let answer = [];

    const update = (x) => {
        if (x === base) {
            ++count;
        } else {
            count = 1;
            base = x;
        }
        if (count === maxCount) {
            answer.push(base);
        }
        if (count > maxCount) {
            maxCount = count;
            answer = [base];
        }
    }

    let cur = root, pre = null;
    while (cur !== null) {
        if (cur.left === null) {
            update(cur.val);
            cur = cur.right;
            continue;
        }
        pre = cur.left;
        while (pre.right !== null && pre.right !== cur) {
            pre = pre.right;
        }
        if (pre.right === null) {
            pre.right = cur;
            cur = cur.left;
        } else {
            pre.right = null;
            update(cur.val);
            cur = cur.right;
        }
    }
    return answer;
};
```

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

    if (node) {
	    res.push(node.val);
	    queue.push(node.left);
	    queue.push(node.right);
    }
    else {
	    res.push(null)
    }
  }
  return res;
};
```

往队列添加所有后继节点, 要实现真正的层次遍历, 必须使用 shift 和 pop 的组合, 这样才是一层层的遍历.

[N叉树的层次遍历](https://leetcode.cn/problems/n-ary-tree-level-order-traversal/)

[690. 员工的重要性](../leetcode/690.%20员工的重要性.md)

# DFS Vs BFS

树的前序后序中序其实都是深度优先遍历的一种，深度优先遍历可以通过栈的形式从递归转换为迭代

树的层次遍历就是广度优先遍历，通过队列实现

## 如何传递额外信息

DFS 可以传参数, 利用函数的调用栈节省空间复杂度, 如果是 sum 类型的, 可以 return 0,

BFS 不能传参数, 只能传队列, 一个存储节点正常走遍历, 另一个存储节点对应题意所需的信息, 当然可以用一个对象把节点和信息都囊括在一个队列当中

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

## 常问的树面试问题

找到一个二叉树的高度

找到一个二叉搜索树中第 k 个最大值

找到距离根部“k”个距离的节点

找到一个二叉树中给定节点的祖先（ancestors）

# 同时遍历两棵树

## 二叉树相等

![100. 相同的树](../leetcode/100.%20相同的树.md)

## 对称二叉树

[101. 对称二叉树](../leetcode/101.%20对称二叉树.md)

## 合并二叉树

[617. 合并二叉树](../leetcode/617.%20合并二叉树.md)

# depth()

传递信息的代表操作, 传递深度信息 depth

## DFS

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

## BFS

直接一行行的遍历, 本身就是与深度强绑定的关系

# print()

depth 的延伸应用, 将相同 depth 的 node 放在一起即可

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

# Linear 遍历

横着来, 竖着来, 反正都是线性的结构

以下题目都是通过传递深度信息从而确定当前节点所在的位置

## 确定当前行

depth 本身就是和行强绑定的

[623. 在二叉树中增加一行](../leetcode/623.%20在二叉树中增加一行.md)

## 确认所在行的前一个或者后一个节点

通过 depth, 可以找到与自己在同一行的所有节点

[199. 二叉树的右视图](../leetcode/199.%20二叉树的右视图.md)

[116. 填充每个节点的下一个右侧节点指针](../leetcode/116.%20填充每个节点的下一个右侧节点指针.md)

[117. 填充每个节点的下一个右侧节点指针 II](../leetcode/117.%20填充每个节点的下一个右侧节点指针%20II.md)

# Linear/path

更一般的特性是由父节点传递信息给子节点, 这样就可以和 Linear 类型统一起来了

> 大一统理论 / 万有理论

## printPath

之前传递的都是 depth 信息, 这次传递的是前序节点拼接而成的 path

![257. 二叉树的所有路径](../leetcode/257.%20二叉树的所有路径.md)

## 相关题目

有两种, 一个是 linear path, 就是从根节点出发到叶节点这种

一种是 sub-tree 类型的 path, 可以从最左节点一直到最有节点, 需要分开理解

| File                                                                   | difficulty | etags                                                                                                                                                                          | date-created               |
| ---------------------------------------------------------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------- |
| [[programming/basic/leetcode/113. 路径总和 II\|113. 路径总和 II]]           | medium     | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/linear</li></ul>                                                                                        | 2023-06-06-Tue, 3:43:29 pm |
| [[programming/basic/leetcode/129. 求根节点到叶节点数字之和\|129. 求根节点到叶节点数字之和]] | medium     | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/linear</li><li>#leetcode/unsolved</li></ul>                                                             | 2023-06-06-Tue, 3:59:26 pm |
| [[programming/basic/leetcode/112. 路径总和\|112. 路径总和]]                 | easy       | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/linear</li></ul>                                                                                        | 2023-06-06-Tue, 3:39:50 pm |
| [[programming/basic/leetcode/257. 二叉树的所有路径\|257. 二叉树的所有路径]]         | easy       | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/linear</li></ul>                                                                                        | 2023-06-06-Tue, 2:26:33 pm |
| [[programming/basic/leetcode/437. 路径总和 III\|437. 路径总和 III]]         | medium     | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/linear</li><li>#leetcode/prefix-sum</li><li>#leetcode/unsolved</li><li>#leetcode/backtracking</li></ul> | 2023-06-03-Sat, 5:12:15 pm |

{ .block-language-dataview}

# Sub Tree 遍历

这种有点像 reduce 的操作, 说不太清楚

return 类型的

感觉也是 depth 类型的, 不过是反过来, 从叶节点开始给根节点传送信息

如果是单调路径, 那么可以传递 sum 累加, 但是这题是计算子树的, 所以不是 depth 类型的传递信息, 而是汇总信息的类型

要说的话, 基础遍历里的 翻转二叉树反而是比较接近这种结构的

主体是子树, 相对的之前的遍历主体是单个节点 / 单条路径

遍历子树, 可以理解为遍历单个节点, 通过递归把信息汇总到左右子树, 从而递归子树

其实 depth 方法自身也是这种类型的, 现在比较麻烦了, 就是感觉两个不同类型的遍历形式因为 depth 一个单词耦合在了一起, 不是很好区分.

path 类型的, 虽然不依赖子树, 但是依赖 depth

依赖子树做遍历的典型又是 depth()

没有办法, 只好把依赖传递信息的类型成为 linear 类型, 依赖子树的就叫做 sub-tree 类型

sub-tree 类型, 往往不好使用层次遍历?

## depth()

![depth()](tree-traversal.md#depth())

## 翻转二叉树

不断的交换左右子树即可

[226. 翻转二叉树](../leetcode/226.%20翻转二叉树.md)

## 相关题目

| File                                                                         | difficulty | etags                                                                                                                                  | date-created                |
| ---------------------------------------------------------------------------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| [[programming/basic/leetcode/105. 从前序与中序遍历序列构造二叉树\|105. 从前序与中序遍历序列构造二叉树]] | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/divide-and-conquer</li><li>#leetcode/unsolved</li></ul>                    | 2023-06-12-Mon, 2:25:56 pm  |
| [[programming/basic/leetcode/106. 从中序与后序遍历序列构造二叉树\|106. 从中序与后序遍历序列构造二叉树]] | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/divide-and-conquer</li><li>#leetcode/unsolved</li></ul>                    | 2023-06-13-Tue, 10:14:12 am |
| [[programming/basic/leetcode/226. 翻转二叉树\|226. 翻转二叉树]]                     | easy       | <ul><li>#leetcode/tree/traversal/sub-tree</li></ul>                                                                                    | 2023-06-03-Sat, 12:57:46 pm |
| [[programming/basic/leetcode/222. 完全二叉树的节点个数\|222. 完全二叉树的节点个数]]           | easy       | <ul><li>#leetcode/tree/traversal/linear</li><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/tree/complete</li></ul>            | 2023-06-02-Fri, 5:08:35 pm  |
| [[programming/basic/leetcode/236. 二叉树的最近公共祖先\|236. 二叉树的最近公共祖先]]           | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/tree/ansector</li><li>#leetcode/unsolved</li></ul>                         | 2023-06-15-Thu, 7:20:18 pm  |
| [[programming/basic/leetcode/337. 打家劫舍 III\|337. 打家劫舍 III]]               | medium     | <ul><li>#leetcode/dp/iteration</li><li>#leetcode/recursive/memo</li><li>#leetcode/tree/traversal/sub-tree</li></ul>                    | 2023-07-14-Fri, 11:53:56 am |
| [[programming/basic/leetcode/508. 出现次数最多的子树元素和\|508. 出现次数最多的子树元素和]]       | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/hash-table/count</li></ul>                                                 | 2023-06-03-Sat, 8:04:36 pm  |
| [[programming/basic/leetcode/543. 二叉树的直径\|543. 二叉树的直径]]                   | easy       | <ul><li>#leetcode/tree/traversal/sub-tree</li></ul>                                                                                    | 2023-06-08-Thu, 2:58:58 pm  |
| [[programming/basic/leetcode/572. 另一棵树的子树\|572. 另一棵树的子树]]                 | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/kmp</li><li>#leetcode/sub/consecutive</li><li>#leetcode/unsolved</li></ul> | 2023-06-03-Sat, 8:42:12 pm  |
| [[programming/basic/leetcode/617. 合并二叉树\|617. 合并二叉树]]                     | easy       | <ul><li>#leetcode/tree/traversal/sub-tree</li></ul>                                                                                    | 2023-06-03-Sat, 7:29:10 pm  |
| [[programming/basic/leetcode/654. 最大二叉树\|654. 最大二叉树]]                     | medium     | <ul><li>#leetcode/tree/traversal/sub-tree</li><li>#leetcode/stack/monotonic-stack</li><li>#leetcode/unsolved</li></ul>                 | 2023-06-03-Sat, 10:12:52 pm |
| [[programming/basic/leetcode/687. 最长同值路径\|687. 最长同值路径]]                   | medium     | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/sub-tree</li></ul>                                              | 2023-06-04-Sun, 3:01:09 pm  |
| [[programming/basic/leetcode/700. 二叉搜索树中的搜索\|700. 二叉搜索树中的搜索]]             | easy       | <ul><li>#leetcode/tree/bst</li><li>#leetcode/tree/traversal/sub-tree</li></ul>                                                         | 2023-06-09-Fri, 5:36:56 pm  |
| [[programming/basic/leetcode/968. 监控二叉树\|968. 监控二叉树]]                     | hard       | <ul><li>#leetcode/greedy-algorithm</li><li>#leetcode/dp</li><li>#leetcode/unsolved</li><li>#leetcode/tree/traversal/sub-tree</li></ul> | 2023-07-09-Sun, 8:14:47 pm  |

{ .block-language-dataview}

# Sub Tree/path

| File                                                       | difficulty | etags                                                                                     | date-created               |
| ---------------------------------------------------------- | ---------- | ----------------------------------------------------------------------------------------- | -------------------------- |
| [[programming/basic/leetcode/687. 最长同值路径\|687. 最长同值路径]] | medium     | <ul><li>#leetcode/tree/traversal/path</li><li>#leetcode/tree/traversal/sub-tree</li></ul> | 2023-06-04-Sun, 3:01:09 pm |

{ .block-language-dataview}

# 综合遍历

同时具有线性遍历和子树遍历的特征

[222. 完全二叉树的节点个数](../leetcode/222.%20完全二叉树的节点个数.md)

# 根据遍历确定树

[105. 从前序与中序遍历序列构造二叉树](../leetcode/105.%20从前序与中序遍历序列构造二叉树.md)

# serialize()

序列化的两种格式:

1. `1,2,3,4,null,2,4,null,null,4`
2. `1(2(4()())())(3(2(4()())())(4()()))`

## 前序序列化

| File                                                                   | difficulty | etags                                                                                                                                | date-created                |
| ---------------------------------------------------------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------ | --------------------------- |
| [[programming/basic/leetcode/297. 二叉树的序列化与反序列化\|297. 二叉树的序列化与反序列化]] | hard       | <ul><li>#leetcode/tree/serialize</li><li>#leetcode/unsolved</li></ul>                                                                | 2023-06-08-Thu, 7:16:40 pm  |
| [[programming/basic/leetcode/331. 验证二叉树的前序序列化\|331. 验证二叉树的前序序列化]]   | medium     | <ul><li>#leetcode/tree/serialize</li><li>#leetcode/graph/degree</li><li>#leetcode/unsolved</li></ul>                                 | 2023-06-08-Thu, 8:39:33 pm  |
| [[programming/basic/leetcode/606. 根据二叉树创建字符串\|606. 根据二叉树创建字符串]]     | easy       | <ul><li>#leetcode/tree/serialize</li></ul>                                                                                           | 2023-06-08-Thu, 7:27:39 pm  |
| [[programming/basic/leetcode/652. 寻找重复的子树\|652. 寻找重复的子树]]           | medium     | <ul><li>#leetcode/tree/serialize</li><li>#leetcode/hash-table</li><li>#leetcode/sub/consecutive</li><li>#leetcode/unsolved</li></ul> | 2023-06-09-Fri, 10:16:18 am |

{ .block-language-dataview}
