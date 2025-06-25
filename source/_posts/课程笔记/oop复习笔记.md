---
title: oop复习
date: 2025-06-21 18:55:00
tags: 
- 编程语言
- 面向对象编程
- CS课程
categories: 
- 课程笔记
excerpt: 复习阶段整理的oop课程笔记，整体分为核心知识点回顾以及针对部分历年卷题目的整理
thumbnail: https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250509094812819.png?imageSlim
---
# 知识点回顾

- `end（）`返回的是容器最后一个位置的**下一个位置**的迭代器
- `string::npos`是 `size_t`类型的最大值;
- `to_string`将数字转换成字符串:

  ```cpp
  int num = 123;
  string str = to_string(num);
  ```
- `stoi`将字符串转换成整数
- 参数的默认值只能从右到左给出；默认值只能出现在函数的原型或者将定义和声明放在一起，而不能在分离的定义中声明
- 列表初始化是按成员的**声明顺序**执行的，和成员在列表中的顺序无关。

## 实验操作

#### 截取字符串

用邮箱地址的截取，说明 `rfind`与 `str`等方法的使用：

```cpp
string email = "user.name@example.com";

// 获取用户名的部分
int atPos = email.find('@');
string username = email.substr(0, atPos); // result: "user.name"

// 获取域名部分
string domain =  email.substr(atPos + 1); // result: "example.com"

// 获取顶级域名(最后一个.之后的部分)
int lastDotPos = email.rfind('.');
string topLevelDomain = email.substr(lastDotPos + 1); // result: "com"
```

## Stream

### 文件流

- 一个简单的输入/输出：流

```cpp
#include <fstream>  // 需要包含这个头文件
using namespace std;

// 文件输出（写入文件）
ofstream outFile("output.txt");
outFile << "Hello World" << endl;
outFile.close();

// 文件输入（读取文件）
ifstream inFile("input.txt"); 
string line;
getline(inFile, line);
inFile.close();

```

> `getline()`函数可以显式指定终止符（第三个参数），默认为 `\n`也就是换行符。并且会将终止符丢弃

#### 位或运算符指定模式

可以使用 `｜`来指定多个模式：

```cpp
// 组合使用打开模式
ofstream outFile("test.txt", ios::out | ios::app);
```

### 字符串流

字符串流的作用原理：

- 作用: 将字符串转换成一个类似于输入流的对象;
- 内部维护了一个字符串和一个位置指针;
- 每次读取时, 位置指针向后移动, 且自动跳过空白字符.

`stringstream`表示双向字符串流

#### 字符串分词

我们可以将字符串转换成字符串流，来实现字符串的分词：

```cpp
#include <string>
#include <iostream>
#include <sstream>

...

string name("xiao ming");

istringstream is(name);
string token;
while(is >> token)
	cout << token << endl;
```

> 按照空白字符i.e. 空格、`\t`, `\n`来分词

#### 字符串的拼接

```cpp
#include <sstream>
#include <string>
#include <iostream>

using namespace std;

int main() {
    ostringstream oss;
    string name = "Alice";
    int age = 25;
  
    oss << "Name: " << name << ", Age: " << age;
    string result = oss.str();
    cout << result << endl;
}

```

**Output:**

```shell
Name: Alice, Age: 25
```

> 通过 `.str()`方法可以对象转换为字符串类型, 从而**格式化输出**.
>
> `.str("")`方法可以**清空**字符串流:

## STL

#### for-each

以更简单的方式遍历容器内元素:

```cpp
std::map<std::string, double> price;
// Assume we've inserted a lot of name-price pairs
for(auto [key, value]: price){
    std::cout << key << ": " << value << std::endl;
}
```

### map

#### lower_bound

使用～函数可以查找不小于某个值的第一个键：

```cpp
std::map<long long, int> tags{{10, 1}, {100, 2}, {1000, 3}, {10000, 4}, {10000000000, 10}};
std::map<long long, int>::iterator it = tags.lower_bound(2000);
std::cout << it->first << " " << it->second << std::endl; // it should be "10000 4"
it++;
std::cout << it->first << " " << it->second << std::endl; // it should be "10000000000 10"
```

## class

#### include机制

`#include` 语句的作用是将某个文件插入到语句所在位置。根据搜索的顺序，可以划分不同的用法。

* `#include "xx.h"`：先搜索当前文件夹，再搜索系统库
* `#include <xx.h>`：搜索系统库
* `#include <xx>`：搜索系统库

注意权限的管理是**针对于类**的，同一类的不同对象可以在其成员函数内任意访问别的成员

友元不具有传递性：

- `friend class`+一个类名，可以指定友元类
- `friend`+一个函数的声明，指定友元函数

生命周期：

- main外的类的对象（i.e. 全局作用域），其构造函数调用的时间早于 `main`函数；其析构函数的调用也在 `main`函数返回之后

#### 静态

静态指的是：

- 空间的静态
- 受限的访问
- 静态局部变量在第一次遇到的时候初始化
- 静态成员变量在 `.h`文件中的声明有 `static`标签，但是在 `.cpp` 中不应该有～标签；否则无法被其他文件中使用。 静态成员函数同理

两种访问静态内容的方式：

```cpp
<class name>::<static member>
<object name>.<static member>
```

#### 引用

一般的字面量都是右值，但是字符串是例外，因为字符串实际上存储在静态内存区

一般来说，左值引用不能绑定右值，但是**常量左值引用可以绑定右值**，因为常量的特性确定了不会对右值进行修改

> 但是如果同时存在右值引用，右值作为参数时还是会优先重载右值引用的版本

**规范：**

- 不允许定义引用的引用；
- **不允许定义引用的数组**；

  - 引用不是单独存在的对象，无法按照数组存储
- **不允许定义指向引用的指针**

  - 指针必须指向对象，而引用不是对象

#### 常量

使用 `const`标记声明为常量

常量可以直接让编译器尝试替换：

```cpp
const int bufsize = 1 << 10;
const int index[] = {1, 2, 3, 4};
int f[bufsize]; // Ok: f[1024]
int f[index[3]]; // Error
```

但是需要特别注意的是：**对象的常量不是编译器常量！ i.e.**

```cpp
class Array{
    const int size = 10;
    int array[size]; // Error！
};
```

> 可以使用枚举或者 `static`来解决上述的问题：
>
> ```cpp
> enum {size = 10};
> static const int size = 10;
> ```

常量和指针；

- `const *p`表示不能通过指针改变指向的对象内容；
- `* const p`表示不能改变指针的指向位置

> 不需要关注类型与 `*`的位置关系

**字符指针和字符数组；**

```cpp
char *sp = "Hello World!"; // 字符指针可以移动，不能修改
char array[] = "Hello World!"; // 字符数组不能移动，可以修改

array[0] = 'h' ; // 合法
array = 'hello'; // 非法！

sp = 'world'; // 合法
```

> 实际上，`char *sp`就是 `const char *sp`，所以不能改变字符串的值，但是可以改变sp的指向
>
> 而字符串数组的数组名是栈中的固定地址，无法移动，但是可以修改

注意区分**常量函数**和返回值的常量：

```cpp
int getName(int id) const; // 常量函数，无法改变成员变量，常量对象只能调用自己的常量成员函数（与静态成员函数）

const int getAge(int id); // 限制了返回值是一个常量
```

#### delete

注意 `[]`搭配的使用：

```cpp
int *p = new int[10];
...
delete [] p;
```

## Inside class

#### 代理构造

可以在一个构造函数中调用另一个构造函数，减少代码的重复：

```cpp
class sorted{
public:
    sorted(){}
    sorted(int _x){
        x = _x > 0? _x: 10;
    }
    sorted(int _x, int _z): sorted(_x){
        z = _z > 0 && _z < x? _z: 1;
    }
    sorted(int _x, int _y, int _z): sorted(_x, _z){
        y = _y < x && _y > z? _y: 5;
    }
private:
    int x, y, z;
};
```

#### 内联函数

通过 `inline`关键字，**建议编译器将函数调用处替换为函数体代码本身** ，从而避免函数调用开销。

> 是否作为内联函数，实际上由编译器所决定

内联函数必须提供完整的函数定义

> i.e. 内联函数的声明必需伴随实现（在同一个头文件中即可，二者可以分离）

## 组合与继承

### 组合

组合的对象分为完全包含和引用包含，什么时候使用引用包含呢？

- 逻辑上子对象应该在对象的外部；
- 子对象的大小不确定
- 子对象的空间应该在运行时被分配或者链接

#### 命名空间

```cpp
namespace sp1{
    void f();
    void g();
}
namespace sp2{
    void f();
    void g();
}// No terminating end colon!
namespace alias = sp1;
void f();
void g();
int main(){
    sp1::f();
    sp2::f();
    ::f();
    f();// the same as ::f()
    alias::f();// the same as sp1::f()
    return 0;
}
```

> - 可以为命名空间声明别名；
> - 命名空间的末尾没有分号 `；`

#### using

使用 `using`可以在当前的作用域引入其他的命名空间的成员、函数：

- `using <namespace>::<member>` 引入部分的成员
- `using namespace <namespace>` 引入该命名空间的全部成员

e.g.

```cpp
using std::cin; // 只引入 cin
using namespace std; // 引入std的所有成员
```

> 如果引入同名的对象或者函数，将会导致编译器链接失败

### 继承

父类的析构函数更晚调用

父类的**私有成员变量在子类的对象中依旧存在**，但是不可直接访问（只能通过父类方法来间接访问）

父类的受保护成员可以被子类访问，但是无法被外界访问

#### 非公开的继承

```cpp
class B: protected A{
    ...  
};
class B: private A{//default
    ...
};
```

> 如果定义 `protected` 继承，只有子类及其派生类可以调用父类方法，外部是不可以的。
>
> 如果定义 `private` 继承，只有子类本身可以调用父类方法。

#### 静态成员的继承

父类的静态成员不会在子类中具有自己的副本，**子类和父类共享一个静态成员！**

以下介绍using相关的几个问题：

#### name hiding

如果子类重载了父类的函数，父类的同名函数将会失效，无法直接调用，需要使用 `using`来声明：

e.g.

```cpp
class Base{
  public: 
  	void f(double){
      cout << "double\n" << endl;
    }
};

class Derived : Base{
  public:
  	using Base::f; // 将基类中的私有函数本地使用
  	void f(int){
      cout << "int\n"<<endl;
    }
};
```

#### 默认参数的重载传递

在cpp中, 默认参数值绑定在函数声明的作用域上, 而不是函数本身! 这是为了避免 **多重继承时参数值产生冲突或二义性** 。

> 默认参数是静态绑定（编译期行为），它必须清楚地知道取哪个作用域的值.

e.g.

```cpp
class A {
public:
    void f(int a = 3, double b = 2.0);
};

class B : public A {
public:
    using A::f;         // ✅ 此时默认参数仍可见
    void f(int a);      // ❌ 重载后，这个版本没有默认参数
};
```

#### 子类重写父类函数

如果子类直接重写了父类的函数，但是父类中的同名函数本身具有重载的版本，那么也需要使用 `using`来声明：

```cpp
#include <iostream>

class Base
{
public:
    // 基类中的重载函数
    void display(int x)
    {
        std::cout << "Base display(int): " << x << std::endl;
    }

    void display(double x)
    {
        std::cout << "Base display(double): " << x << std::endl;
    }
};

class Derived : public Base
{
public:
    // 子类重新定义了基类的 display(int)
    void display(int x)
    {
        std::cout << "Derived display(int): " << x << std::endl;
    }
    using Base::display;
};

int main()
{
    Derived d;
    d.display(5); // 调用 Derived 的 display(int)

    d.Base::display(5.5); // 或者d.display(5.5);
    return 0;
}

```

## 多态

- 我们应当将所有类的析构函数都设置为**虚析构函数**，因为每个类都有成为父类的可能

```cpp
class Base {
public:
    virtual ~Base() {
        cout << "Base Destructor" << endl;
    }
};

class Derived : public Base {
public:
    ~Derived() {
        cout << "Derived Destructor" << endl;
    }
};

```

> 子类和父类的析构函数先后调用

- `vptr`在构造的时候确定，虚继承的子类在调用父类的构造函数时，默认**调用父类的成员函数**

  ```cpp
  class A {
  public:
      A() {
          f();
      }
      virtual void f() {
          cout << "A::f()";
      }
  };
  class B : public A {
  public:
      B() {
          f();
      }
      void f() {
          cout << "B::f()";
      }
  };
  B temp;
  ```

#### 虚函数的规范

注意 `virtual`与 `override`的搭配：

```cpp
class Animal {
public:
    virtual void speak() {
        cout << "Animal speaks" << endl;
    }
};

class Dog : public Animal {
public:
    void speak() override {
        cout << "Dog barks" << endl;
    }
};
```

> override是为了让编译器检查该函数在父类中是虚函数，但不是必须的

- vtable是类级别的, 所有该类的对象共享一个vtable;
- vptr是对象级别的, 隐含于各个对象当中.并且在内存的开头

#### 抽象类

抽象类：至少包含一个**纯虚函数**的类是～

```cpp
class Shape {
public:
    // 纯虚函数，子类必须实现
    virtual void draw() = 0;
};
```

> 纯虚函数类似于协议，要求子类必须实现
>
> 只有完成了所有纯虚函数定义的、抽象类的子类，才能够实例化

**接口类 Interface Class：**

- 比抽象类更加抽象）
- 只定义接口, 不提供实现的抽象类
- 所有的**成员函数都是纯虚函数**;
- 一般不包含任何数据成员.

#### 菱形继承与虚继承

在继承时添加 `virtual` 关键字实现,子类中不存在父类的对象，而是保有父类的指针。

```cpp
class A
{
public:
    int value;
    void ptr()
    {
        cout << "value: " << value << endl;
    }
};
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {};
```

上述的虚继承确保了B，C只会拥有A的一份value，从而避免了从D的对象访问 `value`时存在的**二义性**

> 并非所有的菱形继承都会因为二义性的访问而导致访问的问题！

TODO：检查什么时候没有二义性？

由于虚继承带来的是“共享”的基类对象，所以：

- 虚基类的构造 **必须由最底层派生类负责**
- 派生类的构造函数中要**显式初始化**虚基类

## Copy & Move

- 拷贝构造函数的签名：`T::T(const T&)`
- 默认的拷贝构造函数的指针类型成员是直接赋值的，也就是共享同一地址
  - 因此我们需要显式定义类的拷贝构造函数，避免依赖默认的～
  - 默认拷贝构造中，成员对象也会调用自己的拷贝构造函数

#### 拷贝构造函数的调用时机

* **按值传递参数时**：当对象作为参数按值传递给函数时
  ```cpp
  void func(MyClass obj); // 调用时会触发拷贝构造
  ```
* **对象初始化时**：
  ```cpp
  MyClass a;
  MyClass b = a;    // 初始化，调用拷贝构造函数
  MyClass c(a);     // 初始化，调用拷贝构造函数
  ```
* **函数返回对象时**：
  ```cpp
  MyClass func() {
      MyClass obj;
      return obj;   // 可能触发拷贝构造（取决于编译器优化）
  }
  ```

一个简单的例子：

```cpp
StringHolder(const StringHolder &other)
    {
        if (other.data)
        {
            data = new char[strlen(other.data) + 1];
            strcpy(data, other.data);
            std::cout << "深拷贝构造函数: 为\"" << data << "\"分配新内存" << std::endl;
        }
        else
        {
            data = nullptr;
            std::cout << "深拷贝构造函数: 复制空字符串" << std::endl;
        }
    }
```

#### 右值引用

两种可以同时输入左值和右值引用作为参数的方法:

- **重载**

  ```cpp
  // 重载函数，分别处理左值和右值
  void process(int& x) {
      std::cout << "重载函数 - 处理左值: " << x << std::endl;
  }

  void process(int&& x) {
      std::cout << "重载函数 - 处理右值: " << x << std::endl;
  }
  ```
- `const int& x `

  ```cpp
  // 接受const左值引用的函数（可以接受左值和右值）
  void processAny(const int& x) {
      std::cout << "处理任意值: " << x << std::endl;
  }
  ```

#### 移动构造函数

- 签名：`T::T(T&& other)`, 也就是**将右值引用作为参数的构造函数**
- 用于"窃取"即将销毁的对象的资源，避免不必要的深拷贝
- 通常将源对象的指针成员置为 `nullptr`，防止资源被错误释放. 其他成员设置为零值

一个简单的例子：

```cpp
class DynamicArray
{
private:
    int *m_array;
    size_t m_size;
...
}

// 移动构造函数
DynamicArray(DynamicArray &&other) noexcept : m_array(other.m_array), m_size(other.m_size)
    {
        // "窃取"other的资源，并将other置为安全状态
        other.m_array = nullptr;
        other.m_size = 0;
        std::cout << "移动构造函数: 移动大小为" << m_size << "的数组" << std::endl;
    }
```

> `noexcept`标记表示不会抛出异常；
>
> 标准库容器在进行元素移动时会**优先选择不会抛异常的移动构造函数**，如果不存在～会退而求其次选择拷贝构造函数（更慢）。

#### move

如果对象不是右值引用，我们可以使用 `std::move`来显式调用移动构造函数：

```cpp
vector<int> v1{1, 2, 3, 4};
vector<int> v2 = v1; // v2 是 v1 的副本
vector<int> v3 = std::move(v1); // 调用移动构造函数
```

### 初始化的方式

C++11提供了多种初始化对象的方式：

- **小括号初始化**：`MyClass obj(arg1, arg2);`
- **等号初始化**：`MyClass obj = value;`
- **大括号初始化**（统一初始化）：`MyClass obj{arg1, arg2};`
- **列表初始化**：

  ```cpp
  int arr[] = {1, 2, 3};
  MyClass* ptr = new MyClass{arg1, arg2};
  ```

#### 基本类型的初始化

```cpp
// 基本类型的初始化方式
int a = 10;             // 等号初始化
int b(20);              // 小括号初始化
int c{30};              // 大括号初始化（C++11）
int d = {40};           // 等号+大括号初始化（C++11）
int arr1[] = {1, 2, 3}; // 数组初始化
int arr2[]{4, 5, 6};    // 数组大括号初始化（C++11）
```

#### 对象的初始化

```cpp
// 对象的初始化方式
Person p1("张三", 25);          // 小括号初始化
Person p2 = Person("李四", 30); // 等号+临时对象初始化
Person p3{"王五", 35};          // 大括号初始化（C++11）
Person p4 = {"赵六", 40};       // 等号+大括号初始化（C++11）
```

- 动态分配对象的初始化

  ```cpp
  // 动态分配对象的初始化方式
  Person *pp1 = new Person("动态张三", 25); // 传统new
  Person *pp2 = new Person{"动态李四", 30}; // 大括号初始化（C++11）
  ```

#### 统一初始化

对于简单的类或者容器内部的类，我们可以不写构造函数，而是用花括号进行 **统一初始化** 。

如果类没有构造函数，参数应按照成员的声明顺序给出；如果有，参数应按照构造函数的参数顺序给出。

```cpp
class Test{
    int a, b;
};
Test t{0, 0};
Test *pt = new Test{1, 2};
int *a = new int[3]{1, 2, 0};
vector<string> vec = { "first", "second", "third"};
```

## 操作符重载

运算符重载本质上是一个以 `operator`关键字为前缀，后跟运算符的特殊函数

- 赋值运算符首先要**检查是否为自赋值**
- 必须在**类或者枚举类**上定义
- 类内成员函数的重载将第一个参数作为隐式的 `this`传递，操作符左端的类型决定了使用的操作符的类型
  - `3+a1`非法 if `a1`无法转换为 `int`

#### 成员函数与自由函数

* `=, (), [], ->, ->*` 必须是成员函数
* 单目运算符应该声明为成员；
* 二目运算符应该声明为自由函数

#### 无法重载的运算符

```cpp
. .* :: ?:
sizeof typeid
static_cast dynamic_cast const_cast
reinterpret_cast
```

#### 参数传递与返回类型

- `+ - * / % ^ & | ~` 返回新的对象
  - `const Tp operator X(const Tp & l, const Tp & r);`
- 布尔运算，返回 `bool`类型
- `[ ]` 返回可以修改的引用，类似于数组的赋值
  - `Tp & operator X(int index);`
- `= += *= /= <<= >>=` 返回可以修改的引用，确保链式操作
  - `Tp & operator X(const Tp &l, const Tp &r);`

前缀自增和后缀自增需要区分，在后缀自增的参数列表中添加 `int`即可

```cpp
// 前缀自增返回引用
const Integer& Integer::operator++(){
    *this += 1;
    return *this;
}

// 后缀自增返回对象
const Integer Integer::operator++(int){// just leave the parameter unnamed
    Integer old(*this);
    ++(*this);
    return old;
}
```

#### 比较运算符

考虑实现基础的 `==` 与 `<` ，其余的比较重载在上述的基础上通过 `!` 运算实现，便于直接迁移

#### 流运算

```cpp
istream& operator>>(istream& is, _Tp& obj){
    // read obj from is
    return is;
}
ostream& operator<<(ostream& os, const _Tp& obj){
    // output obj in os
    return os;
}
ostream& tab(ostream& os){// manipulator
    return os << '\t';
}
```

- 输出流运算符的第一个参数不能是 `const`，因为输出会修改流
- 输入流运算符的第二个参数不能是 `const`，因为需要修改对象
- 通常需要声明为友元以访问私有成员

#### 赋值运算符

```cpp
_Tp& _Tp::operator=(const _Tp& rhs){
    if(this != &rhs){
        size = rhs.size;
        delete[] p;
        p = new int[size];
        for(int i = 0 ; i < size; ++i)
            p[i] = rhs.p[i];
    }
    return *this;
}
```

> 首先检查是否为自赋值

如果不希望进行赋值运算，将上述的 `=` 重载声明为 `private`，并且无需实现。

> 这是因为 `a = b;  // 实际等价于：a.operator=(b)`， 发起调用的是当前代码片段所在的作用域，如果不是类内函数，那么就是非法的

#### 隐式转换

**1. 构造函数转换**：从其他类型到当前类型

```cpp
class PathName {
    string name;
public:
    PathName(const string& s) : name(s) {}  // string到PathName的转换
};

string abc("abc");
PathName xyz = abc;  // 隐式转换：abc => PathName

```

2.**转换运算符**：从当前类型到其他类型

> `operator <typename> {}`关键字

```cpp
class Rational {
public:
    operator double() const {  // Rational到double的转换
        return numerator_ / (double)denominator_;
    }
};

Rational r(1, 3);
double d = r;  // 隐式转换：r => double

```

可以使用 `explicit`关键字要求显式转换：

```cpp
explicit operator double() const;
double d = (double)r;  // 必须显式转换
```

#### 显式转换

- 构造函数的显式转换

```cpp
// 如果想要强制显式转换，应该这样声明：
explicit Rational(double value) {
    const int PRECISION = 10000;
    numerator = static_cast<int>(value * PRECISION);
    denominator = PRECISION;
    simplify();
}

Rational r1 = 3.14;        // 错误：不允许隐式转换
Rational r2(2.5);          // 正确：显式构造
Rational r3 = Rational(3.14); // 正确：显式转换

```

- 类型转换运算符的显式转换

```cpp
explicit operator double() const;
double d = (double)r;  // 显式转换 or double(r)

double d2 = static_cast<double>(r2);  // 显式转换

```

#### 转换优先级

如果同时存在两种方向的转换，将优先采取**构造函数**的转换方式

> 可以通过声明其中的一种情况必须显式调用来并存；比如必须显式调用构造函数的转换，那么下面的情况将会调用构造函数将A转换到B的对象：
>
> ```cpp
> class B{
> public:
>     B();
>     explicit B(A); // 从A到B的显式构造函数
> };
>
> void functionTakingB(B thing){
>     cout << "OK" << std::endl;
> }
>
> functionTakingB(static_cast<B>(a));
> ```

#### 转换运算符

C++ 中有四个**转换运算符** Cast Operator：

* `static_cast`：

  * 基本类型的转换
  * 子类向父类的指针/引用的转换
  * `void`与其他类型指针的转换
* `dynamic_cast`：**down-cast**，安全

  * 父类向子类的指针/引用的转换，不一定总是安全（要求原本指向的对象就是子类对象）
  * 要求基类中**至少存在一个虚函数**（因此具有 `vptr`，从而可以通过不同类的 `vptr`进行类的区分）
  * ```cpp
    Base* basePtr = new Derived();
    Derived* derivedPtr = dynamic_cast<Derived*>(basePtr);

    if(derivedPtr){
    ...
    }

    ```
* `const_cast`：修改 `const` 属性

  * ```cpp
    const int a = 10;
    int *b = const_cast<int*>(&a);

    *b = 20; // 转换之后可以修改value
    ```

    > 但是更加常见的是将非const类型的属性修改为 `const`；原本是常量类型的属性可能被编译器存储在只读内存区域，如果编译器没有在 `const_cast`的转换中进行优化，可能导致运行问题？
    >
* `reinterpret_cast`：忽略类型检查，强制转换，低安全性

## Template

一个模板完全都是声明，应该只有 `.h`，而不含有 `.cpp`

> 必须**都放在头文件的实现**包含:
>
> - 函数模板;
> - inline函数
> - 带有default参数的声明.
> - 类模板的成员函数

### 函数模板

#### 参数匹配

```cpp
#include <algorithm>
#include <iostream>
 
int add(int x, int y){
    return x + y;
}
 
template<typename _Tp>
_Tp add(_Tp x, _Tp y){
    return x + y;
}
 
signed main(int argc, char **argv){
    std::cout << add(1, 2) << std::endl; 
    std::cout << add(1.1, 2.2) << std::endl;
    return 0;
}

/* T add(int, int) 和 T double add<double>(double, double) */
```

1. 如果有原生的完全匹配的函数，优先使用原生函数，例如 `add(1, 2)` 调用 `add(int, int)`。
2. 其次，如果有模板能完全匹配的函数，使用模板生成函数，例如 `add(1.1, 2.2)` 调用 `add<double>(dobule, double)`。
3. 再其次，尝试使用类型转换来匹配其他原生函数。但是，类型转换不能用于匹配模板，例如 `add(1, 2.2)`。

### 类模板

简单的示例：

```cpp
template<typename T>
class Vector{
public:
    Vector(int s):size(s){
        content = new T[size];
    }
    virtual ~Vector(){
        delete[] content;
    }
    T& operator[](int p){
        return content[p];
    }
private:
    T* content;
    int size;
};
```

## Exception

**异常的类型如何定义？**

> ```cpp
> // 异常对象的定义
> class DivisionError {
> private:
>     string message;
> public:
>     DivisionError(const string& msg) : message(msg) {
>         cout << "创建DivisionError异常对象" << endl;
>     }
>   
>     ~DivisionError() {
>         cout << "销毁DivisionError异常对象" << endl;
>     }
>   
>     string what() const {
>         return message;
>     }
> };
>
> // 内层函数
> double divide(double a, double b) {
>     cout << "进入divide函数" << endl;
>     Resource r("divide函数的局部资源");
>   
>     if (b == 0) {
>         throw DivisionError("除数不能为零");
>     }
>   
>     cout << "divide函数正常返回" << endl;
>     return a / b;
> }
> ```

执行流程：

1. 通过 `throw` 创建对应的**异常对象**
2. 将异常所在的内层函数的**资源释放**；
3. 再被外层的 `catch` 所**捕获**

在 `catch`块中可以通过 `throw;`再次抛出当前的异常

自定义异常类时，通常应该继承自 `std::exception`或其派生类

#### 异常规范

在函数原型中声明可能返回的异常类型：

```cpp
void print(Document& p) throw(PrintOffLine, BadDocument);
void goodguy() throw();// throw no exceptions, until C++11
void alloc() throw(...);// can throw any exception
void abc() noexcept;// throw no exceptions, since C++11
```

> 如果函数返回了规范之外的异常，将调用 `std::unexpected()`处理（默认调用 `std::terminate()`终止程序）

`noexcept`也可以作为运算符使用，检查表达式是否声明为不抛出异常：

```cpp
bool willNotThrow = noexcept(func());  // 检查func()是否声明为noexcept
```

#### 层次结构

```cpp
std::exception
├── std::logic_error
│   ├── std::invalid_argument
│   ├── std::domain_error
│   ├── std::length_error
│   ├── std::out_of_range
│   └── std::future_error
├── std::runtime_error
│   ├── std::range_error
│   ├── std::overflow_error
│   ├── std::underflow_error
│   └── std::system_error
├── std::bad_alloc
├── std::bad_cast
├── std::bad_typeid
├── std::bad_exception
└── std::bad_function_call
```

- 数组的 `.at`可以自动抛出数组访问异常的 `range_error`；
- 容器的 `resize`方法可以自动抛出长度异常 `length_error`；

#### 构造与析构

析构函数应该避免抛出异常，否则会导致系统调用 `std::terminate()`

由于在 `try-catch`中，如果发生了异常，本地变量将自动调用自己的析构函数，此时如果存在 `new`申请的空间资源，将导致其无法指向正确的地址

因此，我们采取两阶段的构造确保构造函数不会抛出异常：

1. 在构造函数内对基本变量赋值
2. 在 `init()`函数中显式申请内存空间

```cpp
class Widget {
public:
    Widget() : initialized(false) {
        // 只做最小的初始化： 不存在抛出异常的可能
    }
  
    bool initialize() {
        try {
            // 执行可能失败的初始化操作
            initialized = true;
            return true;
        } catch (...) {
            return false;
        }
    }
  
    void use() {
        if (!initialized) {
            throw std::runtime_error("对象未初始化");
        }
        // 使用对象
    }
  
private:
    bool initialized;
};

```

---

# 知识蒸馏

> 将个人印象比较浅的部分重新摘了一遍

- 字符指针与字符数组

```cpp
char *sp = "Hello World!"; // 字符指针可以移动，不能修改
char array[] = "Hello World!"; // 字符数组不能移动，可以修改

array[0] = 'h' ; // 合法
array = 'hello'; // 非法！

sp = 'world'; // 合法
```

> 实际上，`char *sp`就是 `const char *sp`，所以不能改变字符串的值，但是可以改变sp的指向
>
> 而字符串数组的数组名是栈中的固定地址，无法移动，但是可以修改

- 不同文件之间的全局变量, 初始化的前后顺序由链接器随机决定. 此时需要确保它们**之间没有初始化的依赖.**
- 返回类型的常量

  ```cpp
  const int* f();
  // 只能将函数的返回值赋值给一个 const int*
  ```

#### vptr的大小

如果父类具有 `virtual`也就是虚函数，子类继承之后也会得到一个虚函数表，对应有一个**vptr**指针指向自己的虚函数表：

- 在64位的机器下，一个vptr指针的大小是**8字节**
- 普通函数不占据类的大小，因为函数地址存放在全局空间
- 如果类内没有成员变量，也没有虚函数，那么大小就是**1**（告诉编译器这个类的存在）

#### 编译器的对齐

* C++ 编译器会对类进行**按最大对齐的成员**进行对齐

```cpp
#include <iostream>
using namespace std;

class Nothing {
public:
    Nothing() {}
    int a;
    virtual ~Nothing() {}
};

int main() {
    Nothing obj;
    cout << "Sizeof Nothing: " << sizeof(Nothing) << endl;
    cout << "Address of obj: " << &obj << endl;
    cout << "Address of a: " << &(obj.a) << endl;
}
```

对应的输出：

```bash
Sizeof Nothing: 16
Address of obj: 0x16d57e830
Address of a: 0x16d57e838
```

我们可以观察到两个现象：

1. `size`=16说明了对齐现象；
2. Nothing对象的地址首先是其vptr，然后是其他的成员

# 期末题集

> 补天专用楼

## 程序填空

#### 题目摘录

##### 类模板：Array

```cpp
#include <iostream>
using namespace std;

template <typename T>
class Array {
public:
    Array() {
        data = new T[BLK_SIZE];
        next = nullptr ;
     }
    ~Array() {
        delete [] data;
        delete next;
     }
    T& operator[](int i);
    void iterate(void (*f)(T&));
private:
    T  *data; // data of type T
    static const int BLK_SIZE=32; // fixed block size
    Array *next;  // the next array block
};

template <typename T>
T& Array<T>::operator[](int i) {
    if (i < BLK_SIZE) {
        return data[i];
     } else {
        if (next == NULL) {
            next = new Array<T>;
         }
        return (*next)[i-BLK_SIZE];
    }
}

template <typename T>
void Array<T>::iterate(void (*f)(T&)) {
    for (int i = 0; i < BLK_SIZE; i++) {
        f(data[i]);
    }
    if (next != NULL) {
        next-> iterate(f);
    }
}

int main()
{
    Array<int> a;
    int size = 100;
    cin >> size;
    for (int i = 0; i < size; i++) {
        a[i] = i;
    }
    a.iterate([](int &x) { cout << x << endl; });
}
```

##### 函数模板：内积

此处的 `op`操作之前没有接触过，利用的是标准库提供的二元操作：

```cpp
#include <functional>
#include <iostream>
#include <vector>

template <class InputIt1, class InputIt2, class T, class BinaryOp1, class BinaryOp2>
T inner_product(InputIt1 first1, InputIt1 last1, InputIt2 first2, T init, BinaryOp1 op1, BinaryOp2 op2)
{
  while (first1 != last1)
  {
    init = op1 (init, op2(*first1, *first2) );
    ++first1;
	++first2;
  }
  return init;
}
 
int main()
{
  std::vector<int> a{0, 1, 2, 3, 4};
  std::vector<int> b{5, 4, 2, 3, 1};
  int r1 = inner_product(a.begin(), a.end(), b.begin(), 0, std::plus<>(), std::multiplies<>());
  std::cout << "Inner product of a and b: " << r1 << '\n';
 
  int r2 = inner_product(a.begin(), a.end(), b.begin(), 0, std::plus<>(), std::equal_to<>());
  std::cout << "Number of pairwise matches between a and b: " <<  r2 << '\n';
}

```

#### 类模板的填写

- 非内联定义成员函数时，需要在类型与函数名之间加上 `<class-name><T>::` ，不要忘记了其中的 `<T>`

```cpp
template <typename T>
T& Array<T>::operator[](int i) {
    if (i < BLK_SIZE) {
        return data[i];
     } else {
        if (next == NULL) {
            next = new Array<T>;
         }
        return (*next)[i-BLK_SIZE];
    }
}
```

## 长话短说

**注意函数模板的返回类型：**

```cpp
#include <iostream>

using namespace std;

template<typename T>
T func(T x, double y) {
    return x + y;
}

int main() {
    cout << func(2.7, 3) << endl;
    cout << func(3, 2.7) << endl;
}
```

此时第二个输出从 5.7 向 int转换，得到的结果是 5

如果是数组空间的管理, 注意 `new` 和  `delete` 都需要对应的 `[]`

如果没有显式定义任何的构造函数，那么编译器会自动创建一个默认构造函数

- 但是如果程序员定义了任何的构造函数（无论是否带有默认参数），编译器就不用自动创建默认构造函数

**upcast**：将一个派生类的指针或者引用赋值给基类的指针或引用

- **动态绑定**：发生upcast之后，通过基类指针或引用调用虚函数时，实际调用的是指针或引用锁指向的对象的虚函数的实现；而不是根据指针或引用的静态类型
- 如果此时发生了析构，如果基类的析构函数是虚函数，就会先后调用子类和父类的析构函数；如果父类的析构函数不是虚函数，就只会调用父类的析构函数
  - 为了确保子类的资源可以被释放，总是应当将类的析构函数作为虚析构函数

**析构函数不允许被重载**

- 事实上，析构函数的名称固定，并且没有参数，因此无法通过参数列表来区分不同的韩苏版本，自然也就无法重载

C++标准只规定了整数类型的相对顺序：

```cpp
sizeof(char) <= sizeof(short) <= sizeof(int) <= sizeof(long) <= sizeof(long long)
```

- 也就是说, 可能存在 size上 `int = long`的情况

A program is a bunch of objects telling each other how to do **by sending messages**

- 此处的消息在oop中指的就是通过调用对象内部的方法

## 一句话说不清楚的

#### 重载与友元函数

- 完全无法重载的：

  ```cpp
  . .* :: ?:
  sizeof typeid
  static_cast dynamic_cast const_cast
  reinterpret_cast
  ```
- 只能作为成员函数（无法作为友元函数）重载的：

  ```
  =, (), [], ->, ->*
  ```

  > 以及单目运算符
  >

#### 父类的构造函数

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250613213521969.png?imageSlim"/>

构造函数与普通的成员函数不同：

- 如果父类的构造函数被声明为 `private` 的，和普通的成员函数一样——只能被自己的成员和友元函数可以调用
- 如果父类的构造函数被声明为 `protected` 的，那么只有父类的成员、友元以及**派生类的构造函数**可以调用；
  - 这意味着此时无法直接在子类除了构造函数之外的地方来直接创建独立的父类对象

#### C++对编程范式的支持

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250613214216540.png?imageSlim"/>

- 过程式编程的特性包括：函数、全局变量、顺序执行、选择（if/else, switch）、循环等
  - C就是过程式编程的一种，C++继承了C的特性，因此对过程式变成支持良好
- 声明式编程指的是“做什么”而非如何做，比如SQL、HTML、Swift等。C++和C是命令式语言，关注“如何做”

> 因此答案选C

#### 对象切片

```cpp
C2* pC2 = new C2();
cout << endl;
{
    C1 a = *pC2; // 发生了对象切片，只保留了基类对象的属性
    cout << endl;
}
C1* pC1 = pC2;
delete pC1; // 基类的析构函数是virtual的，所以此处发生多态析构
cout << endl;
```

#### 拷贝初始化

用基本类型的值来隐式调用构造函数以创建对象:

```cpp
class ResId {
public:
    ResId(int Id);
};

ResId res = 5;
// 等价于
// ResId res(5);
// ResId res = ResId(5);
```

这个过程分为两步:

1. 用 5 构造一个临时的ResId对象;
2. 用这个临时对象初始化res

因此我们整体上可以说:

```cpp
An object of class ResId will be created by 5
```

#### 禁止隐式的拷贝初始化

通过声明 `explicit` 来禁止上述通过值隐式调用构造函数:

```cpp
#include <iostream>

class C {
public:
    explicit C(int) {
        std::cout << "i" << std::endl;
    }
    C(double) {
        std::cout << "d" << std::endl;
    }
};

int main() {
    C c1(7); // 匹配了第一个构造函数
    C c2 = 7; // 隐式构造, 只能将7转换为double, 然后调用第二个构造函数
}
```

**输出:**

```cpp
i
d
```

#### 子类对父类函数的重载与重写

子类如果重载或者重写了父类的同名函数，将无法通过子类的对象访问父类中的这些函数：

```cpp
#include <iostream>

using namespace std;

class A {
public:
    void F(int) { cout << "A::F(int)" << endl; }
    void F(double) { cout << "A::F(double)" << endl; }
    void F2(int) { cout << "A::F2(int)" << endl; }
};

class B : public A {
public:
	  using A::F;
    void F(double) { cout << "B::F(double)" << endl; }
};

int main() {
    B b;
    b.F(2.0);
    b.F(2);
    b.F2(2);
}
```

输出：

```cpp
B::F(double)
B::F(double)
A::F2(int)
```

但是我们可以通过在子类中声明 `using A::F`来重新获得访问权限：

```cpp
class B : public A {
public:
    using A::F;
    void F(double) { cout << "B::F(double)" << endl; }
};
```

此时的输出为：

```cpp
B::F(double)
A::F(int)
A::F2(int)
```

#### 默认参数的静态绑定

- 虚函数：运行时多态（动态绑定）
- 默认参数：编译时确定（静态绑定）

```cpp
#include <iostream>

struct A {
    virtual void foo(int a = 1) {
        std::cout << "A" << '\n' << a;
    }
};

struct B : A {
    virtual void foo(int a = 2) {
        std::cout << "B" << '\n' << a;
    }
};

int main() {
    A *a = new B;
    a->foo();
    delete a;
}
```

输出：

```cpp
B
1
```

> **为什么cpp要选择让静态参数实现静态绑定？**
>
> 为了保持语言的一致性与可预测性，设计者让静态参数作为编译时期自动替换的值，避免在运行过程中动态替换

#### 函数模板与模板特化

模板特化：在函数模板的基础上，如果我们希望对某个类型实现不一样的逻辑，就可以使用～

```cpp
template<typename T>
void f(const T& value) {
    std::cout << "泛型模板: " << value << std::endl;
}

// 对int类型采取模板特化
template<>
void f<int>(const int& value) {
    std::cout << "特化版本: int 类型" << std::endl;
}
```

- 因此，模板特化必须首先存在一个主模板

**模板特化的结果无法被重载**

```cpp
#include <iostream>

template<class T> void f(T &i) { std::cout << 1; }
template<> void f(const int &i) { std::cout << 2; }

int main() {
    int i = 24;
    f(i);
}
```

将会输出： `1`

- 如果我们只保留 `void f(const int &i) { std::cout << 2; }`  函数，将会触发类型转换，可以调用
- 如果我们只去除模板特化中的 `const`， 就可以匹配（输出 `2`）

再比如：

```cpp
template<typename T>
void add(T, T);

add(1, 2.2); // ❌ 模板不能推导出统一的 T（int vs double）
```

#### 常量对象

- 静态函数同样参与函数重载，但是优先匹配非静态函数
- 静态函数不受 `const`限制——即使没有被声明为 `const`也可以被常量对象调用

```cpp
#include <iostream>

using namespace std;

class A {
public:
    static void f(double) {
        cout << "f(double)" << endl;
    }
    void f(int) {
        cout << "f(int)" << endl;
    }
};

int main() {
    const A a;
    a.f(3);
}
```

输出：

```cpp
f(double)
```

如果存在完全匹配的普通函数，就会直接调用非静态函数：

```cpp
void f(int) const {
    cout << "f(int) const" << endl;
}
void f(int) {
    cout << "f(int)" << endl;
```

> 此处的 `void f(int) const` 无法改为 `void f(double) const`, 否则与同名静态函数的参数完全一致

#### 异常的 `catch`顺序

`catch`块的匹配是从上到下的，因此只要遇到第一个匹配的，后续的匹配就会结束

- 子类的对象可以被父类捕获
- 注意此时的动态绑定——如果父类层级（引用或者指针）在前，内部抛出的是子类的异常对象，捕捉之后调用的函数是子类的对象

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    virtual const char* what() const {
        return "Base::what()";
    }
    virtual ~Base() {}  // 虚析构保证多态安全
};

class Derived : public Base {
public:
    const char* what() const override {
        return "Derived::what()";
    }
};

int main() {
    try {
        throw Derived();  // 抛出子类对象
    } catch (const Base& e) {  // 用父类引用接收
        cout << "由父类层级捕获到异常: " << e.what() << endl;  // 动态绑定调用子类 what()
    } catch (const Derived& e) {
        cout << "由子类层级捕获到异常: " << e.what() << endl;
    } catch (...) {
        cout << "捕获到未知异常" << endl;
    }
    return 0;
}
```

输出：

```cpp
由父类层级捕获到异常: Derived::what()
```

> 如果将 `catch`内部改为普通的对象，将会输出 `由父类层级捕获到异常: Base::what()`

由此可见，我们应该遵循：将子类对象的捕捉放在其父类之前，最后是 `catch(...)`
