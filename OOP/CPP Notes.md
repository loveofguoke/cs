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
同样的函数名，不同的参数链，返回类型不同不算
对成员函数，显式参数链相同时，const与非const也构成重载，const对象会调用const函数，非const对象会调用非const函数，因为const会加到隐参数this上，所以参数链还是不同的
```cpp
void f(short i);
void f(double d);

f('a'); //error，与多个重载函数匹配
f(2); //error，与多个重载函数匹配
f(2L); //error，与多个重载函数匹配
f(3.2);


```




### Default arguments
给在函数声明处，与函数无关，编译器给的，相应代码生成在调用函数的地方而不在函数定义的地方，必须从右往左给

### access control
- public
- private
- protected 对子类有效
- friend: Can declare a global function as a friend, as well as a member function of another class, or even an entire class, as a friend. 同class的objects互为友元





### Object Interactive




### Inline Functions
有点像预处理宏，调用时将函数体在该处展开，需要在函数声明和定义处都使用inline关键词，inline函数的定义其实是声明，may not generate any code in .obj file

将inline function放在.cpp中时，编译器会将其视作static，只能在此编译单元被访问

将inline functions放在头文件里：
• So you can put inline functions’ bodies in header
file. Then #include it where the function is needed.
• Never be afraid of multi-definition of inline
functions, since they have no body at all.
• Definitions of inline functions are just declarations

class声明内的成员函数(含函数体)自动inline，如果函数体放在外面，则需要在两处都加上inline关键词，且函数体应放在头文件中。
• You can put the definition of an inline member
function out of the class braces.
• But the definition of the functions should be put
**before where they may be called**


### const
编译器保证它不被改变，其实可以改(在RAM里)

本地const变量(compile time)可以被优化
全局const，编译器一定会为其保留空间
静态全局const，也会被优化
参数，成员变量不会被优化，对const成员变量必须初始化
1.在初始化链中对该变量初始化
2.在声明处赋默认初始化值 const int i = 10;
3. **声明时通过函数之类的？？？？？？？**

const char* p = &a;
不能通过p改变其所指向的对象a


Can always treat a non-const value as const
可以将非const赋给const，可以加限制，不能减限制

You cannot treat a constant object as non-constant without an explicit cast(const_cast)

对于函数参数和返回值，传的是值，所以参数本身是安全的，但可以通过加const限定保证与参数相关联的对象不会被改变(当参数和返回值是指针或引用时)

不改成员变量的成员函数都应当加上const,函数原型声明与定义处都要加const


### static
第一次遇到static objects的定义语句时被构造

- 全局objects
不同编译单元内的全局static objects的创建顺序未知，同一编译单元内是按照定义顺序来创建的
逆序析构

- 成员变量
访问限于类内部，被类对象共享，但不包含于类实例，即不包含于实例的size()
静态成员函数只能访问静态成员变量，不需要有类实例存在
必须要在.cpp文件里定义该变量，并初始化(可以分开)，且不能加static前缀

