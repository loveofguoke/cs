# FDS-Shortest Path Algorithms

> 走到某一点时，其自身的路径已经是最短的了，所以它对周围点施加的影响是确定的，不会再发生变化。	11.16 1:20:00
>
> 最短路径具有最优子序列结构 所以只需要记前面那个点就行了

### Single-Source Shortest-Path Problem

#### Unweighted Shortest Paths

Table[ i ].Dist ::= distance from s to vi  /* initialized to be $\infin$ except for s */

Table[ i ].Known ::= 1 if vi is checked; or 0 if not

Table[ i ].Path ::= for tracking the path   /* initialized to be 0 */

```c
void Unweighted( Table T )  //T = O( |V|^2 )

{   int  CurrDist;
    Vertex  V, W;
    for ( CurrDist = 0; CurrDist < NumVertex; CurrDist ++ ) {
        for ( each vertex V )
	if ( !T[ V ].Known && T[ V ].Dist == CurrDist ) {
	    T[ V ].Known = true;
	    for ( each W adjacent to V )	//修改周围点的信息
	        if ( T[ W ].Dist == Infinity ) {
		T[ W ].Dist = CurrDist + 1;
		T[ W ].Path = V;
	        } /* end-if Dist == Infinity */
	} /* end-if !Known && Dist == CurrDist */
    }  /* end-for CurrDist */
}
```

![image-20211118173753211](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211118173753211.png)

```c
void Unweighted( Table T )	//T = O( |V| + |E| ) 带记忆性
{   /* T is initialized with the source vertex S given */
    Queue  Q;
    Vertex  V, W;
    Q = CreateQueue (NumVertex );  MakeEmpty( Q );
    Enqueue( S, Q ); /* Enqueue the source vertex */
    while ( !IsEmpty( Q ) ) {
        V = Dequeue( Q );
        T[ V ].Known = true; /* not really necessary */
        for ( each W adjacent to V )
	if ( T[ W ].Dist == Infinity ) { //O(E)
	    T[ W ].Dist = T[ V ].Dist + 1;
	    T[ W ].Path = V;
	    Enqueue( W, Q );
	} /* end-if Dist == Infinity */
    } /* end-while */
    DisposeQueue( Q ); /* free memory */
}
```

#### Dijkstra’s Algorithm (for weighted shortest paths)

```c
void Dijkstra( Table T )	//用堆
{   /* T is initialized by Figure 9.30 on p.303 */
    Vertex  V, W;
    for ( ; ; ) {
        V = smallest unknown distance vertex;
        if ( V == NotAVertex )
	break; 
        T[ V ].Known = true;
        for ( each W adjacent to V )
	if ( !T[ W ].Known ) 
	    if ( T[ V ].Dist + Cvw < T[ W ].Dist ) {
	    	Decrease( T[ W ].Dist  to
			 T[ V ].Dist + Cvw );
		T[ W ].Path = V;
	    } /* end-if update W */
    } /* end-for( ; ; ) */
}	/* not work for edge with negative cost */
```

#### Graphs with Negative Edge Costs

```c
void  WeightedNegative( Table T )	//T = O( |V| * |E| )
{   /* T is initialized by Figure 9.30 on p.303 */
    Queue  Q;
    Vertex  V, W;
    Q = CreateQueue (NumVertex );  MakeEmpty( Q );
    Enqueue( S, Q ); /* Enqueue the source vertex */
    while ( !IsEmpty( Q ) ) {
        V = Dequeue( Q );
        for ( each W adjacent to V )
	if ( T[ V ].Dist + Cvw < T[ W ].Dist ) {
	    T[ W ].Dist = T[ V ].Dist + Cvw;
	    T[ W ].Path = V;
	    if ( W is not already in Q )
	        Enqueue( W, Q );
	} /* end-if update */
    } /* end-while */
    DisposeQueue( Q ); /* free memory */
}	/* negative-cost cycle will cause indefinite loop */
```

#### Acyclic Graphs



## 实现Dijkstra(in C)

```c
#include<stdio.h>
#include<stdlib.h>
#define MinData -1 //The value of the sentinel node of the smallest heap,H[0]
#define Infinite 1000000000 //Used to initialize 'Dist'
#define NotAVertex -1 //Used to initialize 'Path'
#define False 0 //Means Unknown
#define True 1 //Means Known

//Definition of Heap
/* Each element in the heap is a structure that stores 
the sequence number and distance of a vertex */
typedef struct{
    int vertex;
    int distance;
}HeapElement;

typedef struct HeapStruct *MinHeap;
struct HeapStruct{
    HeapElement *Elements; //A structural array that stores heap elements
    int Size; //Number of current elements
    int Capacity; //Maximum Capacity
};

//Definition of Graph
typedef struct AdjVNode *PtrToAdjVNode; 
//Nodes in adjacency list
struct AdjVNode{
    int vertex; 
    int weight; //Distance between two vertexs
    PtrToAdjVNode Next;
};

typedef PtrToAdjVNode* AdjList; //Adjacency lists
typedef struct GraphRecord *Graph;
struct GraphRecord{  
    int v; //Number of vertexs in the graph
    int e; //Number of edges in the graph
    AdjList G; //Adjacency lists
    AdjList RG; //Inverse adjacency lists
};

//The structure needed to find the shortest path
typedef struct TableStruct *Table;
struct TableStruct{
    int *Dist; //The distance between this vertex and the start vertex 
    int *Path; //Stores the previous vertex of the current vertex on the shortest path
    int *Known; //Indicates whether the shortest path has been found for this vertex
};

//Function declaration
//Stack related functions

MinHeap CreateHeap(int Capacity); //Create a new heap
void Insert(MinHeap H,HeapElement item); //Insert a new element into the heap
int IsEmptyH(MinHeap H);
HeapElement DeleteMin(MinHeap H); //Delete and return the minimum element
//Graph related functions
Graph CreateGraph(int v,int e); 
void InsertNewNode(Graph g,int v1,int vertex,int weight); //Insert a new node into an adjacency list
//The key functions of this problem
Table CreateTable(int v,int start);
void Dijkstra(int start,MinHeap H,AdjList G,Table T); //Dijkstra's Algorithm in class

int main(){
    int M,N;
    Graph g=CreateGraph(M,N);
    MinHeap H=CreateHeap(N);
    Table T=CreateTable(M,1); 
    Dijkstra(1,H,g->G,T); 
    return 0;
}

MinHeap CreateHeap(int Capacity){
    MinHeap H=malloc(sizeof(struct HeapStruct));
    H->Elements=malloc(sizeof(HeapElement)*(Capacity+1)); //begin at H[1]
    H->Capacity=Capacity+1;
    H->Size=0; //Initialize to empty heap
    H->Elements[0].distance=MinData; //Set the sentinel of the heap
    return H;
}

void Insert(MinHeap H,HeapElement Newone)  //O(logv)
{	//H->Elements[0] is the sentinel
    int i;
    i=++H->Size;
    //Percolate up
    for(;H->Elements[i/2].distance>Newone.distance;i/=2)//Compare with parent node
        H->Elements[i]=H->Elements[i/2];//Smaller than the parent node, the parent node moves down
    H->Elements[i]=Newone;//Larger than the parent node, insert at this location
}

int IsEmptyH(MinHeap H){
    if(H->Size==0) return 1;
    else return 0;
}

HeapElement DeleteMin(MinHeap H){ //O(logv)
    int i,Child; 
    HeapElement Minone,Lastone; 
    Minone = H->Elements[1]; // save the min element 
    Lastone = H->Elements[H->Size--]; // take last one and reset size 
    for (i=1;i*2<=H->Size;i=Child){  // Find smaller child 
         Child = i*2; 
         if (Child != H->Size && H->Elements[Child+1].distance < H->Elements[Child].distance) 
	        Child++;     
         //Percolate down
         if (Lastone.distance > H->Elements[Child].distance)   
	       	H->Elements[i] = H->Elements[Child];   
         else break;   // find the proper position
    } 
    H->Elements[i] = Lastone;  //Readjusted to minimum heap
    return Minone; 
}

/* The railway is from v1 to v2, and the length of it is weight  */
void InsertNewNode(Graph g,int v1,int v2,int weight){
    //Update adjacency lists   
    PtrToAdjVNode p=malloc(sizeof(struct AdjVNode));//Create a new AdjVNode
    p->vertex=v2;
    p->weight=weight;
    //Insert into the head of the adjacency list of v1
    p->Next=g->G[v1];
    g->G[v1]=p;
    //Update inverse adjacency lists
    PtrToAdjVNode q=malloc(sizeof(struct AdjVNode));
    q->vertex=v1;
    q->weight=weight;
    //Insert into the head of the inverse adjacency list of v2
    q->Next=g->RG[v2];
    g->RG[v2]=q;
}

Graph CreateGraph(int v,int e){
    int i;
    Graph p=malloc(sizeof(struct GraphRecord));
    p->G=malloc(sizeof(PtrToAdjVNode)*(v+1));//The sequence number of vertices starts with 1
    p->RG=malloc(sizeof(PtrToAdjVNode)*(v+1));
    p->v=v;//Update the information of graph
    p->e=e;
    for(i=1;i<=v;i++){
        p->G[i]=NULL;//Initialize the adjacency lists as an empty linked lists
        p->RG[i]=NULL;
    }
    for(i=0;i<e;i++){
        int v1,v2,weight;
        //Read the edges one by one and insert them into the adjacency lists
        scanf("%d%d%d",&v1,&v2,&weight);
        InsertNewNode(p,v1,v2,weight);
    }
    return p;
}

Table CreateTable(int v,int start){
    int i;
    Table T=malloc(sizeof(struct TableStruct));
    //ALL start with the array index 1
    T->Dist=malloc(sizeof(int)*(v+1)); 
    T->Path=malloc(sizeof(int)*(v+1));
    T->Known=malloc(sizeof(int)*(v+1));
    //Initialize information to unknown before finding
    for(i=1;i<=v;i++){
        T->Dist[i]=Infinite;
        T->Path[i]=NotAVertex;
        T->Known[i]=False;
    }
    //The starting vertex is the algorithm entry
    T->Dist[start]=0;
    return T;
}

/* Method 2 in class: insert vertex with updated Dist into the MinHeap
So T=O(elogv) ; S=O(e) */
void Dijkstra(int start,MinHeap H,AdjList G,Table T){
    HeapElement p;
    p.distance=T->Dist[start];
    p.vertex=start;
    Insert(H,p); //Insert the start vertex into heap
    while(H->Size){
        /*Update the information of adjacent vertices 
        from the element with the smallest distance*/
        p=DeleteMin(H); 
        T->Known[p.vertex]=True; //Shortest path found
        PtrToAdjVNode q=G[p.vertex];
        //for each vertex adjacent to p.vertex
        while(q){
            int adjacent=q->vertex;
            /* If the shortest path of the adjacent vertex has
            been found, there is no need to update its information */
            if(T->Known[adjacent]==True) {
                q=q->Next;
                continue;
            }
            int distance=q->weight+p.distance;
            if(distance<T->Dist[adjacent]){
                /* Update the information */
                T->Dist[adjacent]=distance;
                T->Path[adjacent]=p.vertex;
                /* Insert the new information to heap
                help other vertex update information */
                HeapElement k;
                k.vertex=adjacent;
                k.distance=distance;
                Insert(H,k);
            }
            q=q->Next;
        }
    }
}
```

