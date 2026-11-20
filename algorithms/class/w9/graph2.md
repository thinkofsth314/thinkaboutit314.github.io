# 图2：最小生成树，最短路径算法、AOV，AOE网


# 一、最小生成树

## 定义

* **生成树的代价**：设G=(V,E)是一个**无向连通网**。生成树上各边的权值之和称为该生成树的代价
* **最小生成树**：在G的所有生成树中，代价最小的生成树
  
## MST性质
  - 若G(V,E)是一个无向连通网，U是顶点集V的一个非空子集，(u,v)是一条具有**最小权值**的边，u∈U,v∈V-U，则**必然存在一棵包含边(u,v)的最小生成树**
  - 
## prim算法
### 思路
  - 先选定G中一个任意顶点A，此时U={A}.
  - 取与A邻接，且边的权值最小的点，设为B,将U扩为{A,B}，再看U连到V-U权值最小的边。设这条边是(B,C),则将U扩为{A,B,C}
  - 继续扩张，直到V-U为空。此时之前的流程选出来的边加上所有顶点就是最小生成树
    
### 代码实现
- 存储：邻接矩阵
  
- 1、建立三个数组
  - bool isInU[numVer]:顶点是否已经进入U
  - int dist[numVer]：从U接到V-U的最小边的权值
  - int from[numVer]：最小权值边在U里的端点
 
- 2、初始化
  - 一开始只在U放入第0个顶点
  - i从0到numVer：isInU[i]=false
  - dist[i]初始化为0到第i个顶点的边权值，如果没有边就为-1
  - 如果0到i有边：from[i]=0，没有边：from[i]=-1

- 3、不断加入U
  - 循环的结束条件：U里的顶点=numVer
  - 初始化最小距离minDist=-1,最小距离对应的在V-U的顶点minInd=-1
  - 1、找最小边和对应顶点，用minInd表示
  - 2、将minInd加入U，对应边也加入
  - 3、更新
    - dist[i]=第二步加入的minInd到i的边权值
    - from[i]=minInd
    - 若没有仍然填-1


``` cpp

AMGraph<T>* PrimMST(){

  // 默认连通无向图
  T* arr = new T[numVer];
  for(int i=0;i<numVer;i++){
      arr[i] = verList.getEleAtPos(i);
  }
  AMGraph<T>* amg = new AMGraph<T>(arr,numVer,false);

  bool isInU[numVer];//顶点是否已经进入U
  int dist[numVer];//从U接到V-U的最小权值边
  int from[numVer]; //最小权值边在U里的端点

  //初始化
  for(int i=1;i<numVer;i++){
      isInU[i] = false;//所有顶点都没加入U

      //一开始只把第0个顶点放入，所以距离初始化成别的顶点和0的距离，没有就是-1
      dist[i] = (adjMatrix[0][i] !=0 ? adjMatrix[0][i] : -1);

      
      from[i] = (adjMatrix[0][i] != 0 ? 0 : -1);
  }

  int numU = 1;//进入U的顶点数
  
  while(numU < numVer){
      int minDist = -1;//初始化最小距离
      int minInd = -1;//V-U里面最小边对应的顶点

      for(int i=1;i<numVer;i++){

          if(isInU[i]) continue;//不看在U里面的点
          if(dist[i]==-1) continue;//没有边就过


          if(minDist==-1 || dist[i] < minDist){
              minDist = dist[i];
              minInd = i;
          }
      }


      isInU[minInd] = true;//V-U的最小边顶点入U
      numU++;
      amg->addEdge(verList.getEleAtPos(minInd), verList.getEleAtPos(from[minInd]), minDist);
      //amg->printGraph();


      //更新
      for(int i=1;i<numVer;i++){
          if(isInU[i]) continue;
          if(adjMatrix[minInd][i]==0) continue;//没有边就过

          
          if(dist[i]==-1 || adjMatrix[minInd][i] <dist[i]){
              dist[i] = adjMatrix[minInd][i];
              from[i] = minInd;
          }
      }
  }
  return amg;
}

```


### 时间复杂度O(n*n)


## kruskal算法

### 思路: 不断填入边，看连通分量

- 设T为最小生成树
- 初始化就让U=V，让最小生成树T的边集先为空（一开始T的连通分量就有n个，即n个独立的顶点）
- 在E中寻找**最短边**(u,v)，**如果(u,v)位于T两个不同的连通分量，则将这条边加入T的边集**，并把**两个连通分量合并为一个连通分量**。重复操作，直到T**只剩下一个连通分量**

### eg

<img width="515" height="250" alt="屏幕截图 2026-11-20 180207" src="https://github.com/user-attachments/assets/122a40dd-6e73-455d-afc7-1eaf4cf81471" />

- 合并B,E→并C,D→并A,F→并{A,F},{C,D}→并{A,F,C,D},{B,E}

### 代码实现

- 存储边：使用三元数组
- 合并连通分量：使用一个parent数组，记录哪些顶点属于同一个连通分量
  - 初始化各个顶点parent[i]=-1
  - 合并连通分支时，假设是2和1合并到0，则p[2]=1,p[1]=0,p[0]=-1。称此时0为根。
  - 考察是否在同一个连通分支：找到两个顶点的根是否为同一个顶点
    

### 复杂度：排序边最快是O(klogk),k为边数。找连通分量




# 二、最短路径算法
* 非网图中：两顶点边数最少的路径
* 在网图中，两顶点之间边权重和最小的路径

## 单源点最短路径问题：Dijkstra算法

* **固定顶点v**，给定**带权有向图G**，求v到G其他顶点的最短路径

### 算法思路
- 初始化S={v}（S：存放**已经找到最短路径的顶点**）
- 从v开始，将除v以外顶点的**值**初始化为**v直接连到它的边的权重**。没有直接连接的记为∞.
- 设值最小的顶点为k，则S更新为{v,k}，再看v→k→其他顶点的边,如果v→k→j小于v→j，就**更新j的值为v→k→j之和**
- 再次选择值最小的顶点i,S扩为{v,k,i}，继续更新值
- 重复这个过程，直到S=V。此时所有顶点对应的值为v到它的最小路径权重和。

### 实现
- 存储：带权的邻接矩阵
- 数组dist[i]：表示从v到vi的最短路径长度（思路中每个顶点的值），每次循环会更新
- path[i]:表示这一次循环更新后vi到v的**最短路径**（可以**只存储连接vi的最后一个顶点**）
- s[i]：算法思路中的S

### 伪代码
1. 初始化
   对每个顶点 v：
       dist[v] = ∞
       path[v] = -1
       s[v] = false
   dist[a] = 0

2. 重复以下步骤：
   - 2.1 在所有 s[v] == false 的顶点中，选 dist[v] 最小的一个，记作 k
       - 如果没有可选顶点，或者 dist[k] == ∞，结束循环

   - 2.2 确定 k 的最短距离
       - s[k] = true

   - 2.3 对每个还没确定的顶点 v：
       - 如果存在边 k → v：
           - 新距离 = dist[k] + 边权(k, v)
           - 如果 新距离 < dist[v]：
               - dist[v] = 新距离
               - path[v] = k

3. 循环结束，dist[v] 就是起点 a 到各顶点 v 的最短距离。
   如果 dist[v] == ∞，说明从 a 到不了 v。

   
## 每一对顶点之间的最短路径：floyd算法
* 给定带权有向图G，对任意顶点vi,vj，求vi到vj的最短路径
* 一个方法：每次以一个顶点调用dijkstra算法，时间复杂度为O(n^2)
  
### floyd思路


# AOV网和拓扑排序
# AOE网和关键路径
