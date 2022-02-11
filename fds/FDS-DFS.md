# FDS-DFS

```c
void DFS ( Vertex V )  /* this is only a template */
{   visited[ V ] = true;  /* mark this vertex to avoid cycles */
    for ( each W adjacent to V )
        if ( !visited[ W ] )
	DFS( W );
} /* T = O( |E| + |V| ) as long as adjacency lists are used */
```

### 1.无向图

#### 存在多个连通分量时

```c
void ListComponents ( Graph G ) 
{   for ( each V in G ) 
        if ( !visited[ V ] ) {
		  DFS( V );
          printf(“\n“);
        }
}
//需要几次DFS,就有几个连通分量
```

### 2.Biconnectivity(双向连通性)

v is an **articulation point(关节点)** if G’ = DeleteVertex( G, v ) has at least 2 connected components

G is a **biconnected graph** if G is connected and has no articulation points.

A biconnected component is a maximal biconnected subgraph.

***Note:  No edges can be shared by two or more biconnected components.  Hence E(G) is partitioned by the biconnected components of G.***

#### 2.1 Finding the biconnected components of a connected undirected G

先通过DFS的访问顺序获得生成树

顺序存在Num[]中，从0开始







### 3.Euler Circuits

Draw each line exactly once without lifting your pen from the paper – Euler tour

Draw each line exactly once without lifting your pen from the paper, AND finish at the starting point – Euler curcuit

〖Proposition〗 An Euler circuit is possible only if the graph is connected and each vertex has an **even** degree(偶数度)

〖Proposition〗 An Euler tour is possible if there are exactly **two** vertices having **odd** degree.  One must **start at one of the odd-degree vertices**.



### 应用 PTA-强连通分量

思路：假设有n个强连通分量，则执行n个主元的DFS，对每一个DFS，以是否有指向主元的邻边为判据，如果有，则该条递归路径与主元在同一分量上(return 1依次向前传递这一信息，同时print这条路径上的点，设为printed),如果没有，就说明这条递归路径无效，return 0即可。

为了防止某些也在分量上的点A因为与主元的路径点B已被visited而无法连接到主元，要设置thisturn[]，如果那个已被visited的路径点B在此次主元DFS中被print了，说明该路径点与主元已连通，那么点A自然也能与主元连通，A也需要print.这样就不会遗漏A这类点了。

其实还有bug,而且弥补法行不通，因为只要在B连通主元前从B走到A，那么A就被visited了，且再也没机会被考虑，A就被遗漏了。

修正方法是允许每个点visit它指向的点一次，这样只要它指向的点K与主元连通，那么1.K还没去走过那条路，K就会去走 2.K已经走过了，K已被thisturn (同理一直推到和主元直接相连的点，循环不变式)  这两种情况都能保证点A被视作与主元连通，A就一定不会遗漏了。

已经被printed(即thisturn)的点不需要再被visit

效率有待提升

```c

```

能不能先跑一遍，再去加直接连通边，然后依次影响







直接正向图和反向图都跑一遍DFS，共同遍历到的点，就在一个分量上(有双向路径)



#### Tarjan 算法





#### Kosaraju 算法