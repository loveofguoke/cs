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
All Sequential Containers(序列容器，**元素顺序保持**)
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

//vector的基本操作
如果x.size() = 100，
然后x[1000] = 100;
x的size不会变，因为其实是越界访问了，不会自动去拓展成1000size，这样可能不会报错，有时甚至取x[1000]也能得到100，不过这只是因为在硬件层面不存在"界"，所以越界就只是去相应地址操作了，且是堆空间，所以可能不会出错，但这是不安全的。
要扩展size只能x.push_back()或者在初始化时就给一个size值

//preallocate
vector<int> v(100);
v[80] = 1;
v[200] = 1; //bad

//add/remove/find
v.push_back(e);
v.pop_back(e);
v.insert(pos, e);
v.erase(pos);
v.clear();
v.find(first, last, item);

```
vector的空间不是连续的，是分块的
x.end()应该指向末尾元素的后一元素位置
泛型类(generic class):
- Have to specify two types: the type of the collection itself (here: vector) and the type of the elements that we plan to store in the collection

```cpp
//list，以链表形态实现，双向链表
x.push_back();
x.push_front();
x.pop_back();
x.pop_front();
x.insert(pos, e);
x.remove(item); //去找到item然后remove

迭代器时要用p != s.end(), 不能用p < s.end()，因为链表未必是stored in order的，即p的值未必小于s.end()

#include<iostream>
#include<list>
#include<string>
using namespace std;

int main()
{
    list<string> s;
    list<string>::iterator p;
    for(p = s.begin(); p != s.end(); p++)
        cout << *p << " ";
}

```

```cpp
//map，映射，基于内容映射(即不是基于下标映射)的容器
#include<iostream>
#include<map>
using namespace std;

int main()
{
    map<string, float> price;
    price["apple"] = 0.75;
    price["banana"] = 0.35;
    cout << price["orange"]; //0，虽然不存在，但是没报错
}

```



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
实际上只在编译时刻保护

• A const in C++ defaults to internal linkage,内部链接，默认状态下，const对象仅在文件内有效,编译器把用到该变量的地方替换成相应的值。若多个文件出现同名const变量，相当于再不同文件分别定义独立变量。
– the compiler tries to avoid creating storage for a const --
holds the value in its symbol table. 用到它了，找到这个symbol得到值即可
– extern forces storage to be allocated.
本地const变量(compile time)可以被优化, const int x = 123;

#### extern const限定
如果要在文件间共享const变量，且其初始值不是常量表达式，同时我们希望只需在一个文件中定义，在其它文件中声明并使用，则可以在声明和定义处都添加extern关键字，给初始值的地方是定义的地方。
``` cpp
// file1.cpp
extern const int bufsize = fcn();
//file1.h
extern const int bufsize;
//file2.cpp
extern const int bufsize;
```
####

- 常量特征只在执行改变其值的操作时才会发挥作用

- 由于const对象一旦创建就不能再更改，所以必须初始化，初始值可以是任意表达式
    - const int k; //错误 



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

### namespace
- avoid name clashes
- 表达classes, functions, variables的逻辑group
- 当需要对name进行封装时使用

``` cpp
namespace Math
{
    double abs(double );
    double sqrt(double );
    int trunc(double );
} // 没有分号

```
namespace只是声明，放在头文件中，
#### Using-Declarations
• Introduces a local synonym for name
• States in one place where a name comes from.
• Eliminates redundant scope qualification
``` cpp
void main()
{
    using MyLib::foo; //使用namespace里具体的东西
    using Mylib::Cat;
    foo();
    Cat c;
    c.Meow();
}

```
#### Ambiguities
当两个命名空间有同名函数时，如果都用了using namespace，然后仅用名称去调用，可能会有冲突。using-directives只是让你能够仅通过名称去用，而没有实际去用，所以只有当你显式调用的时候冲突才会产生。

#### 别名
``` cpp
namespace supercali{
    void f();
}

namespace short = supercali;
short::f();
```

#### 编译
``` cpp
//为了避免冲突我们用命名空间将函数声明包装
// old1.h
namespace old1 
{
    void f();
    void g();
}

//old2.h
namespace old2
{
    void f();
    void g();
}

//但我们能不能这样，不改头文件，就在要用的地方把它包起来
namespace old1
{
    #include "old1.h"
}

old1::h(); 编译器时将其汇编成 __old1_h，但是在其对应的源文件中并没有声明h()是属于old1命名空间的，所以汇编成 _h，因此在链接的时候找不到相关函数，报错
```
A::f(int, student ) 汇编成 __A_f_int_student
f(double )          汇编成 _f_double

老版本的C语言编译器不会加下划线和额外信息，只有函数名称，所以如果用老版本的二进制文件(.lib)和头文件，则新编译器会为头文件里被调用的函数都加上额外信息，导致链接的时候在二进制文件中找不到相应函数，报错。可以这样：
``` cpp
extern "C"
{
    #include "old1.h"
}
```
CPP编译器就会按老版本去编译里面的函数
正因此**Namespaces are open**,在多个同名的命名空间中的声明会被加到同一个命名空间中，因为其汇编代码都是加了该命名空间的前缀，所以对编译器而言是一样的，因此命名空间可以被分布到多个文件中

#### namespace composition
``` cpp
namespace mine
{
    using namespace first; //将first中的东西都放到mine里
    using namespace second;
    using first::y(); //当别人用mine::y时用的是first中的y
    using orig::Cat;
    void mystuff;
}

```
### Inheritance
接口重用，共享设计(而不是共享数据?)，包括成员变量、成员函数、接口(对外公布的东西)
为了便于维护，比如有两个类大部分结构都一样，如果出错了，每次都要改两个地方，如果使用继承就可以只改一个地方，即他们的超类，在超类定义共同的属性，子类继承超类的属性(所有的东西都在,属性和操作,但可能有些东西碰不了)，子类有自己的属性
子类和父类的关系: Is A

``` cpp
class Item
{

}

class CD: public Item //继承自Item
{

}

class DVD: public Item
{

}

```
#### 可视度
protected表示自己和子类可以访问，别人(main)不能访问
虽然子类中包含父类的所有信息，但是如果在父类中是private的，那就不能直接在子类中访问，可以通过调用父类中的成员函数来访问。
父类public的到子类还是public

#### ex
``` cpp
class Manager: public Employee //默认是private,私有继承，父类public的变成了子类的private，把父类当作自己的私有成员变量，protected则只有子类的子类知道父类是什么
{
public:
    Manager(const string& _name, const string& _ssn, const string& _title)
        : Employee(name, ssn), title(_title) {} //先调用父类的构造函数，再调用自己的
    
    void print() const //会覆盖父类中所有名称为print的重载函数，name hide
    {
        Employee::print(); //父类的同名函数
        cout << title << endl;
    }
protected:
    string title;
};

//子类继承父类的所有东西，所以构造子类对象时会构造出相应的父类对象，因此子类构造函数需要满足父类中至少一种构造函数的参数格式(可以有自己的额外参数)，且在初始化链中应当将父类中的参数传给父类的构造函数，让它去构造

```
#### 
子类的对象中有父类的对象，属于自己(会分配空间)，先调用父类构造函数来初始化父类的对象(有些是private的)，所以内存结构中先是父类对象才是自己的成员变量

### Polymorphism
#### up-casting 造型
sizeof在编译时运算,没有任何成员变量的类对象的size为1
可以把子类的对象当作父类的对象
把子类的指针和引用(值)赋给父类的指针和引用(类型)
指向D的指针可以赋给指向B的指针，D的引用可以赋给B的引用，不改变内容
B* ep = &D; //把D视作B，指向父类的指针可以指向子类，虽然指的是D类对象，但指针觉得指向的是B类对象
ep->print(); //用的是B类的print()
#### virtual
``` cpp
class B
{
public:
    virtual void f(); //告知子类可能会有此函数的不同版本
};

class D
{
public: 
    void f();
}
```
在父类函数加virtual关键字后，ep->f()，调用的是子类D的f()，即会去调用实际指向的对象自己版本的f()而不是简单地去用父类的f()

- Upcast: take an object of the derived class as an object of the base one.
    - Ellipse can be treated as a Shape
- Dynamic binding:
    - Binding: which function to be called
        - Static binding: call the function as the code //编译时刻确定，通过.运算符(对象本身)调用是静态绑定(即便有virtual)，通过指针和引用去调用一个virtual函数是动态绑定
        - Dynamic binding: call the function of the object
``` cpp
void render(Shape* p) { //Shape为父类， p为多态变量，有两个类型，静态类型是定义时的类型(编译时刻获得),以及运行时的动态类型(取决于实际指向什么类型的对象)
    p->render(); // calls correct render function
} // for given Shape!
```
8字节对齐
带有virtual的类成员有一个指针成员vptr，指向(自己的)virtual函数表(vtable,是静态的)，如果没有自己版本的函数，则用的是父类的版本，然后是父类对象和自己的成员变量。自己增加的虚函数会放在表末。可以用*vptr去调用

在类构造函数中调用虚函数，调用的是自己的版本，虚函数不起作用了。先分配空间再构造。vptr在构造函数最前面被写入(在初始化链前),在链接时vtable才被确定。其实构造函数内的f()依然是动态绑定，只是此时vptr指向的是自己的vtable，所以调用的是自己的版本
在父类成员函数中调用虚函数(this->f())，还是动态绑定，调用具体指向的对象的版本
``` cpp
Ellipse elly(20F, 40F);
Circle circ(60F);
elly = circ; // 10 in 5?

• Area of circ is sliced off
–(Only the part of circ that fits in elly gets copied)
• Vtable from circ is ignored; the vtable in elly is the Ellipse vtable //vptr在构造后不改变
elly.render(); // Ellipse::render()

```









