---
{"dg-publish":true,"permalink":"/programming/basic/data-structure/tree/"}
---


2020-09-09

2022-08-05

# 常问的树面试问题

- 找到一个二叉树的高度
- 找到一个二叉搜索树中第 k 个最大值
- 找到距离根部“k”个距离的节点
- 找到一个二叉树中给定节点的祖先（ancestors）

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

# 同时遍历

## 二叉树相等

### 题目

- Given two binary trees, write a function to check if they are equal or not.
- Two binary trees are considered equal if they are structurally identical and the nodes have the same value.

### 递归法

- 判断两棵树是否相同，最直接的方法是用递归，假设两棵树的根节点分别是 nodeOne、nodeTwo，则它们相同的前提是 nodeOne 和 nodeTwo 的值相同，并且它们的左右子树也分别相同，显然对左右子树可以递归地调用原函数。

  ```java
  function isEqual(m,n){
    if(m === null && m === null) return true
  	if(m === null || n === null) return false // 其中一个为空
    if(m.key === n.key){
      return isEqual(m.left,n.left) && isEqual(m.right,n.right)
    }
    return false
  }
  BinarySearchTree.isEqual = isEqual
  ```

### 迭代法

- 运用迭代法的话，只需要维护一个栈，如果 nodeOne、nodeTwo 的值相同，则依次将 nodeOne->left，nodeTwo->left ，nodeOne->right，nodeTwo->right 入栈，然后下一次循环开始时，就判断栈顶的 nodeOne->right 和 nodeTwo->right 的值是否相同，相同的话，再把它们的左右孩子依次入栈，循环以上过程，直到碰到值不相等或者栈为空。【迭代法的算法流程可参考下面图解】

  ```java
  function isEqual(m,n){
    const stack = new Stack()
    stack.push(m)
    stack.push(n)
    while(!stack.isEmpty()){
      const p = stack.pop()
      const q = stack.pop()
      if(p===null && q===null) continue
      if(p===null || q===null) return false // 其中一个为空
      if(p.key !== q.key) return false
  
      stack.push(p.left)
      stack.push(q.left)
      stack.push(p.right)
      stack.push(q.right)
    }
    return true
  }
  ```

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

# 遍历

## 根据遍历确定树

- 知道前序中序、中序后序遍历序列，那么可以唯一确定一棵二叉树，但是知道前序后序遍历序列就不一定能唯一确定一棵二叉树。
- 链接：https://www.nowcoder.com/questionTerminal/9fb40807179a4fe2bcd12abcaabda891?toCommentId=45662
- 简单解法： 先序序列得到 根节点为 A，根节点为 A，再根据中序序列得到，B 在 A 的右子树中，此时可以推出：后序序列最后两个是 BA。
- 再看，A 的左子树先序序列为 DCEFGH，得到左子树的根为 D，所以后序序列的倒数第三个是 D，得到 DBA，只有 D 符合。

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

- ```java
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

- ```js
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
- ```java
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

+ ```js
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
