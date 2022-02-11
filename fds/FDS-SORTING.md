# FDS-SORTING

## Insertion Sort

```c
void InsertionSort ( ElementType A[ ], int N ) 
{ 
      int  j, P; 
      ElementType  Tmp; 

      for ( P = 1; P < N; P++ ) { 
	Tmp = A[ P ];  /* the next coming card */
	for ( j = P; j > 0 && A[ j - 1 ] > Tmp; j-- ) 
	      A[ j ] = A[ j - 1 ]; 
	      /* shift sorted cards to provide a position 
                       for the new coming card */
	A[ j ] = Tmp;  /* place the new card at the proper position */
      }  /* end for-P-loop */
}
//最坏情况,A[]逆序 T(N)=O(N^2)
//最好情况,A[]顺序 T(N)=O(N) 
```



## A Lower Bound for Simple Sorting Algorithms

An **inversion(逆序对)** in an array of numbers is any ordered pair ( i, j ) having the property that i < j but A[i] > A[j].

排序就是在消除逆序对

T(N, I) = O(I+N) where I is the number of inversions in the original array.

The average number of inversions in an array of N distinct numbers is  N ( N-1 ) / 4.

**Any algorithm that sorts by exchanging adjacent elements requires $$\Omega$$ ( N^2) time on average.**

(基于交换的排序算法，每次消除一对逆序对)



## **Shellsort** ---- by Donald Shell

定义增量序列，h1=1,即最后一步就是插入排序

根据增量分成多个子序列(隔几个选一个)分别进行插入排序

前一次排序的结果会保留到下一次排序，因为逆序对已经被消除了

#### Shell's increment sequence 取半数

h~t~=$\lfloor$N/2$\rfloor$,h~k~=$\lfloor$h~k+1~/2$\rfloor$

```c
void Shellsort( ElementType A[ ], int N ) 
{ 
      int  i, j, Increment; 
      ElementType  Tmp; 
      for ( Increment = N / 2; Increment > 0; Increment /= 2 )  
	/*h sequence */
	for ( i = Increment; i < N; i++ ) { /* insertion sort [0,increment-1]是每个子序列的首元素，从increment开始，相当于原来的从A[1]开始，即从序列的第二个开始*/
	      Tmp = A[ i ]; 
	      for ( j = i; j >= Increment; j - = Increment ) //只和与自己同序列的元素排序，相邻元素间相差increment，每次和A[j-increment]比较，所以j>=increment
		if( Tmp < A[ j - Increment ] ) 
		      A[ j ] = A[ j - Increment ]; 
		else 
		      break; 
		A[ j ] = Tmp; 
	} /* end for-I and for-Increment loops */
}
```

#### Worst Case

<img src="C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211210143014074.png" alt="image-20211210143014074" style="zoom: 67%;" />

增序序列有共同的因子，以该因子为增量的子序列都有序，则相当于最后完整地执行了一遍插入排序

#### Hibbard’s Increment Sequence

h~k~ =2^k^ - 1 ---- consecutive increments have no common factors

The worst-case running time of Shellsort, using Hibbard’s increments, is  Q(N^3/2^).

T~avg~=O(N^5/4^)

#### Sedgewick's Increment Sequence

<img src="C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211210144548366.png" alt="image-20211210144548366" style="zoom:67%;" />



## Heapsort

给定一个数组，利用该数组建最大堆，同时将deletemax的结果直接依次存在无序序列尾部，最后得到增序序列，且无需利用多余的空间

```c
//注意，在堆排序时，堆不再有哨兵节点，因此是从下标为0开始的
//left_child = parent*2+1
//right_child = parent*2+2
//#define leftchild(i) ( 2*(i)+1 )
void Heapsort( ElementType A[ ], int N ) 
{  int i; 
    for ( i = N / 2; i >= 0; i - - ) /* BuildHeap */ 
        PercDown( A, i, N ); 
    for ( i = N - 1; i > 0; i - - ) { 
        Swap( &A[ 0 ], &A[ i ] ); /* DeleteMax */ 
        PercDown( A, 0, i ); 
    } 
}

void PercDown( ElementType A[ ], int p ,int Size)
{ /* 下滤：将H中以H->Data[p]为根的子堆调整为最大堆 */
    int Parent, Child;
    ElementType X;
 
    X = A[p]; /* 取出根结点存放的值 */
    for( Parent=p; Parent*2+1<Size; Parent=Child ) {
        Child = Parent * 2+1;
        if( (Child!=Size-1) && (A[Child]<A[Child+1]) )
            Child++;  /* Child指向左右子结点的较大者 */
        if( X >= A[Child] ) break; /* 找到了合适位置 */
        else  /* 下滤X */
            A[Parent] = A[Child];
    }
    A[Parent] = X;
}
```

#### Time Complexity

The average number of comparisons used to heapsort a random permutation of *N* distinct items is 

  2*N* log *N* - O( *N* log log *N* ) .



## **Mergesort**

### Recursive version

```c
//要求写出
//至此为止唯一需要额外空间的排序算法 S(N)=O(N)
//如果在Merge的时候申请空间，S(N)=O(NlogN)
void MSort( ElementType A[ ], ElementType TmpArray[ ], 
		int Left, int Right ) 
{   int  Center; 
    if ( Left < Right ) {  /* if there are elements to be sorted */
	Center = ( Left + Right ) / 2; 
	MSort( A, TmpArray, Left, Center ); 	/* T( N / 2 ) */
	MSort( A, TmpArray, Center + 1, Right ); 	/* T( N / 2 ) */
	Merge( A, TmpArray, Left, Center + 1, Right );  /* O( N ) */
    } 
} 

void Mergesort( ElementType A[ ], int N ) 
{   ElementType  *TmpArray;  /* need O(N) extra space */
    TmpArray = malloc( N * sizeof( ElementType ) ); 
    if ( TmpArray != NULL ) { 
	MSort( A, TmpArray, 0, N - 1 ); 
	free( TmpArray ); 
    } 
    else  FatalError( "No space for tmp array!!!" ); 
}

/* Lpos = start of left half, Rpos = start of right half */ 
void Merge( ElementType A[ ], ElementType TmpArray[ ], 
	       int Lpos, int Rpos, int RightEnd ) 
{   int  i, LeftEnd, NumElements, TmpPos; 
    LeftEnd = Rpos - 1; 
    TmpPos = Lpos; 
    NumElements = RightEnd - Lpos + 1; 
    while( Lpos <= LeftEnd && Rpos <= RightEnd ) /* main loop */ 
        if ( A[ Lpos ] <= A[ Rpos ] ) 
	TmpArray[ TmpPos++ ] = A[ Lpos++ ]; 
        else 
	TmpArray[ TmpPos++ ] = A[ Rpos++ ]; 
    while( Lpos <= LeftEnd ) /* Copy rest of first half */ 
        TmpArray[ TmpPos++ ] = A[ Lpos++ ]; 
    while( Rpos <= RightEnd ) /* Copy rest of second half */ 
        TmpArray[ TmpPos++ ] = A[ Rpos++ ]; 
    for( i = 0; i < NumElements; i++, RightEnd - - ) 
         /* Copy TmpArray back */ 
        A[ RightEnd ] = TmpArray[ RightEnd ]; 
}
```

#### Time Complexity

T(1)=1

T(N)=2T(N/2)+O(N)

=2^k^T(N/2^k^)+k*O(N)

=NT(1)+logN*O(N)

=**O(N+NlogN)** 

一般比quicksort慢，因为做了许多非排序的步骤，内存访问耗时间

### Iterative version

```c
#include <stdio.h>

#define ElementType int
#define MAXN 100

void merge_pass( ElementType list[], ElementType sorted[], int N, int length ){
    for(int i=0;i<N;i+=2*length){
            int Lpos=i;
            int Rpos=i+length;
            int Tmp=i;
            while(Lpos<i+length&&Rpos<i+2*length&&Rpos<N){
                if(list[Lpos]<list[Rpos]) sorted[Tmp++]=list[Lpos++];
                else sorted[Tmp++]=list[Rpos++];
            }
        while(Lpos<i+length) sorted[Tmp++]=list[Lpos++];
        while(Rpos<i+2*length&&Rpos<N) sorted[Tmp++]=list[Rpos++];
    }
}

void output( ElementType list[], int N )
{
    int i;
    for (i=0; i<N; i++) printf("%d ", list[i]);
    printf("\n");
}

void  merge_sort( ElementType list[],  int N )
{
    ElementType extra[MAXN];  /* the extra space required */
    int  length = 1;  /* current length of sublist being merged */
    while( length < N ) { 
        merge_pass( list, extra, N, length ); /* merge list into extra */
        output( extra, N );
        length *= 2;
        merge_pass( extra, list, N, length ); /* merge extra back to list */
        output( list, N );
        length *= 2;
    }
} 

int main()
{
    int N, i;
    ElementType A[MAXN];

    scanf("%d", &N);
    for (i=0; i<N; i++) scanf("%d", &A[i]);
    merge_sort(A, N);
    output(A, N);

    return 0;
}
```

>重点来了

## **Quicksort**

目前所知的实践过程中最快的排序算法(非并行排序)

### Time Complexity



### Space Complexity

虽然在一次run中只需要O(1)的额外空间，但是要递归多次，所以**空间复杂度并不是O(1)**

最优情况下为O(logN),此时partition正好或接近二分，只需递归logN层

最坏情况下为O(N),此时partition每次都分为1和rest，退化为冒泡排序，需要递归N层









# Hashing(散列)

## Concepts

> **非**基于比较的排序算法
>
> nlogn并不是optimal time
>
> 不考虑/处理冲突的话 search insert delete 均为O(1)
>
> 以常数平均时间执行插入，删除和查找。但是任何需要元素间排序信息的操作是不支持的，例如FindMin,FindMax,以及以线性时间将排过序的整个表打印

### Search

通过值来寻找位置(地址或下标，0-TableSize-1)，基于哈希函数映射的查找是很快的。

哈希函数的自变量是不同的键值key(关键字),可能是数字，也可能是字符串，而函数值就是key在hash表中所处的位置。

### Implement

由于函数特性，不同的键值可能映射到同一个位置，就会产生冲突。有两种解决方案，一是在同一位置多开几个slots,允许冲突的键值共享一个位置，主要用链表数组实现；二是设计冲突解决函数，当冲突产生时，通过二级映射映射到空位置，这样就可以用一维数组实现了。

### Hash Function

当关键字为整数时，一般是**key%TableSize**，通常使TableSize为质数。

当关键字是字符串时，对字符串中字符的ASCII码进行计算处理

比较好的函数是计算$\sum_{i=0}^{KeySize-1}Key[KeySize-i-1]*32^i$，利用秦九韶算法实现

```c
Index Hash(const char* Key,int TableSize){
    unsigned int HashVal=0;
    while(*Key!='\0')
        HashVal=(HashVal<<5)+*Key++; //可以用按位异或实现
    return HashVal%TableSize;
}
```

当然，如果字符串过长，可能会导致前面的结果被移出，此时需要合适地选择字符串中的几个字符进行计算

下面给出几种简单的冲突处理方法

## Separate Chaining(分离链表法)



## Open Addressing(开放地址法)

```c
Algorithm: insert key into an array of hash table
{
    index = hash(key);
    initialize i = 0 ------ the counter of probing;
    while ( collision at index ) {
	index = ( hash(key) + f(i) ) % TableSize;	//f(0)=0,sometimes two tablesizes are not equal
	if ( table is full )    break;
	else    i ++;
    }
    if ( table is full )
	ERROR (“No space left”);
    else
	insert key at index;
}
```

### Linear Probing

>**f(i)=i**		i从1开始

### Quadratic Probing

>**f(i)=i^2^ or f(i)=-i^2^**		i从1开始

**定理**：如果使用平方探寻，且哈希表的大小为质数，且哈希表至少有一半的位置空着，那么总能够成功地插入新的元素

**Note**：如果哈希表的大小是形式为4k+3的质数，那么平方探寻可以探寻整一个哈希表

>**f(i)=+-i^2^ 	则先是向右探测，再向左探测**

### Double Hashing

>冲突解决方案的一种
>
>**f(i)=i*hash~2~(x)**

hash~2~(x)=R-(x%R)，R是小于哈希表大小的第一个质数

## Rehashing

>Build another table that is **about twice** as big
>
>实际大小为大于原大小两倍的第一个质数



# Review

每种排序算法的run的概念

快速排序

最大流的寻找

注意邻接表形式是否给定

如何在BST中找到第k大的数
