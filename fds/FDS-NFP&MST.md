# FDS-NFP&MST

### Network Flow Problems

原图，流图Gf,残余图Gr

augmenting path(增长通路):any path between s and t in the garph Gr(残余图)

不能用贪婪算法

实际上，如果不需要流的具体状况，就不需要Gf

#### Solution1

在残余图上加反向流

1.用无权图最短路径算法寻找到一条增长通路，T=O(E)

2.因为每条增长通路使流的值至少增长1，若最大流为f，则总的运行时间为O(f*E)

由于增长通路的随机选择可能导致算法运行时间远大于实际所需时间

#### 选择增长通路的两种方法

**1.总是选择使流增长最大的通路(Update Dijkstra's Algorithm) 不需要逆邻接表**

不需要dist了，换成flow,即从起点到这个点的当前路径能通过的最大流，保留path记录路径。

首先令每个点的flow初始化为0，起点初始化为infinity，让起始点入堆，出堆时，如果这个点A已知，就跳过，如果未知，就设为已知，然后对他的每一个邻接点B做操作：if(edge==0) skip

else newflow=min(flow(A),edge),

if(newflow>flow[B]) B更新信息，B入堆，当堆为空时结束。注意，此时我们希望最大的先出堆，所以改用最大堆  同样是O(e)

此时终点已经存有能使flow最大的路径及flow的值，然后对Gr，Gf做操作：从B开始，沿路径回走，减小A指向它的边的值，并在Gf中查A的邻接表，如果有A就直接加相应的值，如果没有就在头部加一条边，并初始化相应的值，同时，让MAXFLOW增加相应的值(为了省去逆邻接表).

，并查B的邻接表，如果有A，就增加相应的值，如果没有A，就在头部加一条边，并初始化相应的值。

一直这样往回走到起点结束。

然后对Gr持续进行上述操作，直到跑完Dijkstra后终点flow值为0，说明Gr内已经没有增长路径了，

此时Gf中存着流的具体路线，MAXFLOW中存着最大流的值。算法结束。



**2.总是选择具有最少边数的路径**(未完待续)



### Minimum Spanning Tree

#### Definition

> A spanning tree of a graph G is a tree which consists of V( G ) and a **subset** of E( G )
>
> It is minimum for the total cost of edges is minimized.
> It is spanning because it covers every vertex.
> A minimum spanning tree exists iff G is connected.
> Adding a non-tree edge to a spanning tree, we obtain a cycle.

#### Kruskal 算法-无向连通图

把每对点和边长扔到最小堆，每次取出边长最小的，判断这两个点是否在同一集合里，如果不在，说明不会成环，可以相连，并把它们并到一个集合里，如果想要最小树的结构，可以根据这两个点和边长去生成图，如果不需要就只要让总Weight+=edge即可。要记录已经获得的有效边，当有效边=v-1时结束算法。



#### Prim 算法

单点出发，归入visited,从没连接的里面找和visited里的点边长最短的，连好后将该点也放入visited,同上知道visited所有点

#### 判断最小生成树是否唯一
