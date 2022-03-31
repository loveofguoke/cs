## OOP Characteristics
1.Everything is an object.
2.A program is a bunch of objects telling each other what to do by sending messages.
3.Each object has its own memory made up of other objects.
4.Every object has a type.
5.All objects of a particular type can receive the same messages.

### Compile unit
一个.cpp file是一个编译单元
每个源代码文件独立被编译
链接器把.o文件和静态库文件，引导代码等链接在一起生成可执行文件

### The header file
头文件里只放声明：
1.extern variables
2.function prototypes
3.class/struct declaration
4.inline function
```cpp
//标准头文件结构
#ifndef HEADER_FLAG
#define HEADER_FLAG
//Type declaration...
#endif
```
Tips for header
1.每个类声明分别放在一个的头文件中
2.对应一个头文件有一个前缀相同的源文件
3.头文件必须采用标准结构来防止重复声明变量和结构体

### string
是一个类，可以拼接，结尾无'\0'，可以用[]取字符
```cpp
#include<iostream>
#include<string>
using namespace std;
//---------------------------------------------//
    string s;
    //string的长度 
    s.length(); 
    //ctors
    string(const char *cp, int len);
    string(const string& s2, int pos);
    string(const string& s2, int pos, int len);
    //sub-string
    s.substr(int pos, int len);
    //alter string
    assign();
    insert(const string&, int len);
    insert(int pos, const string& s);
    erase();
    append(); //append单个字符
    replace();
    //search string
    s.find("hello", 0); //从pos 0开始找到第一次出现"hello"的pos(h)
//----------------------------------------------//
```
### dynamically allocated memory
```cpp
//new
new int;
new Stash; //容器
new int[10];
//delete
delete p;
delete[] p; 
//usage
int* p = new int[10];
delete[] p; //free 
```
delete不是new申请的空间会抛异常
不要连续delete相同的内存块
delete null是安全的，无事发生
**int* p = new int[10]时会附带起始地址及对应的内存块的字节数，用于delete**
**delete p, delete[] p都能free整个数组，但是只有delete[]能触发所有数组元素的dtors**

### reference
```cpp
char c;
char* p = &c;
char& r = c; //需要初始值,r成为c的别名
const int& z = x; //不能通过z改变x
int&* p; //illegal,没有指向引用的指针，必须指向实际对象
int&& k; //illegal, no references to references
int& k[10]; //illegal, no arrays of references, 数组的每个单元都是实体

void f(int& a)
{
    a = 30;
}

int main()
{
    int b = 0;
    f(b); // 当函数被调用时初始化，int& a = b;
    cout << b << endl; //30
    f(b * 3); //error,不是左值
}

```
reference不能是null, 不能改变绑定对象

### Class
```cpp
void S::f()
{
    ::f(); //global f()
    ::a++; //global a
    a--; //The a at class scope
}

class Point
{
public: 
    void init(int x, int y); //声明

private: 
    int x; //声明，不会产生实际代码
    int y;
};


```
隐含指向class object本身的指针this,为第一个参数
**函数参数入栈是先右后左的，所以this在栈顶**

### Ctors and Dtors
```cpp
struct Y
{
    float f;
    int i;
    Y(int a);
    ~Y();
};

Y y1[] = {Y(1), Y(2), Y(3)};



```
The compiler allocates all the storage for a scope at the opening brace of that scope.
构造器在对象被创建的时候触发，而不是被分配空间的时候触发，即一定是在运行到对象定义语句时被触发，而不是在编译器进入此scope预先为其分配好栈空间时触发
不能显式调用构造器
自动默认构造器什么也不干
当有构造器时，初始化必须按照构造器的要求来
对于有构造器的对象数组初始化，如果没有无参数构造器，则必须对每个数组单元显式调用构造器
某种意义上可以认为构造器的返回类型就是其对应的object类型

析构器永远没有参数，在被对象要被free(go out of scope)时由编译器调用
通常在析构里处理class object自身空间以外的其他资源，比如关闭打开的文件和delete申请的空间

### Initialization
```cpp
#include<iostream>
using namespace std;

class P
{
public:
    P(float f)
    {
        k = f;
        cout << k << "cons" << endl;
    }

    ~P()
    {
        cout << k << "des" << endl;
    }
private:
    float k;    
};

class Point
{
public:
    Point(float xa, float xb)
        : b(xb), a(xa) {}
private:
    P a;
    P b;
};

int main()
{
    Point p(1.0, 2.0);
}

//output
1cons
2cons
2des
1des

```
初始化链中才是真正的初始化，构造器里的已经是赋值了，初始化的顺序是声明的顺序，而不是初始化链中的顺序，如果成员变量是类，初始化顺序也就是create类对象的顺序，即调用构造器的顺序。而destroyed的顺序相反，和入栈顺序有关，maybe后声明的后入栈，所以在栈顶，先被pop后destroyed

```cpp
#include<iostream>
using namespace std;

class P
{
public:
    P()
    {
        cout << "default" << endl;
    }

    P(float f)
    {
        k = f;
        cout << k << "cons" << endl;
    }

    ~P()
    {
        cout << k << "des" << endl;
    }
private:
    float k;    
};

class Point
{
public:
    Point(float xa, float xb)
        /*: b(xb), a(xa)*/ 
        {
            cout << "Point cons" << endl;
            b = P(xb);
            a = P(xa);
        }
private:
    P a;
    P b;
};

int main()
{
    Point p(1.0, 2.0);
}

//output
default
default
Point cons
2cons
2des
1cons
1des
2des
1des

```
当不用初始化链时，相当于无参数create class P的object，所以此时P必须有无参数的默认构造器，同时由于放弃了初始化的机会，后面就不能再去调用对象本身的构造器，即不能b(xb)，而只能通过赋值来给"初值"，如b = P(xb)或b = xb，注意这两者的结果是相同的，因为b = xb时编译器会将xb隐式类型转换为P(xb)，依然会调用构造器

### container
All Sequential Containers(序列容器，元素顺序保持)
- vectors: variable array
- deque: dual-end queue
- list: double-linked-list
- forward_list: as it
- array: as "array"
- string: char. array
```cpp
//vector，模板类
#include<iostream>
#include<vector>
using namespace std;

int main()
{
    vector<int> x;
    for(int a = 0; a < 1000; a++)
        x.push_back(a);
    vector<int>::iterator p;
    for(p = x.begin(); p < x.end(); p++)
        cout << *p << endl;
    return 0;
}





```
vector的空间不是连续的，是分块的
x.end()应该指向末尾元素的后一元素位置
泛型类(generic class):
- Have to specify two types: the type of the collection itself (here: vector) and the type of the elements that we plan to store in the collection




### Function overloading
同样的函数名，不同的参数链





### Default arguments
