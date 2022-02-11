# FDS-Binary Tree

> 1. 子树不相交
> 2. 结点的度：结点的子树个数
> 2. 树的度：Max(结点的度)
> 3. 叶结点：度为0的结点
> 4. 树的深度/高度：所有结点的最大层次
> 5. 完美二叉树/满二叉树：结点满
> 6. 完全二叉树：结点序号与完美二叉树相同，只缺最右下的几个
> 7. n0=n2+1  因为n-1=e

```c
//操作集
Boolean IsEmpty(BinTree BT);
void Traversal(BinTree BT);
BinTree CreatBinTree();
```

```c
//结构定义
typedef char ElementType;
typedef struct TNode *Position;
typedef Position BinTree;
struct TNode{
    ElementType Data;
    BinTree Left;
    BinTree Right;
};
```

```c
//获取二叉树高度
int GetHeight( BinTree BT ){
    if(BT==NULL) return 0;//空树
    int lheight=0;
    int rheight=0;
    int height=1;
    if(BT->Left) lheight=GetHeight(BT->Left);
    if(BT->Right) rheight=GetHeight(BT->Right);
    height+=lheight>rheight?lheight:rheight;
    return height;
}
```

```C
//遍历方式
void PreOrderTraversal(BinTree BT){//先序遍历——根，左子树，右子树
    if(BT){
        printf("%d",BT->Data);//可以是其他操作
        PreOrderTraversal(BT->Left);
        PreOrderTraversal(BT->Right);
    }
}

void PreOrderTraversal(BinTree BT){//非递归先序遍历
    BinTree T=BT;
    Stack S=CreatStack(MaxSize);//创建并初始化堆栈S
    while(T||!IsEmpty(S)){
        while(T){	//向左并将沿途结点压栈，存储路径
            printf("%d",T->Data);
            Push(S,T);
            T=T->Left;
        }
        if(!IsEmpty(S)){
            T=Pop(S);	//左子树遍历结束，弹出结点
            T=T->Right;	//对右子树进行以上操作
        }
    }
}

void InOrderTraversal(BinTree BT){//中序遍历——左子树，根，右子树
	if(BT){
        InOrderTraversal(BT->Left);
        printf("%d",BT->Data);
        InOrderTraversal(BT->Right);
    }
}

//Push序列即前序遍历序列
void InOrderTraversal(BinTree BT){//非递归中序遍历
    BinTree T=BT;
    Stack S=CreatStack(MaxSize);//创建并初始化堆栈S
    while(T||!IsEmpty(S)){
        while(T){	//向左并将沿途结点压栈，存储路径
            Push(S,T);
            T=T->Left;
        }
        if(!IsEmpty(S)){
            T=Pop(S);	//左子树遍历结束，弹出结点
            printf("%d",T->Data);
            T=T->Right;	//对右子树进行以上操作
        }
    }
}

void PostOrderTraversal(BinTree BT){//后序遍历——左子树，右子树，根
	if(BT){
        PostOrderTraversal(BT->Left);
        PostOrderTraversal(BT->Right);
        printf("%d",BT->Data);
    }
}

void PostOrderTraversal(BinTree BT){//非递归后序遍历 pop后还要再push进去，第二次pop出来时需要printf，所以做个记号，表示是第几次push和pop，或者用两个栈
    BinTree T=BT;
    Stack S=CreatStack(MaxSize);//创建并初始化堆栈S
    while(T||!IsEmpty(S)){
        while(T){	//向左并将沿途结点压栈，存储路径
            Push(S,T);
            T=T->Left;
        }
        if(!IsEmpty(S)){
            T=Pop(S);	//左子树遍历结束，弹出结点
            printf("%d",T->Data);
            T=T->Right;	//对右子树进行以上操作
        }
    }
}

//队列实现，首先将根结点入队，然后开始循环：结点出队并访问该结点，其左右结点入队
void LevelOrderTraversal(BinTree BT){//层次遍历，从上至下，从左至右
	Queue Q;
    Bintree T;
    if(!BT) return;	//空树
    Q=CreatQueue(MaxSize);	//创建并初始化队列Q
    AddQ(Q,BT);//根结点入队
    while(!IsEmptyQ(Q)){
        T=DeleteQ(Q);//出队
        printf("%d\n",T->Data);
        if(T->Left) AddQ(Q,T->Left);//左结点入队
        if(T->Right) AddQ(Q,T->Right);//右结点入队
    }
}
```

```c
//输出叶结点
void PreOrderTraversal(BinTree BT){//先序遍历——根，左子树，右子树
    if(BT){
        if(!BT->Left&&!BT->right)
        printf("%d",BT->Data);//可以是其他操作
        PreOrderTraversal(BT->Left);
        PreOrderTraversal(BT->Right);
    }
}
```

> 对二元运算表达式树
>
> 1. 先序遍历得到前缀表达式
> 2. 后序遍历得到后缀表达式
> 3. 中序遍历，左子树开始时加左括号，右子树结束时加右括号得到中缀表达式
> 4. 中序遍历和任意另一种遍历可以唯一确定一个二叉树

> 给定树T1，T2,如果T1通过若干次左右结点交换可变成T2，则这两棵树同构
>
> 其实就是两棵树相同结点的左右儿子均相同(次序随意)

```c
//用结构数组表示二叉树，用数组下标来定位,哪个下标没出现就是根结点
#define MaxTree 10
#define ElemrntType char
#define Tree int
#define Null -1

struct TreeNode{
    ElementType Element;
    Tree Left;//左结点下标
    Tree Right;//右结点下标
}T1[MaxTree],T2[MaxTree];
```

#### Threaded Binary Trees

利用空出来的指针作为线索. 分为先序，中序，后序遍历线索树

>以中序遍历线索树为例
>
>Rule 1:  If Tree->Left is null, replace it with a pointer to the **inorder** predecessor of Tree.
>
>Rule 2:  If Tree->Right is null, replace it with a pointer to the **inorder** successor of Tree.
>
>Rule 3:  There must not be any loose threads.  Therefore a threaded binary tree must have a **head node** of which the left child points to the first node.

```c
typedef struct ThreadedTreeNode *PtrTo ThreadedNode;
typedef struct PtrToThreadedNode ThreadedTree;
typedef struct ThreadedTreeNode{
       int LeftThread;  /* if it is TRUE, then Left */
       ThreadedTree Left; /* is a thread, not a child ptr. */
       ElementType Element;
       int RightThread; /* if it is TRUE, then Right */
       ThreadedTree Right; /* is a thread, not a child ptr. */
}
```

<img src="C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211114094923869.png" alt="image-20211114094923869" style="zoom:80%;" />

中序遍历时，如果右指针是线索，那么直接输出右指针指向的结点键值并移动至该结点。如果右指针是结点，那么就寻找当前结点右子树(因为左子树的肯定都在此结点前面出现过了)中序遍历的第一个结点并输出和移动。**加头结点取消了根结点的特殊性，使算法统一**

## Binary Search Tree

> 1. 静态查找与动态查找
> 1. Every node has a key which is an integer, and the keys are distinct.
> 2. 非空左子树的所有键值小于其根结点的键值
> 3. 非空右子树的所有键值大于其根结点的键值
> 4. 左右子树都是二叉搜索树

>**给你一个查找序列，判断是否可能**
>
>16-10-20-5 impossible,because we want to find 5,but 20>10

```c
/*Operations:
SearchTree  MakeEmpty( SearchTree T ); 
Position  Find( ElementType X, SearchTree T ); 
Position  FindMin( SearchTree T ); 
Position  FindMax( SearchTree T ); 
SearchTree  Insert( ElementType X, SearchTree T ); 
SearchTree  Delete( ElementType X, SearchTree T ); 
ElementType  Retrieve( Position P ); 
*/

//T(N)=S(N)=O(d) where d is the depth of X
Position Find(ElementType X,SearchTree T){
    if(T == NULL) 
    	return NULL; /* not found in an empty tree */
    if (X < T->Element)  /* if smaller than root */
    	return Find(X, T->Left);  /* search left subtree */
    else if(X > T->Element)  /* if larger than root */
	  	return Find(X, T->Right);  /* search right subtree */
    else   /* if X == root */
	  	return T;  /* found */
} 
//迭代版本
Position Find(ElementType X,BinTree BST){//查找X，返回其结点地址
	while(BST){
        if(X>BST->Data)
            BST=BST->Right;
        else if(X<BST->Data)
            BST=BST->Left;
        else
            return BST;
    }
    return NULL;
}

//T(N)=O(d) 对于完全二叉树，最小值一定是叶结点，但最大值不一定
Position FindMin(BinTree BST);//查找最小值并返回地址
Position FindMax(BinTree BST){//查找最大值并返回地址
	if(BST)
        while(BST->Right) BST=BST->Right;
    return BST;
}

BinTree Insert(ElementType X,BinTree BST){
    if(!BST){
        //原树为空，生成并返回一个结点的二叉搜索树
        BST=malloc(sizeof(struct TreeNode));
        BST->Data=X;
        BST->Left=BST->Right=NULL;
    }else //查找要插入的位置
        if(X<BST->Data)
            BST->Left=Insert(X,BST->Left);
    				   //递归插入左子树
    	else if(X>BST->Data)
            BST->Right=Insert(X,BST->Right);
    	//else X已存在，什么都不做
    return BST;
}

BinTree Delete(ElementType X,BinTree BST){
    Position Tmp;
    if(!BST) printf("Not Found");
    else if(X<BST->Data)
        BST->Left=Delete(X,BST->Left);
    else if(X>BST->Data)
        BST->Right=Delete(X,BST->Right);
    else //找到了
        if(BST->Left&&BST->Right){ //被删结点有左右两个子结点
            Tmp=FindMin(BST->Right); //在右子树中找到最小元素填充删除结点
            BST->Data=Tmp->Data;
            BST->Right=Delete(BST->Data,BST->Right);
            					//改为删除结点的右子树中最小元素
        }else{ //被删结点有一个或无子结点
            Tmp=BST;
            if(!BST->Left)
                BST=BST->Right;
            else if(!BST->Right)
                BST=BST->Left;
            free(Tmp);
        }
    return BST;
}
//找左边最大元或右边最小元
```

<img src="C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211114104636144.png" alt="image-20211114104636144" style="zoom:80%;" />

####  Average-Case Analysis

<img src="C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211114105855765.png" alt="image-20211114105855765" style="zoom:80%;" />

## Balanced Binary Tree

> 平衡因子 BF：左右子树高度差
>
> 平衡二叉树：空树或者任一结点左右子树高度差的绝对值不大于1
>
> 设nh是高度为h的平衡二叉树的最小结点数：满足nh=n(h-1)+n(h-2)+1 斐波那契数列
>
> 所以平衡二叉树的h=O(log2n)

> 平衡二叉树的调整
>
> 1. 插入结点在被破坏者的右子树的右子树上，RR旋转
> 2. 插入结点在被破坏者的左子树的左子树上，LL旋转
> 3. 同理，还有LR,RL旋转

```c
void FreeTree(Tree T) //释放树的空间
{
    if(T->Left) FreeTree(T->Left);
    if(T->Right) FreeTree(T->Right);
    free(T);
}
```

### Review

>Concepts

<img src="C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211114084531224.png" alt="image-20211114084531224" style="zoom:80%;" />

>General Tree

<img src="C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211114085113449.png" alt="image-20211114085113449" style="zoom: 80%;" />

>Expression Trees

利用后缀表达式建树：借助地址栈，运算数结点地址入栈，遇到运算符时，先pop两个结点，再让运算符入栈，且运算符结点右指针指向先出栈的结点，左指针指向后出栈的结点。

>前序，中序，后序遍历的堆栈操作是一样的

>**一颗树先序遍历，中序遍历，后序遍历的第一个结点分别是什么？**

