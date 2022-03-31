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

### Class




