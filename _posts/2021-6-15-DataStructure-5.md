---
layout:     post
title:      "数据结构"
subtitle:   "第五章：树"
date:       2021-6-15
author:     "Burt"
header-style: text 
mathjax: true
tags:
    - CS
    - Data Structure
---





## 一、基本概念

树其实是一种递归性质的数据结构

### 1.概念

根节点、边、分支结点、叶子结点

空树：结点数为0的树

非空树：至少有且仅有一个根节点

无后继的结点为**叶子结点**（终端结点）

有后继的结点为**分支结点**（非终端结点）

除了根结点外，任何一个结点都**有且仅有一个前驱**

根节点的子树



### 2.术语

路径，只能从上往下

路径长度：经过几条边



属性：

- 结点的层次（深度）：从上往下数。默认从1开始，有些会从当成0开始
- 结点的高度：从下往上数
- 树的高度（深度）：总共多少层
- 结点的度：该结点几个孩子（分支）
- 树的度：结点的度的最大值



有序树：逻辑上看，树中结点的各子树从左至右是**有**次序的，**不能**互换

无序树：逻辑上看，树中结点的各子树从左至右是**无**次序的，**可以**互换



森林：是m（m>0）棵互不相交的树的集合

考点：森林和树相互转换的问题



### 3.性质

- 结点数 = 总度数 + 1

- 度为m的树、m叉树的区别

  - 度为m的树

    - 任意结点的度≤m（最多m个孩子）
    - 至少有一个结点度为m（有m个孩子）
    - 一定是非空树，至少有m+1个结点
    - 第i层至多有m<sup>i-1</sup>个结点（i ≥ 1）
    - 高度为h的度为m的树至少有h+m-1个结点

  - m叉树

    - 任意结点的度≤m（最多m个孩子）

    - 允许所有结点的度都 < m

    - 可以是空树

    - 第i层至多有m<sup>i-1</sup>个结点（i ≥ 1）

    - 高度为h的m叉树至多有$\frac{m^h-1}{m-1}$个结点

    - 高度为h的m叉树至少有h个结点

    - n个结点的m叉树的最小高度为 log<sub>m</sub>(n(m-1)+1)



### 4.存储



**双亲表示法（顺序存储）**：每个结点中保存指向双亲的指针

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fzcdll.github.io%2Fimages%2Fparent-tree.png&refer=http%3A%2F%2Fzcdll.github.io&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626415571&t=9516cc645e0c4ce8e9546cf600474a7c" style="zoom:80%;" />



根结点固定存放在0，-1表示没有双亲

- 优点：查指定结点的双亲很方便
- 缺点：查指定结点的孩子只能从头遍历



**孩子表示法（顺序+链式存储）**：顺序存储各个节点，每个结点中保存孩子链表头指针

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimage.codes51.com%2FArticle%2Fimage%2F20160223%2F20160223101345_0722.png&refer=http%3A%2F%2Fimage.codes51.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626415983&t=7dc2c34cf176795842c51460fc497e6c" style="zoom:80%;" />



**孩子兄弟表示法（链式存储）**：左指针指向孩子，右指针指向兄弟

![](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=372481326,1853316544&fm=15&gp=0.jpg)

其实这就是树转二叉树

树转二叉树，**根结点的右子树总是空的**



**森林和二叉树的转换**

森林是m（m ≥ 0）棵互不相交的树的集合

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fwww.51wendang.com%2Fpic%2F80800cc6d9e460b5009fc74ffc3ae1833307aa4f%2F2-170-png_6_0_0_256_838_352_123_833.4_1152.54-494-0-777-494.jpg&refer=http%3A%2F%2Fwww.51wendang.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626416427&t=6674dcae772c69613edbba7264c095f8" style="zoom:80%;" />

先把三棵树转成二叉树

再把A看成根节点，D、G看出A的兄弟结点，再按照孩子兄弟表示法转换成二叉树







## 二、二叉树

特点：

- 每个结点至多只有两颗子树
- 左右子树不能颠倒（二叉树是有序树）



n个结点的二叉树有$\frac {C_{2n}^{n}}{n+1}$种形态





### 1.常考性质



**二叉树常考性质**

1. 设非空二叉树中度为0、1、2的结点个数分别为n<sub>0</sub>、n<sub>1</sub>和n<sub>2</sub>，则**n<sub>0</sub> = n<sub>2</sub> + 1**（叶子结点比二分支结点多一个）
2. 二叉树**第i层**至**多**有**2<sup>i-1</sup>个结点**（i≥1）
   - m叉树第i层至多有m<sup>i-1</sup>个结点（i≥1）
3. 高度为**h**的二叉树至**多**有**2<sup>h</sup> - 1个结点**（也就是满二叉树）
   - 高度为h的m叉树至多有$\frac{m^h-1}{m-1}$个结点



**完全二叉树常考性质**

1. 具有n个（n>0）结点的完全二叉树的高度h为**$\lceil \log_2(n+1) \rceil$** 或 **$\lfloor \log_2n + 1\rfloor$**
2. 对于完全二叉树，可以由结点数n推出度为0、1和2的结点个数为n<sub>0</sub>、n<sub>1</sub>和n<sub>2</sub>
   - 若完全二叉树有2k个（偶数）结点，则必有n<sub>1</sub> = 1，n<sub>0</sub> = k，n<sub>2</sub> = k - 1
   - 若完全二叉树有2k-1个（奇数）结点，则必有n<sub>1</sub> = 0，n<sub>0</sub> = k，n<sub>2</sub> = k - 1
3. 深度为k的完全二叉树至少有2<sup>k-1</sup>个结点，至多有2<sup>k</sup> - 1个结点



### 2.特殊二叉树



**满二叉树**：一颗高度为h，且具有2<sup>h</sup> - 1个结点的二叉树

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimage.mamicode.com%2Finfo%2F202007%2F20200705174025362527.png&refer=http%3A%2F%2Fimage.mamicode.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626328217&t=63216ceefb6f503a3f56edee44f8d6f0" alt="满二叉树" style="zoom:80%;" />

特点：

1. 只有最后一层有叶子结点
2. 不存在度为1的结点
3. 按层序从1开始编号
   - 结点i的左孩子为2i，有孩子为2i+1
   - 结点i的父节点为$\lfloor i/2 \rfloor$（假如有的话）



**完全二叉树**：当且仅当其每个结点都与高度为h的满二叉树中编号1~n的结点一一对应时，称为完全二叉树

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimage1.bubuko.com%2Finfo%2F202007%2F20200702002547976538.png&refer=http%3A%2F%2Fimage1.bubuko.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626328171&t=c15cc203e795961390fa8b37b96c70af" alt="完全二叉树" style="zoom:80%;" />

所以满二叉树就是一种特殊的完全二叉树

特点：

1. 只有最后两层可能有叶子结点
2. 最多只有一个度为1的结点
3. 同上3
4. i ≤ $\lfloor n/2 \rfloor$为分支结点，i >$\lfloor n/2 \rfloor$为叶子结点

如果完全二叉树的某结点只有一个孩子，那么一定是左孩子



**二叉排序树（BST）**

左子树上所有结点的关键字均**小**于根节点的关键字

左子树上所有结点的关键字均**大**于根节点的关键字

即：左子树结点值 < 根节点值 < 右子树结点值

左子树和右子树又各是一棵二叉排序树

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fupload-images.jianshu.io%2Fupload_images%2F8897049-856881d6ab38ecbb.jpg&refer=http%3A%2F%2Fupload-images.jianshu.io&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626328134&t=4ffc14c23a28d6bcab9cc668f41efe9d" alt="二叉排序树" style="zoom:80%;" />

对其进行中序遍历，可以得到一个递增的有序序列

二叉排序树可用于元素的有序组织、搜索

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fupload-images.jianshu.io%2Fupload_images%2F13944677-7d803e184085115a.gif&refer=http%3A%2F%2Fupload-images.jianshu.io&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626421582&t=c309331f53d13d2c160790754ee452e1" style="zoom: 33%;" />

新插入的结点一定是叶子结点

二叉排序树越不平衡，效率就越低，所以需要提到平衡二叉树来解决





**平衡二叉树（BBT）**

简称平衡树（AVL树）

树上任一结点的左子树和右子树的**深度之差**不超过1

结点的平衡因子 = 左子树高度 - 右子树高度

平衡二叉树结点的平衡因子的值只可能是-1、0和1

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fpic4.zhimg.com%2Fv2-3641cb45e889c1d344e9164c95a0644f_1200x500.jpg&refer=http%3A%2F%2Fpic4.zhimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626328416&t=b983b0cfb0bd3a4715a457a01293c83e" alt="平衡二叉树" style="zoom: 33%;" />

为什么要有平衡二叉树？

因为二叉树有可能退化为单链表，这时候作为树就没有意义

<img src="https://img2018.cnblogs.com/blog/578453/201909/578453-20190927102318864-485895958.png" style="zoom: 67%;" />

平衡二叉树能有更高的搜索效率

二叉排序树加入平衡因子，然后调整成为二叉平衡排序树

![](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=230391890,913215039&fm=26&gp=0.jpg)

只调整的地方叫做**最小不平衡子树**

所以接下来就是如何调整的方法

- LL（右单旋转）：在A的**左**孩子的**左**子树中插入导致不平衡

  <img src="https://img2018.cnblogs.com/blog/578453/201909/578453-20190927103444205-176846244.gif" style="zoom: 67%;" />

- RR（左单旋转）：在A的**右**孩子的**右**子树中插入导致不平衡

  <img src="https://img2018.cnblogs.com/blog/578453/201909/578453-20190927103554968-1963499271.gif" style="zoom:67%;" />

- LR（先左后右双旋转）：在A的**左**孩子的**右**子树中插入导致不平衡

  - A的左孩子的右孩子先左上旋再右上旋

- RL（先右后左双旋转）：在A的**右**孩子的**左**子树中插入导致不平衡

  - A的右孩子的左孩子先右上旋再左上旋



**哈夫曼树**

树的带权路径长度（WPL，Weighted Path Length）：树中所有叶子节点的带权路径长度之和

其中n个带权叶结点的二叉树中，其中WPL最小的二叉树称为**哈夫曼树**，也叫**最优二叉树**

![](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=3158860133,3677539387&fm=26&gp=0.jpg)

哈夫曼树的构造

1. 每次选择两个最小权值的结点两两结合，然后把这两个结点结合的根作为新的结点，权值为两个最小权值的和
2. 重复第一步

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fwww.pianshen.com%2Fimages%2F490%2Ff68b27885d47d7d72e152902967b9f4a.gif&refer=http%3A%2F%2Fwww.pianshen.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626423761&t=8b6eec1b38c33c76876ff5343a8c8cfa" style="zoom:67%;" />

- 哈夫曼树中不存在度为1的结点
- 哈夫曼树并不唯一，但WPL必然相同且为最优
- n个结点构造的哈夫曼树最后的结点总数为2n-1
- 左右顺序任意



哈夫曼编码

就是边也有权值，只是权值是左边0，右边1

是一种前缀编码

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.it610.com%2Fimage%2Fproduct%2Fc490fd4e561446d6939fc7b57ae4ca68.png&refer=http%3A%2F%2Fimg.it610.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626424505&t=35a84161490cf82a960990823317a74f" style="zoom:67%;" />



**线索二叉树**

就是利用序列，将空的指针利用起来

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimage.mamicode.com%2Finfo%2F201911%2F20191118165525934117.png&refer=http%3A%2F%2Fimage.mamicode.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626333791&t=387ba46270aa09eb7a456e0122ff6634" alt="线索二叉树" style="zoom:67%;" />

作用：方便从一个指定结点出发，找到其前驱、后继；方便遍历

指向前驱/后继的指针称为线索

|        | 中序线索二叉树 | 先序线索二叉树 | 后序线索二叉树 |
| :----: | :------------: | :------------: | :------------: |
| 找前驱 |       √        |       ×        |       √        |
| 找后继 |       √        |       √        |       ×        |

 



### 3.存储结构

几个重要常考的基本操作

- i 的左/右孩子：2i/2i + 1
- i 的父节点：$\lfloor i/2\rfloor$
- i所在的层次：$\lceil \log_2(n+1) \rceil$



若完全二叉树中共有n个结点，则

- 判断i是否有左/右孩子：2i/2i + 1 ≤ n
- 判断i是否是叶子/分支结点：i > $\lfloor n/2\rfloor$



**顺序存储**

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.it610.com%2Fimage%2Finfo5%2F6920dd95659e4d85964a2b2c4578ef79.jpg&refer=http%3A%2F%2Fimg.it610.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626331086&t=0ac3e75d53d9c2a96bb5d5b19d7902a8" alt="顺序存储" style="zoom:67%;" />

顺序存储的二叉树会浪费很多空间

寻找结点的最坏情况：

高度为h且只有h个结点的单支树（所有结点只有右孩子），也至少需要2<sup>h</sup> - 1个存储单元

假如一定要用顺序存储二叉树，一定要把二叉树的结点编号与完全二叉树对应起来，这样上述判断才能起作用

结论：二叉树的顺序存储结构，只适合存储完全二叉树





**链式存储**

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimage.mamicode.com%2Finfo%2F201801%2F20180120183527333756.png&refer=http%3A%2F%2Fimage.mamicode.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626331670&t=e110c92b12091d856d8880226c325a4f" alt="链式存储" style="zoom: 80%;" />

n个结点的二叉链表有n+1个空链表（可用于构造线索二叉树）

这样找孩子很方便，但是找父亲要重头开始遍历，假如数据少还好，数据多的时候就很不方便了

这时候再加个指针指向父结点，就是三叉链表

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fpic002.cnblogs.com%2Fimages%2F2012%2F331765%2F2012031419200953.png&refer=http%3A%2F%2Fpic002.cnblogs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626331890&t=535e53e5f32cb0754636ff2a13a1bbd7" alt="三叉链表" style="zoom:80%;" />

方便找父结点

可根据实际需求决定要不要加父结点指针



**双亲表示法**

每个结点中保存指向双亲的指针

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fzcdll.github.io%2Fimages%2Fparent-tree.png&refer=http%3A%2F%2Fzcdll.github.io&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626336093&t=bf405b48c632b20e8d93dc977bf1a101" alt="双亲表示法" style="zoom:80%;" />



### 4.遍历

先根：**根**左右（树/森林的**先根**遍历和树/森林转二叉树的**先根**遍历的结果是一样的）

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fpic4.zhimg.com%2Fv2-9db573561b49f6929d7612262f0954af_b.gif&refer=http%3A%2F%2Fpic4.zhimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626422311&t=85671b5d4bb63044443a6d4cee09d862" style="zoom:67%;" />

中根：左**根**右（森林的**中根**遍历和森林转二叉树的**中根**遍历的结果是一样的）

![](https://pic3.zhimg.com/v2-97f2e02b5bad3679931dfca8275a6a52_b.webp)

后根：左右**根**（树的**后根**遍历和树转二叉树的**中根**遍历的结果是一样的）

![](https://pic3.zhimg.com/v2-9250fccf446cf06d42dd73296e48d0be_b.webp)

层次遍历

![层次遍历](https://pic4.zhimg.com/v2-0927ca68e27ca9fd6978cd6cecf4436f_b.webp)



**由遍历序列构造二叉树**

若只给出一个前/中/后/层序遍历序列，不能确定一颗二叉树

所以遍历序列需要至少两种序列才能构造二叉树：

- 前 + 中
- 后 + 中
- 层 + 中

重点：找到树的根节点，并根据中序序列划分左右子树，再找到左右子树，再找到左右子树根节点，重复操作即可

注意：

- 前序、后序、层序的两两组合无法唯一确定一棵二叉树
- 一个树的叶结点,在前序遍历和后序遍历下,皆以相同的相对位置出现




