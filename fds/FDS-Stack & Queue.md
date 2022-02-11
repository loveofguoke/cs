# FDS-Stack & Queue

### Lists

>**Operations
>Finding the length, N, of a list.
>Printing all the items in a list.
>Making an empty list.
>Finding the k-th item from a list, 0 <= k < N.
>Inserting a new item after the k-th item of a list, 0 <= k < N.
>Deleting an item from a list.
>Finding next of the current item from a list.
>Finding previous of the current item from a list.**

```c
/*Sequential Lists  MaxSize has to be estimated
Find_Kth takes O(1);
Insertion and Deletion take O(N);
*/
Seq_Lists[N];
```

```c
/*Linked Lists	
Find_Kth takes O(N);
Insertion and Deletion take O(1);
Sometimes we will add a dummy head node
*/
typedef int ElementType;
typedef struct Node *PtrToNode;
typedef PtrToNode List;
typedef PtrToNode Position;
struct Node {
    ElementType Element;
    Position Next;
};
```

```c
/*Doubly Linked Circular Lists
We will add a dummy head node
*/
typedef int ElementType;
typedef struct Node *PtrToNode;
typedef PtrToNode Dou_List;
typedef PtrToNode Position;
struct Node {
    ElementType Element;
    Position prior;
    Position next;
};
```

![image-20211113101105415](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211113101105415.png)

#### Applications

```c
/*Representation of a polynomial
Finding degree,max{ei},of a polynomial.
Addition of two polynomials.
Subtraction between two polynomials.
Multiplication of two polynomials.
Differentiation of a polynomial.
*/
typedef struct poly_node *poly_ptr;
struct poly_node {
      int Coefficient;  /* assume coefficients are integers */
      int Exponent;
      poly_ptr Next;
};
typedef poly_ptr a;
```

```c
/*Multilists
Courses-Students Match
*/
typedef int/char* ElementType;
typedef struct Node *PtrToNode;
typedef PtrToNode Dou_List;
typedef PtrToNode Position;
struct Node {
    ElementType Student;
    ElementType Course;
    Position Stu_next;//Next course the student takes
    Position Cou_next;//Next student takes the course
};
Position Stu_Last[M];//记录每位学生最后一门课程，每次插入都从末尾插
Position Cou_Last[N];//记录每门课程最后一位学生，每次插入都从末尾插
```

![image-20211113102429023](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211113102429023.png)

```c
/*Cursor Implementation of Linked Lists (no pointer)
用下标表示地址，Next指向下一块可用的空间
*/
```

>初始

![image-20211113104843950](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211113104843950.png)

>malloc & free

![image-20211113105751838](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211113105751838.png)

### Stack

```c
/*Operations:
Int IsEmpty(Stack S);
Stack CreateStack( ); 
DisposeStack(Stack S); 
MakeEmpty(Stack S); 
Push(ElementType X,Stack S); 
ElementType Top(Stack S); 
Pop(Stack S); 
本质：比A先进栈但比A后出栈的序列{Bi}必须是逆序的
或者从push和pop的操作序列来考虑，操作序列从头开始的任意一段，其push数目必须大于等于pop数目
排列组合一下，总可能性为：
*/
```

![image-20211113112445289](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211113112445289.png)

***Note: 插入和删除一定是在链表头部进行***

```c
/*Array Implementation
Empty: Top==-1 
Full: Top==Maxsize-1
*/
typedef struct StackRecord *Stack;
struct StackRecord{
	int Capacity;  /* size of stack */
	int TopOfStack;  /* the top pointer */
	/* ++ for push, -- for pop, -1 for empty stack */
	ElementType *Array;    /* array for stack elements */
}; 
Stack S;
```

#### Applications

```c
/*Balancing Symbols
Check if parenthesis ( ), brackets [ ], and braces { } are balanced.
*/
Algorithm { //O(N)
    Make an empty stack S;
    while (read in a character c) {
        if (c is an opening symbol)
            Push(c, S);
        else if (c is a closing symbol) {
            if (S is empty)  { ERROR; exit; }
            else  {  /* stack is okay */
                if  (Top(S) doesn’t match c)  { ERROR, exit; }
                else  Pop(S);
            }  /* end else-stack is okay */
        }  /* end else-if-closing symbol */
    } /* end while-loop */ 
    if (S is not empty)  ERROR;
}
```

```c
/*Postfix Evaluation
O(N)
*/
运算数入栈，遇到运算符，pop两个运算数计算，将结果push，当结束输入时pop得到最终结果
```

```c
/*Infix to Postfix Conversion
O(N)
*/
运算数直接输出，运算符入栈，若其优先级<=栈顶运算符，则pop栈顶元素，继续与新栈顶元素比较...若优先级>栈顶运算符，则不pop
事实上=时看的是运算符结合律，一般运算符都是从左到右，所以=时先入栈的(更靠左)隐藏优先级高于后入栈的，要pop.但如果是右结合算符，例如^则=时不需要pop
对于(括号，当它在栈外时优先级最高，当它在栈内时优先级最低，遇到)时，一直pop到(
```

```c
/*Function Calls -- System Stack
因为每次调用的函数的本地变量所占空间不同，所以需要fp指针告诉程序return回上一层后需要pop多少本地变量(即sp需要回到哪里)
*/
```

![image-20211113131242881](C:\Users\12445\AppData\Roaming\Typora\typora-user-images\image-20211113131242881.png)

### Queue

```c
/*Operations:
int IsEmpty(Queue Q);
Queue CreateQueue(); 
DisposeQueue(Queue Q); 
MakeEmpty(Queue Q); 
Enqueue(ElementType X,Queue Q); 
ElementType Front(Queue Q); 
Dequeue(Queue Q); 
front=rear=0;
front指向队头元素，rear指向队尾元素后一个位置
Empty: front==rear
Full: front==(rear+1)%Maxsize
*/
typedef QueueRecord *Queue;
struct QueueRecord{
	int Capacity ;  /* Optional - max size of queue */
	int Front; /* the front pointer */
	int Rear;  /* the rear pointer */
	int Size;  /* Optional - the current size of queue */
	ElementType *Array; /* array for queue elements */
 } ; 
Queue Q;

void Enqueue(ElementType X,Queue Q){
    //从队尾入队
    if(Q->front==(Q->rear+1)%Maxsize){
        printf("Queue is full!");
        return;
    }
    else (Q->Array)[(Q->rear++)%MaxSize]=X;
}

ElementType Dequeue(Queue Q){
    //从队头出队
    if(IsEmpty(Q)){
        printf("Queue is empty!");
        return -1;
    }
    else return (Q->Array)[(Q->front++)%Maxsize];
}

int IsEmpty(Queue Q){
    if(Q->front==Q->rear) return 1;
    return 0;
}
```

