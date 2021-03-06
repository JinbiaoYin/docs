# 树和二叉树

## 树的基本术语
- 结点：树中的每一个独立单元。
- 结点的度：结点拥有的子树数称为结点的度。例如叶子结点的度为0，它没有子树。
- 树的度：树的度是树内各结点度的最大值。
- 叶子：度为 0 的结点称为叶子结点或终端结点。
- 非终端结点：度不为0的结点。
- 双亲和孩子：结点子树的根称为该结点的孩子，相应的，该结点是其子树根的双亲（也称为父结点）。
- 兄弟：同一个双亲的孩子互称兄弟。
- 祖先：从根到该结点的必经的所有结点。
- 子孙：以该结点为根的所有结点都是该结点的子孙。
- 层次：结点的层次从根开始定义，根为第一层。
- 堂兄弟：双亲在同一层的结点互为堂兄弟。
- 树的深度：树中结点的最大层次称为树的深度或高度。
- 有序树和无序树：如果将树中结点的各子树看成从左到右是有次序的，则称该树为有序树，否则为无序树。
- 森林：是 m(m>=0) 颗互不相交的树的集合。

## 二叉树
二叉树是 n(n>=0) 个结点所构成的集合，它可以为空树，也可以是非空树。对于非空树，必须满足两个条件才是二叉树
1. 有且仅有一个根节点  
2. 除根节点外的其余结点分为两个互不相交的子集，称为左子树和右子树

### 二叉树和树的区别
1. 二叉树每个结点至多只有两颗子树（即不存在度大于2的结点）。  
2. 二叉树的子树有左右之分，其次序不能颠倒。

### 二叉树的五种形态
1. 空二叉树。   
2. 只有一个根节点。  
3. 左子树为空。  
4. 右子树为空。  
5. 左右子树都不为空。  

## 二叉树的性质和特点
**性质1**  在二叉树的第 i(i>=1) 层上至多有 2^(i-1)个结点。

**性质2**  深度为 k 的二叉树至多有 2^k - 1 个结点

**性质3**  对任意一个二叉树，如果叶子结点数为 n0 ，度为 2 的结点数为 n2 ，则 n0 = n2 + 1

**满二叉树**  ：最后一层全满的二叉树，深度为 k 含有 2^k - 1 个结点。

**完全二叉树** ： 深度为 k ，有 n 个结点的二叉树，每一个结点都与深度为 k 的满二叉树中编号为 1 至 n 的结点一一对应。

## 二叉树的存储结构
二叉树的存储结构可采用顺序存储和链式存储两种方式。

顺序存储结构使用一组地址连续的存储单元来存储元素的数据。如同数组一般。这对于完全二叉树是完全可行的，但是对于非完全二叉树，由于中间存在空结点，就会造成空间的浪费。因此采用链式存储。

完全二叉树：  
| 1 | 2 | 3 | 4 | 5  | 6 | 7 | 8 | 9 | 10 | 11 | 12 |

一般二叉树（0表示空结点）
| 1 | 2 | 3 | 4 | 5  | 0 | 0 | 0 | 0 | 0 | 6 | 7 |


链式存储结构中，根据我们使用可分成二叉链表和三叉链表两种，我们在每个结点结构中增加两个指针域，分别指向它的左孩子结点和右孩子结点，链表的头指针指向这个二叉链表的根节点。容易得知，在含有 n 个结点的二叉链表中有 n+1 个指针域。
而有时为了方便根据孩子结点找到它的父结点，我们可在每个结点结构中再增加一个指针域，用来指向它的父结点。这就是三叉链表。

## 遍历二叉树
先序遍历：  
1. 访问根节点；
2. 先序遍历左子树；  
3. 先序遍历右子树；

![先序遍历](https://img-blog.csdn.net/20150711163642225)

中序遍历：
1. 中序遍历左子树；  
2. 访问根结点；  
3. 中序遍历右子树；

![中序遍历](https://img-blog.csdn.net/20150711163947878)

后序遍历：
1. 后序遍历左子树；
2. 后序遍历右子树；
3. 访问根节点；

![后序遍历](https://img-blog.csdn.net/20150711164230063)

先序，中序和后序是根据访问根节点的顺序来判断的。

**注意**  
1）已知 前序遍历序列 和 中序遍历序列，可以唯一确定一颗二叉树    
2）已知 中序遍历序列和 后序遍历序列，可以唯一确定一颗二叉树 

===========================
已知一颗二叉树的中序序列和后序序列分别是BDCEAFHG和DECBHGFA，请画出这颗二叉树。
1. 由后序遍历特征，根结点必在后序序列尾部，即根结点是A。
2. 由中序遍历特征，根结点在中间，因此左子树是BDCE，右子树是FHG。
3. 由后序遍历特征DECB可知B为根结点，由先序遍历特征BDCE可知DCE为右子树。
4. 再以DEC可知C为根结点，由DCE可知，D为左子树，E为右子树。
5. 同样的道理看中序序列FHG和后序序列HGF。

## 哈夫曼树
哈夫曼树又称最优树，是一类带权路径长度最短的树。

### 基本概念
- 路径：从树中一个结点到另一个结点之间的分支构成这两个结点之间的路径。
- 路径长度：路径上的分支数目称作路径长度。
- 树的路径长度：从树根到每一结点的路径长度之和。
- 权：对实体的某个或某些属性的数值化描述。
- 结点的带权路径长度：从该结点到树根之间的路径长度与结点上权的乘积。
- 树的带权路径长度：树中所有叶子结点的带权路径长度之和。
- 哈夫曼树：m个权值构造出一个n个叶子结点的二叉树，带权路径最短的二叉树叫做最优二叉树或哈夫曼树。

### 哈夫曼树的构造算法
在构造哈夫曼树时，首先选择权小的，这样保证权大的离根较近。这样一来自然得到最小带权路径长度，这种生产算法是一种典型的贪心法。