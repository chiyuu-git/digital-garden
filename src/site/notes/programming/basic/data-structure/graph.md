---
{"dg-publish":true,"permalink":"/programming/basic/data-structure/graph/"}
---


# 概述

图是网络结构的抽象模型。图是一组由边连接的节点（或顶点）。学习图是重要的，因为任何二元关系都可以用图来表示。

任何社交网络，例如 Facebook、Twitter 和 Google plus，都可以用图来表示。

图就是一组节点，以网络的形式互相连接。节点也被称为**顶点**（vertices）。一对（v,u）就叫做一个**边**，表示顶点 v 和顶点 u 相连。一个边可能包含**权重/成本**，显示从顶点 v 到 u 所需的成本

> 常用小写字母 v 和 u 代表一对相连的顶点，（v,u）代表连接他们的边

我们还可以使用图来表示道路、航班以及通信状态，如下图所示：

![1554636075580](/img/user/programming/basic/data-structure/graph/1554636075580.png)

一个图 G = (V, E) 由以下元素组成：

- V：一组顶点
- E：一组边，连接 V 中的顶点

图表示一个图：

![1554636133312](/img/user/programming/basic/data-structure/graph/1554636133312.png)

由一条边连接在一起的顶点称为**相邻顶点**。比如，A 和 B 是相邻的，A 和 D 是相邻的，A 和 C 是相邻的，A 和 E 不是相邻的。

一个顶点的**度**是其相邻顶点的数量。比如：

  - A 和其他三个顶点相连接，因此，A 的度为 3；
  - E 和其他两个顶点相连，因此，E 的度为 2。

**路径**是顶点 v1, v2,…,vk 的一个**连续序列**，其中 vi 和 vi+1 是相邻的。以上一示意图中的图为例，其中包含路径 A B E I 和 A C D G。

简单路径要求**不包含重复**的顶点。举个例子，A D G 是一条简单路径

**环**也是一个简单路径，称为**简单闭路径**，比如 A D C A（最后一个顶点重新回到 A）

如果图中不存在环，则称该图是**无环的**。如果图中每两个顶点间都存在路径，则该图是**连通的**

# 有向图和无向图

图可以是无向的（边没有方向）或是有向的（有向图）。如下图所示，有向图的边有一个方向：

![1554681804658](/img/user/programming/basic/data-structure/graph/1554681804658.png)

如果图中每两个顶点间在**双向**上都存在路径，则该图是**强连通**的。例如，C 和 D 是强连通的，

而 A 和 B 不是强连通的。

有向图中：**前溯点和后继点** @@@

> predecessor successor

无向图中：**前溯点和邻居结点** @@@

> predecessor neighbour

图还可以是未加权的（目前为止我们看到的图都是未加权的）或是**加权**的。如下图所示，加权图的边被赋予了权值：

![1554681885050](/img/user/programming/basic/data-structure/graph/1554681885050.png)

我们可以使用图来解决计算机科学世界中的很多问题，比如搜索图中的一个特定顶点或搜索一条特定边，寻找图中的一条路径（从一个顶点到另一个顶点），寻找两个顶点之间的最短路径，以及环检测。

# 图的表示

从数据结构的角度来说，我们有多种方式来表示图。在所有的表示法中，不存在绝对正确的方式。图的正确表示法取决于待解决的问题和图的类型。

## 邻接矩阵

图最常见的实现是邻接矩阵。每个节点都和一个整数相关联，该整数将作为数组的索引。我们用一个二维数组来表示顶点之间的连接。如果索引为 `i` 的节点和索引为 `j` 的节点相邻，则 `array[i][j] === 1，否则array[i][j] === 0`，如下图所示：

![1554682126164](/img/user/programming/basic/data-structure/graph/1554682126164.png)

### 缺点

不是**强连通**的图（稀疏图）如果用邻接矩阵来表示，则矩阵中将会有很多 0，这意味着我们浪费了计算机存储空间来表示根本不存在的边。

> 例如，找给定顶点的相邻顶点，即使该顶点只有一个相邻顶点，我们也不得不迭代一整行

邻接矩阵表示法不够好的另一个理由是，图中顶点的数量可能会改变，而 2 维数组不太灵活。

## 邻接表

我们也可以使用一种叫作邻接表（adjacency List）的**动态数据结构**来表示图。邻接表由图中每个顶点的相邻顶点列表所组成。存在好几种方式来表示这种数据结构。我们可以用列表（数组）、链表，甚至是散列表或是字典来表示相邻顶点列表。下面的示意图展示了邻接表数据结构。

  ![1554682276338](/img/user/programming/basic/data-structure/graph/1554682276338.png)

在邻接表中，顶点用键名来表示，边通过键名对应的字典中的元素来表示

## 对比

尽管邻接表可能对大多数问题来说都是更好的选择，但以上两种表示法都很有用，且它们有着不同的性质

1. 要找出顶点 v 和 w 是否相邻，使用邻接矩阵会比较快, 直接访问 `matrix[v][w]` 即可 ???

## 关联矩阵

我们还可以用关联矩阵来表示图。在关联矩阵中，**矩阵的行表示顶点，列表示边**。如下图所示，我们使用二维数组来表示两者之间的连通性，如果顶点 v 是边 e 的入射点，则 `array[v][e] === 1；否则，array[v][e] === 0`。

  ![1554682398659](/img/user/programming/basic/data-structure/graph/1554682398659.png)

关联矩阵通常用于边的数量比顶点多的情况下，以节省空间和内存。

# 图的实现

## Graph 类骨架

```js
  function Graph() {
    const vertices = []; //{1}
    const adjList = new Dictionary(); //{2}
  }
```

我们使用一个数组来存储图中所有顶点的名字（行{1}），以及一个字典（在第 7 章中已经实现）来存储**邻接表**（行{2}）。字典将会使用顶点的名字作为键，邻接顶点列表作为值。vertices 数组和 adjList 字典两者都是我们 Graph 类的私有属性。

## addVertex()

```js
  this.addVertex = function (v) {
    vertices.push(v); //{3}
    adjList.set(v, []); //{4}
  };
```

这个方法接受顶点 v 作为参数。我们将该顶点添加到顶点列表中（行{3}），并且在邻接表中，设置顶点 v 作为键对应的字典值为一个空数组（行{4}）

## addEdge()

```js
  this.addEdge = function (v, u) {
    // 添加从v到u的边
    adjList.get(v).push(u); //{5}
    // 添加从u到v的边
    adjList.get(u).push(v); //{6}
  };
```

这个方法接受两个顶点作为参数。首先，通过将 u 加入到 v 的邻接表中，我们添加了一条自顶点 v 到顶点 u 的边。

如果你想实现一个有向图，则行{5}就足够了。由于本章中大多数的例子都是基于无向图的，我们需要添加一条自 u 向 v 的边（行{6}）。

实现有向图和无向图，只有这个方法有区别 @@@

加权图如何实现呢？@@@ 添加边的同时添加权值

```js
  this.addEdge = function (v, u, w) {
    // 添加从v到u的边
    adjList.get(v).push([u, w]); //{5}
    // 添加从u到v的边
    adjList.get(u).push([v, w]); //{6}
  };
```

## toString()

```js
  this.toString = function () {
    let s = "";
    for (let i = 0; i < vertices.length; i++) {
      //{10}
      s += vertices[i] + " -> ";
      // 遍历该顶点的邻接表
      const neighbors = adjList.get(vertices[i]); //{11}
      for (let j = 0; j < neighbors.length; j++) {
        //{12}
        s += neighbors[j] + " ";
      }
      s += "\n"; //{13}
    }
    return s;
  };
```

我们为邻接表表示法构建了一个字符串。首先，迭代 vertices 数组列表（行{10}），将顶点的名字加入字符串中。接着，取得该顶点的邻接表（行{11}），同样也迭代该邻接表（行{12}），将相邻顶点加入我们的字符串。邻接表迭代完成后，给我们的字符串添加一个换行符（行{13}），这样就可以在控制台看到一个漂亮的输出了。运行如下代码：

```js
  var graph = new Graph();
  var myVertices = ["A", "B", "C", "D", "E", "F", "G", "H", "I"]; //{7}
  for (var i = 0; i < myVertices.length; i++) {
    //{8}
    graph.addVertex(myVertices[i]);
  }
  
  graph.addEdge("A", "B"); //{9}
  graph.addEdge("A", "C");
  graph.addEdge("A", "D");
  graph.addEdge("C", "D");
  graph.addEdge("C", "G");
  graph.addEdge("D", "G");
  graph.addEdge("D", "H");
  graph.addEdge("B", "E");
  graph.addEdge("B", "F");
  graph.addEdge("E", "I");
  
  graph.toString();
```

![1554685310014|200](/img/user/programming/basic/data-structure/graph/1554685310014.png)

## Class 实现

```js
class Graph {
    vertices = [];
    adjList = new Map();

    addVertex(vertex) {
        this.vertices.push(vertex);
        this.adjList.set(vertex, []);
    }

    addEdge(v, u) {
        this.adjList.get(v).push(u)
    }

    toString() {
        let s = "";
        for (let i = 0; i < this.vertices.length; i++) {
            //{10}
            s += this.vertices[i] + " -> ";
            // 遍历该顶点的邻接表
            const neighbors = this.adjList.get(this.vertices[i]); //{11}
            for (let j = 0; j < neighbors.length; j++) {
                //{12}
                s += neighbors[j] + " ";
            }
            s += "\n"; //{13}
        }
        return s;
    }

    dfs (callback) {
        const color = this.initializeColor();

        // 直接从 vertices 数组开始递归, 所以不需要起始顶点
        for (let i = 0; i < this.vertices.length; i++) {
            if (color[this.vertices[i]] === "white") {
                this.dfsVisit(this.vertices[i], color, callback);
            }
        }
    }

    initializeColor() {
        const color = {};
        for (let i = 0; i < this.vertices.length; i++) {
            color[this.vertices[i]] = "white"; //{1}
        }
        return color;
    }

    dfsVisit(v, color, callback) {
        color[v] = "grey";
        // 第一次遍历顶点时打印
        if (callback) callback(v);

        const neighbors = this.adjList.get(v);
        // 按照字典序排序
        neighbors.sort();
        for (let i = 0; i < neighbors.length; i++) {
            const u = neighbors[i];
            if (color[u] === "white") {
                this.dfsVisit(u, color, callback);
            }
            else {
                // 每次遍历顶点时打印
                if (callback) callback(u);
            }
        }
        color[v] = "black";
    }



}
```

# 图的遍历

和树数据结构类似，我们可以访问图的所有节点。有两种算法可以对图进行遍历：广度优先搜索（Breadth-First Search，BFS）和深度优先搜索（Depth-First Search，DFS）。

图遍历可以用来**寻找**特定的**顶点**或寻找两个顶点之间的**路径**，检查图是否连通，检查图是否含有环等。

图遍历算法的思想是必须**追踪每个第一次访问的节点**，并且追踪有哪些节点还没有被完全探索。对于两种图遍历算法，都需要明确指出**第一个被访问的顶点**。

完全探索一个顶点要求我们查看该顶点的**每一条边**。对于每一条边所连接的没有被访问过的顶点，将其标注为被发现的，并将其加进**待访问顶点列表**中。

为了保证算法的效率，务必**访问每个顶点至多两次**。连通图中每条边和顶点都会被访问到。

广度优先搜索算法和深度优先搜索算法基本上是相同的，只有一点不同，那就是**待访问顶点列表的数据结构**

|    算 法     | 数据结构 | 描 述                                                        |
| :----------: | :------: | ------------------------------------------------------------ |
| 广度优先搜索 |   队列   | 通过将顶点存入队列中，最先入队列的顶点先被探索               |
| 深度优先搜索 |    栈    | 通过将顶点存入栈中，顶点是沿着路径被探索的，存在新的相邻顶点就去访问 |

当要标注已经访问过的顶点时，我们用三种颜色来反映它们的状态。

- 白色：表示该顶点还没有被访问。
- 灰色：表示该顶点被访问过，但并未被探索过。
- 黑色：表示该顶点被访问过且被完全探索过。

这就是之前提到的务必访问每个顶点最多两次的原因。

## 广度优先搜索

广度优先搜索算法会从指定的第一个顶点开始遍历图，先访问其所有的相邻点，就像一次访问图的一层。换句话说，就是先宽后深地访问顶点，如下图所示：

> 给定源顶点是在特定情况下使用 BFS 算法的要求，如求解最短路径。但如果只是要遍历整个图而不关注最短路径，那么 BFS 可以从任意一个顶点开始，最终都能遍历到所有的节点。

求解最短路径, 正是 BFS 最最常见的场景

常用的场景

常用的用途

![1554685757535](/img/user/programming/basic/data-structure/graph/1554685757535.png)

以下是从顶点 v 开始的广度优先搜索算法所遵循的步骤：

1. 创建一个队列 Q。
2. 将 v 标注为被发现的（灰色），并将 v 入队列 Q。
3. 如果 Q 非空，则运行以下步骤：
	1. 将 u 从 Q 中出队列；
	2. 将标注 u 为被发现的（灰色）；
	3. 将 u 所有未被访问过的邻点（白色）入队列；
	4. 将 u 标注为已被探索的（黑色）。

```js
this.bfs = function (v, callback) {
    const color = initializeColor(),
        queue = new Queue();
    // 入队作为遍历的起点
    queue.enqueue(v);
    // 如果队列非空
    while (!queue.isEmpty()) {
        // 从队列弹出一个顶点
        let v = queue.dequeue();
        // 获取该顶点的邻接表
        neighbors = adjList.get(v);
        // 发现了该顶点, 但是还没有完成探索
        color[v] = "grey";
        // 将所有未被探索的邻居加入队列
        for (let i = 0; i < neighbors.length; i++) {
            let u = neighbors[i];
            if (color[u] === "white") {
                // 颜色标记就是为了避免重复将节点推入队列
                color[u] = "grey";
                queue.enqueue(u);
            }
        }
        // 邻接表访问后, 标记为黑色
        color[v] = "black";
        if (callback) {
            callback(v);
        }
    }
};

/**
 * 广度优先搜索和深度优先搜索都需要标注被访问过的顶点
 * 初始化所有的顶点标记为白色
 */
function initializeColor() {
    const color = [];
    for (let i = 0; i < vertices.length; i++) {
        color[vertices[i]] = "white"; //{1}
    }
    return color;
}
```

首先，我们声明了一个回调函数（行{16}），它仅仅在浏览器控制台上输出已经被完全探索过的顶点的名字。接着，我们会调用 bfs 方法，给它传递第一个顶点（A——从本章开头声明的 myVertices 数组）和回调函数。当我们执行这段代码时，该算法会在浏览器控制台输出下示的结果：

![1554686712896|200](/img/user/programming/basic/data-structure/graph/1554686712896.png)

## 深度优先搜索

深度优先搜索算法将会从第一个指定的顶点开始遍历图，沿着路径直到这条路径最后一个顶点被访问了，接着原路回退并探索下一条路径。换句话说，它是先深度后广度地访问顶点，如下图所示：

![1554699221000](/img/user/programming/basic/data-structure/graph/1554699221000.png)

深度优先搜索算法不需要一个源顶点。在深度优先搜索算法中，若图中顶点 v 未访问，则访问该顶点 v。

要访问顶点 v，照如下步骤做：

- 标注 v 为被发现的（灰色）。
- 对于 v 的所有未访问的邻点 w，访问顶点 w，标注 v 为已被探索的（黑色）。

如你所见，深度优先搜索的步骤是**递归**的，这意味着深度优先搜索算法使用栈来存储函数调用（由递归调用所创建的栈）

让我们来实现一下深度优先算法：

```js
this.dfs = function (callback) {
    const color = initializeColor();

    // 直接从 vertices 数组开始递归, 所以不需要起始顶点
    for (let i = 0; i < vertices.length; i++) {
        if (color[vertices[i]] === "white") {
            dfsVisit(vertices[i], color, callback);
        }
    }
};

function dfsVisit(v, color, callback) {
    color[v] = "grey";
    if (callback) callback(v);

    const neighbors = adjList.get(v);
    for (let i = 0; i < neighbors.length; i++) {
        const u = neighbors[i];
        if (color[u] === "white") {
            dfsVisit(u, color, callback);
        }
    }
    color[v] = "black";
}
```

> 此处的 callback 仅是表意作用，因为很多时候不仅仅需要 v，也需要其他信息，更多的情况下需要在不同的地方增加代码，以深度遍历的形式完成任务，比如拓扑排序

执行过程如下图所示：

![1554705028085](/img/user/programming/basic/data-structure/graph/1554705028085.png)

## 注意

黑白灰三种状态的标记更适合处理复杂的状态, 一般的遍历只需要记住这个节点已经遍历过了就行了, 是类似 boolean 的二元枚举状态

其实只需要邻接表就可以理清一副图了，为什么要需要 vertices 数组呢？ 因为不是所有的顶点都是想通的, 不相通的顶点就需要通过 vertices 数组继续完成遍历

如果是有向图，出度邻接表，可能会遗漏出度为 0 的那个顶点（终点），但是仍然可以通过边到达该顶点

# 常问的图面试问题：

- 实现广度优先搜索和深度优先搜索
- 检查一个图是否为树
- 计算一张图中的边的数量
- 找到两个顶点之间的最短路径

# 网格类问题

## 基本概念

我们所熟悉的 DFS（深度优先搜索）问题通常是在树或者图结构上进行的。而我们今天要讨论的 DFS 问题，是在一种「网格」结构中进行的。岛屿问题是这类网格 DFS 问题的典型代表。网格结构遍历起来要比二叉树复杂一些，如果没有掌握一定的方法，DFS 代码容易写得冗长繁杂。

本文将以岛屿问题为例，展示网格类问题 DFS 通用思路，以及如何让代码变得简洁。

我们首先明确一下岛屿问题中的网格结构是如何定义的，以方便我们后面的讨论。

网格问题是由 m×n 个小方格组成一个网格，每个小方格与其上下左右四个方格认为是相邻的，要在这样的网格上进行某种搜索。

岛屿问题是一类典型的网格问题。每个格子中的数字可能是 0 或者 1。我们把数字为 0 的格子看成海洋格子，数字为 1 的格子看成陆地格子，这样相邻的陆地格子就连接成一个岛屿。

![](/img/user/programming/basic/data-structure/graph/image-20230629185208787.png)

在这样一个设定下，就出现了各种岛屿问题的变种，包括岛屿的数量、面积、周长等。不过这些问题，基本都可以用 DFS 遍历来解决。

## DFS 的基本结构

对于网格上的 DFS，我们完全可以参考二叉树的 DFS，写出网格 DFS 的两个要素：

首先，网格结构中的格子有多少相邻结点？答案是上下左右四个。对于格子 (r, c) 来说（r 和 c 分别代表行坐标和列坐标），四个相邻的格子分别是 (r-1, c)、(r+1, c)、(r, c-1)、(r, c+1)。换句话说，网格结构是「四叉」的。

其次，网格 DFS 中的 base case 是什么？从二叉树的 base case 对应过来，应该是网格中不需要继续遍历、`grid[r][c]` 会出现数组下标越界异常的格子，也就是那些超出网格范围的格子。

这一点稍微有些反直觉，坐标竟然可以临时超出网格的范围？这种方法我称为「先污染后治理」—— 甭管当前是在哪个格子，先往四个方向走一步再说，如果发现走出了网格范围再赶紧返回。这跟二叉树的遍历方法是一样的，先递归调用，发现 root == null 再返回。

这样，我们得到了网格 DFS 遍历的框架代码：

```java
void dfs(int[][] grid, int r, int c) {
    // 判断 base case
    // 如果坐标 (r, c) 超出了网格范围，直接返回
    if (!inArea(grid, r, c)) {
        return;
    }
    // 访问上、下、左、右四个相邻结点
    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}

// 判断坐标 (r, c) 是否在网格中
boolean inArea(int[][] grid, int r, int c) {
    return 0 <= r && r < grid.length 
        	&& 0 <= c && c < grid[0].length;
}
```

### 如何避免重复遍历

网格结构的 DFS 与二叉树的 DFS 最大的不同之处在于，遍历中可能遇到遍历过的结点。这是因为，网格结构本质上是一个「图」，我们可以把每个格子看成图中的结点，每个结点有向上下左右的四条边。在图中遍历时，自然可能遇到重复遍历结点。

这时候，DFS 可能会不停地「兜圈子」，永远停不下来

如何避免这样的重复遍历呢？答案是标记已经遍历过的格子。以岛屿问题为例，我们需要在所有值为 1 的陆地格子上做 DFS 遍历。每走过一个陆地格子，就把格子的值改为 2，这样当我们遇到 2 的时候，就知道这是遍历过的格子了。也就是说，每个格子可能取三个值：

- 0 —— 海洋格子
- 1 —— 陆地格子（未遍历过）
- 2 —— 陆地格子（已遍历过）

我们在框架代码中加入避免重复遍历的语句：

```js
// 增加填充行, 参考的是 dummyHead 的逻辑
const startRow = Array.from({length: grid[0].length}, () => '0');
const endRow = Array.from({length: grid[0].length}, () => '0');
grid.unshift(startRow);
grid.push(endRow);

function dfs(r, c) {
	// if (!inArea(grid, r, c)) return;
	if (grid[r][c] !== '1') return;

	// 已经遍历过的岛屿标记为 2
	grid[r][c] = '2';

	// 继续递归四个方向
	dfs(r - 1, c);
	dfs(r + 1, c);
	dfs(r, c + 1);
	dfs(r, c - 1);
}

function inArea(grid, r, c) {
	// 仔细想一下这个判断其实很重复, 有点没有必要
	// 首先 r 只有第一行和最后一行可能越界
	// 然后 col 即使越界了也没有关系, 读取到 undefined, bad case 直接结束递归了
	// 所以只需要给最开头和最后加上一个填充行就好了
	if (r > grid.length -1 || c > grid[0].length - 1) return false;
	if (r < 0 || c < 0) return false;
	return true;
}
```

这样，我们就得到了一个岛屿问题、乃至各种网格问题的通用 DFS 遍历方法。以下所讲的几个例题，其实都只需要在 DFS 遍历框架上稍加修改而已。

小贴士： 在一些题解中，可能会把「已遍历过的陆地格子」标记为和海洋格子一样的 0，美其名曰「陆地沉没方法」，即遍历完一个陆地格子就让陆地「沉没」为海洋。这种方法看似很巧妙，但实际上有很大隐患，因为这样我们就无法区分「海洋格子」和「已遍历过的陆地格子」了。如果题目更复杂一点，这很容易出 bug。

## 岛屿问题

在 LeetCode 中，「岛屿问题」是一个网格系列问题，比如：

| File                                                   | difficulty | etags                                                                                                                             |
| ------------------------------------------------------ | ---------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [[programming/basic/leetcode/200. 岛屿数量\|200. 岛屿数量]] | medium     | <ul><li>#leetcode/grid/island</li><li>#leetcode/graph/traversal</li><li>#leetcode/unsolved</li><li>#leetcode/union-find</li></ul> |

{ .block-language-dataview}

- [463. 岛屿的周长](https://leetcode.cn/problems/island-perimeter/)
- [695. 岛屿的最大面积](https://leetcode.cn/problems/max-area-of-island/)

# 最短路径问题

## 使用 BFS 寻找最短路径

到目前为止，我们只展示了 BFS 算法的工作原理。我们可以用该算法做更多事情，而不只是输出被访问顶点的顺序。例如，考虑如何来解决下面这个问题。

给定一个图 G 和源顶点 v，找出对每个顶点 u，u 和 v 之间最短路径的距离（以边的数量计）。

对于给定顶点 v，广度优先算法会访问所有与其距离为 1 的顶点，接着是距离为 2 的顶点，以此类推。所以，可以用广度优先算法来解这个问题。我们可以修改 bfs 方法以返回给我们一些信息：

  - 从 v 到 u 的距离 `d[u]`；
  - 前溯点 `pred[u]`，用来推导出从 v 到其他每个顶点 u 的最短路径。

让我们来看看改进过的广度优先方法的实现：

  ```js
this.bfs2Graph = function (v) {
    const color = initializeColor(),
        queue = new Queue(),
        d = {},    //{1} 
        pred = {}; //{2} 
    queue.enqueue(v);
    for (let i = 0; i < vertices.length; i++) {
        // 初始化每个顶点到源点的距离为0
        d[vertices[i]] = 0
        // 前溯点为null
        pred[vertices[i]] = null
    }
    while (!queue.isEmpty()) {
        const v = queue.dequeue(),
            neighbors = adjList.get(v)
        color[v] = 'grey'
        for (i = 0; i < neighbors.length; i++) {
            var w = neighbors[i]
            if (color[w] === 'white') {
                color[w] = 'grey'
                // 前溯点的距离+1
                pred[w] = v               //{6} 
                d[w] = d[v] + 1           //{7} 
                queue.enqueue(w)
            }
        }
        color[v] = 'black'
    }
    return { //{8} 
        distances: d,
        predecessors: pred
    }
}
  ```

初始化每个顶点到源点的距离为 0（行{4}），用 null 来初始化数组 pred。

当我们发现顶点 vertex 的邻点 w 时，则设置 w 的前溯点值为 u（行{7}）。我们还通过给 `d[u]` 加 1 来设置 v 和 w 之间的距离（vertex 是 w 的前溯点，`d[vertex]` 的值已经更新了）。

方法最后返回了一个包含 d 和 pred 的对象（行{8}）。

  ```js
  var shortestPathA = graph.BFS(myVertices[0]); 
  console.log(shortestPathA); 
  ```

![1554698147689](/img/user/programming/basic/data-structure/graph/1554698147689.png)

通过前溯点数组，我们可以用下面这段代码来构建从顶点 A 到其他顶点的路径：

  ```js
this.bfs2Graph = function (v) {
    /.../
    // 利用前溯点对象打印路径
    const start = v,
        path = new Stack()
    // 刨去起点
    const ends = [...vertices]
    ends.splice(ends.indexOf(start), 1)
    for (let i = 0; i < ends.length; i++) {
        let end = ends[i]
        while (end !== start) {
            path.push(end)
            // 变更为前溯点，回溯到start为止
            end = pred[end]
        }
        // 循环结束，栈中保存了该条路径，除了源顶点(A)外的顶点
        let s = start
        while (!path.isEmpty()) {
            // 逐个出栈拼接路径
            s += ' - ' + path.pop()
        }
        console.log(s)
    }
    console.log(s)
    return { //{8} 
        distances: d,
        predecessors: pred
    }
}
  ```

对于每个其他顶点（除了起点——行{10}），我们会计算顶点 A 到它的路径。我们从顶点数组得到 end（行{11}），然后会创建一个栈来存储路径值（行{12}）。

变量 v 被赋值为其前溯点的值，这样我们能够反向追溯这条路径。将变量 v 添加到栈中（行{14}）。最后，源顶点也会被添加到栈中，以得到完整路径。

这之后，我们创建了一个 s 字符串，并将源顶点赋值给它（相当于它是最后一个加入栈中的，所以它是第一个被弹出的项 ——行{16}）。当栈是非空的，我们就从栈中移出一个项并将其拼接到字符串 s 的后面（行{18}）。最后（行{19}）在控制台上输出路径。

![1554699059738|300](/img/user/programming/basic/data-structure/graph/1554699059738.png)

## 深入学习最短路径算法

- 本章中的图不是加权图。如果要计算加权图中的最短路径（例如，城市 A 和城市 B 之间的最短路径——GPS 和 Google Maps 中用到的算法），广度优先搜索**未必合适**。
- Dijkstra 算法解决了**单源最短路径**问题。
- Bellman-Ford 算法解决了边**权值为负的单源最短路径问题**。
- A* 搜索算法解决了求**仅一对顶点间的最短路径**问题，它用**经验法则**来加速搜索过程。
- Floyd-Warshall 算法解决了求**所有顶点对间的最短路径**这一问题。
- 设想你要从街道地图上的 A 点，通过可能的最短路径到达 B 点。举例来说，从洛杉矶的圣莫尼卡大道到好莱坞大道，如下图所示：

  ![1554708978071](/img/user/programming/basic/data-structure/graph/1554708978071.png)

- 这种问题在生活中非常常见，我们（特别是生活在大城市的人们）会求助于苹果地图、谷歌地图、Waze 等应用程序。当然，我们也有其他的考虑，如时间或路况，但根本的问题仍然是：A 到 B 的最短路径是什么？
- 我们可以用图来解决这个问题，相应的算法被称为最短路径。本节我们将介绍两种非常著名
  的算法，即 Dijkstra 算法和 Floyd-Warshall 算法。

## Dijkstra 算法——一到多

+ Dijkstra 算法是一种计算从**单个源**到**所有其他顶点**的最短路径的**贪心算法**（你可以在第 11 章了解到更多关于贪心算法的内容），这意味着我们可以用它来计算从图的一个顶点到其余各顶点的最短路径。

### 算法思想

- 把图中顶点集合 V 分成两组：
  - 第一组为已求出最短路径的顶点集合（用 S 表示，初始时 S 中只有一个源点，以后每求得一条最短路径 , 就将加入到集合 S 中，直到全部顶点都加入到 S 中，算法就结束了）
  - 第二组为其余未确定最短路径的顶点集合（用 U 表示）
- 按最短路径长度的递增次序依次把第二组的顶点加入 S 中。
- 在加入的过程中，总保持从源点 v 到 S 中各顶点的最短路径长度不大于从源点 v 到 U 中任何顶点的最短路径长度。
- 此外，每个顶点对应一个距离，S 中的顶点的距离就是从 v 到此顶点的最短路径长度，U 中的顶点的距离，是从 v 到此顶点只包括 S 中的顶点为中间顶点的当前最短路径长度。

### 具体步骤

1. 初始时，S 只包含起点 s；U 包含除 s 外的其他顶点，且 U 中顶点的距离为 " 起点 s 到该顶点的距离 "[例如，U 中顶点 v 的距离为 (s,v) 的长度，然后 s 和 v 不相邻，则 v 的距离为∞]。
2. 从 U 中选出 " 距离最短的顶点 u"，并将顶点 u 加入到 S 中；同时，从 U 中移除顶点 u。

   > 贪心

3. 更新 U 中各个顶点到起点 s 的距离。之所以更新 U 中顶点的距离，是由于上一步中确定了 u 是求出最短路径的顶点，从而可以利用 u 来更新其它顶点的距离；例如，(s,v) 的距离可能大于 (s,u)+(u,v) 的距离。
4. 重复步骤 (2) 和 (3)，直到遍历完所有顶点。

+ 当然只要稍加修改，记录一下最短路径时的前溯点，就可以用同样的方法给出最短的路径

### 实现

- 因为此处使用的是邻接矩阵，所以实现的方式有所不同，通过 visited 数组来区分 S 和 U，本质都是一样的

  ![1554709052620](/img/user/programming/basic/data-structure/graph/1554709052620.png)

- 我们来看看如何找到顶点 A 和其余顶点之间的最短路径。但首先，我们需要声明表示上图的**邻接矩阵**，如下所示：

  ```js
  var graph = [[0, 2, 4, 0, 0, 0], 
               [0, 0, 2, 4, 2, 0], 
               [0, 0, 0, 0, 3, 0], 
               [0, 0, 0, 0, 0, 2], 
               [0, 0, 0, 3, 0, 2], 
               [0, 0, 0, 0, 0, 0]] 
  ```

- 现在，通过下面的代码来看看 Dijkstra 算法是如何工作的：

  ```js
  const dijkstra = function(graph,src) { 
    const length = graph.length,
    INF = Number. MAX_SAFE_INTEGER // 直接使用Infinity也可以
  	// 把所有的距离（dist）初始化为无限大，将visited[]初始化为false
    const dist = new Array(length).fill(INF), 
          // 标记为true的相当于是S
          visited = new Array(length).fill(false)
  
    // 把 源顶点 到 自己的 距离设为0 
    dist[src] = 0 
    // 要找出到其余顶点的最短路径
    for (let i = 1; i < length; i++) {
      // 从尚未处理的顶点中选出 距离最近 的顶点，作为局部的最优起点
      let v = minDistance(dist, visited)
      // 把选出的顶点标为visited，以免重复计算
      visited[v] = true 
  
      for (let u = 0; u < length; u++) { 
        if (!visited[u] 
          && graph[v][u] != 0 
          && dist[v] != INF 
          && dist[v] + graph[v][u] < dist[u]) {
          // 如果找到更短的路径，则更新最短路径的值  贪心算法 
          dist[u] = dist[v] + graph[v][u]
        } 
      } 
    }
    // 处理完所有顶点后，返回从源顶点到图中其他顶点最短路径的结果 
    return dist 
  } 
  ```

- 要计算顶点间的 minDistance，就要搜索 dist 数组中的最小值，返回它在数组中的索引

  ```js
  function minDistance(dist, visited) { 
    let min = Number.MAX_SAFE_INTEGER, 
        minIndex = -1 
   
    for (let v = 0; v < dist.length; v++) { 
      if (visited[v] == false && dist[v] <= min) { 
        min = dist[v] 
        minIndex = v 
      } 
    } 
    return minIndex 
  }
  ```

- 对本节开始的图执行以上算法，会得到如下输出：

  ![1554709499849](/img/user/programming/basic/data-structure/graph/1554709499849.png)

### 参考

+ 可以注意到想要渠道 F 点，经过 C 和经过 E 的花费是不同的，一开始先遍历到 C 点，此时到 F 的花费为 9，之后遍历到 E 点，进行了一次更新，因此到达 F 的最短路径是 6

![img](https://github.com/wangkuiwu/datastructs_and_algorithm/blob/master/pictures/graph/dijkstra/02.jpg?raw=true&_=3711516)

## Floyd-Warshall 算法——多到多

+ Floyd-Warshall 算法是一种计算图中所有最短路径的动态规划算法（你可以在第 11 章了解到更

  多关于动态规划算法的内容）。通过该算法，我们可以找出从**所有源到所有顶点**的最短路径。

+ 原理：

  ![1565008372289](/img/user/programming/basic/data-structure/graph/1565008372289.png)

+ 实现如下：

  ```js
  function floydWarshall(graph) { 
    const dist = [], 
          length = graph.length
    // 首先，把dist数组初始化为每个顶点之间的权值，因为i到j可能的最短距离就是这些顶点间的权值
    for (let i = 0; i < length; i++) { //{1} 
      dist[i] = [] 
      for (let j = 0; j < length; j++) { 
        dist[i][j] = graph[i][j]===0?Infinity:graph[i][j] 
      } 
    } 
     
    // 通过k，得到i途经顶点k，到达j的最短路径
    for (let k = 0; k < length; k++) { //{2} 
      for (let i = 0; i < length; i++) { 
        for (let j = 0; j < length; j++) { 
          // 状态转移方程
          if (dist[i][k] + dist[k][j] < dist[i][j]) {
            dist[i][j] = dist[i][k] + dist[k][j]
          }
        } 
      } 
    } 
    return dist 
  }
  
  
  var graph = [[0, 2, 4, 0, 0, 0], 
               [0, 0, 2, 4, 2, 0], 
               [0, 0, 0, 0, 3, 0], 
               [0, 0, 0, 0, 0, 2], 
               [0, 0, 0, 3, 0, 2], 
               [0, 0, 0, 0, 0, 0]] 
  
  console.log(...floydWarshall(graph))
  ```

+ 对图中每一个顶点执行 Dijkstra 算法，也可以得到相同的结果。

# 染色问题

## [1042. 不邻接植花](https://leetcode-cn.com/problems/flower-planting-with-no-adjacent/) @@@

### 题目

+ 有 N 个花园，按从 1 到 N 标记。在每个花园中，你打算种下四种花之一。
+ paths[i] = [x, y] 描述了花园 x 到花园 y 的双向路径。
+ 另外，没有花园有 3 条以上的路径可以进入或者离开。
+ 你需要为每个花园选择一种花，使得通过路径相连的任何两个花园中的花的种类互不相同。
+ 以数组形式返回选择的方案作为答案 answer，其中 answer[i] 为在第 (i+1) 个花园中种植的花的种类。花的种类用 1, 2, 3, 4 表示。
+ 保证存在答案。

  ```
  输入：N = 3, paths = [[1,2],[2,3],[3,1]]
  输出：[1,2,3]
  ```

### 分析

+ 这是一道简单题，限制每个节点的度为 3，同时提供四种颜色，因此不需要回溯

  - 存储邻接点信息
  - 遍历所有节点，对于每个节点，查看其邻接点颜色，使用不同的颜色染色即可

```js
  var gardenNoAdj = function(N, paths) {
    const adjList = []
    // 初始化邻接表 base 0
    for (let i = 0; i < N; i++) {
      adjList[i] = []
    }
    // 初始化路径信息
    for (const path of paths) {
      // path[0] 到 path[1] 双向路径
      // 转化为base0
      const [a,b] = [path[0]-1,path[1]-1]
      adjList[a].push(b)
      adjList[b].push(a)
    }
    const res =[]
    // res base 0
    for (let i = 0; i < N; i++) {
      const used = new Array(N+1)
      const neighbors = adjList[i]
      // 记录当前节点的已涂色的邻接点的色彩
      for (const n of neighbors) {
        if(n<i) used[res[n]] = true
      }
      // 为当前节点染色
      for (let j = 1; j <= 4; j++) {
        if(!used[j]){
          res[i] = j
          break
        }
      }
    }
    return res
  }
  
  let N = 3, paths = [[1,2],[2,3],[3,1]]
  console.log(gardenNoAdj(N,paths))
  ```

1000+ms 需要优化

# 题集

## [399. 除法求值](https://leetcode-cn.com/problems/evaluate-division/)

### 题目

+ 给出方程式 A / B = k, 其中 A 和 B 均为代表字符串的变量， k 是一个浮点型数字。根据已知方程式求解问题，并返回计算结果。如果结果不存在，则返回 -1.0。

  ```
  示例 :
  给定 a / b = 2.0, b / c = 3.0
  问题: a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ? 
  返回 [6.0, 0.5, -1.0, 1.0, -1.0 ]
  ```

+ 输入为: `vector<pair<string, string>> equations`, `vector<double>& values`, `vector<pair<string, string>> queries`(方程式，方程式结果，问题方程式)， 其中 `equations.size() == values.size()`，即方程式的长度与方程式结果长度相等（程式与结果一一对应），并且结果值均为正数。以上为方程式的描述。 返回 `vector<double>` 类型。
+ 基于上述例子，输入如下：

  ```
  equations(方程式) = [ ["a", "b"], ["b", "c"] ],
  values(方程式结果) = [2.0, 3.0],
  queries(问题方程式) = [ ["a", "c"], ["b", "a"], ["a", "e"], ["a", "a"], ["x", "x"] ]. 
  ```

+ 输入总是有效的。你可以假设除法运算中不会出现除数为 0 的情况，且不存在任何矛盾的结果。

### 分析

# 最小生成树

## 概念

+ 最小生成树（MST）问题是网络设计中常见的问题。想象一下，你的公司有几间办公室，要以最低的成本实现办公室电话线路相互连通，以节省资金，最好的办法是什么？
+ 这也可以应用于岛桥问题。设想你要在 n 个岛屿之间建造桥梁，想用最低的成本实现所有岛

  屿相互连通。

+ 这两个问题都可以用 MST 算法来解决，其中的办公室或者岛屿可以表示为图中的一个顶点，边代表成本。这里我们有一个图的例子，其中较粗的边是一个 MST 的解决方案。

  ![1564994728953](/img/user/programming/basic/data-structure/graph/1564994728953.png)

+ 本节我们将学习两种主要的求最小生成树的算法：Prim 算法和 Kruskal 算法。

## Prim 算法

+ 适用于邻接矩阵
+ Prim 算法是一种求解加权无向连通图的 MST 问题的贪心算法。它能找出一个边的子集，使得其构成的树包含图中所有顶点，且边的权值之和最小。
+ 现在，通过下面的代码来看看 Prim 算法是如何工作的：

  ```js
  function prim (graph) { 
    const len = graph.length
    // 首先，把所有顶点（key）初始化为无限大，visited[]初始化为false
    const key = new Array(len).fill(Infinity),
          visited = new Array(len).fill(false),
          pred = [] // 记录前溯点
  
    // 选择第一个key作为第一个顶点
    key[0] = 0
    // 因为第一个顶点总是MST的根节点，所以pred[0] = -1
    pred[0] = -1 
  	// 对所有顶点求MST
    for (let i = 0; i < len-1; i++) {
      // 从未处理的顶点集合中选出key值最小的顶点
      // 与Dijkstra算法中使用的函数一样，只是名字不同
      const v = minKey(key, visited)
      // 把选出的顶点标为visited，以免重复计算
      visited[v] = true
      for (let u = 0; u < len; u++) { 
        // 如果得到更小的权值，则保存MST路径（parent，行{7}）并更新其权值（行{8}）
        if (graph[v][u] 
          && visited[u] == false 
          && graph[v][u] < key[u]) {
          pred[u] = v //{7} 
          key[u] = graph[v][u] //{8} 
        } 
      } 
    }
    // 索引对应的顶点的前溯点 
    return pred
  }
  function minKey(key, visited) { 
    let min = Number.MAX_SAFE_INTEGER, 
        minIndex = -1 
   
    for (let v = 0; v < key.length; v++) { 
      if (visited[v] == false && key[v] <= min) { 
        min = key[v] 
        minIndex = v 
      } 
    } 
    return minIndex 
  }
  
  var graph = [[0, 2, 4, 0, 0, 0], 
               [2, 0, 2, 4, 2, 0], 
               [4, 2, 0, 0, 3, 0], 
               [0, 4, 0, 0, 3, 2], 
               [0, 2, 3, 3, 0, 2], 
               [0, 0, 0, 2, 2, 0]] 
  console.log(...prim(graph).entries())
  ```

+ 比较 Prim 算法和 Dijkstra 算法，我们会发现除了行{7}和行{8}之外，两者非常相似。行{7}用 parent 数组保存 MST 的结果。行{8}用 key 数组保存权值最小的边，而在 Dijkstra 算法中，用 dist 数组保存距离。我们可以修改 Dijkstra 算法，加入 parent 数组。这样，就可以在求出距离的同时得到路径。
+ 我们会得到如下输出：

  ![1565009572691](/img/user/programming/basic/data-structure/graph/1565009572691.png)

## Kruskal 算法

+ 适用于邻接表和邻接矩阵
+ 和 Prim 算法类似，Kruskal 算法也是一种求加权无向连通图的 MST 的贪心算法。
+ 现在，通过下面的代码来看看 Kruskal 算法是如何工作的：

  ```js
  /**
   * kruskal算法
   * 遍历所有的边，按权值从小到大排序，每次选取当前权值最小的边，只要不构成回环，则加入生成树
   * 邻接矩阵转换成边集数组
   * 优点：适合点多边少的情况
   * @param graph 邻接矩阵
   * @return Array 最小生成树的边集数组
   * */
  function kruskal(graph) {
    const edgeArray = getEdgeArr(graph),
          result = [],
          // 使用一个数组保存当前顶点的所在的树的终点，即是顶点下标最大的点
          // 0表示还没有当前顶点还没有加入任何一颗树
          parent = new Array(graph.length).fill(0)
  
    let i = 0
    // 边数=顶点数-1时就全部联通了
    while(result.length<graph.length-1){
      const edge = edgeArray[i]
      // 找到begin顶点的终点，找到end顶点的最终终点
      const fx = findParent(parent, edge.begin)
      const fy = findParent(parent, edge.end)
      
      // 合并操作，两个顶点的父亲不同，说明在不同的树，连接不会形成环路
      if (fx !== fy) {
        result.push(edge)
        // 更新终点
        parent[fx] = fy
      }
      i++
    }
    console.log(...parent.entries())
    return result
  }
  var graph = [[0, 2, 4, 0, 0, 0], 
               [2, 0, 2, 4, 2, 0], 
               [4, 2, 0, 0, 3, 0], 
               [0, 4, 0, 0, 3, 2], 
               [0, 2, 3, 3, 0, 2], 
               [0, 0, 0, 2, 2, 0]]
  
  console.log(...kruskal(graph).entries())
  ```

+ edge 类定义如下：

  ```js
  class Edge {
    constructor(begin, end, weight) {
      this.begin = begin
      this.end = end
      this.weight = weight
    }
  }
  ```

+ getEdege 定义如下：

  ```js
  /**
   * 邻接矩阵转邻接表的函数
   * @param matrix 邻接矩阵
   * @return adjList 邻接表
   * */
  function getEdgeArr(matrix) {
    const rows = matrix.length,
          cols = rows,
          result = []
    for (let i = 0; i < rows; i++) {
      const row = matrix[i]
      for(let j = 0 ; j < cols; j++) {
        if(row[j] !== 0) {
          result.push(new Edge(i, j, row[j]))
        }
      }
    }
    result.sort((a, b) => a.weight - b.weight)
    return result
  }
  ```

+ 下面是 find 函数的定义，它能防止 MST 出现环路：

  ```js
  /**
   * 查找连线顶点的尾部下标
   * @param parent 判断边与边是否形成环路的数组
   * @param v 连线开始的顶点
   * @return Number 连线顶点的尾部下标
   * */
  function findParent(parent, v) {
    //就是一直循环，直到找到终点
    while (parent[v] > 0) v = parent[v]
    return v
  }
  ```

# 题集

## Leetcode 1135：最低成本联通所有城市

### 题目

+ 想象一下你是个城市基建规划者，地图上有 N 座城市，它们按以 1 到 N 的次序编号。
+ 给你一些可连接的选项 conections，其中每个选项 `conections[i] = [city1, city2, cost] ` 表示将城市 city1 和城市 city2 连接所要的成本。（连接是双向的，也就是说城市 city1 和城市 city2 相连也同样意味着城市 city2 和城市 city1 相连）。
+ 返回使得每对城市间都存在将它们连接在一起的连通路径（可能长度为 1 的）最小成本。该最小成本应该是所用全部连接代价的综合。如果根据已知条件无法完成该项任务，则请你返回 -1。

  ```
  输入：N = 3, conections = [[1,2,5],[1,3,6],[2,3,1]]
  输出：6
  解释：
  选出任意 2 条边都可以连接所有城市，我们从中选取成本最小的 2 条。
  ```

### Prim 算法

+ 转换成邻接矩阵再使用，没什么必要

### Kruskal 算法

+ 根据 `conections` 数组获取边成本排序即可

  ```js
  function kruskal(N,connections) {
    // 即使联通所有边都无法联通城市
    if(connections.length<N-1) return -1
    const edgeArray = getEdgeArr(connections),
          parent = new Array(N).fill(0)
  
    let i = 0, edgeCount = 0,result = 0
    while(edgeCount< N - 1){
      const edge = edgeArray[i]
      const fx = findParent(parent, edge[0])
      const fy = findParent(parent, edge[1])
      
      if (fx !== fy) {
        result += edge[2]
        parent[fx] = fy
      }
      i++
      edgeCount++
    }
    return result
  }
  
  function getEdgeArr(connections){
    return connections.sort((a,b) => a[2]-b[2])
  } 
  function findParent(parent, v) {
    while (parent[v] > 0) v = parent[v]
    return v
  }
  
  let N = 3, connections = [[1,2,5],[1,3,6],[2,3,1]]
  console.log(kruskal(N,connections))
  ```

# 有向图

## 出度与入度

### [997. 找到小镇的法官](https://leetcode-cn.com/problems/find-the-town-judge/)

#### 题目

+ 在一个小镇里，按从 1 到 N 标记了 N 个人。传言称，这些人中有一个是小镇上的秘密法官。
+ 如果小镇的法官真的存在，那么：

  1. 小镇的法官不相信任何人。

     > 法官没有出度

  2. 每个人（除了小镇法官外）都信任小镇的法官

     > 法官入度等于 N - 1

  3. 只有一个人同时满足属性 1 和属性 2

+ 给定数组 trust，该数组由信任对 trust[i] = [a, b] 组成，表示标记为 a 的人信任标记为 b 的人

  > 入度数组

+ 如果小镇存在秘密法官并且可以确定他的身份，请返回该法官的标记。否则，返回 -1。

  ```
  输入：N = 3, trust = [[1,3],[2,3]]
  输出：3
  
  输入：N = 3, trust = [[1,3],[2,3],[3,1]]
  输出：-1
  
  输入：N = 4, trust = [[1,3],[1,4],[2,3],[2,4],[4,3]]
  输出：3
  ```

#### 分析

+ 遍历查询出度与入度。满足入度为 N-1 且出度为 0 者即为所求。

  ```java
  var findJudge = function(N, trust) {
    const adjList = {}
    // base 是 1
    const trustCount = new Array(N+1).fill(0)
    for (const t of trust) {
      // t[0]信任t[1]
      if(!adjList.hasOwnProperty(t[0])) adjList[t[0]] = []
      adjList[t[0]].push(t[1])
      trustCount[t[1]]++
    }
    // 遍历邻接表，找到没有出度的，判断入度是否为N-1
    for (let i = 1; i <= N; i++) {
      if(!adjList.hasOwnProperty(i) && trustCount[i]=== N-1) return i
    }
    return -1
  }
  let N = 2, trust = [[1,2]]
  console.log(findJudge(N,trust))
  ```

### [277. 搜寻名人](https://leetcode-cn.com/problems/find-the-celebrity/)

+ [https://liyiye012.github.io/2018/09/13/277.Find%20the%20Celebrity%E5%AF%BB%E6%89%BE%E5%90%8D%E4%BA%BA/](https://liyiye012.github.io/2018/09/13/277.Find the Celebrity 寻找名人/)

## 拓扑排序

参考：

+ <https://blog.csdn.net/dm_vincent/article/details/7714519>

### 定义和前置条件

- 定义：将有向图中的顶点以线性方式进行排序。即对于任何连接自顶点**u**到顶点**v**的有向边**uv**，在最后的排序结果中，顶点**u**总是在顶点**v**的前面。
- 那么是不是所有的有向图都能够被拓扑排序呢？显然不是。继续考虑上面的例子，如果告诉你在选修计算机科学概论这门课之前需要你先学习机器学习，你是不是会被弄糊涂？在这种情况下，就无法进行拓扑排序，因为它中间存在互相依赖的关系，从而无法确定谁先谁后。在有向图中，这种情况被描述为存在环路。因此，一个有向图能被拓扑排序的充要条件就是它是一个有向无环图 (DAG：Directed Acyclic Graph)。

### 偏序

- 假设我们在学习完了算法这门课后，可以选修机器学习或者计算机图形学。这个**或者**表示，学习机器学习和计算机图形学这两门课之间没有特定的先后顺序。
- 因此，在我们所有可以选择的课程中，任意两门课程之间的关系要么是确定的 (即拥有先后关系)，要么是不确定的 (即没有先后关系)，绝对不存在互相矛盾的关系 (即环路)。
- 以上就是偏序的意义，抽象而言，有向图中两个顶点之间不存在环路，至于连通与否，是无所谓的。所以，有向无环图必然是满足偏序关系的。

### 全序

- 理解了偏序的概念，那么全序就好办了。所谓全序，就是在偏序的基础之上，有向无环图中的任意一对顶点还需要有明确的关系 (反映在图中，就是单向连通的关系，注意不能双向连通，那就成环了)。
- 可见，全序就是偏序的一种特殊情况。回到我们的选课例子中，如果机器学习需要在学习了计算机图形学之后才能学习 (可能学的是图形学领域相关的机器学习算法……)，那么它们之间也就存在了确定的先后顺序，原本的偏序关系就变成了全序关系。
- 实际上，很多地方都存在偏序和全序的概念。

  > 比如对若干互不相等的整数进行排序，最后总是能够得到唯一的排序结果**(**从小到大，下同**)**。
  >
  > 但是如果我们以偏序**/**全序的角度来考虑一下这个再自然不过的问题，可能就会有别的体会了。

- 那么如何用偏序**/**全序来解释排序结果的唯一性呢？

  > 我们知道不同整数之间的大小关系是确定的，即 1 总是小于 4 的，不会有人说 1 大于或者等于 4 吧。
  >
  > 这就是说，这个序列是满足全序关系的。而对于拥有全序关系的结构 (如拥有不同整数的数组)，在其线性化 (排序) 之后的结果必然是唯一的。
  >
  > 对于排序的算法，我们评价指标之一是看该排序算法是否稳定，即值相同的元素的排序结果是否和出现的顺序一致。比如，我们说快速排序是不稳定的，这是因为最后的快排结果中相同元素的出现顺序和排序前不一致了。如果用偏序的概念可以这样解释这一现象：相同值的元素之间的关系是无法确定的。因此它们在最终的结果中的出现顺序可以是任意的。而对于诸如插入排序这种稳定性排序，它们对于值相同的元素，还有一个潜在的比较方式，即比较它们的出现顺序，出现靠前的元素大于出现后出现的元素。因此通过这一潜在的比较，将偏序关系转换为了全序关系，从而保证了结果的唯一性。

- 拓展到拓扑排序中，结果具有唯一性的条件也是其所有顶点之间都具有全序关系。如果没有这一层全序关系，那么拓扑排序的结果也就不是唯一的了。在后面会谈到，如果拓扑排序的结果唯一，那么该拓扑排序的结果同时也代表了一条**哈密顿路径**。

### 深度优先

#### 探索时间

- 对于给定的图 G，我们希望深度优先搜索算法遍历图 G 的所有节点，构建“森林”（**有根树**的一个集合）以及一组**源顶点**（根），并输出两个数组：发现时间和完成探索时间。我们可以修改 dfs 方法来返回给我们一些信息：
  - 顶点 u 的发现时间 d[u]； （discovery）
  - 当顶点 u 被标注为黑色时，u 的完成探索时间 f[u]；
- 让我们来看看改进了的 DFS 方法的实现：

  ```js
  let time = 0; //{1} 
  this.dfs2Time = function(){ 
    const color = initializeColor(), //{2} 
          d = {}, 
          f = {}
  
    for (let i=0; i<vertices.length; i++){ //{3} 
      f[vertices[i]] = 0; 
      d[vertices[i]] = 0;
    } 
    for (let i=0; i<vertices.length; i++){ 
      if (color[vertices[i]] === 'white'){ 
        DFSVisit(vertices[i], color, d, f, p) 
      } 
    } 
    return {           //{4} 
      discovery: d, 
      finished: f, 
    } 
  } 
  
  function DFSVisit (v, color, d, f, p){ 
    console.log('discovered ' + v) 
    color[v] = 'grey'; 
    d[v] = ++time //{5} 
    const neighbors = adjList.get(v) 
    for (let i=0; i<neighbors.length; i++){ 
      const u = neighbors[i]; 
      if (color[u] === 'white'){ 
        DFSVisit(u,color, d, f, p) 
      } 
    } 
    color[v] = 'black' 
    f[v] = ++time;      //{7}  
    console.log('explored ' + v) 
  } 
  ```

- 我们需要一个变量来要追踪发现时间和完成探索时间（行{1}）。时间变量不能被作为参数传递，因为非对象的变量不能作为引用传递给其他 JavaScript 方法（将变量作为引用传递的意思是如果该变量在其他方法内部被修改，新值会在原始变量中反映出来）
- 接下来，我们声明对象 d、f 和 p（行{2}）。我们需要为图的每一个顶点来初始化这些数组（行{3}）。在这个方法结尾处返回这些值（行{4}），之后我们要用到它们。
- 当一个顶点第一次被发现时，我们追踪其发现时间（行{5}）。最后，当这个顶点被完全探索后，我们追踪其完成时间（行{7}）。
- 深度优先算法背后的思想是什么？边是从最近发现的顶点 u 处被向外探索的。只有连接到未发现的顶点的边被探索了。当 u 所有的边都被探索了，该算法回退到 u 被发现的地方去探索其他的边。这个过程持续到我们发现了所有从原始顶点能够触及的顶点。如果还留有任何其他未被发现的顶点，我们对新源顶点重复这个过程。重复该算法，直到图中所有的顶点都被探索了。
- 对于改进过的深度优先搜索，有两点需要我们注意：
  - 时间（time）变量值的范围只可能在图顶点数量的一倍到两倍之间；
  - 对于所有的顶点 u，d[u]<f[u]（意味着，发现时间的值比完成时间的值小，完成时间意思是所有顶点都已经被探索过了）。
- 在这两个假设下，我们有如下的规则：
  - $1 ≤ d [u] < f [u] ≤ 2|V| $
- 如果对同一个图再跑一遍新的深度优先搜索方法，对图中每个顶点，我们会得到如下的发现/完成时间：

  ![1554706085966](/img/user/programming/basic/data-structure/graph/1554706085966.png)

- 但我们能用这些新信息来做什么呢？来看下一节。
- **本质**：就是深度优先搜索，一遍记录发现和探索完成的时间

#### 应用于拓扑排序

- 给定下图，假定每个顶点都是一个我们需要去执行的任务：

  ![1554707425491](/img/user/programming/basic/data-structure/graph/1554707425491.png)

  > 这是一个**有向图**，意味着任务的执行是有顺序的。例如，任务 F 不能在任务 A 之前执行。注意这个图没有环，意味着这是一个无环图。所以，我们可以说该图是一个有向无环图（DAG）。

- 当我们需要编排一些任务或步骤的执行顺序时，这称为拓扑排序（topological sorting，英文亦写作 topsort 或是 toposort）。在日常生活中，这个问题在不同情形下都会出现。例如，当我们开始学习一门计算机科学课程，在学习某些知识之前得按顺序完成一些知识储备（你不可以在上算法 I 前先上算法 II）。当我们在开发一个项目时，需要按顺序执行一些步骤，例如，首先我们得从客户那里得到需求，接着开发客户要求的东西，最后交付项目。你不能先交付项目再去收集需求。
- **拓扑排序只能应用于 DAG。**那么，如何使用深度优先搜索来实现拓扑排序呢？让我们在本节开头的示意图上执行一下深度优先搜索。

  ```js
  graph = new Graph(); 
  myVertices = ['A','B','C','D','E','F']; 
  for (i=0; i<myVertices.length; i++){ 
    graph.addVertex(myVertices[i]); 
  } 
  graph.addEdge('A', 'C'); 
  graph.addEdge('A', 'D'); 
  graph.addEdge('B', 'D'); 
  graph.addEdge('B', 'E'); 
  graph.addEdge('C', 'F'); 
  graph.addEdge('F', 'E'); 
  
  const {discovery,finished,predecessors} = graph.dfs2Time()
  console.log(discovery,finished,predecessors)
  ```

- 这段代码将创建图，添加边，执行改进版本的深度优先搜索算法，并将结果保存到 result 变量。下图展示了深度优先搜索算法执行后，该图的发现和完成时间。

  ![1554707995953](/img/user/programming/basic/data-structure/graph/1554707995953.png)

- 现在要做的仅仅是**以倒序来排序完成时间数组**，这便得出了该图的拓扑排序： **B - A - D - C - F - E**
- 注意之前的拓扑排序结果仅是多种可能性之一。如果我们稍微修改一下算法，就会有不同的结果，比如下面这个结果也是众多其他可能性中的一个： **A - B - C - D - F - E**

#### 使用栈

- 使用栈结构，探索完成后把该节点推入到栈中
- 同时处理有环图：

  ```js
  // 深度优先 拓扑排序
  let hasCycle = false
  this.topSort = function(){
    const color = initializeColor()
    const stack = new Stack()
    for (let i=0; i<vertices.length; i++){ 
      if (color[vertices[i]] === 'white'){ 
        dfsVisit(vertices[i], color, stack)
      } 
    }
    return !hasCycle && stack
    function dfsVisit (v, color, stack){ 
      // 成环，没必要继续递归了
      if(hasCycle) return
      color[v] = 'grey'
  
      const neighbors = adjList.get(v)         
      for (let i=0; i<neighbors.length; i++){
        const u = neighbors[i]              
        if (color[u] === 'white'){         
          dfsVisit(u, color, stack)
        }else if(color[u] === 'grey'){
          // An edge to a GRAY vertex represents a cycle
          hasCycle = true
        } 
      } 
      color[v] = 'black'
      // 完全就是深度优先遍历的代码，完成探索之后推入栈中
      stack.push(v)
    }  
  } 
  ```

- 优化，发现成环后立即退出

  ```js
  var findOrder = function(numCourses, prerequisites) {
    // 初始化图
    const vertices = []
    const adjList = {}
    const color = []
    for (let i = 0; i < numCourses; i++) {
      vertices[i] = i
      adjList[i] = []
      color[i] = 'white'
    }
    // 栈先进后出，题目给的是前溯点，修正为后继点
    for (const p of prerequisites) {
      adjList[p[1]].push(p[0])
    }
    // 仅可以使用push和pop
    const stack = []
    // 随便一个节点开始遍历都是ok的
    /**
     * 只会遍历该节点的后继节点，当该节点遍历完成时，所以以该课程为先修课程的都已经入栈
     * 再遍历其他节点（可能是该节点的先修课程），也一定会在该节点之后入栈，不会影响到先修关系
     */
    for (const v of vertices) {
      // 成环，不可能完成课程，返回空数组
      if(dfsVisit(v,adjList,color,stack)) return []
    }
    let order = []
    // 遍历完毕，且没有成环,从栈顶学习到栈底即可
    for (let i = 0; i < numCourses; i++) {
      // 从栈顶弹出元素
      order.push(stack.pop())
    }
    return order
    // 返回值代表是否成环
    function dfsVisit(v,adjList,color,stack){
      if(color[v]==='grey') return true
      if(color[v]==='black') return false
  
      color[v] = 'grey'
      const successors = adjList[v]
      for (const u of successors) {
        // 层层递归返回 true ，表示图中存在环
        if(dfsVisit(u,adjList,color,stack)) return true
      }
      // v 的所有后继点都访问完了，都没有存在环，则这个结点就可以被标记为已经访问结束
      color[v] = 'black'
      // 压入栈中
      stack.push(v)
      // false 表示图中不存在环
      return false
    }
  }
  let numCourses = 2, prerequisites = [[1,0\|1,0]]
  
  console.log(findOrder(numCourses,prerequisites))
  ```

#### 复杂度分析

- 复杂度同**DFS**一致，即**O(E+V)**。具体而言，首先需要保证图是有向无环图，判断图是**DAG**可以使用基于**DFS**的算法，复杂度为**O(E+V)**，而后面的拓扑排序也是依赖于**DFS**，复杂度为 O(E+V)

### Kahn 算法 - 广度优先

- 具体到拓扑排序，每一次都从图中删除没有前驱的顶点，这里并不需要真正的做删除操作，我们可以设置一个入度数组，每一轮都输出入度为 0 的结点，并移除它、修改它指向的结点的入度（−1 即可），依次得到的结点序列就是拓扑排序的结点序列。如果图中还有结点没有被移除，则说明“不能完成所有课程的学习”。
- 拓扑排序保证了每个活动（在这题中是“课程”）的所有前驱活动都排在该活动的前面，并且可以完成所有活动。拓扑排序的结果不唯一。拓扑排序还可以用于检测一个有向图是否有环。相关的概念还有 AOV 网，这里就不展开了。

#### **算法流程**：

1. 在开始排序前，扫描对应的存储空间（使用邻接表），将入度为 0 的结点放入队列。
2. 只要队列非空，就从队首取出入度为 0 的结点，将这个结点输出到结果集中，并且将这个结点的所有邻接结点（它指向的结点）的入度减 1，在减 1 以后，如果这个被减 1 的结点的入度为 0 ，就继续入队。
3. 当队列为空的时候，检查结果集中的顶点个数是否和课程数相等即可。

#### **注意**

- 在代码具体实现的时候，除了保存入度为 0 的队列，我们还需要两个辅助的数据结构：
  1. 邻接表：通过结点的索引，我们能够得到这个结点的后继结点；
  2. 入度数组：通过结点的索引，我们能够得到指向这个结点的结点个数。
- 这个两个数据结构在遍历题目给出的邻边以后就可以很方便地得到。

#### **实现**

- ```java
  const {Queue} = require('./utils/structure/Structure')
  
  var canFinish = function(numCourses, prerequisites) {
    if(numCourses<=0) return false
    const len = prerequisites.length
    // 没有任何依赖，直接返回
    if(len===0) return true
    // 入度数组
    const requireCount = new Array(numCourses).fill(0)
    for (const p of prerequisites) {
      // course[0]对应的课程，需要前置课程，有入度
      requireCount[p[0]]++
    }
    const queue = new Queue()
    // 首先加入入度为 0 的结点
    for (let i = 0; i < numCourses; i++) {
      if (requireCount[i] == 0) {
        queue.enqueue(i);
      }
    }
    const res = []
    // 拓扑排序
    while(!queue.isEmpty()){
      const learned = queue.dequeue()
      res.push(learned)
      // 把邻边全部遍历一下
      for (const p of prerequisites) {
        if (p[1] == learned) {
          requireCount[p[0]]--
          if (requireCount[p[0]] == 0) queue.addLast(p[0])
        }
      }
    }
    return res.length === numCourses
  }
  let numCourses = 3, prerequisites = [[1,0],[1,2],[2,1]]
  console.log(findOrder(numCourses,prerequisites))
  ```

#### 复杂度分析：

- 初始化入度为**0**的集合需要遍历整张图，检查每个节点和每条边，因此复杂度为 O(E+V);
- 然后对该集合进行操作，又需要遍历整张图中的，每条边，复杂度也为 O(E+V);
- 因此**Kahn**算法的复杂度即为**O(E+V)**。

### 总结

- 对于基于**DFS**的算法，加入结果集的条件是：顶点的**出度**为**0**。这个条件和**Kahn**算法中**入度**为**0**的顶点集合似乎有着异曲同工之妙，这两种算法的思想犹如一枚硬币的两面。一个是从入度的角度来构造结果集，另一个则是从出度的角度来构造。
- 实现上的一些不同之处：
  - Kahn 算法不需要检测图为**DAG**，如果图为**DAG**，那么在出度为**0**的集合为空之后，图中还存在没有被移除的边，这就说明了图中存在环路。
  - 而基于**DFS**的算法需要首先确定图为**DAG**，当然也能够做出适当调整，让环路的检测和拓扑排序同时进行，毕竟环路检测也能够在**DFS**的基础上进行。
- 二者的复杂度均为**O(V+E)**。

## 题集

### [207. 课程表](https://leetcode-cn.com/problems/course-schedule/)

#### 题目

- 现在你总共有 n 门课需要选，记为 0 到 n-1。
- 在选修某些课程之前需要一些先修课程。 例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示他们: [0,1]
- 给定课程总量以及它们的先决条件，判断是否可能完成所有课程的学习？

  ```
  输入: 2, [[1,0],[0,1]]
  输出: false
  解释: 总共有 2 门课程。学习课程 1 之前，你需要先完成课程 0；并且学习课程 0 之前，你还应先完成课程 1。这是不可能的。
  ```

#### Kahn 算法 - 广度优先

- ```js
  const {Queue} = require('./utils/structure/Structure')
  
  var canFinish = function(numCourses, prerequisites) {
    if(numCourses<=0) return false
    const len = prerequisites.length
    if(len===0) return true
    // 入度数组
    const requireCount = new Array(numCourses).fill(0)
    for (const prerequisite of prerequisites) {
      // course[0]对应的课程，需要前置课程，有入度
      requireCount[prerequisite[0]]++
    }
    const queue = new Queue()
    // 首先加入入度为 0 的结点
    for (let i = 0; i < numCourses; i++) {
      if (requireCount[i] == 0) {
        queue.enqueue(i);
      }
    }
    const res = []
    // 拓扑排序
    while(!queue.isEmpty()){
      const learned = queue.dequeue()
      res.push(learned)
      // 把邻边全部遍历一下
      for (const p of prerequisites) {
        if (p[1] == learned) {
          requireCount[p[0]]--
          if (requireCount[p[0]] == 0) queue.addLast(p[0])
        }
      }
    }
    return res.length === numCourses
  }
  ```
- 该算法的实现十分直观，关键在于需要维护一个入度为 0 的顶点的集合。
- 每次从该集合中取出一个顶点，将该顶点放入保存结果的 List 中。

  > 没有特殊的取出规则，随机取出也行，使用队列/栈也行，下同

- 紧接着循环遍历由该顶点引出的所有边，从图中移除这条边，同时获取该边的另外一个顶点，如果该顶点的入度在减去本条边之后为 0，那么也将这个顶点放到入度为 0 的集合中。然后继续从集合中取出一个顶点
- 如果图中存在环的话，不可能所有的节点入度都迭代到 0，环上的所有节点入度都会保持 1，所以已排序的节点个数会少于整体的节点个数
- 不存在的话则返回结果 List，此 List 中的顺序就是对图进行拓扑排序的结果。

**复杂度分析**

- 初始化入度为**0**的集合需要遍历整张图，检查每个节点和每条边，因此复杂度为 O(E+V);
- 然后对该集合进行操作，又需要遍历整张图中的，每条边，复杂度也为 O(E+V);
- 因此**Kahn**算法的复杂度即为**O(E+V)**。

#### 是否成环 - 深度优先 I

+ ```js
  function initializeColor(vertices){ 
    const color = [] 
    for (let i=0; i<vertices.length; i++){ 
      color[vertices[i]] = 'white' //{1} 
    } 
    return color 
  }
  let hasCycle = false
  var canFinish = function(numCourses, prerequisites) {
    if(numCourses<=0) return false
    const len = prerequisites.length
    if(len===0) return true
  
    // 初始化有向图
    const vertices = []
    // 维护一个邻接表，题目给的是前溯点，需要修正为后继点
    const adjList = new Map()
    for (let i = 0; i < numCourses; i++) {
      vertices[i] = i
      inAdjList.set(i,[])
    }
    // 逆邻接表,[1]是后继节点，[0]是前驱节点
    for (const p of prerequisites) {
      inAdjList.get(p[1]).push(p[0])
    }
    // 初始化颜色列表
    const color = initializeColor(vertices)
    // 因为此题并不需要排序，仅仅判断有无成环即可，所以无需维护栈
    for (let i = 0; i < vertices.length; i++) {
      if(color[vertices[i]]==='white'){
        dfsVisit(vertices[i],adjList,color)
      }
    }
    // 在遍历的过程中，一直 dfs 都没有遇到已经重复访问的结点，就表示有向图中没有环
    // 所有课程任务可以完成，应该返回 true
    return !hasCycle
  
    function dfsVisit(v,adjList,color){
      // 成环，没必要继续递归了
      if(hasCycle) return
      color[v] = 'grey'
      const predecessors = adjList.get(v)         
      for (let i=0; i<predecessors.length; i++){
        const u = predecessors[i]              
        if (color[u] === 'white'){         
          dfsVisit(u, adjList, color)
        }else if(color[u] === 'grey'){
          // An edge to a GRAY vertex represents a cycle
          hasCycle = true
        } 
      } 
      color[v] = 'black'
    }
  }
  let numCourses = 3,prerequisites = [[1,0]]
  
  console.log(canFinish(numCourses,prerequisites))
  ```
+ 照搬深度优先的模板，这种写法的缺点在于，已经知道图有环了，仍然在进行遍历，虽然马上就会退出，但这是没有必要的

#### 是否成环 - 深度优先 II

- 只需要稍微改写以下 dfsVisit 即可

  ```java
  function initializeColor(vertices){ 
    const color = [] 
    for (let i=0; i<vertices.length; i++){ 
      color[vertices[i]] = 'white' //{1} 
    } 
    return color 
  }
  var canFinish = function(numCourses, prerequisites) {
    if(numCourses<=0) return false
    const len = prerequisites.length
    if(len===0) return true
      
    const vertices = []
    const adjList = new Map()
    for (let i = 0; i < numCourses; i++) {
      vertices[i] = i
      adjList.set(i,[])
    }
    for (const p of prerequisites) {
      adjList.get(p[1]).push(p[0])
    }
    let color = initializeColor(vertices)
  	// 因为此题并不需要排序，仅仅判断有无成环即可，所以无需维护栈
    for (let i = 0; i < vertices.length; i++) {
      if(dfsVisit(vertices[i],adjList,color)){
        return false
      }
    }
    // 在遍历的过程中，一直 dfs 都没有遇到已经重复访问的结点，就表示有向图中没有环
    // 所有课程任务可以完成，应该返回 true
    return true
  
    function dfsVisit(v,adjList,color){
      // 访问了正在访问的节点，成环，没必要继续递归了
      if(color[v]==='grey') return true
      // 访问过的节点，说明有共同的后继点，但是不成环
      if(color[v]==='black') return false
  
      // 表示正在访问中
      color[v]='grey'
      const predecessors = adjList.get(v)         
      for (let i=0; i<predecessors.length; i++){
        const u = predecessors[i]  
         // 层层递归返回 true ，表示图中存在环
        if(dfsVisit(u,adjList,color)) return  true      
      } 
      // v 的所有后继点都访问完了，都没有存在环，则这个结点就可以被标记为已经访问结束
      color[v] = 'black'
      // false 表示图中不存在环
      return false
    }
  }
  let numCourses = 2,prerequisites = [[0,1],[1,0]]
  
  ```

console.log(canFinish(numCourses,prerequisites))

  ```
  
- 仅需要判断是否成环，无需排序

### [210. 课程表 II](https://leetcode-cn.com/problems/course-schedule-ii/)

#### 题目

+ 现在你总共有 n 门课需要选，记为 0 到 n-1。

+ 在选修某些课程之前需要一些先修课程。 例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示他们: [0,1]

+ 给定课程总量以及它们的先决条件，返回你为了学完所有课程所安排的学习顺序。

+ 可能会有多个正确的顺序，你只要返回一种就可以了。如果不可能完成所有课程，返回一个空数组。

  ```

  输入: `4, [[1,0],[2,0],[3,1],[3,2]]`

  输出: [0,1,2,3] or [0,2,1,3]

  解释: 总共有 4 门课程。要学习课程 3，你应该先完成课程 1 和课程 2。并且课程 1 和课程 2 都应该排在课程 0 之后。

  因此，一个正确的课程顺序是 [0,1,2,3] 。另一个正确的排序是 [0,2,1,3] 。

  ```
#### 深度优先

+ 这一次要输出顺序了，需要维护栈

  ```js
  var findOrder = function(numCourses, prerequisites) {
    // 初始化图
    const vertices = []
    const adjList = {}
    const color = []
    for (let i = 0; i < numCourses; i++) {
      vertices[i] = i
      adjList[i] = []
      color[i] = 'white'
    }
    // 栈先进后出，题目给的是前溯点，修正为后继点
    for (const p of prerequisites) {
      adjList[p[1]].push(p[0])
    }
    // 仅可以使用push和pop
    const stack = []
    // 随便一个节点开始遍历都是ok的
    /**
     * 只会遍历该节点的后继节点，当该节点遍历完成时，所以以该课程为先修课程的都已经入栈
     * 再遍历其他节点（可能是该节点的先修课程），也一定会在该节点之后入栈，不会影响到先修关系
     */
    for (const v of vertices) {
      // 成环，不可能完成课程，返回空数组
      if(dfsVisit(v,adjList,color,stack)) return []
    }
    let order = []
    // 遍历完毕，且没有成环,从栈顶学习到栈底即可
    for (let i = 0; i < numCourses; i++) {
      // 从栈顶弹出元素
      order.push(stack.pop())
    }
    return order
    // 返回值代表是否成环
    function dfsVisit(v,adjList,color,stack){
      if(color[v]==='grey') return true
      if(color[v]==='black') return false
  
      color[v] = 'grey'
      const successors = adjList[v]
      for (const u of successors) {
        // 层层递归返回 true ，表示图中存在环
        if(dfsVisit(u,adjList,color,stack)) return true
      }
      // v 的所有后继点都访问完了，都没有存在环，则这个结点就可以被标记为已经访问结束
      color[v] = 'black'
      // 压入栈中
      stack.push(v)
      // false 表示图中不存在环
      return false
    }
  }
  let numCourses = 2, prerequisites = [[1,0]]
  
  console.log(findOrder(numCourses,prerequisites))
  ```

**复杂度分析**

+ **时间复杂度**: O(N)O(N)，其中 NN 为课程数。我们需要要对森林中的所有结点执行完全的深度优先搜索。之所以是森林而不是图，是因为并非所有结点都连接在一起。也可能存在不连通的部分。
+ 空间复杂度: O(N)O(N), 递归栈占用的空间 (不是用于存储拓扑排序的栈)

#### Kahn 算法

+ ```js
  var findOrder = function(numCourses, prerequisites) {
    if(numCourses<=0) return []
    const len = prerequisites.length
    // 没有依赖，直接返回
    if(len===0){
      const res = []
      for (let i = 0; i < numCourses; i++) {
        res[i] = i
      }
      return res
    }
    // 尽可以使用push和shift
    const queue = []
    // 入度数组，统计入度
    const requireCount = new Array(numCourses).fill(0)
    for (const p of prerequisites) {
      requireCount[p[0]]++
    }
    // 找出所有入度为0的节点，入列
    for (let i = 0; i < requireCount.length; i++) {
      if(requireCount[i]===0) queue.push(i)
    }
    const topSort = []
    while(queue.length>0){
      const learned = queue.shift()
      topSort.push(learned)
      // 遍历该节点的全部邻边
      for (const p of prerequisites) {
        if(p[1]===learned){
          requireCount[p[0]]--
          if(requireCount[p[0]]===0) queue.push(p[0])
        }
      }
    }
    return topSort.length === numCourses?topSort:[]
  }
  ```
