# FDS-Union and Find

>集合运算：交、并、补、差，判定一个元素是否属于某一集合
>
>并查集：集合并、查某元素属于什么集合
>
>1.把有关联的元素的集合并在一起
>
>2.查找元素
>
>***Note:此处一个元素只能属于一个集合***

>并查集问题中集合存储如何实现？
>
>用树结构表示，孩子指向双亲

![union1](D:\typora\课程笔记\FDS\picture\union1.png)

```c
//use array
typedef struct{
    ElementType Data;//content
    int Parent;//the Array_index of its parent
}SetType;
```

![union2](D:\typora\课程笔记\FDS\picture\union2.png)

```c
//查找某元素所在集合(用根结点表示)
int Find(SetType S[ ],ElementType X){
    //Maxsize 为数组的最大长度
    int i;
    for(i=0;i<Maxsize&&S[i].Data!=X;i++);
    if(i>=Maxsize) return -1;//not found
    for(;S[i].Parent>=0;i=S[i].Parent);//向上溯根，Root.Parent==-1
    return i;//找到X所属集合，返回根结点在数组S中的下标
}

//集合的并运算
/*分别找到X1,X2所在集合树的根结点，如果不同根，就将其中一个根结点的父结点指针设成另一个根结点的数组下标*/
void Union(SetType S[],ElementType X1,ElementType X2){
    int Root1,Root2;
    Root1=Find(S,X1);
    Root2=Find(S,X2);
    if(Root1!=Root2) S[Root2].Parent=Root1;
}
```

## 集合的简化表示(in class)

任何有限集合的N个元素都可以被一一映射为整数0-N-1，于是我们只需要处理整数间关系。这样一来，我们可以用数组下标直接表示Data,即表示每一个元素，不需要花时间去寻找某元素的位置

```c
typedef int ElementType;//默认元素可以用非负整数表示
typedef int SetType;//默认用根结点的下标作为集合名称
typedef ElementType DisjSet[MaxSize];//存储各集合的数组

SetType Find(DisjSet S,ElementType X){
    //默认集合元素全部初始化为-1
    for(;S[X]>=0;X=S[X]);
    return X;//返回根结点下标
}

void SetUnion(DisjSet S,SetType Root1,SetType Root2){
    //默认Root1和Root2是不同集合的根结点
    S[Root2]=Root1;//将集合2并到集合1中
}
```

### Smart Union Algorithms

>按上面这种算法一直并下去可能导致树的高度快速变大。最坏的情况是建成了斜的树，这样规模为N的集合并查操作(1.查根结点是否相同 2.若不相同，则并在一起)的时间复杂度为O($N^2$):
>
><img src="C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211104104732344.png" alt="image-20211104104732344" style="zoom:50%;" />
>
>为了改善查找性能，可以将小的集合并到相对大的集合中去。可以用Root.Parent的绝对值来表示集合的元素个数，-3表示有三个元素。
>
>也可以将高度小的集合树并到高度大的中去，用Root.Parent的绝对值来表示集合的高度。这样只有当两个高度相同的树合并并高度才会加一

```c
//Union by Height,已知根结点
void SetUnion(DisjSet S,SetType Root1,SetType Root2){
    if(S[Root2]<S[Root1])//集合树2更高
        S[Root1]=Root2;
    else{
        if(S[Root1]==S[Root2])//一样高，则高度加一
            S[Root1]--;
        S[Root2]=Root1;
    }
}

//Union by Size
void SetUnion(DisjSet S,SetType Root1,SetType Root2){
    if(S[Root2]<S[Root1]){//集合树2更大
        S[Root2]+=S[Root1];//合并个数
        S[Root1]=Root2;
    }
    else{
        S[Root1]+=S[Root2];
        S[Root2]=Root1;
    }
}
```

若一直按秩归并，则规模为N时，最坏情况是一直等秩归并(树高++)，能进行logN次，所以最坏情况下树高为O(logN),则并查操作时间复杂度为O(NlogN).

### Path Compression

```c
//递归，递的时候查找根结点，归的时候让中间路径上的那些结点和目标结点都直接指向根结点，压缩树的高度
SetType Find(ElementType X,DisjSet S){
    if(S[X]<=0) return X;
    else return S[X]=Find(S[X],S);
}
//非递归版本
SetType Find(ElementType X,DisjSet S)
{   ElementType root,trail,lead;
    for(root=X;S[root]>0;root=S[root])
        ;  /* find the root */
    for(trail=X;trail!=root;trail=lead){
       lead=S[trail];//存储父结点下标   
       S[trail] =root;//让当前结点直接指向根结点   
    }  /* collapsing */
    return root ;
}
```

### Worst Case for Union-by-Rank and Path Compression

![image-20211104114127783](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211104114127783.png)

### Review

#### Equivalence Relations

>【Definition】A relation, ~, over a set, S, is said to be an equivalence relation over S if it is               symmetric, reflexive, and transitive over S.
>
>【Definition】Two members x and y of a set S are said to be in the same equivalence class if x ~ y.

```c
Algorithm: (Union / Find)
{   /* step 1: read the relations in */
    Initialize N disjoint sets;
    while ( read in a ~ b ) {
        if ( ! (Find(a) == Find(b)) )
	Union the two sets;
    } /* end-while */
    /* step 2: decide if a ~ b */
    while ( read in a and b ) 
        if ( Find(a) == Find(b) )   output( true );
        else   output( false );
}
```

