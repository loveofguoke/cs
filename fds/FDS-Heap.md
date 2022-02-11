# FDS-Heap

> Priority Queue
>
> 优先队列，根据元素的优先权(关键字)大小取出元素，而非元素进入队列的先后顺序
>
> 结构性：用数组表示的完全二叉树，从下标为1处开始，下标为0处作为哨兵
>
> 有序性：任一结点的关键字是其子树所有结点的最大值(或最小值)
>
> ​	最大堆：最大值
>
> ​	最小堆：最小值

```c
//MaxHeap operation set
MaxHeap Create(int MaxSize);//创建一个空的最大堆
Boolean IsFull(MaxHeap H);//判断最大堆是否已满
void Insert(MaxHeap H,ElementType item);//将元素item插入最大堆H
Boolean IsEmpty(MaxHeap H);//判断是否为空
ElementType DeleteMax(MaxHeap H);//返回H中最大元素
```

```c
//defination
typedef struct HeapStruct *MaxHeap;
struct HeapStruct{
    ElementType *Elements;//存储堆元素的数组
    int Size;//当前元素个数
    int Capacity;//最大容量
}
```

```c
MaxHeap Create(int MaxSize)
{
    MaxHeap H=malloc(sizeof(struct HeapStruct));//给结构体整体预分配空间
    H->Elements=malloc((MaxSize+1)*sizeof(ElementType));//给数组分配空间
	H->Size=0;
    H->Capacity=MaxSize;
    H->Elemrnts[0]=MaxData;//定义哨兵，大于堆中所有可能的值
    return H;
}
```

```c
void Insert(MaxHeap H,ElementType item)
{	//H->Elements[0]为哨兵
    int i;
    if(IsFull(H)){
        printf("H is full");
        return;
    }
    i=++H->Size;
    for(;H->Elements[i/2]<item;i/=2)//与父结点比较
        H->Elements[i]=H->Elements[i/2];//向下过滤小的父结点
    H->Elements[i]=item;//比较结束，将item插入
}
```

```c
ElementType DeleteMax(MaxHeap H)
{	//从最大堆中取出最大的元素并删除,将最后结点移到根结点，再过滤到合适位置
    int Parent，Child;
    ElementType MaxItem,temp;
    if(IsEmpty(H)){
        printf("H is empty");
        return;
    }
    MaxItem =H->Elements[1];//取出根结点最大值
    //用最后一个元素从根结点开始向上过滤下层结点
    temp=H->Elements[H->Size--];
    for(Parent=1;Parent*2<=H->Size;Parent=Child){
        Child=Parent*2;//左儿子
        if((Child!=H->Size)&&(H->Elements[Child]<H->Elements[Child+1]))
            Child++; //Child指向左右子结点的较大者
        if(temp>=H->Elements[Child]) break;//比左右儿子都大，可以放了
        else H->Elements[Parent]=H->Elements[Child];//将temp移动到下一层
    }
    H->Elements[Parent]=temp;
    return MaxItem;
}
```

> 最大堆的建立
>
> 将已经存在的N个元素按最大堆的要求存放在一个一维数组中
>
> 在线性时间复杂度下建立最大堆
>
> (1)将N个元素按输入顺序存入，先满足完全二叉树的结构特性
>
> (2)调整各结点位置，以满足最大堆的有序特性
>
> 从倒数第一个有儿子的结点开始逐渐调成堆

### In class-MinHeap

```c
Operations:
PriorityQueue Initialize(int MaxElements); 
void Insert(ElementType X,PriorityQueue H); 
ElementType DeleteMin(PriorityQueue H); 
ElementType FindMin(PriorityQueue H); 
```

```c
Array Representation:BT[n+1](BT[0] is not used)
/*保证是完全二叉树：
1.每次插入都从最后开始插入，再比较调整
2.每次删除都删除根结点，再把最后那个移到根结点，再比较调整
父亲是i，则左儿子为2i,右儿子为2i+1
*/
```

```c
PriorityQueue  Initialize( int  MaxElements ) 
{ 
     PriorityQueue  H; 
     if ( MaxElements < MinPQSize ) 
	return  Error( "Priority queue size is too small" ); 
     H = malloc( sizeof ( struct HeapStruct ) ); 
     if ( H ==NULL ) 
	return  FatalError( "Out of space!!!" ); 
     /* Allocate the array plus one extra for sentinel */ 
     H->Elements = malloc(( MaxElements + 1 ) * sizeof( ElementType )); 
     if ( H->Elements == NULL ) 
	return  FatalError( "Out of space!!!" ); 
     H->Capacity = MaxElements; 
     H->Size = 0; 
     H->Elements[ 0 ] = MinData;  /* set the sentinel */
     return  H; 
}

void Insert(MinHeap H,ElementType item)  //O(logN)
{	//H->Elements[0]为哨兵
    int i;
    if(IsFull(H)){
        printf("H is full");
        return;
    }
    i=++H->Size;
    //Percolate up
    for(;H->Elements[i/2]>item;i/=2)//与父结点比较
        H->Elements[i]=H->Elements[i/2];
    H->Elements[i]=item;//比较结束，将item插入
}

ElementType DeleteMin(PriorityQueue H) //O(logN)
{ 
    int i,Child; 
    ElementType MinElement,LastElement; 
    if(IsEmpty(H)){ 
        Error("Priority queue is empty"); 
        return H->Elements[0];} 
    MinElement = H->Elements[1]; /* save the min element */
    LastElement = H->Elements[H->Size--]; /* take last and reset size */
    for (i=1;i*2<=H->Size;i=Child){  /* Find smaller child */ 
         Child = i * 2; 
         if (Child != H->Size && H->Elements[Child+1] < H->Elements[Child]) 
	        Child++;     
         if (LastElement > H->Elements[Child])   /* Percolate one level */ 
	       	H->Elements[i] = H->Elements[Child];   //Percolate down
         else break;   /* find the proper position */
    } 
    H->Elements[i] = LastElement; 
    return MinElement; 
}

//For MinHeap
void PercolateUp( int p, PriorityQueue H ){
    ElementType x=H->Elements[p];
    ElementType Parent;
    for(Parent=p/2;H->Elements[Parent]>x;Parent/=2){
        H->Elements[p]=H->Elements[Parent];
        p=Parent;
    }
    H->Elements[p]=x;//找到位置，放进去
}

BuildHeap(H); //O(N)
//BuildMaxHeap
void PercDown( MaxHeap H, int p )
{ /* 下滤：将H中以H->Data[p]为根的子堆调整为最大堆 */
    int Parent, Child;
    ElementType X;
 
    X = H->Data[p]; /* 取出根结点存放的值 */
    for( Parent=p; Parent*2<=H->Size; Parent=Child ) {
        Child = Parent * 2;
        if( (Child!=H->Size) && (H->Data[Child]<H->Data[Child+1]) )
            Child++;  /* Child指向左右子结点的较大者 */
        if( X >= H->Data[Child] ) break; /* 找到了合适位置 */
        else  /* 下滤X */
            H->Data[Parent] = H->Data[Child];
    }
    H->Data[Parent] = X;
}
 
void BuildHeap( MaxHeap H )
{ /* 调整H->Data[]中的元素，使满足最大堆的有序性  */
  /* 这里假设所有H->Size个元素已经存在H->Data[]中 */
 
    int i;
    /* 从最后一个结点的父节点开始，到根结点1 */
    for( i = H->Size/2; i>0; i-- )
        PercDown( H, i );
}

//BuildMinHeap
void PercolateDown( int p, PriorityQueue H ){
    ElementType x=H->Elements[p];
    ElementType MinChild=p*2;
    for(;MinChild<=H->Size;MinChild*=2){
        if(MinChild+1<=H->Size&&H->Elements[MinChild]>H->Elements[MinChild+1]){
            MinChild+=1;
        } 
        if(H->Elements[MinChild]<x){
            H->Elements[p]=H->Elements[MinChild];
            p=MinChild;
        }
        else break;
    }
    H->Elements[p]=x;
}

void BuildHeap( MinHeap H )
{ /* 这里假设所有H->Size个元素已经存在H->Data[]中 */
    int i;
    /* 从最后一个结点的父节点开始，到根结点1 */
    for( i = H->Size/2; i>0; i-- )
        PercDown( H, i );
}
```

#### Applications

```c
//Given a list of N elements and an integer k.  Find the kth largest element.
ElementType FindKthLargest( int A[], int N, int K )
{   /* it is assumed that K<=N */
    ElementType *H;
    int i, next, child;
    H = (ElementType *)malloc((K+1)*sizeof(ElementType));
    for ( i=1; i<=K; i++ ) H[i] = A[i-1];
    BuildMinHeap(H, K);
    for ( next=K; next<N; next++ ) {
        H[0] = A[next];
        if ( H[0] > H[1] ) {
            for ( i=1; i*2<=K; i=child ) {
                child = i*2;
                if (child!=K && H[child]>H[child+1]) child++;
                if (H[0]>H[child])
                    H[i] = H[child];
                else break;
            }
            H[i] = H[0];
        }
    }
    return H[1];
}
```

#### d-Heaps ---- All nodes have d children
