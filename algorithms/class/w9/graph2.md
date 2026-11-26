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

* **固定顶点v**，给定**带权有向图G**，求**v到G其他顶点的最短路径**

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

### 时间复杂度：一个点O(n^2),总共O(n^3)
   
## 每一对顶点之间的最短路径：floyd算法
* 给定带权有向图G，对任意顶点vi,vj，**求vi到vj的最短路径**
* 一个方法：每次以一个顶点调用dijkstra算法，时间复杂度为O(n^2)
  
### 思路：不断加入中继点
- dist矩阵，记录最短路径。初始化成每两个顶点的距离。每次加入中继点的时候更新一次
- path矩阵，记录dist矩阵对应位置的路径
- 对于vi~vj的路径，一开始初始化U为空，加入一个中继点v，然后更新一次dist（**允许走v之后每两个顶点的最小距离**）
- 结束：把所有的顶点都加入U
- <img width="496" height="305" alt="屏幕截图 2026-11-24 165011" src="https://github.com/user-attachments/assets/3e32b1c7-ff8e-4fca-b0a8-fbda53326ea5" />

### 代码实现
- 存储结构：邻接矩阵（直接就可以初始化，但是无边的改成-1，自己到自己的边记为0）
- 数组dist[n][n]存放每两个顶点的最短路径。每次加入中继点的时候更新一次
- 数组path[n][n]存放最短路径。有边的初始化为"vivj",无边的为空""。也是加入一次中继点就迭代一次

``` cpp
void printFloyd(int v1,int v2){
  int** dist=new int*[numVer]();
  string* path=new string*[numVer]();
  bool* isInU=new bool[numVer]();
  
  for(int i=0;i<numVer;i++){
      dist[i]=new int[numVer];
      path[i]=new string[numVer];
  }
  
  //初始化
  for(int i=0;i<numVer;i++){
      for(int j=0;j<numVer;j++){
          dist[i][j]=-1;
          path[i][j]="";
          if(adjMatrix[i][j]!=0){
              dist[i][j]=adjMatrix[i][j];
              path[i][j]=to_string(verList.getEleAtPos(i))+" -> "+to_string(verList.getEleAtPos(j));
          }
          if(i==j)dis[i][j]=0;
      }
   }
  
   int numU=0;
  //迭代
  while(numU<numVer){
      for(int i=0;i<numVer;i++){
          if(isInU[i])continue;
  
          isInU[i]=true;
          numU++;
  
          //迭代(i是中继点，j和k是要连的两个点)j->i->k
          for(int j=0;j<numVer;j++){
              if(dist[j][i]==-1)continue;
  
              for(int k=0;k<numVer;k++){
                  //如果连不通就不用迭代
                  if(dist[i][k]==-1)continue;
                  if(j==k)continue;
  
                  int newDist=dist[j][i]+dist[i][k];
                  if(dist[j][k]==-1||newDist<dist[j][k]){
                      dist[j][k]=newDist;
  
                      string mid=to_string(verList.getEleAtPos(i));
                      path[j][k]=path[j][i]+path[i][k].substr(mid.size());
                  }
              }
          }
      }
          
  }
  
  //打印
  if(dist[v1][v2]==-1)cout<<"Unreachable"<<endl;
  else cout<<path[v1][v2]<<" , cost = "<<dist[v1][v2]<<endl;
  
  for(int i=0;i<numVer;i++){
      delete[] dist[i];
      delete[] path[i];
  }
  delete[] isInU;
  
  
}
```


# 有向无环图及其应用

* 任一个有向图，把一个强连通分量看为一个点，则新的图为无环图

## AOV网和拓扑排序
- <img width="489" height="228" alt="屏幕截图 2026-11-24 165349" src="https://github.com/user-attachments/assets/31cb6b6f-efc8-4872-9b64-91dc1d10867e" />

- <img width="498" height="305" alt="屏幕截图 2026-11-24 165617" src="https://github.com/user-attachments/assets/fa0bf2ab-11fa-4c10-b0ad-eb3737f32475" />

### 拓扑排序：对有向图排序（不唯一）
- 拓扑序列：若vi→vj有一条路径，且在顶点序列里面，vi的下标在vj的下标之前，则vi排在vj前
- 拓扑排序：对有向图构造拓扑序列的过程

#### 实现思路
- 找没有前驱的顶点v。输出。
- 删去v和v连出去的边，对于剩下的图，继续找没有前驱的点，进行同样操作。
- 直到输出完所有的顶点
- **若拓扑序列无法进行，则说明有回路**

#### 代码实现
- 存储结构：邻接表。存储顶点，顶点入度，链表储存顶点的所有后继
- 可以找到一个入度为0的顶点就直接输出，然后删去它连的边（它后继的入度都-1）
- 也可以通过栈存储（不过没什么必要。。）
  
## AOE网和关键路径（有权值）
- <img width="500" height="275" alt="屏幕截图 2026-11-24 170951" src="https://github.com/user-attachments/assets/28e7ae6a-ce30-4481-a1cd-57b356a8f6ae" />

- <img width="505" height="247" alt="屏幕截图 2026-11-24 171137" src="https://github.com/user-attachments/assets/d135e7e9-d626-4949-8d59-7c8bcf57bf04" />

- 每条线可以同时开工，要求每个活动vi都要经过，所以最小的工期反而是最长的路径
  - 如下面的最短工期就是10
  - <img width="218" height="117" alt="屏幕截图 2026-11-24 172800" src="https://github.com/user-attachments/assets/745f8f25-018a-4911-bfb8-5b441eaa02c0" />


### 关键路径
- <img width="496" height="209" alt="屏幕截图 2026-11-24 173500" src="https://github.com/user-attachments/assets/1154bc6a-6764-451e-9c0e-d982aa62e966" />

- 找关键活动
  - 事件的最早，最迟发生时间ve[k]，vl[k]
  - 活动的最早，最晚开始时间e[i],l[i]
  - eg，对于下面的例子，已知工程最快10天完工。如果恰在10天结束，则四个值为以下：
    - <img width="218" height="117" alt="屏幕截图 2026-11-24 172800" src="https://github.com/user-attachments/assets/ecc4db8a-c8cd-4e72-8a67-f83c9b1ce0d8" />

    - <img width="1424" height="1050" alt="00931745d13e892cabcd0fa43c34d249" src="https://github.com/user-attachments/assets/c0b5efdb-dd34-404d-8abc-62036267b240" />

