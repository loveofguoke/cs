# FDS-Graph

> Vertex 顶点集合
>
> Edge 边的集合
>
> (a,b)无向边  <a,b> 有向边
>
> G(V,E) 由一个非空的有限顶点集合V和一个有限边集合E组成
>
> 出度：从该点发出的边数
>
> 入度：指向该点的边数

```c
//操作集
Graph Create();//建立并返回空图
Graph InsertVertex(Graph G,Vertex v);//将v插入G
Graph InsertEdge(Graph G,Edge e);//将e插入G
void DFS(Graph G,Vertex v);//从顶点v出发深度优先遍历图G
void BFS(Graph G,Vertex v);//从顶点v出发广度优先遍历图G
void ShortestPath(Graph G,Vertex v,int Dist[]);//计算图G中顶点v到任意其他顶点的最短距离
void MST(Graph G);//计算图G的最小生成树
```

```c
//邻接矩阵表示法
G[N][N];
G[i][j]=1; //存在边<vi,vj>,也可以为权重
G[N(N+1)/2]; //对无向图，使用一维数组存储{G00,G10,G11,....,Gn-1 0,....,Gn-1 n-1}
//Gij对应的下标为(i*(i+1)/2+j)
```

```c
//比较稀疏时，邻接表表示法
G[N]//指针数组
G[0]//指向由与0有关联的顶点组成的链表
//对有向图而言正邻接表只能计算出度，需要构造逆邻接表(存指向自己的边)来计算入度
```

```c
//遍历
//深度优先搜索
void DFS(Vertex V){
    visited[V]=true;
    for(V的每个邻接点W)
        if(!visited[W])
            DFS(W);
}
//广度优先搜索
void BFS(Vertex V){
    visited[V]=true;
    Enqueue(V,Q);
    while(!IsEmpty(Q)){
        V=Dequeue(Q);
        for(V的每个邻接点W)
            if(!visited[W]){
                visited[W]=true;
                Enqueue(W,Q);
            }
    }
}
```

> 连通：V到W存在路径
>
> 路径：V到W经过的一系列顶点的集合。路劲长度为路径中的边数(或者所有边的权重和)。如果所有顶点都不同，则为简单路径
>
> 回路：起点等于终点的路径
>
> 连通图：图中任意两顶点均连通
>
> 连通分量：无向图的极大连通子图
>
> ​	极大顶点数：再加一个顶点就不连通了
>
> ​	极大边数：包含子图中所有顶点相连的所有边
>
> 强连通：有向图中顶点v和w之间存在双向路径
>
> 强连通图：有向图中任意两顶点均强连通
>
> 强连通分量：有向图的极大强连通子图

```c
//图不连通的遍历
void KistComponents(Graph G){
    for(each V in G)
        if(!visited[V]){
            DFS(V);//or BFS(V)
        }
}
```

## In class

### Representation

>有向边 tail->head
>
>约定：1.自己不指向自己  2.两个点之间只有一种关系
>
>**完全图： 有最大可能数目的边的图**
>
>路径长度：边的个数
>
>简单路径：一条路径中除了头尾两个点外所有点都不同
>
>cycle：头与尾相同的简单路径
>
>连通：A与B间存在一条路径
>
>图连通：每对点之间都存在路径
>
>连通分量：无向图的**极大连通子图**
>
>​	极大顶点数：再加一个顶点就不连通了
>
>​	极大边数：包含子图中所有顶点相连的所有边
>
>A tree: 没有cycle的图      n-1条边
>
>A DAG：a directed acyclic graph 有向无环图
>
>强连通：有向图中顶点v和w之间存在双向路径   对无向图叫弱连通
>
>强连通图：有向图中任意两顶点均强连通
>
>强连通分量：有向图的极大强连通子图
>
>Degree: 一个点连的边的个数
>
>出度：从该点发出的边数
>
>入度：指向该点的边数

```C
//Adjacency Matrix representation
adj_mat[n][n];
//1-D array
adj_mat[n(n+1)/2+1];  a11,a21,a22,a31......ann  
aij=adj_mat[i*(i-1)/2+j];  从1开始
```

```C
//Adjacency Lists representation
G[N]//指针数组
G[0]//指向由与0有关联的顶点组成的链表
//对有向图而言正邻接表只能计算出度，需要构造逆邻接表(存指向自己的边)来计算入度
```

<img src="C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211110205217331.png" alt="image-20211110205217331" style="zoom: 67%;" />

>十字链表，将邻接链表和逆邻接表合二为一，右指针连成邻接表(出度，行)，左指针连成逆邻接表(入度，列)

![image-20211110211022026](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211110211022026.png)

![image-20211110211809784](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211110211809784.png)

>无向图 多重链表

![image-20211110212352235](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211110212352235.png)

>权重 Weight

### Operations

```C
/*Topological Sort for DAG 拓扑排序
A topological order is a linear ordering of the vertices of a graph such that, for any two vertices, i, j, if i is a predecessor of j in the network then i precedes j in the linear ordering.*/
void Topsort(Graph G)  // T = O( |V|2 )
{   int Counter;
    Vertex V,W;
    for ( Counter = 0; Counter < NumVertex; Counter ++ ){
	V=FindNewVertexOfDegreeZero( );
	if (V==NotAVertex){
	    Error (“Graph has a cycle”);  break;}
	TopNum[V]=Counter; /* or output V , TopNum[V]存了点V的顺序9*/
	for(each W adjacent to V)
	    Indegree[W]––;
    }
}

//将入度为0的点放到一个集合里，用队列或堆栈来实现这个集合，此处用队列 T = O(|V|+|E|)
void Topsort( Graph G )
{   Queue  Q;
    int  Counter = 0;
    Vertex  V, W;
    Q = CreateQueue( NumVertex );  MakeEmpty( Q );
    for ( each vertex V )
	if ( Indegree[ V ] == 0 )   Enqueue( V, Q );
    while ( !IsEmpty( Q ) ) {
	V = Dequeue( Q );
	TopNum[ V ] = ++ Counter; /* assign next */
	for ( each W adjacent to V )
	    if ( – – Indegree[ W ] == 0 )  Enqueue( W, Q );
    }  /* end-while */
    if ( Counter != NumVertex )
	Error( “Graph has a cycle” );
    DisposeQueue( Q ); /* free memory */
}
```

 
