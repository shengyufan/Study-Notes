[toc]

<div STYLE="page-break-after:always;"></div>

# 基本概念

## 数据结构

数据对象在计算机中的组织方式，包含逻辑结构和物理存储结构两部分

数据对象必定与一系列加在其上的操作相关联

## 抽象数据类型

数据类型

- 数据对象集

- 数据集合相关联的操作集

抽象：描述数据类型的方法不依赖于具体实现

- 与存放数据的机器无关
- 与数据存储的物理结构无关
- 与实现操作的算法和编程语言均无关

## 算法

算法包含几个要素

- 一个有限指令集
- 接受一些输入（有些情况下不需要输入）
- 产生输出
- 一定在有限步骤之后终止
- 每一条指令必须
  - 有充分明确的目标，不可以有歧义
  - 计算机能处理的范围之内
  - 描述应不依赖于任何一种计算机语言以及具体的实现手段

### 算法的衡量标准

- 空间复杂度：根据算法写成的程序在执行时**占用存储单元**的长度。这个长度往往与输入数据的规模有关。空间复杂度过高的算法可能导致使用的内存超限，造成程序非正常中断
- 时间复杂度：根据算法写成的程序在执行时**耗费时间**的长度。这个长度往往也与输入数据的规模有关。时间复杂度过高的低效算法可能导致我们在有生之年都等不到运行结果

复杂度的表示方式

1. $O(n)$ 表示复杂度的上界
2. $\Omega(n)$ 表示复杂度的下界
3. $\Theta(n)$ 表示上下界满足同样条件

# 线性结构

## 线性表

由同类型数据元素构成**有序序列**的线性结构

- 表中元素个数称为线性表的长度
- 线性表没有元素时，称为空表
- 表起始位置称表头，表结束位置称表尾

### 存储实现

1. 利用数组的连续存储空间顺序存放线性表的各元素
   - 查找（按值）：从头到尾依次遍历，时间复杂度为 $O(n)$
   - 插入：插入元素前将后面元素依次向后移动，时间复杂度为 $O(n)$
   - 删除：删除元素后将后面元素依次向前移动，时间复杂度为 $O(n)$
2. 不要求逻辑上相邻的两个元素物理上也相邻，通过链表建立起数据元素之间的逻辑关系
   - 查找（按值）：依次遍历链表元素，时间复杂度为 $O(n)$
   - 插入：遍历至插入位置前一个元素， 分别修改前一个元素和插入元素的指针，时间复杂度为 $O(n)$
   - 删除：遍历至删除位置前一个元素，修改其指针并释放空间，时间复杂度为 $O(n)$

### 广义表

广义表是线性表的推广。对于线性表而言， 所有元素都是基本的单元素，而广义表中，这些元素不仅可以是单元素也可以是另一个广义表

**多重链表**：链表中的节点可能同时隶属于多个链，多重链表中结点的指针域会有多个。但包含两个指针域的链表（如双向链表）并不一定是多重链表

## 堆栈

具有一定操作约束的线性表

- 只允许在表的一端（栈顶）进行插入和删除操作
- 具有后入先出（LIFO）的特性

### 存储实现

1. 由一个一维数组和一个记录栈顶元素位置的变量组成
   - 入栈：判断栈是否已满，如果没有则在栈顶压入元素
   - 出栈：判断栈是否为空，如果不是则将栈顶元素推出
2. 由链式存储实现，栈的链式存储结构实际上就是一个单链表，插入和删除操作只能在链栈的栈顶进行
   - 入栈：将元素插入到虚拟头节点之后
   - 出栈：判断链栈是否为空（虚拟头节点指向空），如果不是将虚拟头节点之后的元素推出

## 队列

具有一定操作约束的线性表

- 插入和删除操作：只能在一端插入，而在另一端删除
- 具有先入先出（FIFO）的特性

### 存储实现

1. 由一个一维数组和一个记录队列**头**元素位置的变量以及一个记录队列**尾**元素位置的变量组成
   - 入队：判断队列是否已满，如果没有则插入元素并更新队尾指针
   - 出队：判断队列是否为空，如果不是则删除元素并更新队首指针
2. 用一个单链表实现。插入和删除操作分别在链表的两头进行
   - 入队：将元素插入头节点之后
   - 出队：判断队列是否为空，如果不是则删除元素并更新队首指针

# 树

分层次的数据组织形式。对一棵非空树，其有一个根结点并且其余节点可分为多个**互不相交**的有限集合（子树）

一棵树无环。除了根结点外，每个结点有且仅有一个父结点。一棵 N 个结点的树有 N - 1 条边

## 二分查找

对于**有序**存放的多个元素，可以通过二分法进行查找，将时间复杂度从 $O(n)$ 降为 $O(logn)$

原理：通过构建一棵平衡二叉搜索树，所以查找次数一定不会超过树的深度（树的深度为 $log_{2}n + 1$）

### 边界条件

使用**开区间**写法，不需要思考加一减一等细节

#### 不存在重复元素

返回中间值（如果等于目标值）

```python
def f(a, target):
  	left = 0
    right = len(a) + 1
    while left + 1 < right:
      	mid = left + (right - left) // 2
        if a[mid] == target:
          	return mid
        elif a[mid] < target:
          	right = mid
        else:
          	left = mid
     
    return -1
```

#### 存在重复元素

找最小返回右侧（如果值满足条件）

```python
def f(a, target):
  	left = 0
    right = len(a) + 1
    while left + 1 < right:
      	mid = left + (right - left) // 2
        if target <= a[mid]:
          	right = mid
        else:
          	left = mid
    # 需要根据条件进行判断
    if a[right] == target:
      	return right
    
    return -1
```

找最大返回左侧（如果值满足条件）

```python
def f(a, target):
  	left = 0
    right = len(a) + 1
    while left + 1 < right:
      	mid = left + (right - left) // 2
        if target < a[mid]:
          	right = mid
        else:
          	left = mid
		
    if a[left] == target:
      	return left
    
    return -1
```

## 相关概念

结点的度（Degree）：结点的子树个数

树的度：树的所有结点中最大的度数

叶结点（Leaf）：度为 0 的结点

父结点（Parent）：有子树的结点是其子树的根结点的父结点

子结点（Child）：若 A 结点是B结点的父结点，则称 B 结点是 A 结点的子结点；子结点也称孩子结点

兄弟结点（Sibling）：具有同一父结点的各结点彼此是兄弟结点

路径和路径长度：从结点 $n_{1}$ 到 $n_{k}$ 的路径为一个结点序列 $\{n_{1}, n_{2}, ...., n_{k}\}$，其中 $n_{i}$ 是 $n_{i+1}$ 的父结点。路径所包含边的个数为路径的长度

祖先结点(Ancestor)：沿树根到某一结点路径上的所有结点都是这个结点的祖先结点

子孙结点(Descendant)：某一结点的子树中的所有结点是这个结点的子孙

结点的层次（Level）：规定根结点在 1 层，其它任一结点的层数是其父结点的层数加1

树的深度（Depth）：树中所有结点中的最大层次是这棵树的深度

## 二叉树

它是由根结点和称为其左子树和右子树的两个不相交的二叉树组成

特殊二叉树：满二叉树（除叶子节点外，其余节点都有两个子节点）和完全二叉树（除叶子节点外，其余节点要么有两个子节点要么没有子节点）

### 存储结构

1. 顺序存储：完全二叉树按从上至下、从左至右顺序存储
   - 非根结点（序号 i > 1）的父结点的序号是 i / 2（向下取整）
   - 结点（序号为 i ）的左孩子结点的序号是 2i，若2i <= n，否则没有左孩子
   - 结点（序号为 i ）的右孩子结点的序号是 2i+1，若2i +1<= n，否则没有右孩子
2. 链表存储：使用顺序存储一般二叉树会造成空间的浪费

### 遍历方式

**先序遍历**：遍历过程为根结点、左子树、右子树

**中序遍历**：遍历过程为左子树、根结点、右子树

**后序遍历**：遍历过程为左子树、右子树、根结点

**非递归遍历**：使用堆栈实现

**层序遍历**：使用队列实现。队首元素出队，将其左右子节点入队

通过两种遍历序列可以确定唯一的一棵二叉树，这两种遍历中一定包含中序遍历（可以根据根结点将序列分为左子树和右子树）

## 二叉搜索树

如果不为空，满足以下性质：

1. 非空左子树的所有键值小于其根结点的键值
2. 非空右子树的所有键值大于其根结点的键值
3. 左、右子树都是二叉搜索树

### [删除二叉搜索树中的节点](https://leetcode.cn/problems/delete-node-in-a-bst/)

## 平衡二叉树 AVL树

如果不为空，任一结点左、右子树高度差的绝对值不超过 1

给定结点数为 n 的AVL树的最大高度为 $O(log_{2}n)$

### 调整

| 失衡类型 | 描述     | 插入位置               | 调整方式               |
| -------- | -------- | ---------------------- | ---------------------- |
| LL型     | 左左失衡 | 新节点插在左子树的左边 | 右旋（Right Rotation） |
| RR型     | 右右失衡 | 新节点插在右子树的右边 | 左旋（Left Rotation）  |
| LR型     | 左右失衡 | 新节点插在左子树的右边 | 先左旋，再右旋         |
| RL型     | 右左失衡 | 新节点插在右子树的左边 | 先右旋，再左旋         |

右旋操作

```jav
    y                      x
   / \                   / \
  x   T3     =>         T1  y
 / \                       / \
T1 T2                     T2 T3
```

```python
def right_rotate(y):
    x = y.left
    T2 = x.right

    x.right = y
    y.left = T2

    return x
```

左旋操作

```java
  x                         y
 / \                       / \
T1  y       =>            x  T3
    / \                  / \
   T2 T3                T1 T2
```

```python
def left_rotate(x):
    y = x.right
    T2 = y.left

    y.left = x
    x.right = T2

    return y
```

[将二叉搜索树变平衡](https://leetcode.cn/problems/balance-a-binary-search-tree/)

## 堆 Heap

优先队列（**Priority Queue**）：特殊的“队列”，取出元素的顺序是依照元素的优先权（关键字）大小，而不是元素进入队列的先后顺序

堆的特性

- 结构性：用数组表示的**完全二叉树**
- 有序性：任一结点的关键字是其子树所有结点的最大值(或最小值)

最大堆（MaxHeap），也称“大顶堆”：根结点为最大值

最小堆（MinHeap），也称“小顶堆” ：根结点为最小值

### 插入

按照完全二叉树性质插入元素后，还可能需要调整堆，保证其有序性。时间复杂度为 $O(logn)$

### 删除

删除元素后，将最后一个元素交换至根结点中，然后还可能需要调整堆。时间复杂度为 $O(logn)$

## 哈夫曼树

带权路径长度（WPL）：设二叉树有 *n* 个叶子结点，每个叶子结点带有权值 $w_{k}$，从根结点到每个叶子结点的长度为 $l_{k}$，则每个叶子结点的带权路径长度之和就是 $WPL=\sum_{k=1}^{n}w_{k}*l_{k}$

哈夫曼树：WPL最小的二叉树。构造方法为每次将两个权值最小的节点合并为一棵树，根结点权值为这两个节点权值之和，时间复杂度为 $O(nlogn)$

特点：1）没有度为 1 的结点；2）n 个叶子结点的哈夫曼树共有 $2n - 1$ 个结点；3）哈夫曼树的任意非叶节点的左右子树交换后仍是哈夫曼树；4）对同一组权值，可能存在同构的两棵哈夫曼树

### 哈夫曼编码

需要避免二义性，解决方法为保证任何字符的编码都不是另一字符编码的前缀，即保证所有字符都出现在叶子节点上

如何检查是否存在二义性：根据每个字符的编码逐步建立一棵树，所有节点初始权重都为 0，每个编码最终达到的节点做标记（改变其权重），如果在构建过程中发现1）路径中包含已被标记过（带权重）的节点或2）最终节点有子节点，说明当前编码存在二义性

通过哈夫曼树构建方法可确定代码最小的编码

## 并查集

并查集可以判断两个节点是否属于同一个集合。用树结构表示集合，树中每个节点代表一个集合元素，其中子节点指向父节点（双亲表示法）

### 查找操作

查找某个元素所在的集合（集合用根结点表示）

```python
def find(a):
  if fa[a] != a:
    fa[a] = find(fa[a])
  return fa[a]
```

### 合并操作

分别找到两个元素所在集合的根结点，如果不同则将两个集合合并，即将其中一个根结点的父结点指针设置成另一个根结点的数组下标

```python
def union(a, b):
  ra = find(a)
  rb = find(b)
  if ra != rb:
    fa[rb] = ra
```

改进方法

1. 按大小合并：将小的集合合并到相对大的集合

```python
def union(a, b):
  # size存储集合元素，初始值都为1
  ra = find(a)
  rb = find(b)
  if ra == rb:
    return False
  if size[ra] < size[rb]:
    fa[ra] = rb
    size[rb] += size[ra]
  else:
    fa[rb] = ra
    size[ra] += size[rb]
  
  return True
```

2. 按秩合并：将高度比较低的树合并到另一棵树上

```python
def union(a, b):
  ra = find(a)
  rb = find(b)
  if ra == rb:
    return False
  
  if rank[ra] < rank[rb]:
    fa[ra] = rb
  elif rank[ra] > rank[rb]:
    fa[rb] = ra
  else:
    fa[ra] = rb
    rank[rb] += 1
  
  return True
```

# 图

表示多对多的关系，包含

- 一组顶点：通常用 **V** (Vertex) 表示顶点集合
- 一组边：通常用 **E** (Edge) 表示边的集合
  - 边是顶点对：$(v, w) \in E$ ，其中 $v$ , $w$ 为两个顶点 
  - 有向边 $<v, w>$ 表示从 v 指向 w 的边（单行线）
  - 不考虑重边和自回路

## 相关概念

无向图：图中边没有方向

有向图：图中边存在方向

完全图：任意两个顶点之间都存在边

邻接点：有边直接相连的顶点

度：从该点发出的边数为“出度”，指向该点的边数为“入度”

路径：v 到 w 的路径是一系列顶点的集合，其中任一对相邻的顶点间都有图中的边。路径的长度是路径中的边数（如果带权，则是所有边的权重和）。如果 v 到 w 之间的所有顶点都不同，则称简单路径

回路：起点等于重点的路径

连通：如果从 v 到 w 存在一条（无向）路径，则称 v 和 w 是连通的

连通图：图中任意两顶点均连通

连通分量：无向图的**极大**连通子图

- 极大顶点数：再加 1 个顶点就不连通了
- 极大边数：包含子图中所有顶点相连的所有边

强连通：有向图中顶点 v 和 w 之间存在双向路径，则称 v 和 w 是强连通的

强连通图：有向图中任意两顶点均强连通

强连通分量：有向图的极大强连通子图

## 表示方法

1. 邻接矩阵：二维数组表示无向图，若两个顶点之间存在边，则对应位置为 1，否则为 0
2. 一维数组：表示无向图用二维数组会浪费存储空间，可以使用一维数组节省空间，$g_{ij}$ 在数组中的位置是 $\frac{i \times (i + 1)}{2} + j$
3. 邻接表：邻接矩阵存储稀疏图会造成资源的浪费。可采用指针数组，对应矩阵每行一个链表，只存非 0 元素

## 图的遍历

深度优先搜索（DFS）：每次选择一个未被访问节点往下遍历，如果邻接点都被遍历过，回溯到上一层

广度优先搜索（BFS）：使用队列，每次将对首元素出队，并将其所有未被访问的邻接点入队

## 最短路

单源最短路径问题：从某固定源点出发，求其到所有其他顶点的最短路径

多源最短路径问题：求任意两顶点间的最短路径

### 无权图的单源最短路

使用 BFS，如果遍历过程中某个节点为终点，则直接退出循环

使用两个数组存储最短距离和路径中的上一个节点，在遍历过程中，如果距离不为 -1 说明该点没有被遍历过，然后更新该点的距离，并将其路径数组更新。这样退出循环之后可以得到距离和路径

### Dijkstra 算法

有权图的单源最短路算法（无负值权重）

实现步骤

1. 选源点到哪个节点近且该节点未被访问过
2. 该最近节点被标记访问过
3. 更新非访问节点到源点的距离（即更新minDist数组）

方法1：直接扫描所有未收录顶点，适用于稠密图

方法2：将距离存在最小堆中，适用于稀疏图

### 多源最短路算法

方法1：直接将单源最短路算法调用 $|v|$ 遍

方法2：Floyd 算法（无法处理负值圈）

Floyd 算法：动态规划，利用中间节点逐步更新任意两个点间的最短路径，因此需要三层遍历，第一层遍历为中间节点，第二层为起点，最后一层为终点

## 最小生成树

最小生成树的性质

- 一棵树：无回路，$V$ 个顶点一定有 $V - 1$ 条边
- 生成树：包含全部顶点，$V - 1$ 条边都在图里
- 边的权重和最小

**如果一个图有最小生成树，说明图是连通的**

### Prime 算法

1. 选距离生成树最近未被访问的节点
2. 最近节点加入生成树（可直接更新minDist 数组，使其变为 0，标记该点被访问了）
3. 更新非生成树节点到生成树的距离（即更新minDist数组）

适用于稠密图

### Kruskal 算法

1. 边的权值排序，因为要优先选最小的边加入到生成树里

2. 遍历排序后的边

   - 如果边首尾的两个节点在同一个集合，说明如果连上这条边图中会出现环

   - 如果边首尾的两个节点不在同一个集合，加入到最小生成树，并把两个节点加入同一个集合

适用于稀疏图

## 拓扑排序

如果图中从 v 到 w 有一条有向路径，则 v 一定排在 w 之前。满足此条件的顶点序列称为一个拓扑序

一个图有合理的拓扑序时，则必定是**有向无环图**（DAG）

拓扑序寻找方法：逐步将入度为 0 的顶点输出，然后将其邻接点的入度减一

拓扑排序算法可以用来检测有向图中是否存在环

# 排序

排序的稳定性：任意两个相等的数据，排序前后的相对位置不发生改变

## 冒泡排序

1. 比较相邻的元素。如果第一个比第二个大，就交换它们两个
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数
3. 针对所有的元素重复以上的步骤，除了最后一个
4. 重复步骤 1~3，直到排序完成

```python
def bubble_sort(arr, n):
    for i in range(1, n):
        # 标识是否存在交换操作
        flag = True
        for j in range(n - i):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                flag = False
        if flag:
            break
    return arr
```

## 插入排序

1. 从第一个元素开始，该元素可以认为已经被排序
2. 取出下一个元素，在已经排序的元素序列中**从后向前**扫描
3. 如果该元素（已排序）大于新元素，将该元素移到下一位置
4. 重复步骤 3，直到找到已排序的元素小于或者等于新元素的位置
5. 将新元素插入到该位置后
6. 重复步骤 2~5

```python
def insertion_sort(arr, n):
    for i in range(1, n):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j = j - 1
        arr[j + 1] = key
    return arr
```

## 希尔排序

1. 选择一个增量序列 $\lbrace t_1, t_2, \dots, t_k \rbrace$，其中 $t_i \gt t_j, i \lt j, t_k = 1$. 通常选择增量 $gap=length/2$，缩小增量继续以 $gap = gap/2$ 的方式
2. 按增量序列个数 k，对序列进行 k 趟排序；
3. 每趟排序，根据对应的增量 $t$，将待排序列分割成若干长度为 $m$ 的子序列，分别对各子表进行直接插入排序。仅增量因子为 1 时，整个序列作为一个表来处理，表长度即为整个序列的长度

```python
def shell_sort(arr, n):
    gap = n // 2
    while gap > 0:
        for i in range(gap, n):
            temp = arr[i]
            j = i
            while j >= gap and arr[j - gap] > temp:
                arr[j] = arr[j - gap]
                j -= gap
            arr[j] = temp
        gap = gap // 2
		return arr
```

## 选择排序

1.   首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置
2.   再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾
3.   重复第 2 步，直到所有元素均排序完毕

```python
def selection_sort(arr, n):
  	# 从小到大排序
    for i in range(n):
        minIndex = i
        for j in range(i + 1, n):
            if arr[j] < arr[minIndex]:
                minIndex = j
        
        if minIndex != i:
            arr[i], arr[minIndex] = arr[minIndex], arr[j]
    return arr
```

## 堆排序

1.   将初始待排序列 $(R_1, R_2, \dots, R_n)$ 构建成**大顶堆**，此堆为初始的无序区
2.   将堆顶元素 $R_1$ 与最后一个元素 $R_n$ 交换，此时得到新的无序区 $(R_1, R_2, \dots, R_{n-1})$ 和新的有序区 $R_n$, 且满足 $R_i \leqslant R_n (i \in 1, 2,\dots, n-1)$
3.   由于交换后新的堆顶 $R_1$ 可能违反堆的性质，因此需要对当前无序区 $(R_1, R_2, \dots, R_{n-1})$ 调整为新堆，然后再次将 $R_1$ 与无序区最后一个元素交换，得到新的无序区 $(R_1, R_2, \dots, R_{n-2})$ 和新的有序区 $(R_{n-1}, R_n)$。不断重复此过程直到有序区的元素个数为 $n-1$，则整个排序过程完成

```python
def heapify(arr, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2
    
    if left < n and arr[left] > arr[largest]:
        largest = left

    if right < n and arr[right] > arr[largest]:
        largest = right
    
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)

def heap_sort(arr, n):
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)
    
    return arr
```

## 归并排序（常用于外排序）

1.   如果输入内只有一个元素，则直接返回，否则将长度为 $n$ 的输入序列分成两个长度为 $n/2$ 的子序列
2.   分别对这两个子序列进行归并排序，使子序列变为有序状态
3.   设定两个指针，分别指向两个已经排序子序列的起始位置
4.   比较两个指针所指向的元素，选择相对小的元素放入到合并空间（用于存放排序结果），并移动指针到下一位置
5.   重复步骤 3 ~ 4 直到某一指针达到序列尾
6.   将另一序列剩下的所有元素直接复制到合并序列尾

```python
def merge_sort(arr):
    # Base case: if array has 0 or 1 element, it's already sorted
    if len(arr) <= 1:
        return arr
    
    # Divide the array into two halves
    mid = len(arr) // 2
    left_half = arr[:mid]
    right_half = arr[mid:]
    
    # Recursively sort both halves
    left_half = merge_sort(left_half)
    right_half = merge_sort(right_half)
    
    # Merge the sorted halves
    return merge(left_half, right_half)

def merge(left, right):
    result = []
    left_idx = right_idx = 0
    
    # Compare elements from both arrays and add smaller one to result
    while left_idx < len(left) and right_idx < len(right):
        if left[left_idx] <= right[right_idx]:
            result.append(left[left_idx])
            left_idx += 1
        else:
            result.append(right[right_idx])
            right_idx += 1
    
    # Add remaining elements
    result.extend(left[left_idx:])
    result.extend(right[right_idx:])
    
    return result
```

## 快速排序

1.   从序列中**随机**挑出一个元素，做为 “基准”（pivot）
2.   重新排列序列，将所有比基准值小的元素摆放在基准前面，所有比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个操作结束之后，该基准就处于数列的中间位置。这个称为分区（partition）操作
3.   递归地把小于基准值元素的子序列和大于基准值元素的子序列进行快速排序

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    
    # Choose pivot (here using the first element)
    pivot = arr[0]
    
    # Partition array into elements less than, equal to, and greater than pivot
    less = [x for x in arr[1:] if x < pivot]
    equal = [x for x in arr if x == pivot]
    greater = [x for x in arr[1:] if x > pivot]
    
    # Recursively sort subarrays and combine results
    return quick_sort(less) + equal + quick_sort(greater)
```

## 计数排序

1.   找出数组中的最大值 `max`、最小值 `min`
2.   创建一个新数组 `C`，其长度是 `max-min+1`，其元素默认值都为 0
3.   遍历原数组 `A` 中的元素 `A[i]`，以 `A[i] - min` 作为 `C` 数组的索引，以 `A[i]` 的值在 `A` 中元素出现次数作为 `C[A[i] - min]` 的值
4.   对 `C` 数组变形，**新元素的值是该元素与前一个元素值的和**，即当 `i>1` 时 `C[i] = C[i] + C[i-1]`
5.   创建结果数组 `R`，长度和原始数组一样
6.   **从后向前**遍历原始数组 `A` 中的元素 `A[i]`，使用 `A[i]` 减去最小值 `min` 作为索引，在计数数组 `C` 中找到对应的值 `C[A[i] - min]`，`C[A[i] - min] - 1` 就是 `A[i]` 在结果数组 `R` 中的位置，做完上述这些操作，将 `count[A[i] - min]` 减小 1

```python
def counting_sort(arr):
    # Handle empty array
    if not arr:
        return []
    
    # Find range of elements
    min_val = min(arr)
    max_val = max(arr)
    range_of_elements = max_val - min_val + 1
    
    # Create counting array and output array
    count = [0] * range_of_elements
    output = [0] * len(arr)
    
    # Store count of each element
    for num in arr:
        count[num - min_val] += 1
    
    # Store cumulative count (position of each element in sorted array)
    for i in range(1, len(count)):
        count[i] += count[i - 1]
    
    # Build output array
    # Process array in reverse to maintain stability
    for i in range(len(arr) - 1, -1, -1):
        output[count[arr[i] - min_val] - 1] = arr[i]
        count[arr[i] - min_val] -= 1
    
    return output
```

## 桶排序

1.   设置一个 BucketSize，作为每个桶所能放置多少个不同数值
2.   遍历输入数据，并且把数据依次映射到对应的桶里去
3.   对每个非空的桶进行排序，可以使用其它排序方法，也可以递归使用桶排序
4.   从非空桶里把排好序的数据拼接起来

```python
def bucket_sort(arr):
    # Edge cases
    if not arr:
        return []
    
    # Create buckets
    n_buckets = len(arr)
    buckets = [[] for _ in range(n_buckets)]
    
    # Distribute elements into buckets
    for num in arr:
        # Assuming numbers are in range [0, 1)
        bucket_idx = int(n_buckets * num)
        # Handle edge case for num = 1.0
        bucket_idx = min(bucket_idx, n_buckets - 1)
        buckets[bucket_idx].append(num)
    
    # Sort individual buckets
    for bucket in buckets:
      	# use any sort algorithm
        bucket.sort()
    
    # Concatenate all buckets to get sorted array
    result = []
    for bucket in buckets:
        result.extend(bucket)
    
    return result
```

## 基数排序

1.   取得数组中的最大数，并取得位数，即为迭代次数 $N$（例如：数组中最大数值为 1000，则 $N=4$）
2.   `A` 为原始数组，从最低位开始取每个位组成 `radix` 数组
3.   对 `radix` 进行计数排序（利用计数排序适用于小范围数的特点）
4.   将 `radix` 依次赋值给原数组
5.   重复 2~4 步骤 $N$ 次

```python
def counting_sort_by_digit(arr, exp):
    n = len(arr)
    output = [0] * n
    count = [0] * 10  # 0-9 digits
    
    # Count occurrences of each digit at the current place value
    for i in range(n):
        index = (arr[i] // exp) % 10
        count[index] += 1
    
    # Change count[i] so that it contains the actual
    # position of this digit in output[]
    for i in range(1, 10):
        count[i] += count[i - 1]
    
    # Build the output array
    for i in range(n - 1, -1, -1):  # Process in reverse for stability
        index = (arr[i] // exp) % 10
        output[count[index] - 1] = arr[i]
        count[index] -= 1
    
    # Copy the output array to arr
    for i in range(n):
        arr[i] = output[i]
        
def radix_sort(arr):
    # Find the maximum number to know number of digits
    if not arr:
        return []
    
    max_num = max(arr)
    exp = 1  # Start with least significant digit
    
    # Do counting sort for every digit
    while max_num // exp > 0:
        counting_sort_by_digit(arr, exp)
        exp *= 10
    
    return arr
```

## 表排序

每个排序项目不是数字，而是大容量的元素（如书本）。表排序是一种间接排序算法，只排序指向元素的索引

可以使用插入排序等常规算法得到索引的有序序列（使用表存储当前位置应该存放的原始索引 $T[a[i]] = i$）

**N 个数字的排列由若干个独立的环组成**

## 算法比较

| 排序算法 | 时间复杂度（平均） | 时间复杂度（最差） | 时间复杂度（最好） | 空间复杂度 | 排序方式 | 稳定性 |
| -------- | ------------------ | ------------------ | ------------------ | ---------- | -------- | ------ |
| 冒泡排序 | $O(n^2)$           | $O(n^2)$           | $O(n)$             | $O(1)$     | 内部排序 | 稳定   |
| 选择排序 | $O(n^2)$           | $O(n^2)$           | $O(n^2)$           | $O(1)$     | 内部排序 | 不稳定 |
| 插入排序 | $O(n^2)$           | $O(n^2)$           | $O(n)$             | $O(1)$     | 内部排序 | 稳定   |
| 希尔排序 | $O(nlogn)$         | $O(n^2)$           | $O(nlogn)$         | $O(1)$     | 内部排序 | 不稳定 |
| 归并排序 | $O(nlogn)$         | $O(nlogn)$         | $O(nlogn)$         | $O(n)$     | 外部排序 | 稳定   |
| 快速排序 | $O(nlogn)$         | $O(n^2)$           | $O(nlogn)$         | $O(logn)$  | 内部排序 | 不稳定 |
| 堆排序   | $O(nlogn)$         | $O(nlogn)$         | $O(nlogn)$         | $O(1)$     | 内部排序 | 不稳定 |
| 计数排序 | $O(n+k)$           | $O(n+k)$           | $O(n+k)$           | $O(k)$     | 外部排序 | 稳定   |
| 桶排序   | $O(n+k)$           | $O(n^2)$           | $O(n+k)$           | $O(n+k)$   | 外部排序 | 稳定   |
| 基数排序 | $O(n×k)$           | $O(n×k)$           | $O(n×k)$           | $O(n+k)$   | 外部排序 | 稳定   |

# 散列查找

散列查找法的两项基本工作：1）计算位置：构造散列函数确定关键词存储位置；2）解决冲突：应用某种策略解决多个关键词位置相同的问题

## 散列表

基本思想：以关键字 key 为自变量，通过一个确定的函数 h（散列函数），计算出对应的函数值 h(key)，作为数据对象的存储地址

可能不同的关键字会映射到同一个散列地址上，即 $h(k_1) = h(k_2), k_1 \neq k_2$，称为冲突(Collision)。因此需要某种冲突解决策略

设散列表空间大小为 m，填入表中元素个数是 n，则称 $\frac{n}{m}$ 为散列表的装填因子（Loading Factor）

## 冲突处理

开放地址法：一旦产生了冲突（该地址已有其它元素），就按某种规则去寻找另一空地址

-   线性探测：以增量序列 1，2，…，（TableSize -1）循环试探下一个存储地址
-   平方探测：以增量序列 $1^2$，$-1^2$，$2^2$，$-2^2$，...，$q^2$，$-q^2$ 且 $q \leq \lfloor TableSize / 2 \rfloor$ 循环试探下一个存储地址。如果散列表长度 TableSize 是某个 $4k + 3$（k 是正整数）形式的素数时，平方探测法就可以探查到整个散列表空间
-   双散列：$d_i$ 为 $i * h_{2}(k)$，$h_{2}()$ 为另一个散列函数，探测序列为 $h_{2}(k)$，$2h_{2}(k)$，$3h_{2}(k)$，...。注意对任意的 key，$h_{2}(key) \neq 0$。同时探测序列还应该保证**所有的散列存储单元**都应该能够被探测到，因此常用 $h_{2}(k) = p - (k \ mod \ p)$（p为素数，并且小于Tablesize）

链地址法：将相应位置上冲突的所有关键词存储在同一个单链表中

# 动态规划

动态规划本质是给定一个问题，将其拆分成**子问题**，解决子问题后反推得到原始问题的解

## 记忆化搜索

采用递归思想

-   递：分解问题
-   归：得到答案

注意：分解得到的子问题可能有很多相同的情形。如果采用原始递归方法，则每种情况都需要计算

因此可以将子问题的结果保存到数据结构中。保存的目的是为了**减少重复计算**，加速问题处理

## 递推

递归是一个自顶向下的过程，但其实只有归才真正是得到最终结果的过程，因此考虑**自底向上**的方法，也就是递推（动态规划）

将记忆化搜索翻译成递推

1.   将 `dfs` 函数（递归函数）变为 `f` 数组（状态数组）
2.   将递归过程变为循环过程
3.   将递归的边界条件（底）设为数组初始值

状态数组表示子集最优结果

# 常见算法

## KMP 算法

串：线性存储的一组数据（默认是字符）

KMP 算法解决串的模式匹配问题，即在字符串中返回模式串第一次出现的位置

```python
def buildNext(next, pattern):
    n = len(pattern)
    next[0] = -1
    for j in range(1, n):
        i = next[j - 1]
        while i >= 0 and pattern[j] != pattern[i + 1]:
            i = next[i]
        if pattern[j] == pattern[i + 1]:
           next[j] = i + 1
        else:
          	next[j] = -1  
```

next 数组表示前一个最长相同子串的最后位。例如 `a b a` 的next 数组为 `-1 -1 0`，对于第二个 `a` 所对应的 `next[2] = 0` 表示它的前一个最长相同子串的最后位置为 `0`，即第一个 `a`

next 数组的作用在于假如出现不匹配的问题，可以跳过多个位置。例如 `a b a b c` 的 next 数组为 `-1 -1 0 1 -1`，当发现 `c` 无法匹配时，判断前一个位置的 next 数值 + 1 （第二个 `a`） 是否匹配，如果匹配则两个指针同时后移，如果不匹配则继续判断前一个位置的 next 数值 + 1 是否匹配

```python
def match(s, p):
  	next = [0] * len(p)
    buildNext(next, p)
    p_idx = 0
    s_idx = 0
    while s_idx < len(s) and p_idx < len(p):
      	if s[s_idx] == p[p_idx]:
          	s_idx += 1
            p_idx += 1
        elif p_idx > 0:
            p_idx = next[p_idx - 1] + 1
        else:
          	s_idx += 1
     
    return s_idx - len(p) if p_idx == len(p) else -1
```

## 快速幂

# 习题

## 最大子数组和

给定一个数组，找到数组中和最大的子数组（子数组定义为数组中连续的一段区间）

解决思路：Kadane 算法，使用前缀和思想。从前向后遍历整个数组，每次更新最大值。如果某一次和变为负数，则将总和设为 0 重新开始计算（负数无法使后续相加结果变大，因此抛弃前面的子数组）

## 堆栈实现

使用一个数组实现两个堆栈，要求最大地利用数组空间

解决思路：让两个堆栈分别从数组的两头开始向中间扩展；两个栈的栈顶指针相遇时，表示数组满了

## 中缀表达式求值

使用堆栈保存运算符，将中缀表达式转为后缀表达式，然后进行计算

从头到尾读取中缀表达式的每个对象，对不同对象按不同的情况处理

- 运算数：直接输出；
- 左括号：压入堆栈；
- 右括号：将栈顶的运算符弹出并输出，直到遇到左括号（出栈，不输出）；
- 运算符：
  - 若优先级大于栈顶运算符时，则把它压栈；
  - 若优先级小于等于栈顶运算符时，将栈顶运算符弹出并输出；再比较新的栈顶运算符，直到该运算符大于栈顶运算符优先级为止，然后将该运算符压栈；
- 若各对象处理完毕，则把堆栈中存留的运算符一并输出

## [K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

解决思路：计算链表长度，如果长度小于 $K$，则无法翻转链表。另外如果 $K=1$ 那么链表也不需要翻转

设置两个指针

- prev 指向反转这一段的末尾

- curr 指向反转这一段后续的下一个节点

同时还需要另一个临时变量存储 curr 的下一个节点，这样才能进行链表翻转

## [翻转等价二叉树](https://leetcode.cn/problems/flip-equivalent-binary-trees/)

解决思路：使用递归法判断，首先判断在根结点上判断是否值相等或者两棵左右子树的情况，如果在根结点上无法判断，则交替判断两棵树的左右子树

## 判断是否为同一棵二叉搜索树

对于不同序列可能会构成同一棵二叉搜索树，判断两个序列是否会构造出同一棵二叉搜索树

解决思路：1）构造两棵树，直接比较；2）不构造树，采用递归法，首先判断第一个数字，即根结点是否相同，若相同，则可以根据根结点将数组划分比根结点小和比根结点大两部分，然后递归判断每个部分是否相同；3）通过第一个序列构造树，然后第二个序列进行比较。比较方法是在树中依次查找第二个序列的元素，判断从根结点到该结点的路径上是否包含之前没有出现过的数字（相同二叉搜索树保证了相对顺序性）

## 再次遍历一颗树

中序二叉树遍历可以用堆栈以非递归方式（push 和 pop 操作）实现。给定一个序列，返回其后序遍历结果

解决思路：递归法。从前序（push）遍历中，可以得到根结点，然后从中序（pop）遍历中，根据根结点划分为左右子树分别解决

## 完全二叉搜索树

二叉搜索树 (BST) 被递归定义为具有以下属性的二叉树：节点的左子树仅包含键小于节点键的节点。节点的右子树仅包含键大于或等于节点键的节点。左子树和右子树都必须是二叉搜索树。完全二叉树 (CBT) 是一棵完全填充的树，底层可能除外，底层从左到右填充。现在给定一个不同的非负整数键序列，如果要求树也必须是 CBT，则可以构造唯一的 BST。你应该输出这棵 BST 的层序遍历序列

解决思路：利用完全二叉树的性质，计算出左子树和右子树中节点的个数，从而决定了根结点的值。然后递归解决左右子树

左子树的节点个数：1）根据节点总个数 $N$ 计算出除最后一层的深度 $H = \lfloor log_{2}(N + 1) \rfloor$；2）根据公式 $2^{H} - 1 + X = N$ 计算出 最下一层节点总数 $X$，将其和 $2^{H - 1}$ 进行比较，较小的值 $X_{l}$ 为左子树最下一层节点个数；3）利用计算结果，根据 $L = 2^{H - 1} - 1 + X_{l}$ 求出左子树总节点数量 $L$

## 关键路径问题

关键路径是指 从起点到终点所经历的最长路径（表示项目工期最长），路径上的所有活动都不能被延迟，否则整个项目就会延期

解决思路：1）使用拓扑排序方法确定活动的先后顺序；2）正向遍历计算每个时间的最早发生时间；3）反向遍历计算每个事件的最迟发生时间；4）如果两个时间结果相等，则该活动是关键活动，沿关键活动走出的最长路径，即为关键路径

## 插入还是归并排序

给定序列，判断其使用的插入排序还是归并排序，然后完成排序过程

解决思路：插入排序序列表现为前面有序，后面没变化，而归并排序为分段有序。因此从左向右扫描，直到发现顺序不对，跳出循环，然后从跳出地点继续向右扫描，与原始序列比对，发现不同则判断为不为插入排序

判断归并段长度：假设归并段长度为 $l$ （$l = 2^k$） ，则说明每个长度为 $l$ 的子序列一定是有序，需要考虑归并段长度是否可能为 $2l$。因此需要判断第一段子序列尾部与第二段子序列首部，第三段子序列尾部与第四段子序列首部是否有序，直到找到不满足的情况，退出循环时的长度就是当前归并段长度

