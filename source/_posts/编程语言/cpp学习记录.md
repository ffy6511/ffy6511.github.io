---
title: cpp学习记录
date: 2025-02-14 20:21:21
tags:
categories:
excerpt: 围绕zju-OOP课堂内容扩展的cpp学习笔记
mathjax: true
---

编译时, 从`c`的`gcc`转变为了`g++`.

OOP的三大特点:

- 封装
- 继承
- 多态

# 基本语法
在C语言中,我们主要使用`malloc()`和`free()`来进行动态内存管理。但这种方式存在一些问题:
- 它不会调用构造函数和析构函数,返回的是void*指针需要强制类型转换;
- 容易发生内存泄漏.

为了更好地支持面向对象编程并提供更安全的内存管理机制,C++引入了`new`和`delete`.

new的基本语法十分直观:
```cpp
Type* pointer = new Type;           // 分配单个对象
Type* pointer = new Type[size];     // 分配对象数组
```

可以在创建时进行初始化:
```cpp
int* p1 = new int(5);              // 初始化为5
string* p2 = new string("hello");   // 初始化为"hello"
```

也可以根据变量进行动态的内存分配:
```cpp
int size;
cin >> size;
int* arr = new int[size];  // 根据输入分配内存
```

> [!NOTE]
>
> Use `delete ［］` if `new ［］` was used to allocate an array.



# 输入输出流

通过包含头文件 -- `#include <iostream>` 来使用输入输出流 `cin` 和 `cout`.

```cpp
#include <iostream>

using namespace std;
int main(){
    int age; 
    cin >> age;
    cout << "You are " << age << " years old" << endl;
    // endl 是换行符
    return 0;
}
```

- `cin`读取字符串时以空白字符（空格、制表符、换行符等）作为分隔符:
```cpp
string str="Hello world!";
ofstream fout("out.txt");
fout<<str<<endl;

ifstream fin("out.txt");
string str1,str2;

// 读取文件中的两个字符串
fin>>str1>>str2; 

cout << str1 << endl << str2 << endl;
// 输出:
// Hello
// world!

return 0;
```


## 文件流

### 输入输出流基础
- 头文件: `#include <fstream>`
- 类: `ifstream`(输入流), `ofstream`(输出流)
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
  - 写入文件时, 如果没有文件, 会自动创建.


### 常用操作示例

1. **写入文件**
```cpp
ofstream outFile;
outFile.open("test.txt");  // 打开文件
if (outFile.is_open()) {   // 检查是否成功打开
    outFile << "第一行" << endl;
    outFile << "第二行" << endl;
    outFile.close();       // 完成后关闭文件
}
```

2. **读取文件 **
```cpp
ifstream inFile("test.txt");
string line;
while (getline(inFile, line)) {  // 逐行读取
    cout << line << endl;
}
inFile.close();
```

3. **以追加模式打开文件**
```cpp
ofstream outFile("test.txt", ios::app);  // app 表示追加模式
outFile << "这行会被添加到文件末尾" << endl;
outFile.close();
```


### 文件打开模式

- `ios::in` - 读取模式
- `ios::out` - 写入模式
- `ios::app` - 追加模式
- `ios::ate` - 打开文件后立即定位到文件末尾
- `ios::binary` - 二进制模式
- `ios::trunc` - 如果文件存在则**截断**文件
  - 如果文件已经存在，那么会清空该文件的所有内容，使其变成一个空文件. 然后重新写入内容.
```cpp
// 假设 test.txt 原本内容是:
// Hello World
// This is a test

// 使用 trunc 模式打开
ofstream outFile("test.txt", ios::out | ios::trunc);  
outFile << "新的内容" << endl;
outFile.close();

// 现在 test.txt 的内容只有:
// 新的内容
```
>  或直接用 `ios::out`，因为out默认包含trunc


- 使用位或运算符`|`来同时指定多个模式:
```cpp
// 组合使用打开模式
ofstream outFile("test.txt", ios::out | ios::app);
```

### 错误处理

```cpp
ifstream inFile("nonexistent.txt");
if (!inFile) {
    cerr << "无法打开文件！" << endl;
    return 1;
}

// 或者使用is_open()
if (!inFile.is_open()) {
    cerr << "无法打开文件！" << endl;
    return 1;
}
```

# 变量
## String
需要先引入指定的头文件:
```cpp
#include <string>
```

- 定义时可以使用等号或者用括号包裹字符串:
```cpp
string name = "John"; 
// string name("John");
```

---

### stringstream
`stringstream` 表示**双向**字符串流:
- 需要导入头文件`#include <sstream>`;
- `istringstream` 表示**输入**字符串流
  - 作用: 将字符串转换成一个类似于输入流的对象;
  - 内部维护了一个字符串和一个位置指针;
  - 每次读取时, 位置指针向后移动, 且自动跳过空白字符.
- `ostringstream` 表示**输出**字符串流.


#### 字符串分词
自动以**空白字符**(空格、制表符\t、换行符\n等)分割字符串;

```cpp
#include <string>
#include <iostream>
#include <sstream>

using namespace std;
int main(){
    string name ( "Xiao Ming");

    // 使用括号包字符串
    istringstream is (name); 
    string s;
    while (is>>s){
        cout << s << endl;
    }
}
```
> `>>` 表示从输入流中读取数据;
>
> 注意字符串流也是一种类型, 作用的对象是字符串.

Output:
```shell
Xiao Ming
Xiao
Ming
```

包含更多分词的字符串:
```cpp
#include <string>
#include <iostream>
#include <sstream>

using namespace std;
int main(){
    string words = "hello \n world! \t I am \n here!";
    stringstream is (words);
    
    string word;
    int count  = 1;
    while(is >> word){
        cout << "Word " << count << ": " << word << endl;
        count++;
    }
}
```
Output:
```shell
Word 1: hello
Word 2: world!
Word 3: I
Word 4: am
Word 5: here!
```

#### 字符串拼接
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
Output:
```shell
Name: Alice, Age: 25
```
> 通过`.str()`方法可以对象转换为字符串类型, 从而**格式化输出**.

<br>

`.str("")`方法可以**清空**字符串流:

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
    oss.str("");
    string result = oss.str();
    cout << result << "Nothing" << endl;
}
```
Output:
```shell
Nothing
```

### Getline
**基本语法:**
```cpp
getline(istream& is, string& str, char delim = '\n');
```
- `is`: 输入流（通常是cin;
- `str`: 存储结果的字符串;
- `delim`: 分隔符, 默认为换行符`\n`.

**Example**:
```cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string line;
    
    cout << "请输入一行文本：";
    getline(cin, line);  // 读取整行，包括空格
    cout << "你输入的是：" << line << endl;
    
    // 使用自定义分隔符
    string data;
    cout << "请输入内容（用,分隔）：";
    getline(cin, data, ',');  // 读取直到遇到逗号
    cout << "读取到逗号前的内容：" << data << endl;
}
```

### cin
**特点**:
- 以空白字符（空格、制表符、换行符）为分隔符;
- **忽略**前导空白字符;
- 遇到空白字符就停止读取.


通常需要与`getchar()`方法配合来清除缓冲区当中的`\n`字符:
```cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    int number;
    string line;
    
    cout << "输入一个数字：";
    cin >> number;
    
    //清除输入缓冲区中的换行符
    getchar(); // or cin.ignore(); 
   
    cout << "输入一行文本：";
    getline(cin, line);  // 现在可以正确读取整行
    
    cout << "数字：" << number << endl;
    cout << "文本：" << line << endl;
}
```
> 如果输入`8 \n`, 则`getchar()`读取空格, 文本为空.

### Alter String
**outline** : 常用的字符串方法(成员函数):

```cpp
insert(size_t pos, const string& s);
erase (size_t pos = 0, size_tlen = npos);
append (const string& str);
replace (size_t pos,size_t len,const string& str);
```

---
#### 常用方法

- `insert(int pos, string str)` 在指定位置插入字符串
```cpp
string str = "Hello World";
// 在位置5处插入字符串
str.insert(5, " Beautiful");
cout << str << endl;  // 结果: "Hello Beautiful World"

// 在字符串末尾插入内容
str.insert(str.length(), "!");
cout << str << endl;  // 结果: "Hello Beautiful World!"

// 插入单个字符（使用string构造）
str.insert(0, ">");
cout << str << endl;  // 结果: ">Hello Beautiful World!"
```

---
- `erase(int pos, int length)` 删除从指定位置开始的若干个字符
```cpp
string str = "Hello Beautiful World!";

// 删除从下标6开始的9个字符
str.erase(6, 9); 
cout << str << endl;  // 结果: "Hello World!"

// 删除从某个位置开始到末尾的所有字符
str.erase(5);
cout << str << endl;  // 结果: "Hello"
```
> `length`参数省略, 则删除从`pos`位置开始到字符串末尾的所有字符.


---

- `replace (int pos, int length, string)` 替换指定位置的字符串
```cpp
// 从位置6开始，替换5个字符为"C++"
string str = "Hello World!";
str.replace(6, 5, "C++");
cout << str << endl;  // 结果: "Hello C++!"
```


---


- `append (const string& str);`
```cpp
// 添加整个字符串
string1.append(string2);

// 添加指定位置的字符(索引从开始)
string1.append(string2, start, length);

// 重复字符的添加
string1.append(count, char);

```

除此之外, 还存在着使用$\underline{迭代器}$的用法: 
> 类似于指针, 指向容器(如字符串、数组等)的特定位置.

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string source = "World!";
    string target = "Hello ";
    
    // 添加source中的部分字符（从开始到结束）
    target.append(source.begin(), source.end());
    cout << target << endl;  // 输出: Hello World!
    
    // 只添加部分字符
    string target2 = "Hello ";
    target2.append(source.begin(), source.begin() + 5);  // 只添加"World"，不包含"!"
    cout << target2 << endl;  // 输出: Hello World
    
    return 0;
}
```
1. `begin()`方法返回字符串的第一个字符的迭代器, `end()`方法返回字符串最后一个字符的**下一个**位置的迭代器;
2. 迭代器的范围是**左闭右开**.

---

#### 其他方法

- `find(string, int pos)` 从指定的位置开始寻找字符串位置
```cpp
string str = "Hello World Hello";
// 从位置0开始查找"Hello"
cout << str.find("Hello", 0) << endl;     // 结果: 0
// 从位置1开始查找"Hello"
cout << str.find("Hello", 1) << endl;     // 结果: 12
// 查找不存在的字符串
cout << str.find("Python") << endl;       // 结果: string::npos
```
  - `string::npos`是`size_t`类型的最大值;
  - 可以使用`str.find("Python") == string::npos`作为判断条件, 检查是否找到字符串.

---

- `compare(string)` 字符串比较
```cpp
string str1 = "Hello";
string str2 = "Hello";
string str3 = "World";

cout << str1.compare(str2) << endl;  // 结果: 0  (相等)
cout << str1.compare(str3) << endl;  // 结果: -15 (str1 < str3) 
cout << str3.compare(str1) << endl;  // 结果: 15  (str3 > str1)
```
  - 按照字典序比较得到结果

---

- `to_string(int)` 将数字转换成字符串
```cpp
int num = 123;
string str = to_string(num);
cout << str << endl;          // 结果: "123"
cout << str + "456" << endl;  // 结果: "123456"
```
  - 字符串之间可以通过`+`直接拼接.

---

- `stoi(string)` 将字符串转换成整数
```cpp
string str = "123";
int num = stoi(str);
cout << num + 456 << endl;    // 结果: 579
// 注意：字符串必须是合法的数字格式
// string str = "abc"; 
// int num = stoi(str);  // 这会抛出异常
```
  - 字符串必须是合法的数字格式;
  - ` int num = stoi("abc");`  将会抛出异常

---

### 构造函数
**Outline:**
```cpp
string(const char *cp, int len);
string(const string& s2, int pos);
string(const string& s2, int pos, int len);
```

---

- `string(const char *cp, int len)` 字符数组创建字符串
```cpp
string str1("Hello World", 5);
cout << str1 << endl;
// 输出: Hello
```

- `string(const string& s2, int pos)` 从现有字符串创建新字符串，从指定位置到末尾
```cpp
string s2 = "Hello World";
string str2(s2, 6);
cout << str2 << endl;
// 输出: World
```

- `string(const string& s2, int pos, int len)` 从现有字符串创建新字符串，指定起始位置和长度
```cpp
string s3 = "Hello World";
string str3(s3, 6, 3);
cout << str3 << endl;
// 输出: Wor
```

- `string(int length, char c)` 用指定长度的字符c初始化字符串
```cpp
string str4(5, '*');
cout << str4 << endl;
// 输出: *****

// 实际应用示例
int num = 432;
string str = to_string(num);
cout << string(5 - str.length(), '0') + str << endl;
// 输出: 00432
```


### 成员函数

```cpp
// 提取子字符串
substr(int pos, int len);
string str = "Hello World";
string sub = str.substr(6, 3);  // 结果: "Wor"
```

```cpp
// 字符串赋值
assign();
string str1 = "Hello";
string str2;
str2.assign(str1);  // str2现在是 "Hello"
```


```cpp
// 在指定位置插入字符串
    string str1 = "hello";
    string str2 = "world";
    str1.insert(3, str2);
    cout << str1 << endl;
// 结果: helworldlo 
```

```cpp
// 删除指定位置的指定长度的字符
erase(int pos, int len);
string str = "Hello World";
str.erase(5, 6);  // 结果: "Hello"
```

**Notice：**
1. 所有位置索引都是从0开始计数
2. 如果指定的长度超过字符串实际长度，会自动调整到实际可用长度
3. 使用这些函数时要注意检查参数的有效性，避免越界访问
4. `.assign(str, pos, len)`: 相比于直接赋值, `assign`还提供了精确控制赋值的方法, 也就是指定内容字符串的起始位置和长度.

---
### Substr
在字符串的处理当中, 我们经常需要从一个较长的字符串中提取部分内容. `substr()`方法可以精确地获取字符串片段.

`substr`即substring的缩写, 表示子字符串.

**基本语法**
```cpp
string substr(int pos, int len) ;
```
参数分别表示截取的起始下标以及要截取的长度(如果省略`len`将截取到字符串的末尾).

**e.g.**:
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
- `rfind()`方法: 会从字符串的**末尾向前**搜索，从而返回要查找的字符或子字符串此时第一次出现的位置。如果没有找到，则返回 string::npos.

---

# Function

## Default arguments

要点:

- 默认值必须在函数原型中从右到左地给出, 否则在调用的时候无法分辨;

- 默认值只能出现在函数原型 或者 将声明和定义放在一起, 下面的情况会报错:

  ```cpp
  void f(int i, int j = 10);
  int main()
  {
      ...
  }
  void f(int i, int j = 10){
      ...
  }
  ```







# Group

**选择的标准:**

- 一般情况 $\Rightarrow$ `vector`;
- 程序需要对元素进行**随机访问** $\Rightarrow$ `vector` or `deque`;
- 程序需要在容器**中间插入**元素 $\Rightarrow$ `list` or `forward_list`;
- 程序需要在容器的**首尾插入**元素 $\Rightarrow$ `deque`;
- 容器中的元素**相对较小**但是数量较多 $\nRightarrow$ `list` nor `forward_list`.
  - 否则链表中的指针占用的额外空间反而占比较高, 导致空间浪费.



## Pair

### 基本介绍

#### 1. 基本概念
pair 是 C++ 标准库提供的模板类，用于<u>将两个不同类型的值组合成一个对象</u>。它定义在 `<utility>` 头文件中。

#### 2. 创建与初始化
```cpp
// 默认构造
std::pair<std::string, int> p1;

// 直接初始化
std::pair<std::string, int> p2("tag", 10);

// 使用make_pair
auto p3 = std::make_pair("data", 5);

// 统一初始化
std::pair<std::string, int> p4{"value", 8};
```

#### 3. 访问元素
```cpp
// 传统访问方式
std::cout << p2.first << ": " << p2.second << endl;

// 结构化绑定(C++17)
auto [key, val] = p3;
std::cout << key << ": " << val << endl;
```

#### 4. 常用操作
```cpp
// 比较操作
if (p1 == p2) {...}
if (p1 < p2) {...}  // 先比较first，再比较second

// 交换内容
p1.swap(p2);
std::swap(p1, p2);
```

#### 5. 实际应用示例
```cpp
// 作为函数返回值
std::pair<bool, string> checkInput(const string& input) {
    if (input.empty()) {
        return {false, "输入不能为空"};
    }
    return {true, ""};
}

// 在容器中使用
vector<pair<string, int>> dataList = {
    {"item1", 10},
    {"item2", 20},
    {"item3", 30}
};

// 与map配合使用
map<string, pair<int, double>> complexData;
```

#### 6. 注意事项
1. pair 的元素可以是任意类型，包括自定义类型
2. 使用结构化绑定需要C++17或更高标准
3. pair 常用于需要返回多个值的函数
4. 在性能敏感场景要注意构造和拷贝开销



## Set

**集合**: 用于存储一组不允许重复的元素, 且会自动排序.

可以使用的方法包括:

1. `.insert( )`: 插入元素;

2. `.erase()`: 删除元素. 如果输入是元素值, 那么返回1/0表示是否成功删除; 如果输入是迭代器, 那么返回的是**下一个元素的迭代器**.

3. `.find( )`: 寻找元素, 如果找到 返回对应的 **迭代器**. 否则返回 `.end( )`;

   

## Vector

存储元素在**连续的内存空间**中, 支持**随机访问**.
- 可以动态增长, 适合存储**未知数量**的元素;
- 通过下标访问元素的时间复杂度为 O(1);
- 在末尾插入和删除元素的时间复杂度为 O(1);
- 在中间插入和删除元素的时间复杂度为 O(n);
- **使用场景**: 需要随机访问、排序、内存连续存储的场景.

**语法**:
- 使用 `.end()`返回一个指向容器**末尾后一个位置**的迭代器:
  ```c++
  auto it = find(vec.begin(), vec.end(), value);  // 查找 value
    if (it != vec.end()) {         // 如果找到了（即没有返回 end()）
        vec.erase(it);             // 则删除找到的元素
    }
  ```
- 使用 `.push_back()`在末尾插入元素, 或者使用`.emplace_back()`在末尾原位构造元素(更加高效);
  ```c++
  vec.push_back(10);
  vec.emplace_back(20); //更加高效
  ```
  > `emplace_back`方法**直接**在容器的**内存空间中构造**对象, 相比于`push_back`而言更加**高效**.
- `.erase()`方法删除指定位置的元素, 可以删除单个元素, 也可以删除一段区间;
  
    ```c++
     vec.erase(vec.begin() + 1);       // 删除第二个元素
     vec.erase(vec.begin(), vec.begin() + 3); // 删除前三个元素
     vec.clear();                      // 清空整个 vector
    ```
    > `vec.clear();` 将会清空整个vector.
    >
    > 和`insert`需要的参数一样, 都需要**迭代器**而非索引来定位.
- `vec[i]`的形式访问, 使用`vec.at(i)`的方式可以在越界时抛出异常;
- `.begin()`和`.end()`获取迭代器, 使用范围for循环遍历元素;
    ```c++
     cout << "Vector elements:" << endl;
     for (int num : vec) {
        cout << num << " ";
     }
     cout << endl;
    
    //使用迭代器遍历
    for (auto it = vec.begin(); it != vec.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    ```
- `.size()`获取`vector`的大小, `.empty()`判断`vector`是否为空;
  ```c++
  cout << "Vector size: " << vec.size() << endl;
  if (vec.empty()) {
      cout << "Vector is empty." << endl;
  }
  ```
- 使用`sort()`对`vector`进行排序, 使用`find()`查找元素;
  ```c++
  sort(vec.begin(), vec.end());   // 排序
  auto it = find(vec.begin(), vec.end(), 5); // 查找 5
  ```
  
- `insert` 插入的位置是指定的迭代器位置之前一个;

### Reserve
为了避免频繁地扩展内存, 可以通过`reserve`预先分配合适的空间, 同时通过`.reszie()`调整大小;
```cpp
vector<string> v2;
v2.reserve(1000);  // 一次性分配 1000 个元素的空间

v2.resize(v2.size() + v2.size()/2); // 调整大小为原来的 1.5 倍
```

`reserve`只分配空间而不创建元素,`resize`将同时分配元素(默认值):

```cpp
vector<string> vec;
// reserve: 只分配空间，不创建元素
vec.reserve(10);  
cout << "The capacity with reserve: " << vec.capacity() << endl;
cout << "The size with reserve: " << vec.size() << endl;    

// resize: 分配空间并创建元素
vec.resize(10);   
cout << "The capacity with resize: " << vec.capacity() << endl;
cout << "The size with resize: " << vec.size() << endl;   
```

**Output**:
```shell
The capacity with reserve: 10
The size with reserve: 0
The capacity with resize: 10
The size with resize: 10
```
> [!important]
>
> `.push_back()`的实际作用是在容器索引的`size`处插入元素.
>
>  而`reserve`不会影响容器的`size`,  初始化和`resize`会影响并且填充默认值:

**e.g.  验证:**

```cpp
int main() {
    vector<int> vec(10);
    
    // 打印初始状态
    cout << "初始状态：\n";
    cout << "size: " << vec.size() << ", capacity: " << vec.capacity() << "\n\n";
    
    // 预留5个空间
    vec.reserve(15);
    cout << "reserve(15) 后：\n";
    cout << "size: " << vec.size() << ", capacity: " << vec.capacity() << "\n\n";
    
    vec[20] =20;

    // 添加元素并观察
    cout << "添加元素过程：\n";
    for(int i = 1; i <= 6; i++) {
        vec.push_back(i);
        cout << "添加 " << i << " 后 - ";
        cout << "size: " << vec.size() 
             << ", capacity: " << vec.capacity()
             << ", 元素: ";
        for(int x : vec) cout << x << " ";
        cout << "\n";
    }
    
    return 0;
}

```

**Output:**

```shell
初始状态：
size: 10, capacity: 10

reserve(15) 后：
size: 10, capacity: 15

添加元素过程：
添加 1 后 - size: 11, capacity: 15, 元素: 0 0 0 0 0 0 0 0 0 0 1 
添加 2 后 - size: 12, capacity: 15, 元素: 0 0 0 0 0 0 0 0 0 0 1 2 
添加 3 后 - size: 13, capacity: 15, 元素: 0 0 0 0 0 0 0 0 0 0 1 2 3 
添加 4 后 - size: 14, capacity: 15, 元素: 0 0 0 0 0 0 0 0 0 0 1 2 3 4 
添加 5 后 - size: 15, capacity: 15, 元素: 0 0 0 0 0 0 0 0 0 0 1 2 3 4 5 
添加 6 后 - size: 16, capacity: 30, 元素: 0 0 0 0 0 0 0 0 0 0 1 2 3 4 5 6 
```

1. 此处的 `vector<int> vec(10);`初始化了10个默认值的`int`类型的元素;
2. `vec[20] = 20;`没有进行越界与否的检查, 实际上存在越界, 但是不会报错, 也不会有实际的作用;
   1. 如果换成`vec.at(20) = 20`将会在编译时报错;
3. 可以发现, `reserve`的作用就是避免了多次自动扩容.

> `reserve`的实质: 如果预留的容量大于当前的实际容量, 将自动分配一个指定容量的内存, 将原有的元素**copy**到新的内存空间, 并更新容器的指针, 然后释放原来的内存空间.



### Resize

用法的枚举:

1. `resize(n)`: 将vector的大小调整为n, 如果大于当前值, 则在末尾添加具有默认值的新元素;

2. `resize(n, val)`: 同样调整大小, 但是指定了默认值为新的 `val`;

3. 对于二维向量的内存分配也是类似的:

   ```cpp
   	vector<vector<int>> m;  //二维码向量;
     ...
     m.resize(r,vector<int>(c,0)); //初始化为一个r行c列且初始值为0的矩阵.
   ```

   

## List

- 在`list`容器当中, 迭代器是双向迭代器;
  - 双向迭代器不支持大小的比较, 只支持 `==`,`!=`,`++`,`--`;
  因此, 注意实际的使用:
```cpp
list<int> lst1;
list<int>::iterator iter1 = lst1.begin();
list<int>::iterator iter2 = lst1.end();

// 正确的写法
while (iter1 != iter2) {
    // 处理当前元素
    ++iter1;
}

// 错误的比较
// while(iter1 < iter2) 
    
```

### 有序链表
```cpp
#include <iostream>
#include <list>
#include <string>


using namespace std;

int main() {
    list<string> s;
    string str;
    list<string> :: iterator p;
    int count ;

    cout << "enter the number of the strings:" << endl;

    cin >> count; 


    for(int i = 0; i < count; i++){
        cout << "enter a string:" ;
        cin >>str;
        
        p = s.begin();
        while(p != s.end() && *p <str)
            p++;
        s.insert(p,str);
    }
    for(p = s.begin(); p!=s.end(); p++)
        cout << *p << endl;
    cout << endl;
    return 0;
}
```

**分析:**
- `while(p != s.end() && *p <str)` 每次输入`str`时, 令迭代器从`list`的开头开始, 进行字典序的比较;

> [!important]
>
> 找到插入的位置, 利用`insert()`方法插入到给出迭代器的**前面**!.





## Deque
`deque`即 double-ended queue, **双端队列**.

支持:
- 在两端快速的插入或删除;
- 随机访问;

**语法**:
```cpp
#include <deque>
deque<int> dq;

// 1. 插入操作
dq.push_back(1);    // 在末尾插入
dq.push_front(2);   // 在开头插入
dq.insert(pos, val);// 在指定位置插入

// 2. 删除操作
dq.pop_back();      // 删除末尾元素
dq.pop_front();     // 删除首部元素
dq.erase(pos);      // 删除指定位置元素

// 3. 访问操作
dq[0];              // 随机访问
dq.at(1);           // 带边界检查的访问
dq.front();         // 访问第一个元素
dq.back();          // 访问最后一个元素
```

**示例:**
```cpp
#include <deque>
#include <iostream>
using namespace std;

int main() {
    deque<int> dq;
    
    // 在两端插入元素
    dq.push_back(3);
    dq.push_front(1);
    dq.push_back(4);
    dq.push_front(8);
    
    // dq ：{8, 1, 3, 4}
    
    // 使用随机访问
    for(size_t i = 0; i < dq.size(); ++i) {
        cout << dq[i] << " ";
    }

}

```

### Forward_list
`forward_list`即 单项链表.

- 只能向前遍历, 即对应的迭代器不支持`--`而支持`++`.
- 同时不支持下标访问以及随机访问.
- 单项链表的设计, 使得内部的每个节点只需要**一个**指针来指向下一个节点, 从而比`list`双向链表更加**节省内存.**

**语法**:

```cpp
#include <forward_list>
forward_list<int> fl;

// 1. 插入操作
fl.push_front(1);           // 在开头插入
fl.insert_after(pos, val);  // 在指定位置之后插入

// 2. 删除操作
fl.pop_front();            // 删除第一个元素
fl.erase_after(pos);       // 删除指定位置之后的元素

// 3. 访问操作
fl.front();               // 访问第一个元素

// 4. 特殊操作
fl.before_begin();        // 返回第一个元素之前的迭代器
fl.begin();               // 返回第一个元素的迭代器
```

**示例**:

```cpp
#include <forward_list>
#include <iostream>
using namespace std;

int main() {
    forward_list<int> fl;
    
    // 插入元素
    fl.push_front(3);
    fl.push_front(2);
    fl.push_front(1);
    
    // 在特定位置后插入
    auto it = fl.begin(); // 指向第一个元素
    fl.insert_after(it, 4); // 在第一个元素后插入4
    
    // 遍历打印
    for(const auto& val : fl) {
        cout << val << " ";
    }
    // 输出：1 4 2 3
}
```

#### 访问前一个元素
由于单项链表的设计特点, 要使得我们可以访问某个节点的前一个元素, 必须采用双指针并结合`before_begin()`方法.

```cpp
// 如果需要访问某个元素的前一个元素，必须从头开始遍历
auto prev = fl.before_begin();
auto curr = fl.begin();
while(curr != fl.end() && *curr != target) {
    ++prev;
    ++curr;
}
```

## Map
作为Associative container(关联容器), 存储键值对( key-value pair ), 并根据键**自动排序**
- 如果插入重复的key, 将会覆盖原有的value;
- 通过键查找元素、插入和删除的时间复杂度均为O(log n);
- **使用场景**: 字典、索引、统计等.

**语法**:
- 使用 `.end()`返回一个指向容器**末尾后一个位置**的迭代器, 作为一个标记, 和查找相结合判断某个元素是否存在于`map`当中;
  ```cpp
  auto it = ages.find("Charlie");  // 查找 "Charlie"
    if (it != ages.end()) {         // 如果找到了（即没有返回 end()）
        ages.erase(it);             // 则删除找到的元素
    }
  ```
- 使用下标(键)直接插入,或者通过键值对插入
  ```cpp
  ages["Alice"] = 25;
  ages.insert({"Bob", 30});
  ages.emplace("Charlie", 28); // 使用 emplace 插入 (更高效)
  ```
  > `emplace`方法指**直接**在容器的**内存空间中构造**对象，而不是先在其他地方构造对象后再将其拷贝或移动到容器中, 相比于`insert`而言更加**高效**.
- `.erase()`方法删除指定key的元素, 也可以通过`.find()`找到key对应的迭代器`it`, 然后`erase(it)`.
  
    ```cpp
    ages.erase("Bob");           // 删除键为 "Bob" 的元素
    
    auto it = ages.find("Charlie");
    if (it != ages.end()) {
        ages.erase(it);         // 删除迭代器指向的元素
    }
    ```
    > `ages.clear();` 将会清空整个map.
- `map[key]`的形式访问, 使用`map.at(key)`的方式可以在key不存在时抛出异常;
- `.find(key)`查找对应键的元素( 返回**迭代器** ), `.count(key)`返回对应键的元素个数(0 or 1)
- `.size()`获取map的大小.
- 迭代器的`->first`和`->second`可以分别访问键和值.
    ```cpp
     cout << "Map elements:" << endl;
     for (auto mapIt = ages.begin(); mapIt != ages.end(); ++mapIt) {
        cout << mapIt->first << ": " << mapIt->second << endl; // 访问键和值
     }
    ```

## Iterator
迭代器(Iterator)是一种通用的访问容器元素的方式, 类似于指针.

- **标记位置**: `.begin()`和`.end()` 分别返回容器第一个元素和最后一个元素的下一个位置的迭代器;

迭代器的分类:
- 输入迭代器: 支持读取和递增操作;
  - `istream_iterator`: 用于从输入流读取数据;
- 输出迭代器: 支持写入和递增操作;
  - `ostream_iterator`: 用于向输出流写入数据;
- 前向迭代器: 具有输入、输出迭代器的**所有**功能, 并且可以多次遍历同一个序列;
  - 比如`forwarf_list`的迭代器:`auto it = flist.begin()` or `forward_list<int>::iterator it = flist.begin()`;
- 双向迭代器: 在前向迭代器的原有功能上, 同时支持**递减**操作;
  - 比如双向链表`list`的迭代器.
    ```cpp
    #include <iostream>
    #include <list>
    using namespace std;
    
    int main() {
        list<int> myList = {10, 20, 30, 40, 50};
    
        // 使用双向迭代器正向遍历
        cout << "Forward traversal: ";
        for ( list<int>::iterator it = myList.begin(); it != myList.end(); ++it) {
            cout << *it << " ";
        }
        cout <<  endl;
    
        // 使用双向迭代器逆向遍历
        cout << "Reverse traversal: ";
        for ( list<int>::reverse_iterator rit = myList.rbegin(); rit != myList.rend(); ++rit) {
            cout << *rit << " ";
        }
        cout <<  endl;
    
        return 0;
        // Forward traversal: 10 20 30 40 50 
        // Reverse traversal: 50 40 30 20 10 
    }
    ```
    > 1. `reverse_iterator`用于声明逆向遍历的迭代器, 也可以使用`auto`直接声明.
    > 2. `rbegin()`和`rend()`分别返回容器最后一个元素和第一个元素的前一个位置的逆向迭代器. 此时的`++`相当于正向遍历时的`--`操作.
- 随机访问迭代器: 具有双向迭代器的所有功能, 同时支持**随机访问**, 如`it+n`,`it[n]`.
  - 比如`vector`的迭代器.
  ```cpp
    vector<int> vec = {10, 20, 30, 40, 50};
    cout << "Vector elements (random access): ";
    for (int i = 0; i < vec.size(); ++i) {
        cout << vec[i] << " "; // 使用下标随机访问
    }
    cout << endl;
  ```

另外, 还有一种迭代器称为**插入迭代器**, 比如`back_inserter`
```cpp
vector<int> vec = {10, 20, 30, 40, 50};

//结合copy将容器的元素直接插入到另一个容器中
vector<int> dest = {60,70};
copy(vec.begin(), vec.end(), back_inserter(dest)); // 在 dest 末尾插入元素
cout << "Copied vector: ";
for (int num : dest) {
    cout << num << " ";
}
cout << endl;
// Copied vector: 60 70 10 20 30 40 50 
```

## for-each
for-each 循环的语法：
```cpp
for (range_declaration : range_expression) {
    loop_statement;
}
```
- range_declaration： 声明一个变量，用于存储 range_expression 中的每个元素。这个变量的类型应该与 range_expression 中的元素类型兼容。可以使用 `auto `关键字让编译器自动推导类型;
- range_expression： 一个表示序列的表达式，例如数组、容器（如 vector、list、map 等）或**字符串**;
- loop_statement： 循环体，包含要对每个元素执行的语句.

e.g:
```cpp

#include <iostream>
#include <vector>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};

    // 使用 for-each 循环遍历 vector
    for (int num : numbers) {
        std::cout << num << " "; // 输出每个元素
    }
    std::cout << std::endl;

    // 使用 auto 关键字自动推导类型
    for (auto num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    // 修改容器中的元素（需要使用引用）
    for (int &num : numbers) {
        num *= 2; // 将每个元素乘以 2
    }

    // 输出修改后的元素
    for (auto num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    return 0;
}
```
- `for (int &num : numbers)`：使用引用 &，可以直接修改容器中的元素.

### Map的循环
当range_expression是`map`时, 可以使用`auto`自动推导range_declaration的类型.需要注意是:
- 用迭代器的方式访问`map`中的键值对的形式是 `it->first`与`it->second`;
- 在`for-each`循环当中, range_declaration是一个值, 因此使用`.first`与`.second`来访问键和值.
    ```cpp
    #include <iostream>
    #include <vector>
    #include <map>
    #include <string>
    #include <algorithm>
    
    using namespace std;
    
    int main(){
        map<string, string> m = {{"one", "1"}, {"two", "2"}, {"three", "3"}};
        vector<string> vec;
    
        for(auto& entry : m){
            vec.push_back(entry.first + ":" + entry.second );
        }
        copy(vec.begin(), vec.end(), ostream_iterator<string>(cout, " "));
    }
    ```
    **Output:**
    ```shell
    one:1 three:3 two:2
    ```
> 此处由于`map`自动按照键的字典序进行排序, 因此输出时`three`的元素在`two`前;

在上述的示例中, 也可以使用下面的方式进行`vec`的输出:
```cpp
for(const auto& s : vec) {
    cout << s << " ";
}
```
此时`auto`会自动推导为`string`类型, 且`&`对数组的元素进行了引用, 使得输出更加高效.

### Pro&Con
`for-each`循环的优点:
- 消除了访问数组等越界的风险;
- 不需要事先初始化迭代器;

`for-each`循环的缺点:
- 无法获取元素的索引;
- 只能顺序地遍历.

## typedef
我们可能经常遇到一些复杂的类型声明，特别是在使用模板、函数指针或复杂的数据结构时。这些类型名称可能会变得冗长，不仅书写起来繁琐，而且降低了代码的可读性.

而typedef 就是为了解决这个问题而存在的，它允许我们为类型创建别名，使代码更加简洁和易于理解.
```cpp
typedef old_type new_type;
```

## Notices
1. 直接对数组、字符串和`vector`进行随机访问时, 需要注意可能存在越界问题, 且编译器可能不会报错;
2. 对于`vector`, 可以通过`.at() = `的方式进行安全访问, 编译器会进行边界检查. 或者通过`.push_back()` or `.emplace_back`的方式在末尾赋值. 同时注意用`.reserve()`预先分配充分的内存空间.
3. 避免不经意地向`map`当中插入元素:
   1. 错误的示范:
    ```cpp
    if(foo["bob"] == 1){...}
    // 设置默认的零值
    ```
   2. 使用`.count()`方法正确检查元素是否存在:
    ```cpp
    if( foo.count("bob") ){...}
    ```
   3. 也可以使用`find()`方法检查元素是否存在:
   ```cpp
    auto it = m.find("four");
   
    if(it  != m.end()){
        cout << it->second << endl;
    }
    else{
        cout << "Not found" << endl;
    }
   ```
4. 使用`.empty()`方法来检查容器**整体是否为空**, 而非`.count() == 0`的检查. 前者使用 O(1) 的时间复杂度, 而后者使用 O(n) 的时间复杂度.
5. `erase()` 方法会返回**指向**被删除元素的**下一个**元素的迭代器, 应当直接采用返回值来对迭代器进行赋值:
   
    ```cpp
    //Initialize a list
    list<int> L;
    list<int>::iterator li = L.begin();
    
    // Wrong:
    L.erase(li);    // 删除元素后，li 变成了无效迭代器
    ++li;           // 错误, 不能对无效迭代器进行操作
    
    // Correct:
    li = L.erase(li);  // 删除元素后，li 被更新为指向被删除元素的下一个元素
    ```

# 指针
- Pointers to Objects
```cpp
string str = "hello";
string *p = &str;
```

- Oprators with Pointers
  - `&`: 取地址;
  - `*`: 解引用;
  - `->`: 用于访问对象的成员.
```cpp
(*p).length();
// 等价于
p->length();
```
> `length()`即为`string`类的成员函数, 因此可以用`->`来访问.


# 常量

## 指针
> 以`char`为例.

- 指向常量的指针 `const char *p`
  - 可以改变指针的地址.
  - 无法通过指针改变对象的值
- 常量指针 `char * const p`
  - 指针指向的地址无法改变;
  - 但是可以通过指针改变对象的值
  如果需要同时保证地址和值都无法改变, 则需要使用`const char * const p`.



如果`sp`是指向字符串的指针, 那么这两种的写法是等价的, 注意`.`的优先级高于`*`, 因此括号不可忽略.

```cpp
sp->length();
(*sp).length();
```



# Class

### `::`

`::` resolver: 作用域解析运算符

- 作用: 

  - 访问全局的作用域
    当局部变量和全局变量同名时, 可以使用 `::` 来访问全局变量

    ```cpp
    int value = 10; // 全局变量
    
    void function() {
        int value = 20; // 局部变量
        cout << value;    // 输出 20（局部变量）
        cout << ::value;  // 输出 10（全局变量）
    }
    ```

  - 访问命名空间中的成员

    ```cpp
    namespace Math {
        const double PI = 3.14159;
    }
    
    double circumference = 2 * Math::PI * radius; // 使用命名空间中的常量
    ```

- 语法:

  - `<class_name> :: <function_name>`
  - `::<function_name>`  全局作用域



e.g. 

```cpp
void S::f() {
    ::f();  // Would be recursive otherwise!
    ::a++;  // Select the global a
    a--;    // The a at class scope
}
```

> `S::f()`: 定义了属于类S的成员函数f;
>
> `::f()`:表示调用全局作用域中的函数 `f()`, 默认为递归调用当前的成员函数;
>
> `::a++`表示将全局作用域的 `a` 自增, `a--`则访问并递减类作用域中的成员变量 `a`.



### `this`

`this`指针是成员函数的隐藏参数. 指向**当前对象的实例**.

```cpp
void Point::move(int dx, int dy);
//等价于
void Point::move(Point *this, int dx, int dy);
```

当调用成员函数时, 对象的地址会自动作为 `this`参数传递.



在一个成员函数内部调用同一个类的其他成员函数时, 无需指定显式指定 `this`:

e.g

```cpp
class Point {
private:
    int x, y;
    
public:
    // 移动点的位置
    void move(int dx, int dy) {
        x += dx;
        y += dy;
    }
    
    // 打印点的坐标
    void print() {
        std::cout << "Point at (" << x << ", " << y << ")" << std::endl;
    }
    
    // 组合以上两个功能的函数
    void move_and_print(int dx, int dy) {
        move(dx, dy);  // 等同于 this->move(dx, dy)
        print();       // 等同于 this->print()
    }
};
```

> 但是也可以显式指定 `this->move`, 这可以明确调用的是成员函数, 增强可读性, 便于IDE显示该类可访问的成员函数.



### 封装特性

在OOP中, Object = Attributes + Services, 即数据和操作被**封装**在一起, 构成一个完整的对象.



### 声明与定义

我们应当在头文件中声明对象的成员及其 `public`,`private`和 `protected`等属性, 并且在 `cpp`文件中给出具体的定义:

> 最好为每个类都建立如此对应的头文件和源文件 `cpp`.

e.g. 

```cpp
// Student.h - 类的声明
#ifndef STUDENT_H
#define STUDENT_H

#include <string>
using namespace std;  // 在头文件中使用

class Student {
private:
    // 数据成员
    string name;     
    int id;
    float gpa;
    
public:
    // 构造函数原型
    Student(const string& name, int id);
    
    // 成员函数原型
    void setName(const string& newName);
    string getName() const;
    void calculateGPA();
    bool isEligibleForScholarship() const;
};

#endif // STUDENT_H

```

```cpp
// Student.cpp - 成员函数的定义
#include "Student.h"
using namespace std;  // 在源文件中使用

// 构造函数实现
Student::Student(const string& name, int id) {
    this->name = name;
    this->id = id;
    this->gpa = 0.0;
}

// 成员函数实现
void Student::setName(const string& newName) {
    name = newName;
}

string Student::getName() const {
    return name;
}

void Student::calculateGPA() {
    // 实现GPA计算逻辑
    // ...
}

bool Student::isEligibleForScholarship() const {
    return gpa >= 3.5;
}
```

> ` Student::getName() `指的就是类 `Student`中的成员函数 `getName()`.



具体来说, `.h`头文件当中应该有:

- 外部变量的声明
  e.g. `extern int globalCounter;  // 仅声明，不定义`

- 函数原型
  e.g. `int calculateSum(int a, int b);  // 函数声明，不包含实现`

- 类/结构体的声明
  e.g.

  ```cpp
  class Student;  // 前向声明
  
  // 或完整类声明（不含成员函数定义）
  class Rectangle {
  private:
      double width;
      double height;
  public:
      Rectangle(double w, double h);
      double getArea() const;
  };
  ```



回顾 `#include`: 将被引用的文件插入 `.cpp` 文件当中

- `#include "xx.h"`: 首先在当前目录下寻找;

- `#include <xx.h>`: 直接在指定的目录中寻找

  > 等价于 `#include <xx>`.



为了避免在多个 `.cpp` 文件中重复引用相同的头文件, 可以通过 `#ifndef`等标记来判断是否需要引用当前的头文件:

```cpp
#ifndef HEADER_FLAG
#define HEADER_FLAG

#endif 
```

> `HEADER_FLAG`一般使用完全大写来方便标识, 但是也可以大小写混合.

e.g. 

```cpp
// 文件: vector.h
#ifndef VECTOR_H
#define VECTOR_H
// ...
#endif // VECTOR_H
```

## 生命周期管理

当对象被创建时，通常需要进行一些初始化工作. 而当对象不再使用时，则需要进行相应的清理工作.

为了确保这些工作不被遗忘, `cpp`的类具有构造函数和析构函数, 分别作用于对象的创建和消除过程.

### 构造函数

构造函数是一种特殊的成员函数，其名称与类名相同，没有返回类型（甚至不是void）。当创建类的对象时，构造函数会自动被调用.

- 语法: 

```cpp
class ClassName {
public:
    // 默认构造函数
    ClassName();
    
    // 带参数的构造函数
    ClassName(参数列表);
    
    // 拷贝构造函数
    ClassName(const ClassName& other);
    
};
```

> 1. **默认构造函数**：不带参数或所有参数都有默认值;
> 2. **带参数的构造函数**：接受一个或多个参数;
> 3. **拷贝构造函数**：从同类型的另一个对象创建新对象.



- 构造函数初始化列表

  ```cpp
  Point::Point(int xx, int yy) :x(xx), y(yy) {
    ...
  }
  ```

  > 构造函数时, 传递参数并直接赋值给内部的成员变量 `x` , `y`.



- 结构体中的构造函数:

  ```cpp
  struct Y { 
      float f;     // 浮点型成员变量
      int i;       // 整型成员变量
      Y(int a);    // 声明了一个接受int参数的构造函数
  };
  ```

  > 1. 此处只是声明了构造函数需要 `int a`作为参数, 但是没有给出具体的实现;
  > 2. 声明结构体对象(数组)  e.g. `Y y1[] = { Y(1), Y(2), Y(3) };`



### 默认构造

`auto` default constructor: (自动) 默认构造函数. **当且仅当**不存在任何构造函数时, 程序会自动生成默认构造函数, 不作任何的操作.

`默认构造函数`： 在没有参数的情况下可以调用的构造函数， 称为默认构造函数. 其来源除了上述的程序构造以外, 还包括:

1. 显示定义的无参构造函数;
2. 定义的所有参数都具有默认值的构造函数.



- 对于成员变量: 不进行初始化;

### 析构函数

析构函数也是一种特殊的成员函数，其名称是类名前加上波浪号 `~`. 当对象超出作用域或被显式删除时，析构函数会自动被调用.

```cpp
class ClassName {
public:
    ~ClassName();
};
```

- 类似于栈, 优先创建的后析构.





运用的示例:

```cpp
#include <iostream>
#include <cstring>

class MyString {
private:
    char* data;

public:
    // 默认构造函数
    MyString() : data(nullptr) {
        std::cout << "默认构造函数调用" << std::endl;
    }

    // 带参数的构造函数
    MyString(const char* str) {
        if (str) {
            data = new char[strlen(str) + 1];
            strcpy(data, str);
        } else {
            data = nullptr;
        }
        std::cout << "参数构造函数调用" << std::endl;
    }

    // 拷贝构造函数
    MyString(const MyString& other) {
        if (other.data) {
            data = new char[strlen(other.data) + 1];
            strcpy(data, other.data);
        } else {
            data = nullptr;
        }
        std::cout << "拷贝构造函数调用" << std::endl;
    }

    // 析构函数
    ~MyString() {
        delete[] data;
        std::cout << "析构函数调用" << std::endl;
    }

    // 打印字符串
    void print() const {
        std::cout << (data ? data : "空字符串") << std::endl;
    }
};

int main() {
    // 测试各种构造函数
    MyString s1;                  // 默认构造函数
    MyString s2("Hello");         // 带参数的构造函数
    MyString s3 = s2;             // 拷贝构造函数
    
    s1.print();
    s2.print();
    s3.print();
    
    return 0;  // 所有对象在这里被销毁，调用析构函数
}
```



本地对象: 

`Field`(字段)指的是在类中定义的变量(成员变量):

- 可以直接被类中的所有方法访问;
- 生命周期**和类的对象保持一致;**

其他类型数据的生命周期:

- **参数**: 函数执行期间;
- **局部变量**: 声明的代码块内部.

---

全局对象:





> [!NOTE]
>
> 如果在类或对象的方法内部定义了一个和成员变量同名的变量, 那么将默认访问这个变量, 只有用 `this->xxx`才能显式访问成员变量. e.g. `int MyClass::count `.
>
> ```cpp
> class MyClass {
> public:
>     int value = 10; // 字段
> 
>     void printValue() {
>         int value = 20; // 局部变量
>         std::cout << "Local value: " << value << std::endl; // 输出局部变量
>         std::cout << "Field value: " << this->value << std::endl;//使用this指针访问字段
>     }
> };
> ```



## Access Control

`class`的默认为 `private`, 而 `struct`的默认权限是 `public`.

访问限制符:

### `friend`

在 `class`内部声明友元, 可以使得对应的函数访问自身的成员变量(包括私有和受保护, 也即是所有的变量).

```cpp
struct X {
private:
    int i;
public:
    void initialize();
    friend void g(X*, int i);
    friend void Y::y();
}
```



> [!NOTE]
>
> **友元关系不具有传递性 !**



### `protected`

该声明内的成员可以被以下的范围访问:

1. 该类自身的成员函数;
2. **该类的派生类的成员函数;**

e.g.

```cpp
class Base {
protected:
    int protectedVar;
public:
    Base(int val) : protectedVar(val) {}
};

class Derived : public Base {
public:
    Derived(int val) : Base(val) {}
    void accessProtectedVar() {
        protectedVar = 10; // 派生类可以访问 protectedVar
    }
    int getProtectedVar(){
        return protectedVar;
    }
};
```

> 此处的 `base`就是一个基类, `class Derived : public Base`表明 Derived 是 base的一个派生类.
>
> 因此,  派生类可以通过自己的成员函数, 访问基类的 `protected`内的成员变量.







## Static

对于本地变量和全局变量的作用不同, 前者是生命周期, 后者是访问空间.

静态的对象的生命周期是全局, 但是不会跟全局变量一样(一开始就创建), 而是在第一次调用的时候才构造, 且只构造一次.



- `静态成员变量`由所有的实例**共享**, 初始化的时候不能再添加 `static`标签(否则无法被外文件访问), 必须在类的外部进行初始化, 且需要声明所属于的类;

  > 但是也可以被普通的成员函数所访问.

- `静态成员函数`属于类本身, 因此可以通过类名直接调用, 但是只能访问类的静态成员变量, 因为不存在属于实例的`this`指针. 静态成员函数可以在**类的内部**就定义, 如果在类的外部定义, 也不需要额外的`static`标签;

  ```cpp
  class MyClass {
  public:
      static int count; // 静态成员变量
      int id;
  
      MyClass(int i) : id(i) {
          count++; // 每次创建对象，count加1
      }
  
      ~MyClass() {
          count--;
      }
  
      static int getCount() { // 静态成员函数
          return count;
      }
  };
  
  int MyClass::count = 0; // 静态成员变量的初始化
  
  int main() {
      std::cout << "Count: " << MyClass::getCount() << std::endl; // 通过类名调用静态成员函数
      MyClass obj1(1);
      MyClass obj2(2);
  
      std::cout << "Count: " << MyClass::getCount() << std::endl; // 通过类名调用静态成员函数
  
      return 0;
  }
  ```

  **Output:**

  ```cpp
  Count: 0
  Count: 2
  ```



- 函数内部的静态变量只会在调用的时候**初始化一次**, 直到程序结束.
  e.g. 计数函数的调用次数:

  ```cpp
  void f(){
    static int num_calls = 0;
    ...
    num_calls += 1;
  }
  ```

- `extern`关键字用于声明变量或函数在其他文件中定义. 告诉编译器从而允许跨文件的访问.

  > 但是这种跨文件访问只能作用于**非静态**的全局变量, i.e. 全局变量加上`static`声明之后, 将其作用域限制在了当前文件的内部.

- 函数内部的静态对象, 其构造函数只会在定义的时候调用一次. 并且析构函数当退出程序时调用. 即使采取条件构造, 也只会在第一次条件满足的时候构造.



- 静态成员的使用:

  - 通过类名: `<class_name>::<static member`

  - 通过实例名: `<ob variable>.<static member>`

    > 让人误以为是类的对象变量, 不建议这样使用.





## Reference

引用（Reference）是一个非常重要的特性。它的引入是为了解决一些特定场景下的问题：

- **<u>避免不必要的拷贝</u>**：在某些情况下，传递大型对象或结构体给函数时，如果直接传递拷贝会导致性能下降。引用允许我们传递对象的别名，而不需要拷贝整个对象。
- **<u>简化代码</u>**：引用可以使代码更简洁，特别是在函数返回值和参数传递时。例如，通过引用返回一个对象可以避免构造临时对象。
- **<u>指针的安全替代</u>**：引用提供了指针的功能，但避免了指针容易导致的错误，如空指针解引用或野指针。



**基本语法**: 

引用是一个变量的别名，它在**声明时必须被初始化**，并且一旦初始化后就**<u>不能再指向其他</u>**对象.

```cpp
int a = 10;
int& ref = a;  // ref 是 a 的引用
```

- `int&` 表示引用类型，`ref` 是 `a` 的引用。
- 引用必须在声明时初始化，并且不能重新引用到另一个对象。
- 无法对引用进行引用;
- **不允许**存在 **以引用为元素的数组**



e.g.

```cpp
#include <iostream>

int main() {
    int a = 10;
    int& ref = a;  // ref 是 a 的引用

    std::cout << "Original value of a: " << a << std::endl;
    std::cout << "Value of ref: " << ref << std::endl;

    ref = 20;  // 修改引用会影响原变量

    std::cout << "After modifying ref, value of a: " << a << std::endl;
    std::cout << "Value of ref: " << ref << std::endl;

    return 0;
}
```

在这个例子中：

- `ref` 是 `a` 的引用，修改 `ref` 的值会影响 `a` 的值。
- 通过引用，我们可以访问和修改原始变量 `a` 的值，而不需要直接操作 `a`。



引用可以作为函数的形参, 此时**函数内部的形参作为实参的引用可以改变实参的值**.

引用的绑定必须是一个具有明确地址的左值 ,而不能是临时产生的右值:

```cpp
void func(int &);
func (i * 3); // Warning or error!
```



#### 指针与引用

- 限制:

  - 无法获得指针的引用;

    ```cpp
    int &*p;// illegal
    ```

  - 但是可以获得**指向引用的指针**

    ```cpp
    void f(int *&p);
    ```

 可以如此理解, 引用实际上是对象的别名. 用于修改对象的值. 但是为了修改指针, 可以直接使用指针的指针 `**`.

此外, 引用并非独立的对象, 而是直接 **绑定**. 因此 `int& ref = a;`  `&ref`就是 a的地址.



#### 右值引用

左值是指具有明确地址的变量或者引用, 右值是计算过程中的中间结果或者字面量 (e.g.`10`).等不可寻址的值;

> 涉及到计算的基本都是右值, 但是这四种的计算结果依旧是左值: `*`,`.`,`[]`和 `->`.

右值一般在计算结束后就消失了, 如果我们希望延长其生命周期, 就可以使用 **右值引用**.

- **格式**: `<tyep> && <ref_name> = <right_value>`

  ```cpp
  int x=20; // left-value 
  int&& rx = x * 2:
  ```

- TIps:

  - 右值引用在初始化之后就可以正常赋值;
  - 右值引用无法使用左值进行赋值.



#### 引用参数与函数重载

```cpp
#include <iostream>
using namespace std;

void fun(int& lref){
   cout << "lref = " << lref << endl;
}

void fun(int&& rref){
   cout << "rref = " << rref << endl;
}

int main(){
   int x = 10;
   fun(x);
   fun(10);
}
```

**Output**:

```cpp
lref = 10
rref = 10
```

> 1. 字面量`10`作为右值, 可以通过右值引用作为函数的参数;
> 2. 具有明确地址的变量 `x`是左值;
> 3.  C++ 允许在同一个作用域内声明多个具有相同名称但参数列表不同的函数。这被称为**函数重载**。编译器通过检查函数调用时提供的参数类型和数量来决定调用哪个重载函数。



另外, 加上`const`之后, `& `的形参也可以接受右值作为实参, 比如: `void fun (const int& clref) {...}`

> 但是如果已经存在右值实参作为形参的同名函数, 将会优先选择后者进行重载.

这是因为普通引用对于右值的修改 make no sense, 而 `const` 引用<u>保证不会修改引用的对象</u>，因此即使是临时对象（右值）也可以安全地绑定到 `const` 引用.



## Constants

使用 `const`声明常量, 常量的值不可修改.

`const`声明集合的时候, 其中的值在**编译期间不可知**, 因此<u>无法在代码中, 使用常量集合内部的值进行操作.</u>

```cpp
const int i[] = {1,2,3};
float f[i[2]]; // Illegal!
```



使用`const`对指针类型进行操作的时候:

1. 忽略类似于 `char`之类的类型, 只关注 `const`与 `*`之间的位置关系;
2. 如果是 `const *p` 意思是指针指向的内容不可通过这个指针进行更改;
3. 如果是 `* const p`意思是指针指向的对象不可更改, 但是可以通过 `*p` 的方式改写对象的值



---

关于字符指针与字符数组:

- `char  *p = "hello";` 实际上是 `const char *p`, 也就是说不允许修改 `*p`;
- 而 `char p[] = 'hello';` 则可以通过`*p` 修改.



---

如果**成员函数**的<u>名称后</u>加了 `const`标记, 意味着:

1. 无法通过该成员函数改变成员变量的值.
2. 同时无法调用其他 **非const**的成员函数
3. 实际上, 将其的 `this`指针转换为 `const A* this`, 也就是指向常量的指针

> 因此 `const`修饰的成员函数具有 `this`指针(可访问), 不要与 `static`修饰的静态成员函数混淆! 后者不具有 `this`指针.
>
> 注意不要与 `const  type f()`混淆, 这是限制返回的结果无法修改; 而 `type f() const`限制成员函数本身的操作.



**重载**: 允许根据成员函数是否被 `const`限制, 以及对象本身是否为 `const`来重载成员函数.

```cpp
class A {
public:
    void f() const {
        cout << "const version" << endl;
    }
    void f() {
        cout << "non-const version" << endl;
    }
};

int main() {
    A a;
    const A ca;

    a.f();    // 输出: non-const version
    ca.f();   // 输出: const version

    return 0;
}
```



如果成员变量是 `const`, 那么:

- **必须在对象构造时进行初始化** (无法在构造函数中进行直接赋值):

  ```cpp
  class A {
  public:
      const int i;
      A(int value) : i(value) {} // 在初始化列表中初始化
  };
  ```



如果在实例化对象的时候,  声明了这个实例是 `const`, 那么就无法调用成员函数中没有在后面声明 `const`的部分.( 即使实际上并没有修改的函数, 也不能通过编译).  —— 只能调用那些被标记为`const`的成员函数。

```cpp
class A{
  ...
  int get_value();
  int get_const_value const();
}

const A a();
a.get_value; // ERROR, const对象无法调用非const声明的成员函数
a.get_const_value; // ok
```



---

![image-20250318120332036](https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefinedimage-20250318120332036.png?imageSlim)

> 无法用普通的指针来指向 `const`常量.
>
> 但是可以用指向常量的指针 来指向非常量的对象.



---

全局变量的构造在 `main()`之前. 静态本地变量在第一次执行到的时候才发生构造, 且只构造一次.

---

## 动态内存

在 C++ 中，使用 `new` 关键字分配的空间位于 **堆** 上，与栈上的自动变量不同，堆上的内存不会自动释放，需要手动使用 `delete` 来析构以避免内存泄漏。

#### 示例代码
```cpp
#include <iostream>
using namespace std;

int main() {
    // 使用 new 在堆上分配一个整数
    int* ptr = new int(10);
    cout << "值: " << *ptr << endl;

    // 使用 delete 释放内存
    delete ptr;

    // 指针置空，避免野指针
    ptr = nullptr;
    return 0;
}
```

#### 注意事项
- 每次 `new` 分配的内存都需要对应的 `delete`。
- 对于数组，使用 `new[]` 分配，释放时用 `delete[]`：
```cpp
int* arr = new int[5];  // 分配数组
delete[] arr;           // 释放数组
```

---

## Inline Class

### Delegating Constructor

委托构造函数（Delegating Constructor）是通过一个构造函数调用另一个构造函数来完成初始化的机制, 也就是在初始化列表中调用其他.

相对于委托构造的构造函数, 被称为 *target constructor* 目标构造函数.

目标构造函数的执行先于委托构造函数.

---

#### 什么是委托构造函数？
- **定义**: 一个构造函数可以在其初始化列表中调用同一个类的另一个构造函数来完成部分或全部初始化工作。
- **目的**: 避免在多个构造函数中重复编写相同的初始化逻辑，解决代码重复的问题。
- **限制**: 委托构造函数<u>本身不能在初始化列表中再初始化其他成员变量</u>，只能依赖被调用的构造函数。



#### 代码示例与分析

考虑将下面的冗余代码通过委托构造函数简化:

![](https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefinedundefinedimage-20250325120013059.png?imageSlim)

实现: 

```cpp
#include <iostream>
using namespace std;

class ClassC {
public:
    int max;
    int min;
    int middle;

    // 基础构造函数
    ClassC(int my_max) {
        max = (my_max > 0) ? my_max : 10;  // 默认值10
    }

    // 委托给基础构造函数
    ClassC(int my_max, int my_min) : ClassC(my_max) {
        min = (my_min > 0 && my_min < max) ? my_min : 1;  // 默认值1
    }

    // 委托给第二个构造函数
    ClassC(int my_max, int my_min, int my_middle) : ClassC(my_max, my_min) {
        middle = (my_middle < max && my_middle > min) ? my_middle : 5;  // 默认值5
    }
};

int main() {
    ClassC c1{1, 3, 2};
    cout << "max: " << c1.max << ", min: " << c1.min << ", middle: " << c1.middle << endl;
    return 0;
}
```

#### 运行结果分析
- `ClassC c1{1, 3, 2}`:
  1. 调用 `ClassC(int, int, int)` 构造函数。
  2. 它委托给 `ClassC(int, int)`，后者再委托给 `ClassC(int)`。
  3. 初始化顺序：
     - `max = 1`（因为 1 > 0）。
     - `min = 1`（因为 3 > max，不满足条件，使用默认值 1）。
     - `middle = 2`（因为 2 < max 且 2 > min 不成立，但逻辑上仍赋值 2，需检查代码逻辑）。

#### 关键点
1. **初始化位置**:
   - 成员变量不能在委托构造函数的初始化列表中直接使用参数初始化，必须通过被委托的构造函数完成。
   - 如 `ClassC(int my_max, int my_min) : ClassC(my_max)` 中，不能再初始化 `min`，只能在函数体内赋值。

2. **代码重复问题**:
   - 如果每个构造函数都独立初始化 `max`、`min` 等，会导致重复代码。
   - 委托构造函数将公共逻辑集中到基础构造函数中。

3. **委托链**:
   - 可以形成构造函数调用链，如 `ClassC(int, int, int)` → `ClassC(int, int)` → `ClassC(int)`。

4. **限制与解决方法**:
   - 委托构造函数不能再有其他初始化列表项。
   - 如果需要更灵活的初始化，可以引入一个私有的辅助构造函数：
```cpp
class ClassC {
private:
    int max;  
    int min;
    void init(int my_max) { max = my_max > 0 ? my_max : 10; }
public:
    ClassC(int my_max) { init(my_max); }
    ClassC(int my_max, int my_min) : min(my_min) { init(my_max); } // 直接在初始化列表中初始成员变量	
};
```





---

### 默认参数

#### 定义
- 默认参数是在函数声明中为参数指定的默认值，调用时未提供参数则使用默认值。
- 规则：默认参数必须<u>从右到左设置</u>。

#### 代码示例
```cpp
#include <iostream>
using namespace std;

// 函数声明
int harpo(int n, int m = 4, int j = 5);  // 合法
int chico(int n, int m = 6, int j);       // 非法：j 无默认值
int groucho(int k = 1, int m = 2, int n = 3);  // 合法

int main() {
    int beeps;
    beeps = harpo(2);      // harpo(2, 4, 5) -> 11
    cout << "beeps = " << beeps << endl;
    beeps = harpo(1, 8);   // harpo(1, 8, 5) -> 14
    cout << "beeps = " << beeps << endl;
    beeps = harpo(8, 7, 6);  // harpo(8, 7, 6) -> 21
    cout << "beeps = " << beeps << endl;
    return 0;
}

int harpo(int n, int m, int j) {
    return n + m + j;
}
```

#### 关键点
1. **规则**:
   - 默认参数从右到左设置。
   - `int harpo(int n, int m = 4, int j = 5)` 合法。
   - `int chico(int n, int m = 6, int j)` 非法。
2. **作用**:
   - 省略参数时自动填充默认值。
   - 减少函数重载需求。
3. **注意**:
   - 默认值在声明中指定，<u>不在定义中</u>。
   - 不能“跳跃”使用参数，如 `harpo(1, , 6)` 非法。

#### 改进建议
- 复杂逻辑可考虑函数重载或委托构造函数。
- 避免过度使用默认参数以保持代码清晰。

---

### 内联函数

> Inline Functions

#### 定义
- 内联函数是用 `inline` 关键字修饰的函数，编译器会尝试将函数调用替换为函数体代码，减少函数调用的开销。

  > 普通函数的调用需要经历: 压栈、传递参数、跳转到函数地址、返回值处理、出栈等步骤, 由此可见内联函数可以有效提升性能.
- 适用于小型、频繁调用的函数。

#### 代码示例
```cpp
#include <iostream>
using namespace std;

// 内联函数定义
inline int square(int x) {
    return x * x;
}

int main() {
    int num = 5;
    cout << "Square of " << num << " is " << square(num) << endl;  // 输出 25
    return 0;
}
```

#### 关键点
1. **作用**:
   - 减少函数调用开销（如参数传递、栈帧创建）。
   - 提高执行效率，适合小型函数。
2. **使用场景**:
   - 函数体短小、调用频繁。
   - 不适合复杂函数（可能导致代码膨胀）。
3. **注意**:
   - `inline` 是建议，编译器可能忽略（例如函数过大或包含循环）。
   - 内联函数定义通常放在头文件中，避免链接错误 (在多个源文件被调用的时候, `inline`的声明可以告诉编译器, 重复的定义是被允许的)





> [!NOTE]
>
> 1. **Any function you define inside a class declaration is automatically an inline.**
>
>    > `class`内部**<u>定义</u>**的函数自动为 `inline`类型. 如果是类外定义(相同的`.h`文件), 那么需要显式声明为内联函数.
>
> 2. 内联函数必须在**<u>头文件</u>**中定义，或者在调用它的同一翻译单元中.
>
>    > 如果定义在 .cpp 文件中，调用点无法看到定义，编译器无法内联，链接器会报错.
>
> 3. 如果决定将类的函数定义写在头文件中, 有以下的两种选择使其成为内联函数;
>
>    1. 直接在声明的地方给出完全的定义;
>    2. 在类外声明 `inline`然后定义.
>
> 4. `inline`确实比C语言的 `macro`更好, 因为内联函数实现了对参数的类型检查.
>
> 5. 编译器会对声明为 `inline` 的函数进行检查: 如果包含了递归 或者 代码量较大, 编译器依旧不会将其视为 `inline` .



### inline 变量

> [!NOTE]
>
> - 为静态成员变量声明 `inline`, 不必在 `.cpp`中再次声明.
>
> - 用于**在头文件中定义具有外部链接的变量**，避免了重复定义的问题.



在 C++ 传统规则中，**全局变量**（或者 namespace 作用域的变量）在头文件中定义会导致多个翻译单元（编译文件）出现**重复定义错误**。在 C++17 之前，通常的做法是：

```cpp
// myheader.h
extern int myVar; // 声明

// mysource.cpp
#include "myheader.h"
int myVar = 42; // 定义
```

但在 C++17 之后，可以使用 inline 变量，直接在头文件中定义，而不会导致重复定义错误：

```cpp
// myheader.h
inline int myVar = 42;  // C++17 及以上
```

在任何 `#include "myheader.h`" 的地方，myVar 仍然是<u>**同一个变量**</u>。

> 如果希望不同的文件不是一个实例, 需要声明为 静态全局变量.





**inline 变量的特点**

​	1.	**允许在头文件中定义**，避免 extern 的使用。

​	2.	**所有包含它的翻译单元共享同一个变量**（编译时不会创建多个实例）。

​	3.	**必须初始化**，否则编译器无法确定变量的值。





**示例：多个文件使用 inline 变量**



假设有以下两个源文件，同时包含 myheader.h，但不会引发重复定义错误：

**头文件 myheader.h**

```cpp
#ifndef MYHEADER_H
#define MYHEADER_H

#include <iostream>

inline int globalVar = 100; // inline 变量

#endif
```

**源文件 file1.cpp**

```cpp
#include "myheader.h"

void func1() {
    std::cout << "file1.cpp: " << globalVar << std::endl;
}
```

**源文件 file2.cpp**

```cpp
#include "myheader.h"

void func2() {
    std::cout << "file2.cpp: " << globalVar << std::endl;
}
```

**主程序 main.cpp**

```cpp
#include "myheader.h"

void func1();
void func2();

int main() {
    func1();
    func2();
    globalVar += 10;
    std::cout << "main.cpp: " << globalVar << std::endl;
    return 0;
}
```

**编译 & 运行**

```
g++ file1.cpp file2.cpp main.cpp -o output && ./output
```

**输出示例：**

```
file1.cpp: 100
file2.cpp: 100
main.cpp: 110
```

说明：

​	•	globalVar 是**同一个变量**，而不是多个副本。

​	•	main.cpp 修改 globalVar 之后，globalVar 在所有翻译单元中都被更新。



------



**inline 变量 vs constexpr 变量**

​	•	inline 变量可以是**可变的**，可以修改其值。

​	•	constexpr 变量必须是**编译时常量**，不能修改。

​	•	inline constexpr 变量既有 inline 特性，又是编译时常量。

```
inline constexpr int constantVar = 50; // 不能修改
```



### weak

`weak`允许不同的编译单元存在同名的函数, 并且在实际调用的时候, 优先调用不是 `weak`的函数. 从而提供了一种默认的实现.

`weak`关键字可以用于函数、变量与对象等, 与主要使用于函数的 `inline`不同.

如果没有 `weak`标记, 就是强变量.



- 一般的编译器需要使用 `__attribute__((weak)) ` 来声明:

```cpp
#include <iostream>

// 声明 weak 变量，提供默认值
__attribute__((weak)) int globalValue = 42;

int main() {
    std::cout << "globalValue = " << globalValue << std::endl;
    return 0;
}
```



# Composition

用已有的对象构造新的对象. 称为组合.

可以用 `has-a`的关系来描述.

## 类内对象的初始化



假设我们有一个 `Person` 类，该类内部包含一个 `std::vector<std::string>` 类型的成员变量，用于存储人的爱好。我们将展示如何在类的构造函数中使用完全初始化和引用初始化来初始化这个成员变量。

### 1. Fully

完全初始化是指在类的构造函数中直接为成员变量赋予初始值。这通常通过成员初始化列表（member initializer list）来完成。

```cpp
class Person {
public:
    std::vector<std::string> hobbies;

    // 完全初始化：使用成员初始化列表
    Person(const std::vector<std::string>& initialHobbies)
        : hobbies(initialHobbies) { }
};

```

**解释**

- **成员变量初始化**：在 `Person` 类的构造函数中，我们使用成员初始化列表 `: hobbies(initialHobbies)` 来完全初始化 `hobbies` 成员变量。这意味着 `person1.hobbies` 将拥有 `initialHobbies` 的一个拷贝。
- **独立性**：`person1.hobbies` 是 `initialHobbies` 的一个独立拷贝，修改 `person1.hobbies` 不会影响 `initialHobbies`，反之亦然。



### 2. Reference

引用初始化是指将类内部的成员变量声明为引用类型，并在构造函数中将其绑定到另一个对象。这意味着类内的引用将直接指向外部对象，而不是持有其拷贝。

```cpp
#include <iostream>
#include <vector>
#include <string>

class Person {
public:
    std::vector<std::string>& hobbiesRef;  // 引用类型的成员变量

    // 引用初始化：使用成员初始化列表绑定到外部对象
    Person(std::vector<std::string>& externalHobbies)
        : hobbiesRef(externalHobbies) { }
};

```

**解释**

- **成员变量声明**：`std::vector<std::string>& hobbiesRef;` 声明了一个引用类型的成员变量 `hobbiesRef`，它将引用外部的 `std::vector<std::string>` 对象。
- **引用绑定**：在构造函数中，通过 `: hobbiesRef(externalHobbies)` 将 `hobbiesRef` 绑定到传入的 `externalHobbies` 对象。这意味着 `person2.hobbiesRef` 和 `sharedHobbies` 指向同一个内存位置。
- **共享数据**：对 `person2.hobbiesRef` 的修改（如添加新爱好）会直接影响到 `sharedHobbies`，因为它们共享相同的数据。



### 3. 对比

| 特性             | 完全初始化                       | 引用初始化                                         |
| ---------------- | -------------------------------- | -------------------------------------------------- |
| **存储方式**     | 存储外部对象的拷贝               | 存储对外部对象的引用                               |
| **内存使用**     | 额外占用内存用于拷贝             | 不占用额外内存，直接引用外部对象                   |
| **数据独立性**   | 修改类内成员不会影响外部对象     | 修改类内成员会影响外部对象                         |
| **生命周期依赖** | 类内成员独立于外部对象的生命周期 | 类内引用的生命周期必须至少与外部对象相同           |
| **适用场景**     | 需要独立副本时使用               | 需要与外部对象共享数据时使用, 初始情况下不知道容量 |

#### 注意事项

- **引用必须在构造时初始化**：引用类型的成员变量必须在构造函数的初始化列表中绑定到一个对象，不能在构造函数体内赋值。

  > [!NOTE]
  >
  > 因为引用类型的对象必须在初始化的时候绑定, 并且只能绑定一次.

  ```cpp
  // 错误示例：试图在构造函数体内赋值给引用
  class Person {
  public:
      std::vector<std::string>& hobbiesRef;
      
      Person(std::vector<std::string>& externalHobbies) {
          hobbiesRef = externalHobbies; // 错误：引用必须在初始化时绑定
      }
  };
  ```

- **生命周期管理**：使用引用初始化时，必须确保被引用的对象在使用期间是有效的。如果引用的对象被销毁，引用将变为悬空引用，导致未定义行为。

  ```cpp
  // 危险示例：悬空引用
  std::vector<std::string> createHobbies() {
      std::vector<std::string> temp = {"Temporary"};
      Person person(temp);
      return temp; // temp 被销毁，person.hobbiesRef 悬空
  }
  ```

  为了避免这种情况，通常可以使用智能指针（如 `std::shared_ptr` 或 `std::unique_ptr`）来管理对象的生命周期，或者确保引用的对象在使用期间一直有效。



#### 总结

> [!NOTE]
>
> 1. 如果使用 完全初始化 的方式, 类内必须包含另一个类的完整定义(引用头文件);
>
> 2. 如果是引用初始化, 可以直接在这个类的内部预先声明另一个类的存在即可.
>
>    ```cpp
>    class A; // 前向声明
>    class B{
>      A* ptr;
>    }
>    ```





---

## Embedded objects

> [!NOTE]
>
> - 对于嵌入对象，如果不用初始化列表，就必须有默认构造函数。



## namespace

### 命名空间的别名

如果 `namespace`过长, 可以将其重新赋值并使用:

```cpp
namespace supercalifragilistic {
	void f();
}
namespace short = supercalifragilistic;
short::f();
```



### selection

除了直接在一个命名空间内部嵌套其他, 我们也可以在一个命名空间里选择性地使用其他空间的部分函数:

```cpp
namespace mine｛
	using orig::Cat;	 // use Cat class from origvoid ×O）；
	void x()；
    void y();
}
```



> [!NOTE]
>
> 1. **Multiple namespace declarations add to the same namespace.**
>    也即是说, 多个 `.h`文件内相同的命名空间会自动的视作一个.



# Inheritance

> [!NOTE]
>
> 1. 继承的对象都具有基类的属性, 但是不一定具有访问的权限. 
>    也就是 **Think of inherited traits as an embedded object**
> 2. 派生类的构造函数中的初始化列表应当包含基类的构造函数.
> 3. 由于基类的私有成员无法被子类直接访问, 需要让子类调用基类的public的函数, 从而间接访问.
> 4. 如果子类和父类含有同名的变量, 将会同时存在, 并且可以在子类中声明 `parent_class:A`来访问父类中的属性A.
> 5. 子类中一定有部分是父类的对象, 并且处于子类的开头(父类必须首先完成初始化).

#### 初始化列表的常用场景

1. 调用基类的构造函数 (否则调用默认构造函数)

   ```cpp
   class Base {
   public:
       Base(int data) { /* ... */ }
   };
   
   class Derived : public Base {
   public:
       Derived(int baseVal) : Base(baseVal) { }
   };
   ```

2. 初始化嵌入类

   ```cpp
   class Member {
   public:
       Member(const std::string& str) { /* ... */ }
   };
   
   class Container {
       Member memberObj;
   public:
       Container(const std::string& s) : memberObj(s) { }
   };
   ```

   > 需要注意的是, 此处的初始化列表中的嵌入类的构造, 变量名不是类名.
   >
   > 如果嵌入类不存在默认的构造函数, 那么初始化列表中的显式构造是必要的.

3. 初始化常量成员:

   ```cpp
   class MyClass {
       const int constMember;
   public:
       MyClass(int val) : constMember(val) { }
   };
   ```
   
   > const 成员一旦定义，必须立即初始化，且**只能**在初始化列表中完成，不能在构造函数体内赋值。


4. 初始化引用成员

   ```cpp
   class MyClass {
       int& refMember;
   public:
       MyClass(int& ref) : refMember(ref) { }
   };
   ```
   
   > 引用成员（如 int& ref）必须在初始化时绑定对象，**不能在构造函数体内赋值**，因此也必须使用初始化列表。



继承: 基于已有的类来设计新的类，新的类的对象可以被当作已有类的对象.

相比于 *composition*  的 `has-a`关系, 继承是 `is-a`的关系.

同义词:

- 父类、超类、基类
- 派生类、子类

控制基类成员在派生类中的可见性：

- `public`：基类的 `public` 成员在派生类中仍然是 `public`，`protected` 成员仍然是 `protected`。
- `protected`：基类的 `public` 和 `protected`成员在派生类中都变为 `protected`。
- `private`：基类的 `public` 和 `protected` 成员在派生类中都变为 `private`。

> [!NOTE]
>
> 此时, 仍旧可以通过子类的方法来访问基类的属性或者方法, 但是无法通过子类直接访问基类~!
>
> e.g.
>
> ```cpp
> #include <iostream>
> 
> class Base
> {
> public:
>     void baseFunction()
>     {
>         std::cout << "Base function called." << std::endl;
>     }
> };
> 
> class Derived : private Base
> {
> public:
>     void derivedFunction()
>     {
>         baseFunction(); // 在子类内部可以访问基类的成员函数
>     }
> };
> 
> int main()
> {
>     Derived d;
>     d.baseFunction();    // 错误：无法从子类外部访问基类的成员函数
>     d.derivedFunction(); // 可以调用子类的函数，该函数内部调用了基类的函数
>     return 0;
> }
> ```







clint class 表示这个类要使用另一个类(中的public).



子类的对象也可以认为是父类的对象。子类不能访问父类的私有变量，但私有变量存在于这个类中



当调用构造函数时，我们不能调用父类的私有变量，只能用初始化列表的方式调用父类的构造函数。



赋值的运算符不会被继承:

#### 赋值运算符

赋值运算符是这样形式的方法:

```cpp
Point &operator=(const Point &other)
{
    cout << "Point::operator= 被调用" << endl;
    if (this != &other)
    {
        x = other.x;
        y = other.y;
    }
    return *this;
}
```

当我们如此赋值的时候就会发生上述的调用:

```cpp
Point p1(1,2);
Point p2(3,4);

p1 = p2;
```

可见, 赋值运算符通过引用快速地将右值中的属性赋值给左侧的目标.

但是父类的赋值运算符只会确保自身的属性能够被赋值, 无法确保子类自身的属性可以被正确设置. 因此, 为了安全性, CPP不允许子类自动继承父类的赋值运算符.

> [!NOTE]
>
> 然而, 编译器可能为子类自动生成一个赋值运算符.
>
> e.g.
>
> ```cpp
> class Point
> {
> public:
>     int x, y;
>     Point(int x = 0, int y = 0) : x(x), y(y) {}
> 
>     // 自定义赋值运算符
>     Point &operator=(const Point &other)
>     {
>         cout << "Point::operator= 被调用" << endl;
>         if (this != &other)
>         {
>             x = other.x;
>             y = other.y;
>         }
>         return *this;
>     }
> };
> 
> class ColoredPoint : public Point
> {
> public:
>     string color;
>     ColoredPoint(int x = 0, int y = 0, string color = "white") : Point(x, y), color(color) {}
> 
>     void print() const
>     {
>         cout << "x:" << x << ", y:" << y << ", color:" << color << endl;
>     }
> };
> 
> int main()
> {
>     ColoredPoint cp1(1, 2, "red");
>     cp1.print();
>     ColoredPoint cp2(3, 4, "blue");
>     cp2.print();
>     cp1 = cp2;
>     cp1.print();
> }
> 
> ```
>
> **输出结果**:
>
> ```cpp
> x:1, y:2, color:red
> x:3, y:4, color:blue
> Point::operator= 被调用
> x:3, y:4, color:blue
> ```
>
> 可见, 父类的赋值运算符也被自动地调用了, 这是因为编译器为子类自动生成了一份.



---

父类的构造是在子类的构造之前。



### `using`声明

#### 将基类的函数派生使用

必须使用 `using <parentClass>:: func`的方式, 无法忽略 `using`.

```cpp
#include <iostream>
using namespace std;

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


int main() {
    Derived d;
    d.f(4);
    d.f(4.5);
}
```

**Output:**

```cpp
int 

double
```



#### 默认参数值无法通过重载传递

在cpp中, 默认参数值绑定在函数声明的作用域上, 而不是函数本身! 这是为了避免**多重继承时参数值产生冲突或二义性**。默认参数是静态绑定（编译期行为），它必须清楚地知道取哪个作用域的值.

> 如果你在派生类中**重新声明或重载了基类的函数**，那么你不能获得默认参数值，必须通过多个重载模拟默认参数功能.

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



> 但是使用 `using`声明的父类函数可以获得默认参数值.

e.g.

```cpp
#include <iostream>
using namespace std;
class A
{
public:
    void f(int a = 3, double b = 2.0)
    {
        std::cout << "A::f(" << a << ", " << b << ")" << std::endl;
    }
};

class B : public A
{
public:
    using A::f; // 继承 A::f 到 B 中
};

int main()
{
    B b;
    b.f();        // ✅ 是否等价于 f(3, 2.0)？
    b.f(10);      // ✅ 是否等价于 f(10, 2.0)？
    b.f(10, 5.5); // ✅ 正常调用
}

```

- 输出:

  ```cpp
  A::f(3, 2)
  A::f(10, 2)
  A::f(10, 5.5)
  ```



#### 解决重写函数的重载问题

子类重新定义 (`override`重写) 了父类的某个函数，就会把父类中对应 `overloaded` 的函数覆盖:

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

> - 注意, CPP中的浮点数可以隐式转换为整型.
>
>   - 当将一个浮点数赋值给一个整型变量时，编译器会执行**隐式转换**，将浮点数的值转换为整型。这种转换通常涉及**截断**（truncation），即去掉小数部分，只保留整数部分.
>
> - 可以通过 `using `声明重新引入基类中的重载函数: 
>
>   ```cpp
>   using Base::display;
>   ```
>



---

# Polymorphism

#### 补充

1. 成员函数（无论是虚函数还是非虚函数）本身不占用类对象的内存空间，因为它们的代码存储在程序的代码段中。只有**成员变量和虚表指针**（如果有虚函数）会占用对象的内存空间:

   ```cpp
   class A {
       int i;
       void f();
   };
   ```

   此时的 `sizeof(A)`为4字节.

2. 虚函数指针的大小一般是 <u>8字节</u>. 如果结构体内部存在虚函数指针, 就需要对齐为8字节的倍数.

3. 因为子类的构造在父类的构造之后, 也就是每一个子类对象的前部分都是父类对象. 因此, 我们可以将一个子类对象的指针赋值给父类对象, 只要忽视后续数据结构即可.

4. <u>多态变量</u>: 指向子类对象的基类指针/引用.

5. 如果类内不存在任何成员变量, 它的对象依旧占用 `1` 个字节的空间.

6. 如果一个类将来可能具有子类, 就让其析构函数设置为 `virtual`. —— 任何的类都应该设置它的析构为 `virtual`.

   ```cpp
   class B: public A{};
   
   A* p = new B();
   delete p;
   ```

   如果 A 的析构函数不是virtual的, 那么编译器只会调用A的析构函数.

7. 如果父类的构造函数中调用了 `virtual` 的函数, 那么实际上还是调用自己的函数(静态绑定)

   1. 这是因为, 创建一个派生类的对象时, 首先调用父类的构造函数, 然后执行派生类的构造函数;
   2. 此时, 子类的成员还没有初始化, 父类无法调用其重写版本, 也就无法实现多态.

8. > [!NOTE]
   >
   > 深刻理解什么叫做多态! 也就是子类对象的引用或者指针赋值给了父类! 此后调用这个对象时, 虚函数是动态绑定到子类的!
   >
   > ```c++
   > A &ra = b;    // ra是A类型的引用，但指向B类型的对象
   > ra.f();       // 虽然ra是A类型的引用，但f()会调用B::f()
   > ```

9. 如果B是A的子类:

   ```c++
   A *p1 = new B(3);    // p1是A类型的指针，但指向B类型的对象
   ```

   实际上,  p1指向的对象是B类的对象






当一个被重写的函数在 Derived::func() 中通过 Base::func() 调用其基类版本时，会发生什么？

- 可以正常调用, 这也正是重写存在的意义——扩充原来的功能, 同时保留.



- 多态与返回对象类型:

  - 允许重载返回自身的 <u>指针</u> 与 <u>引用</u>, 但是不支持直接返回自身的类型:

  - i.e. 

    ```cpp
    Class Expr｛
    public:
      virtual Expr* newExpr （）；
      virtual Expr& clone （）；
      virtual Expr Expr self（）；
    ｝；
    
    class BinaryExpr ： public Expr ｛
    public：
      virtual BinaryExpr* newExpr （）； //Ok
      virtual BinaryExpr& clone （）； // Ok
      virtual BinaryExpr self（）；// Error！
      ｝；
    ```

  - 原因: 指针和引用的大小固定, 编译时刻可以确定调用时候所需的空间, 如果按照类型返回, 由于多态的动态绑定是运行时决定调用对应的方法, 所以编译的时候caller不知道要在栈上预留多少的空间.





---

- Polymorphism
  - virtual functions and override
  - abstract functions and classes
- Multiple Inheritance



由于子类具有父类的所有属性, 因此我们可以将一个子类对象看作父类的对象, 并进行对应的操作.

将子类对象看作父类对象的操作, 叫做 <u>upcast</u>. 具体是说, 将子类的指针或者引用赋值给基类的对象.



现在的问题是, 如果采取传统的继承+重写的方式, 我们在子类内部采取重写父类方法, 可能导致 `静态绑定` static binding:

```cpp
#include <iostream>
using namespace std;

class Animal
{
public:
    void speak()
    {
        cout << "Animal speaks" << endl;
    }
};

class Dog : public Animal
{
public:
    void speak()
    {
        cout << "Dog barks" << endl;
    }
};

int main()
{
    Animal *animal = new Dog(); // 注意：父类指针指向子类对象
    animal->speak();            // 会调用哪个？
    delete animal;
    return 0;
}
```

- 输出:

  ```cpp
  Animal speaks
  ```



为了解决上述的问题, 我们引入 <u>虚函数</u> 的概念.

## 虚函数

虚函数可以实现 **运行时多态**. 所谓多态, 就是静态+ 动态的绑定.

通过在父类的函数前加上 `virtual`的声明, 我们将其定义为虚函数

e.g.

```cpp
#include <iostream>
using namespace std;

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

int main() {
    Animal* animal = new Dog(); 
    animal->speak(); // 这次会调用哪个？
    delete animal;
    return 0;
}
```

- 输出:

  ```cpp
  Dog barks
  ```

> - `override`声明明确这个函数是重写父类的虚函数, 可以让编译器检查是否存在错误.
> - 但是上述的关键字也不是必须的.



### 纯虚函数

~指的是需要强制派生类去实现的函数:

```cpp
virtual 返回类型 函数名(...) = 0;
```



### 虚函数表

- 定义: 虚函数表（virtual table）是 C++ 为了实现**运行时多态**而采用的一种底层技术手段;
- 本质:
  - `vtable`是一个函数指针数组;
  - 每个类都有自己的vtable
  - 对象中存在一个隐藏的指针 `vptr`, 指向该类的vtable

#### 内存示意图

假设有如下结构:

```cpp
class Base {
public:
    int a;
    virtual void func();
};
```

内存布局的伪结构如下:

```cpp
+-------------------------+
| vptr  → 指向vtable     |  ←隐藏成员
+-------------------------+
| a : int                |  ←显式成员
+-------------------------+

vtable (Base):
[ func 的地址 ]

vtable (Derived):
[ 重写的 func 的地址 ]
```



#### 拓展说明

- vtable是类级别的, 所有该类的对象共享一个vtable;
- vptr是对象级别的, 隐含于各个对象当中.
- 如果类没有虚函数, 就不存在上述的~



## 抽象类

如果一个类中包含至少一个纯虚函数, 那么这个类就是一个抽象类.

抽象类无法被实例化, **只能用来作为基类.**

### 使用抽象类定义接口

用图形绘制的例子来说明抽象类和纯虚函数的使用:

```cpp
#include <iostream>
using namespace std;

// 抽象类
class Shape {
public:
    // 纯虚函数，子类必须实现
    virtual void draw() = 0;
};

// 派生类：Circle
class Circle : public Shape {
public:
    void draw() override {
        cout << "Drawing Circle" << endl;
    }
};

// 派生类：Rectangle
class Rectangle : public Shape {
public:
    void draw() override {
        cout << "Drawing Rectangle" << endl;
    }
};

// 渲染函数：面向抽象类编程
void render(Shape* shape) {
    shape->draw();
}

int main() {
    Circle c;
    Rectangle r;

    render(&c); // Drawing Circle
    render(&r); // Drawing Rectangle

    return 0;
}
```



### 继承链

一个抽象类可以有多个纯虚函数, 此时继承的子类可能没有完全实现. 

子类的子类, 也就是在继承链上完成了所有纯虚函数的定义的 最末一级的派生类, 才可以被实例化.

```cpp
class Animal {
public:
    virtual void speak() = 0;
    virtual void run() = 0;
    
    void breathe() {
        cout << "Breathing..." << endl;
    }
};
```



## 虚析构函数 

**virtual destructor**: 虚析构函数

同样的, 虚析构函数的作用体现在 `upcast`, 也就是将子类的指针或引用转换为父类的指针或引用的过程. 此时, 如果父类的析构函数不是虚函数, 就会让向上转型之后的对象在析构时调用父类的析构函数.

```cpp
#include <iostream>
using namespace std;

class Base
{
public:
    ~Base()
    {
        cout << "Base Destructor" << endl;
    }
};

class Derived : public Base
{
public:
    ~Derived()
    {
        cout << "Derived Destructor" << endl;
    }
};

int main()
{
    Base *obj = new Derived();
    delete obj; // 注意这里！
    return 0;
}
```

此时会输出 :  `Base Destructor` , 也就是调用了父类的析构函数.

这是十分危险的, 特别是我们需要确保子类的所有资源都被安全地释放.

因此, 我们可以将父类的析构函数也设置为虚函数:

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

此时的输出:

```cpp
Derived Destructor  
Base Destructor
```

> 先后调用子类和父类的析构函数.



## 接口类

> 也被称为 <u>协议类</u>. 其实就像 `swift`的协议一样, 强制要求继承的子类定义某些函数实现.

- 含义:
  - 只定义接口, 不提供实现的抽象类
  - 所有的成员函数都是纯虚函数;
  - 一般不包含任何数据成员.

e.g.

```cpp
class Printable {
public:
    virtual void print() = 0;
    virtual ~Printable() = default; // 记得虚析构函数
};

class Document : public Printable {
public:
    void print() override {
        cout << "Printing Document" << endl;
    }
};
```



## 多重继承

多重继承 *multiple inheritance* 指的是一个类继承自多个基类.

e.g.

```cpp
class A {
public:
    void sayA() { cout << "I am A" << endl; }
};

class B {
public:
    void sayB() { cout << "I am B" << endl; }
};

class C : public A, public B {
    // 继承了 A 和 B 的成员
};
```

可以将多个类的功能整合到一个类中:

```cpp
C c;
c.sayA(); // OK
c.sayB(); // OK
```



### 菱形继承

多重继承中的特例:

```cpp
class A {
public:
    int value;
};

class B : public A {};
class C : public A {};
class D : public B, public C {};
```

继承结构看上去就像菱形:

```cpp
     A
   /   \
  B     C
   \   /
     D
```

如果我们直接使用D类:

```cpp
D d;
d.value = 10; // ❌ 编译错误：'value' is ambiguous
```

这是因为D继承自B和C, 而后者又各自具有自己的A子对象. 导致了二义性的问题.

> [!NOTE]
>
> 注意此处的二义性的问题, 来自于在D类对象中直接访问A类对象的成员变量!  什么时候产生二义性? 就是判断此时是否存在多个相同名称的变量.



### 虚继承

C++ 提供了一个机制：**虚继承（virtual inheritance）**，来解决上述问题:

```cpp
class B : virtual public A{};
class C : virtual public B{};
class D :  public B, public C{}
```

此时, D中只有一个共享的A子对象.

```cpp
#include <iostream>
using namespace std;

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

int main()
{
    D d;
    d.value = 10; // ✅ OK，只有一个 A，二义性消除
    d.ptr();
}
```

此时, B,C不再具有自己的A.



> [!NOTE]
>
> 由于虚继承带来的是“共享”的基类对象，所以：
>
> - 虚基类的构造 **必须由最底层派生类负责**
> - 派生类的构造函数中要**显式初始化**虚基类



# Copy and Move

#### 补充

- `vptr`只会初始化一次, 然后保持不变. 发生在构造函数的第一步.
- 循环内部执行构造和析构时, 作用的地址是同一块. 此时将对象的地址传入到vector of `*` 时, 得到的是相同的地址;
  - 为了避免上述的情况, 我们在循环内部的构造, 需要为对象直接 `new` 一个新的空间.

- `std::move`的作用就是告诉编译器: 这个类型是右值引用, 需要使用移动语义.





## C++中的拷贝构造与移动语义

### 拷贝构造函数基础

拷贝构造函数是创建新对象的一种方式，它从现有对象复制数据来初始化新对象。

- 拷贝构造函数的签名：`T::T(const T&)`
- 如果不提供拷贝构造函数，C++会自动生成一个默认的拷贝构造函数
- 默认拷贝构造函数执行的是成员级别的拷贝（memberwise copy），而非位级别的拷贝（bitwise copy）
  - 对于基本类型成员，直接复制值
  - 对于对象类型成员，调用其拷贝构造函数
  - **<u>对于指针类型成员</u>**，只复制指针值（浅拷贝），导致两个对象共享同一块内存

### 拷贝构造函数的调用时机

拷贝构造函数在以下情况下会被调用：

1. **按值传递参数时**：当对象作为参数按值传递给函数时
   
   ```cpp
   void func(MyClass obj); // 调用时会触发拷贝构造
   ```
   
2. **对象初始化时**：
   
   ```cpp
   MyClass a;
   MyClass b = a;    // 初始化，调用拷贝构造函数
   MyClass c(a);     // 初始化，调用拷贝构造函数
   ```
   
3. **函数返回对象时**：
   
   ```cpp
   MyClass func() {
       MyClass obj;
       return obj;   // 可能触发拷贝构造（取决于编译器优化）
   }
   ```



### 拷贝构造函数的最佳实践

- 显式定义拷贝构造函数，不要依赖默认版本
- 如果类不需要被复制，可以声明私有的拷贝构造函数（不需要定义）
- 如果类包含指针成员，必须实现<u>深拷贝</u>的拷贝构造函数

#### 深拷贝构造函数

将拷贝的值另外寻找新的内存空间, 而不是指向同一处地址.

- 如果没有显式定义深拷贝构造函数, 观察下面的代码:

```cpp
StringHolder original("Hello World");

{
    StringHolder copy = original; // 调用拷贝构造函数
    std::cout << "copy包含: " << copy.getString() << std::endl;

    // 修改copy，如果是浅拷贝，也会影响original
    copy.setString("Modified");
    std::cout << "修改后，copy包含: " << copy.getString() << std::endl;
    std::cout << "修改后，original包含: " << original.getString() << std::endl;

    // copy在此作用域结束时被销毁
}
```

> - `original`的层级在  `copy` 的外面;
>
> - 在代码块的内部, 通过浅拷贝构造, 两者的数据共享一个内存空间;
>
> - 二者先后析构, 导致 `double free`的问题!
>
>   ```bash
>   02_deep_vs_shallow_copy(5896,0x1eddb0840) malloc: Double free of object 0x11f6060c0
>   02_deep_vs_shallow_copy(5896,0x1eddb0840) malloc: *** set a breakpoint in malloc_error_break to debug
>   ```



由此, 我们需要如此定义:

```cpp
class StringHolder
{
private:
    char *data;

public:
    // 构造函数
    StringHolder(const char *str)
    {
        if (str)
        {
            data = new char[strlen(str) + 1];
            strcpy(data, str);
            std::cout << "构造函数: 为\"" << data << "\"分配内存" << std::endl;
        }
        else
        {
            data = nullptr;
            std::cout << "构造函数: 创建空字符串" << std::endl;
        }
    }

    // 自定义拷贝构造函数（深拷贝）
    // 如果复制掉这部分的自定义, 将会调用默认的浅拷贝构造函数, 导致问题:浅拷贝的指针两次释放内存
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

    // 析构函数
    ~StringHolder()
    {
        std::cout << "析构函数: 释放\"" << (data ? data : "nullptr") << "\"的内存" << std::endl;
        delete[] data;
    }
};

```

这样就可以避免上面的问题.





### 函数参数和返回值的选择

- **传入参数**：
  - 按值传递：`void func(Student s)` - 创建新对象，适用于需要存储对象的情况
    - 如果对象内部的属性存在指针类型的属性, 为了避免浅拷贝, 就需要自定义一个深拷贝构造函数.
  - 常量引用：`void func(const Student& s)` - 不创建新对象，适用于<u>只读取值</u>的情况
  - 指针/引用：`void func(Student* s)` 或 `void func(Student& s)` - 适用于需要<u>修改对象</u>的情况
  
- **返回值**：
  - 按值返回：`Student func()` - 返回新创建的对象
  - 返回指针：`Student* func()` - 注意内存管理问题
  - 返回引用：`Student& func()` - 注意生命周期问题

## 右值引用与移动语义

#### 左值与右值
- **左值**：可以出现在赋值号左边的表达式
  - 变量名、引用
  - 解引用操作符（*）和下标操作符（[]）的结果
- **右值**：只能出现在赋值号右边的表达式
  - 字面量
  - 表达式结果
  - 函数返回的临时对象

#### 右值引用
- 使用 `&&` 声明
- 可以绑定到右值，延长其生命周期
- 右值引用变量本身是左值
- 可以使用 `std::move()` 将左值转换为右值引用

```cpp
int x = 10;
int&& rx = x * 2;  // 绑定右值
rx = 100;          // rx本身是左值，可以被赋值
```

- 两种可以同时输入左值和右值引用作为参数的方法:

  - 重载

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
- 通常将源对象的指针成员置为`nullptr`，防止资源被错误释放

```cpp
MyClass(MyClass&& other) : 
    ptr{other.ptr} {
      other.data = 0
      other.ptr = nullptr;  // 防止源对象析构时释放内存
}
```

- 与浅拷贝类似, 但是需要在内部将源对象的指针成员 设置为 `nullptr` , 其他成员设置为有效但是为空的状态



 标准库容器（如 std::vector）在进行元素移动时会优先选择不会抛异常的移动构造函数，否则会退而求其次选择拷贝构造函数（更慢）。

e.g.

```cpp
std::vector<DynamicArray> vec;
vec.push_back(DynamicArray(5));
```

> 在这个 push_back 中，如果 DynamicArray 的移动构造函数被标记为 `noexcept`，那么 vector 会使用移动构造（高效）。





## 初始化方式

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



### 深拷贝与浅拷贝

- **浅拷贝**：默认拷贝构造函数执行的是浅拷贝，只复制指针值，不复制指针指向的内容
- **深拷贝**：自定义拷贝构造函数可以实现深拷贝，为指针成员分配新内存并复制内容

### 何时使用移动语义

- 类包含动态分配的资源（如指针成员）
- 对象需要在函数间传递
- 需要避免不必要的深拷贝操作
- 使用容器时（如`std::vector`、`std::string`等）
  - `move`会将一个左值变成右值引用, 从而允许调用移动构造函数!
  - 使用 `move` 时, 如果存在对应的移动构造函数, 就会优先调用;
  - 数组的 `push_back`也是如此, 优先调用移动构造函数
    - 但是我们可以使用 `emplace`来继续优化上述的效率问题——直接将对象存储到数组的末端.




### 总结

- 拷贝构造函数用于创建对象的副本，深拷贝需要自定义实现
- 移动语义通过右值引用实现**资源的高效转移**，避免不必要的拷贝, 同时**规避了浅拷贝导致的 double free** 的问题.
- 根据需要选择合适的参数传递和返回值方式
- 使用`std::move()`可以将左值转换为右值引用，触发移动语义



---



# Overloaded Operators

#### 补充

自定义类型的方向转换:  *T* ==> *C*

- 当下面情况存在一种时, 可以发生上述的转换:

  1. C存在以 *T* 作为输入参数的构造函数;

  2. *T* 存在 `operator C(){ }`的成员函数.
     e.g.

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

- 不同同时存在两者的转换(编译器无法知道采取什么方式)



题目:

- 并非所有运算符都可以用三种方式重载。例如赋值运算符 =、下标运算符 []、函数调用运算符 ()、成员访问运算符 -> **只能**作为成员函数重载;

- 流提取符 >> 和插入符 << 可以被重载

- 运算符 `+` 返回的类型 **不一定** 要与参数类型一致;

- 对于友元函数, 在声明处加上 `friend`标识, 但是在定义处是没有这个标识的. 

- > [!NOTE]
  >
  > 无法被重载的运算符:
  >
  > - 条件运算符: ` ?: `
  > - 成员指针访问运算符: `.*` 

  `new`是可以重载的!

- 运算符的分类:

  - 成员运算符: 在类内部以成员函数形式重载的运算符 ==> 具有隐式的 `this`;

  - 友元运算符: 定义在类外部（在类内部用 `friend` 声明），没有 `this` 指针.

    因此, 一个重载函数只有一个参数时 ==> 二员成员运算符 / 一元友元运算符.







- 隐式转换的尝试是从左到右的:
  - `1+a`会尝试将类的对象 `a` 尝试转换为int类型.

- 注意类型:

  ```cpp
  // 前缀++
  const Integer& Integer::operator++() {
      *this += 1;  // 先增加
      return *this;  // 再返回
  }
  
  // 后缀++（参数int未使用，仅用于区分）
  const Integer Integer::operator++(int) {
      Integer old(*this);  // 先保存
      ++(*this);  // 再增加
      return old;  // 返回旧值
  }
  ```

  前缀++的返回是 `&` 类型, 因为比较节省空间和时间. 后缀++的返回是值.

  - `[]` 的返回必须是 `&` 类型, 这样可以作为左值赋值.



返回类型设计的**总体原则**：

1. 如果运算符需要作为左值（可以出现在赋值左侧），返回非常量引用。
   1. 否则, 声明为`const`类型, 避免成为左值.

2. 如果运算符创建新对象，返回值（通常是常量值）。
3. 如果运算符返回布尔结果，直接返回 `bool` 类型。
4. 如果需要支持链式操作，返回引用。



全局和成员函数的设计思路:

1. 一元运算符（如 `-a`）应该用**成员函数**，因为只操作一个对象
2. 赋值相关运算符（`=`, `+=`, `[]`, `->()`与 `->*` 等）必须是成员函数，因为它们需要修改对象状态
3. 对于二元运算符（如 `+`, `-`, `*`, `/`)建议使用**全局函数**，因为：
   - 支持操作数的对称转换
   - 更好地支持与其他类型的互操作
   - 保持了运算符的自然语义（如 `3 * x` 和 `x * 3` 应该都能工作）



## C++运算符重载基础

运算符重载是C++的一项强大特性，它允许用户定义的类型像内置类型一样使用各种运算符。本质上，运算符重载是函数调用的另一种形式。

### 可重载与不可重载的运算符

C++允许重载大多数运算符，但以下**运算符不能被重载**：

- `.` (成员访问运算符)
- `.*` (成员指针访问运算符)
- `::` (作用域解析运算符)
- `?:` (条件运算符)
- `sizeof` (获取类型大小)
- `typeid` (获取类型信息)
- 类型转换运算符 (`static_cast`, `dynamic_cast`, `const_cast`, `reinterpret_cast`)

### 运算符重载的限制

1. **只能重载已存在的运算符**
   - 不能创建新的运算符（如Python中的`**`）
   - 可以改变运算符的语义（如重载`+`实现减法），但不推荐

2. **运算符必须在类或枚举类型上重载**
   - 至少有一个操作数必须是用户定义类型

3. **必须保持操作数数量**
   - 如二元运算符`/`重载后仍必须是二元的

4. **必须保持优先级和结合律**
   - 运算符的优先级和结合律是固定的，不能被改变

## 运算符重载的实现方式

运算符重载本质上是一个以`operator`关键字为前缀，后跟运算符的特殊函数。

### 成员函数方式

作为类的成员函数实现运算符重载时：

- 第一个操作数（左操作数）隐式为`this`指针
- 不对接收者（左操作数）执行类型转换

```cpp
class A {
public:
    A(int ii) : i(ii) {}
    int get() { return i; }
    
    // 重载+运算符，返回新对象
    const A operator+(const A& that) const {
        A c(this->i + that.i);
        return c;
    }
private:
    int i;
};
```

使用成员函数重载时，左操作数必须是该类的对象：

- `a + b` 可行（a是A类对象）
- `a + 9` 可行（9会被隐式转换为A类对象）
- `9 + a` 不可行（9不是A类对象）





> [!NOTE]
>
> 隐式转换: 编译器查找是否存在从给出参数作为构造函数的输入, 从而创建一个指定对象的方式:
>
> e.g.
>
> ```cpp
> Integer h = a + 7; // 7被隐式转换为Integer
> ```
>
> 上述发生的前提条件是 类 `Integer` 存在对应的构造函数:
>
> ```cpp
> Integer(int val = 0) : value(val)
> ```







### 全局函数方式

作为全局函数实现运算符重载时：

- 所有操作数都是显式参数
- 开发者不需要特殊访问类的权限
- 可能需要声明为友元函数以访问私有成员

```cpp
class Integer{
private:
  int value;

  friend const Integer operator*(const Integer &left, const Integer& right);
};

// 可以直接在全局函数中访问私有成员
const Integer operator*(const Integer &left, const Integer &right)
{
    std::cout << "调用全局函数*运算符: " << left.value << " * " << right.value << std::endl;
    return Integer(left.value * right.value);
}
```



当然, 全局函数实现的运算符重载, 可以通过调用类的public中的函数接口, 来间接访问内部的私有成员变量:

```cpp
class Integer{
  public: 
  int getValue(){
    return value;
  }
};

// 通过public接口访问内部的私有成员变量.
const Integer operator/(const Integer &left, const Integer &right)
{
    std::cout << "调用全局函数/运算符: " << left.getValue() << " / " << right.getValue() << std::endl;
    if (right.getValue() == 0)
    {
        std::cerr << "错误: 除数不能为零" << std::endl;
        return Integer(0);
    }
    return Integer(left.getValue() / right.getValue());
}
```



使用全局函数重载时，可以处理左操作数不是该类对象的情况：

- `9 - b` 可行（9会被隐式转换为A类对象）
- 因为两个操作数都是普通参数，地位平等, 体现了运算符的对称性





### 成员函数vs全局函数的选择

- **一元运算符**应该作为成员函数
- **赋值运算符**（`=`, `()`, `[]`, `->`, `->`）必须是成员函数
- 其他**二元运算符**最好作为非成员函数（全局函数）

## 参数传递与返回类型

### 参数传递

1. 对于只读参数，使用`const`引用传递（除了内置类型）
2. 对于不修改对象的成员函数，声明为`const`
3. 对于全局函数，如果左操作数会被修改，使用引用传递

### 返回类型

根据运算符的预期含义选择返回类型：

1. **算术运算符**（`+`, `-`, `*`, `/`, `%`, `^`, `&`, `|`, `~`）

   ```cpp
   const T operator X(const T& l, const T& r);
   ```

   - 返回新对象，不应返回引用（除非返回成员引用）
   - 返回`const`对象防止`(a+b) = c`这样的操作

2. **逻辑运算符**（`!`, `&&`, `||`, `<`, `<=`, `==`, `>=`, `>`）

   ```cpp
   bool operator X(const T& l, const T& r);
   ```

   - 返回布尔值

3. **下标运算符**（`[]`）

   ```cpp
   E& T::operator[](int index);
   ```

   - 返回左值（非`const`引用），允许`a[i] = value`操作
   - 不能返回新对象，否则赋值操作无效

## 特殊运算符重载

### 自增自减运算符

C++区分前缀和后缀自增自减运算符：

```cpp
// 前缀++
const Integer& Integer::operator++() {
    *this += 1;  // 先增加
    return *this;  // 再返回
}

// 后缀++（参数int未使用，仅用于区分）
const Integer Integer::operator++(int) {
    Integer old(*this);  // 先保存
    ++(*this);  // 再增加
    return old;  // 返回旧值
}
```

调用方式：

- `++x` 调用 `x.operator++()`

  - 返回的是引用, 从而支持链式操作 如:

    ```cpp
    Counter g = ++(++f); // 可以，因为前缀返回引用
    ```

- `x++` 调用 `x.operator++(0)`

  - 返回的是临时的对象, 也就是旧值的副本. 声明`const`避免了后缀的链式调用

    ```cpp
    Counter h = (f++)++; // 不可以，因为后缀返回const值
    ```

    之所以要防止上述的后缀链式调用, 是因为 对`f++`继续自增将会导致语义的混乱.






### 下标运算符

e.g.

```cpp
// 下标运算符（返回左值引用，允许修改）
int& operator[](int index) {
    if (index < 0 || index >= size) {
        std::cerr << "错误: 下标越界 [" << index << "]" << std::endl;
        // 返回第一个元素作为应急措施（实际应用中应抛出异常）
        return data[0];
    }
    return data[index];
}

// 下标运算符的const版本（返回值，不允许修改）
int operator[](int index) const {
    if (index < 0 || index >= size) {
        std::cerr << "错误: 下标越界 [" << index << "]" << std::endl;
        // 返回0作为应急措施（实际应用中应抛出异常）
        return 0;
    }
    return data[index];
}
```





### 关系运算符

关系运算符通常成对实现，可以相互利用：

```cpp
bool Integer::operator==(const Integer& rhs) const {
    return i == rhs.i;
}

bool Integer::operator!=(const Integer& rhs) const {
    return !(*this == rhs);  // 利用==运算符
}

bool Integer::operator<(const Integer& rhs) const {
    return i < rhs.i;
}

bool Integer::operator>(const Integer& rhs) const {
    return rhs < *this;  // 利用<运算符
}

bool Integer::operator<=(const Integer& rhs) const {
    return !(rhs < *this);  // 利用<运算符
}

bool Integer::operator>=(const Integer& rhs) const {
    return !(*this < rhs);  // 利用<运算符
}
```

### 流运算符

输入输出流运算符通常实现为全局函数：

```cpp
// 输出流运算符
ostream& operator<<(ostream& os, const A& a) {
    os << a.get();
    return os;  // 返回流对象以支持链式操作
}

// 输入流运算符
istream& operator>>(istream& is, A& a) {
    string line;
    cin >> line;
    // 读取a的数据
    return is;  // 返回流对象以支持链式操作
}
```

注意：

- 输出流运算符的第一个参数不能是`const`，因为输出会修改流
- 输入流运算符的第二个参数不能是`const`，因为需要修改对象
- 通常需要声明为友元以访问私有成员

### 自定义流操纵符

可以定义自己的流操纵符：

```cpp
ostream& tab(ostream& out) {
    return out << '\t';
}

// 使用：cout << "Hello" << tab << "World!" << endl;
```





## 赋值运算符与类型转换

### 赋值运算符

赋值运算符有几个重要特点：

- 必须是成员函数
- 如果不提供，编译器会自动生成（行为类似默认拷贝构造函数）
- 需要检查自赋值情况
- 确保为所有数据成员赋值
- 返回`*this`的引用

```cpp
A& A::operator=(const A& rhs) {
    if (this != &rhs) {  // 检查自赋值
        // 释放当前资源
        delete[] data;
        
        // 分配新资源
        data = new int[rhs.size];
        size = rhs.size;
        
        // 复制数据
        for (int i = 0; i < size; i++) {
            data[i] = rhs.data[i];
        }
    }
    return *this;  // 返回对象引用
}
```

### 类型转换



#### 隐式转换

C++支持两种用户定义的类型转换：

1. **构造函数转换**：从其他类型到当前类型

   ```cpp
   class PathName {
       string name;
   public:
       PathName(const string& s) : name(s) {}  // string到PathName的转换
   };
   
   string abc("abc");
   PathName xyz = abc;  // 隐式转换：abc => PathName
   ```

   可以使用`explicit`关键字禁止隐式转换：

   ```cpp
   explicit PathName(const string& s);  // 只能用于显式构造
   ```

2. **转换运算符**：从当前类型到其他类型

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

   同样可以使用`explicit`关键字要求显式转换：

   ```cpp
   explicit operator double() const;
   double d = (double)r;  // 必须显式转换
   ```

注意：如果同时提供了从T到C的构造函数和从C到T的转换运算符，编译器可能会产生歧义。



#### 显式转换

显式转换的外在特点:

- 使用直接初始化语法
- 明确指出要用构造函数创建对象
- 转换过程更加清晰可见

e.g.

```cpp
// 构造函数转换：double到Rational
Rational r1 = 3.14;  // 隐式转换
Rational r2(2.5);    // 显式转换
```



如果声明 `explict`, 就必须使用强制的显式转换:

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



类型的显式转换:

```cpp
double d2 = static_cast<double>(r2);  // 显式转换
```



#### 混合类型的转换

根据运算结果, 自动地将运算的操作数进行类型转换:

```cpp
// 混合类型运算
Rational r3(1, 2);
double d3 = 0.5;

// 这里会将r3转换为double，然后进行double加法
double result = r3 + d3;
```



# Template

## 模板概述

模板是C++中实现**<u>泛型编程</u>**的核心机制，允许我们编写可以处理不同数据类型的代码，而不必为每种类型重复编写相同的逻辑。

- 首先尝试匹配普通的函数, 否则尝试匹配模板函数



- 函数模板是声明, 而非定义. 是在实际调用时候去 **<u>实例化</u>** 对应类型的函数.

- 必须都放在头文件的实现包含:

  - 函数模板;

  - inline函数

  - 带有default参数的声明.

  - 类模板的成员函数

    ```cpp
    template<T>
    int Vector<T>::getSize()const{
      return size;
    }
    ```

    注意, 都需要声明模板类, 并且需要在 `< >`内部声明类.



- `template<class T>` 和 `template<typename T>` 是等价的, 后者是后来引入的更明确的语法.
- 类模板的每个成员函数在类外定义时，都必须以`template<class T>`（或`template<typename T>`）开头，即使该成员函数没有直接使用模板参数T.

- `Vector< int (*)>`

## 函数模板

### 基本语法

```cpp
template<class T>
void swap(T& x, T& y) {
    T temp = x;
    x = y;
    y = temp;
}
```

- `template` 关键字引入模板声明
- `class T` 指定参数化类型名称（`class`在这里表示任何内置类型或用户定义类型）
- 在模板内部，`T` 被用作类型名称

### 模板实例化

```cpp
int i = 3, j = 4;
swap(i, j);  // 使用int类型的swap

float k = 4.5, m = 3.7;
swap(k, m);  // 实例化float类型的swap

std::string s("Hello"), t("World");
swap(s, t);  // 实例化std::string类型的swap
```

编译器会根据传入的参数类型自动生成相应的函数实例。

### 模板匹配规则

- 只使用类型的精确匹配
- 不应用类型转换操作
- 即使是隐式转换也会被忽略

```cpp
swap(int, int);     // 正确
swap(double, double); // 正确
swap(int, double);  // 错误！类型不匹配
```

### 模板函数与普通函数共存

如果同时存在模板函数和普通函数，编译器会优先选择**<u>普通函数</u>**.



#### 显式指定

可以显式地指定模板参数类型:

```cpp
// 带有返回值的函数模板
template <class T>
T myMax(T a, T b)
{
    cout << "调用模板版本的myMax" << endl;
    return (a > b) ? a : b;
}

int main(){
  cout << "myMax<double>(10, 20.5) = " << myMax<double>(10, 20.5) << endl; 
}

// myMax(10, 20.5);  // 错误：参数类型不一致
```



#### 多参数的函数模板

同理的, 我们可以设置多个参数类型, 根据输入的类型来推断:

```cpp
template <class T1, class T2>
T1 myMax(T1 a, T2 b)
{
    cout << "调用模板版本的myMax2" << endl;
    return (a > b) ? a : b;
}

int main(){
  cout <<  myMax(10, 20.5) << endl; 
}
```

 此时, 上述的函数不需要显式的声明参数类型, 因为存在匹配的模板函数.

同时, 此时根据类型推断, 返回的类型是 `int`, 然后舍弃 20.5的小数部分, 最终的结果是 `20`.



## 类模板

### 基本语法

此处的 `Vector`是自己定义的.

```cpp
template<class T>
class Vector {
public:
    Vector(int);
    ~Vector();
    Vector(const Vector&);
    Vector& operator=(const Vector&);
    T& operator[](int);
private:
    T* m_elements;
    int m_size;
};
```

### 类模板的使用

类模板必须显式指定类型参数：

```cpp
Vector<int> v1(100);
Vector<Complex> v2(256);
v1[20] = 10;
v2[20] = v1[20];  // 如果定义了int到Complex的转换，则正确
```

### 类模板成员函数的定义

所有成员函数定义都需要包含模板声明：

```cpp
template <class T>
Vector<T>::Vector(int size) : m_size(size) {
    m_elements = new T[m_size];
}

template <class T>
T& Vector<T>::operator[](int indx) {
    if (indx < m_size && indx >= 0) {
        return m_elements[indx];
    } else {
        // 错误处理
    }
}
```

注意：

- 每个成员函数定义前都要加上 `template <class T>`
- 类名必须写为 `Vector<T>`
- 类模板的函数通常在头文件中实现，不需要分离的.cpp文件

## 多参数模板

模板可以使用多个类型参数：

```cpp
template<class Key, class Value>
class HashTable {
    const Value& lookup(const Key&) const;
    void install(const Key&, const Value&);
    // ...
};
```

## 嵌套模板

模板可以嵌套使用，因为它们只是新的类型：

```cpp
Vector<Vector<double>> matrix;  // 注意C++11之前需要空格：Vector<Vector<double> >
Vector<int (*)(Vector<double>&, int)> functionPointers;  // 函数指针的向量
```



#### 非类型模板参数

~也就是没有使用模板类型的普通参数.

```cpp
template<class Key, class Value, int TableSize = 10>
class HashTable {
    // ...
};
```

其中的 `TableSize` 就是普通的参数. 在编译时就确定了类型.





## 模板的局限性与注意事项

1. 模板代码通常放在头文件中，因为编译器需要在编译时看到完整的模板定义
2. 模板错误通常在实例化时才会被发现，错误信息可能很复杂
3. 模板可能导致代码膨胀，因为每种类型都会生成一份代码
4. 模板参数必须支持模板中使用的所有操作





# 其他

- 使用指针作为参数, 而不是直接将结构体本身作为参数传递给函数, 可以避免对结构体的复制. 从而更加高效.
  - 另外, 如果希望修改结构体本身的数据, 必须传递指向它本身的指针.



## 访问控制

### 继承关系中的访问控制

控制基类成员在派生类中的可见性：

- `public`：基类的 `public` 成员在派生类中仍然是 `public`，`protected` 成员仍然是 `protected`。
- `protected`：基类的 `public` 和 `protected`成员在派生类中都变为 `protected`。
- `private`：基类的 `public` 和 `protected` 成员在派生类中都变为 `private`。

## 作用域与生存期

本地: 均为本地

全局: 均为全局

静态本地: 作用域是本地, 生存期是全局

静态全局: ~



## Includes

### Algorithm
`copy(first, last, result)`:
- `fisrt`和`last`是输入迭代器, 表示要复制的范围, 左闭右开即`last`应当指向要复制元素的下一个位置. 必须支持读取操作和递增操作;
- `result`是输出迭代器, 指向复制目标范围的起始位置, 必须支持写入操作和递增操作
  - e.g.
    ```cpp
    std::vector<int> source = {1, 2, 3, 4, 5};
    std::vector<int> destination(5); // 确保目标容器有足够的空间
    
    std::copy(source.begin(), source.end(), destination.begin());
    
    for (int num : destination) {
        std::cout << num << " "; // 输出：1 2 3 4 5
    }
    std::cout << std::endl;
    ```
- `result`可以直接输出到`cout`中.
    ```cpp
    vector<int> vec;
    
    for(int i = 0; i < 5; i++){
        vec.push_back(i);
    }
    
    vec.erase(vec.begin()+2); //删除第三个元素
    copy(vec.begin(), vec.end(), ostream_iterator<int>(cout, ","));
    cout << endl;
    // 0,1,3,4,
    ```

---

- 数组之间**不可以**直接赋值, 但是字符串可以直接赋值
```cpp
char str1[] = "Hello";
char str2[] = "World";
str1 = str2;  // 错误，数组之间不可以直接赋值

string s1 = "Hello";
string s2 = "World";
s1 = s2;  // 正确，字符串可以直接赋值

```


## 深拷贝
```cpp
string s1 = "Hello";
string s2 = "World";

cout << "初始状态：" << endl;
cout << "s1: " << s1 << endl;  // 输出：Hello
cout << "s2: " << s2 << endl;  // 输出：World

s1 = s2;  // 赋值操作

cout << "赋值后：" << endl;
cout << "s1: " << s1 << endl;  // 输出：World
cout << "s2: " << s2 << endl;  // 输出：World

// 修改 s2 不会影响 s1，因为是深拷贝
s2 = "Changed";
cout << "修改 s2 后：" << endl;
cout << "s1: " << s1 << endl;  // 输出：World
cout << "s2: " << s2 << endl;  // 输出：Changed
```

## Temp
```cpp
// 迭代器
I.begin();
I.end();

// Item Access
V.front();;
V.back();
```

## 区分
### find
`find`是字符串类的一种方法, 同时也是标准库`algorithm`中的一个函数.
- `find`方法: 用于在字符串中查找子字符串的位置。
```cpp
string str = "Hello World";
size_t pos = str.find("World");
if (pos != string::npos) {
    cout << "Found 'World' at position " << pos << endl;
}
```
- `algorithm`中的`find`函数: 用于在容器（如数组、向量等）中查找元素。
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;
int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    vector<int>::iterator it = find(vec.begin(), vec.end(), 3);
    if (it != vec.end()) {
        cout << "Found 3 at position " << distance(vec.begin(), it) << endl;
    }
}
// Found 3 at position 2
```
> 编译: `g++ -std=c++11 test.cpp -o test`

### erase
- 对于字符串的方法: `str.erase(pos, len)`
  - 删除从指定位置开始的指定个数字符
```cpp
string str = "Hello World";
str.erase(6, 5);
cout << str << endl;  // 输出: Hello
```

---

- 对于容器的方法: `erase(pos1, pos2)`
  - 左闭右开式删除容器当中的元素.
```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5, 6};
    std::cout << "Original vector: ";
    for (int i : vec) {
        std::cout << i << " ";
    }
    std::cout << std::endl;

    // 删除从第二个元素 (索引 1) 到第四个元素 (索引 3) 的元素
    vec.erase(vec.begin() + 1, vec.begin() + 4); // 删除 vec[1], vec[2], vec[3]

    std::cout << "Modified vector: ";
    for (int i : vec) {
        std::cout << i << " ";
    }
    std::cout << std::endl; // 输出 "1 5 6"

    return 0;
}
```

## 不知道放在哪里的代码块
```cpp
#include <iostream>
#include <list>
using namespace std;

int main() {
// 1. 创建并填充链表
list<int> L;                      // 创建一个空的整数链表
for(int i=1; i<=5; ++i)          // 循环5次
    L.push_back(i);              // 依次在链表尾部添加数字1,2,3,4,5
                                 // 此时链表内容为：1,2,3,4,5

// 2. 删除第二个元素
L.erase( ++L.begin() );          // L.begin()指向第一个元素
                                 // ++L.begin()指向第二个元素
                                 // erase删除迭代器指向的元素
                                 // 此时链表内容为：1,3,4,5

// 3. 打印链表内容
copy(                            // 标准库算法，用于复制序列
    L.begin(),                   // 源序列的起始位置
    L.end(),                     // 源序列的结束位置
    ostream_iterator<int>(       // 输出流迭代器
        cout,                    // 指定输出到标准输出
        ","                      // 每个元素后面追加的分隔符
    )
);
cout << endl;                    // 换行

}
```



# 课堂缓冲区

- 私有的边界是 `class`而非对象. 也就是说, **相同类的对象可以直接访问对方的私有属性**.

- 不同文件之间的全局变量, 初始化的前后顺序由链接器随机决定. 此时需要确保它们之间没有初始化的依赖.
- 需要尽可能地避免使用全局变量.
- 一个良好的习惯: 当成员函数不需要改变成员变量的值时, 在后面加上 `const`的关键字, 确保不会改变;



```cpp
char* s = "Hello,world！"；
```

> 此时的右侧字符串位于 段 `text`, 不可写. `s`本身是一个固定内存的指针.

```cpp
char s［］ = "Hello,world！"；
```

> 此时的 `s`是一个大小等于数组内容的对象.

```cpp
const int* f();
// 只能将函数的返回值赋值给一个 const int*
```



#### 函数内部的对象的空间分配

编译器在编译的时候计算得到函数内部所有的对象的空间, 在实际进入函数的时候**, 立即分配所有对象的空间;**

- 实际执行到的时候发生构造.



#### Quiz

静态全局、本地和成员变量都存储于全局数据区.

- 静态成员变量的构造也在 `main()`之前.

# 题目梳理

## HW2

![](https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefinedundefinedundefinedundefinedimage-20250225160042809.png?imageSlim)

- ANS:  B
- 由于此处的`map`以`char *`作为key, 同时初始化`str`的操作发生在读取操作的外部, 因此只发生了一次的初始化, 地址是一开始就确定的值. 因此插入时总是插入到同一个键值对.

---

![](https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefinedundefinedimage-20250225160652522.png?imageSlim)

- 逗号表达式, 从左到右分别计算,最后返回的结果是最右侧的值, 此处为1. 

> - `vector<int> v(10);` 创建包含10个元素的容器, 每个元素初始化为 `0`;
>
> - `vector <int> v(10, 1);`: 创建包含10个元素的容器, 但是都初始化为 `1`;
> - `vector <int> v{10, 1};`: 创建包含10,1 这2个元素的容器;
> - 此外, 还可以使用 `vector <int> v`;创建一个空的容器; 
> - 同时也还可以用 **迭代器**进行初始化: `vector <int> v(arr, arr + 5);`



## HW3

- 类成员的默认访问权限是 **<u>私有的</u>**, 即不显式声明访问修饰符, 默认为 `private`;

  

## HW4

### 可变大小矩阵:

```cpp
#include<iostream>
#include<vector>

using namespace std;


class Matrix{
private:
    int r,c;
    vector<vector<int>> m; // 二维向量, 每个向量元素是一个一维向量

public:
    Matrix(int r, int c) : r(r),c(c){
        m.resize(r, vector<int>(c)); // 分配r个一维向量, 每个一维向量的大小为c
    }

	...

    void transform(){
        vector<vector<int>> new_m(c, vector<int>(r,0)); //	声明一个临时的二维向量
        
    	// 将矩阵转置, 放入临时的向量
        for(int i = 0; i < c; i++){
            for(int j = 0 ; j< r; j++){
                new_m[i][j] = m[j][i];
            }
        }

        swap(r,c); //改变矩阵的行与列
        m = move(new_m); //使用 move 直接将临时变量的所有权交给m, 避免拷贝
    }
};
```

> 此处值得注意的是 `resize`在二维向量中的使用, 以及 `move`直接给予“所有权”的特性.





## HW5

**判断题**: const成员函数不能作用于非const对象

> 答案是 **False**. 
>
> 题目的意思大致是: 非const对象无法调用 const对象, 实际上是事实是相反的描述. 只需要理解题目的表述即可做出判断.



## HW6

右值引用本身是一个左值, 可以取地址.

```cpp
int&& r = 5;
std::cout << r; // r 在这里是左值，因为它有名字
```



## HW7



## HW9

1. 因为静态成员函数不能是虚函数，所以它们不能实现多态

   - 静态成员函数不与类的任何具体实例（对象）相关联;

2. 在多继承中，派生类的构造函数需要依次调用其基类的构造函数，调用顺序取决于定义派生类时所指定的各基类的顺序

   ```cpp
   #include <iostream>
   
   // 基类A
   class A
   {
   public:
       A()
       {
           std::cout << "A的构造函数被调用" << std::endl;
       }
   };
   
   // 基类B
   class B
   {
   public:
       B()
       {
           std::cout << "B的构造函数被调用" << std::endl;
       }
   };
   
   // 派生类C，继承自A和B
   class C : public A, public B
   {
   public:
       // 构造函数中显式调用基类构造函数
       C() : B(), A()
       { // 注意：这里虽然B在A之前，但实际调用顺序由类定义决定
           std::cout << "C的构造函数被调用" << std::endl;
       }
   };
   
   int main()
   {
       C c; // 创建C的实例
       return 0;
   }
   ```

   输出:

   ```cpp
   A的构造函数被调用
   B的构造函数被调用
   C的构造函数被调用
   ```

   由此可见, 虽然在C的初始化列表中声明了B前A后, 但是实际上A优先调用.

3. ~~如果一个类的函数全部都是纯虚函数，则这个类不能有自己类的实现（包括引用和指针）~~

   - 这句话的括号内部是错误的.

   - 因为我们依旧可以将其的子类 `upcast`

     ```cpp
     #include <iostream>
     using namespace std;
     
     class A
     {
     public:
         virtual void ptr() = 0;
     };
     
     class B : public A
     {
     public:
         void ptr() override
         {
             cout << "B" << endl;
         }
     };
     
     int main()
     {
         A *a = new B();
         a->ptr();
         return 0;
     }
     ```





- 类内的纯虚函数被认为是 `inline`的函数;

- 在创建派生类的对象时, 构造函数的执行顺序为—— 基类对象->对象成员->派生类自身:

  ```cpp
  #include <iostream>
  using namespace std;
  
  class Base
  {
  public:
      Base() { cout << "Base 构造函数" << endl; }
  };
  
  class Member
  {
  public:
      Member() { cout << "Member 构造函数" << endl; }
  };
  
  class Derived : public Base
  {
      Member m;
  
  public:
      Derived() { cout << "Derived 构造函数" << endl; }
  };
  
  int main()
  {
      Derived d;
      return 0;
  }
  ```

  输出:

  ```cpp
  Base 构造函数
  Member 构造函数
  Derived 构造函数
  ```

- 私有继承问题: 可以通过方法访问私有继承的public, protected的基类成员(不包括私有成员), 但是无法直接访问.

  ```cpp
  #include <iostream>
  using namespace std;
  
  class Base
  {
  private:
      int a = 1; // 私有
  protected:
      int b = 2; // 保护
  public:
      int c = 3; // 公有
  };
  
  class Derived : private Base
  {
  private:
      int d = 4; // 派生类自己的私有成员
  public:
      void show()
      {
          // cout << a;  // ❌ 错误！Base 的私有成员 a，派生类根本不能访问
          cout << b << endl; // ✅ OK：Base 的 protected 成员，在 Derived 中变成了 private
          cout << c << endl; // ✅ OK：Base 的 public 成员，在 Derived 中变成了 private
          cout << d << endl; // ✅ OK：Derived 的私有成员当然能访问
      }
  };
  
  int main()
  {
      Derived d;
      d.show();
      // cout << d.b; // ❌ 错误！Base 的 protected 成员，在 Derived 中变成了 private
      return 0;
  }
  ```

- 虚函数也具有 `this` 指针.

- 在构造函数中调用虚函数，不是动态联编

  - 原因是：**对象还未完全构造完成（或已经开始析构）时，虚函数表（vtable）可能尚未指向最终派生类**.
  - 所以，在构造函数中调用虚函数时，**只会调用当前类中该函数的版本**，不会发生多态。



#### 虚析构函数

- 为了在多重继承的时候, 可以正确的调用各个阶段的析构函数, 我们需要声明基类的析构为虚函数, 然后在子类中重写:

```cpp
virtual ~CRAFT()
{
    cout << "销毁航行器(速度: " << speed << ")" << endl;
}

...

~PLANE() override
{
    cout << "销毁飞机(翼展: " << width << ")" << endl;
}
```



- 菱形继承的时候, 注意 `virtual public`的声明 以及 在底层的子类中的初始化列表的顺序!

  ```cpp
  SEAPLANE(float speed, float width, float depth) : CRAFT(speed), PLANE(speed, width), SHIP(speed, depth)
  {
    ...
  }
  ```





## HW10

- 判断: 对象间赋值将调用拷贝构造函数。  
  - 错误. 对象间的赋值调用的是 <u>拷贝复赋值运算符</u>. 

#### 异常类

![image-20250423094724267](https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefinedimage-20250423094724267.png?imageSlim)

- **A**是错误的。C++允许自定义异常类，可以继承标准异常类如std::exception。
- **B**是正确的，C++异常机制会在异常抛出前自动销毁局部对象。
- **C**是正确的，抛出异常时对象会被拷贝（或移动）到catch块中。
- **D**是正确的，在catch块中可以使用对象引用来接收异常对象。



#### 赋值运算符重载

- 语法:

  ```cpp
  类名& operator=(const 类名& 对象名)
  ```

- 特点:

  - 返回类型是类的引用（为了支持连续赋值 a=b=c）
  - 参数是const引用（防止修改原对象）
  - 通常需要先释放自己的资源，再复制数据

e.g.

```cpp
Array& operator=(const Array& a) {
    if (this != &a) {  // 防止自赋值
        delete[] data;  // 释放原有内存
        size = a.size;  // 复制大小
        data = new int[size];  // 分配新内存
        for (int i = 0; i < size; i++) {  // 复制数据
            data[i] = a.data[i];
        }
    }
    return *this;  // 返回对象自身的引用
}
```



## W12

- 建立类模板对象的实例化过程为: `模板类-对象`.
  - 编译器根据提供的模板参数将类模板实例化为一个具体的模板类（也称为实例化类或特化类），然后才能创建该模板类的对象。
  - 类模板的使用实际上是将类模板实例化成一个 `类`
- 类模板与模板类
  - 类模板是类的蓝图或规范，它本身不是一个类
  - 模板类（或实例化类、特化类）是使用具体的类型参数实例化类模板后得到的具体的类。
- 类模板和函数模板的实例化的时期 -- 均为`编译时期`
  - 函数模板在编译时期检查定义中的基本语法;
  - 尝试调用某一个函数模板时, 编译器根据实际的参数类型来推导模板参数类型/或者由显式指定类型得到, 如果合法就生成函数实例(i.e. ***模板函数***), 这个过程就是模板实例化(生成代码).
- 除了使用构造函数, 还可以直接调用 `make_pair` 让编译器自动推导类型来创建pair对象;
- 



## L3

> [!NOTE]
>
> 1. 内联函数在 **<u>编译时展开</u>**, 而不是运行时.
> 2. 内联函数的声明以及最终的生效与否, 是由 **<u>编译器</u>** 决定的. 也就是说, 无论是否存在 inline 的声明, 编译器最终决定函数的类型.



### C++ 初始化列表与成员变量初始化

> [!NOTE]
>
> - `int`和自己定义的类都是cpp的类, 所以可以直接在初始化列表中用 `:class_instance{input},...{}`来初始化类内的类对象.
> - 推荐使用 `{}`来初始化, 不会产生是函数的歧义.



#### 1. 构造顺序
- 在 C++ 中，**成员变量的初始化顺序由它们在类中声明的顺序决定**，而不是在初始化列表中的顺序。
- 初始化列表用于 **直接初始化** 成员变量，而不是在构造函数体内先创建未初始化的对象再赋值。

#### 2. 为什么必须使用初始化列表？
- **成员变量在进入构造函数体之前就已经完成了初始化**，不能在构造函数体内赋值来替代初始化。
- **如果成员变量是一个没有默认构造函数的对象**，必须在初始化列表中显式调用其构造函数，否则编译会报错。
- 在构造函数体内赋值，意味着：
  1. 先调用默认构造函数创建对象（如果 `NumberDisplay` 没有默认构造函数，这一步会失败）。
  2. 然后使用赋值运算符进行赋值，而赋值运算与初始化是不同的过程。

#### 3. 示例代码
#### ❌ 错误示例（可能会编译失败）
```cpp
class NumberDisplay {
public:
    NumberDisplay(int max) { /* 初始化代码 */ }
};

class Clock {
private:
    NumberDisplay hour_display;
    NumberDisplay minute_display;
    NumberDisplay second_display;
    
public:
    Clock(int hour, int minute, int second) { // ❌ 错误：NumberDisplay 没有默认构造函数
        hour_display = NumberDisplay(24);     // 不能这样赋值
        minute_display = NumberDisplay(60);
        second_display = NumberDisplay(60);
    }
};



```
#### **✅ 正确示例**

```cpp
class Clock {
private:
    NumberDisplay hour_display;
    NumberDisplay minute_display;
    NumberDisplay second_display;
    
public:
    // 使用初始化列表
    Clock(int hour, int minute, int second)
        : hour_display(24), minute_display(60), second_display(60) {
        // 构造函数体内的代码可以进行额外的赋值操作
    }
};
```



**4. 结论**

​	•	**所有成员变量都会在构造函数体执行前被初始化**，不能依赖在构造函数体内赋值。

​	•	**如果成员变量是没有默认构造函数的对象**，必须使用初始化列表进行初始化，否则会导致编译错误。

​	•	**初始化列表的顺序应与成员变量的声明顺序一致**，否则可能会导致未定义行为。









## Project

### P1

```cpp
cout << "\"" << value << "\"";
```

如果要输出引号, 需要加入 `\`来进行转义!



#### 使用匿名函数来排序

```cpp
sort(sortedRecords.begin(), sortedRecords.end(),
     [](const Record& a, const Record& b) {
         if (a.qso_date != b.qso_date) return a.qso_date < b.qso_date;
         return a.time_on < b.time_on;
     });
```

`sort`是 `algorithm`头文件中的库函数, 支持原地排序.

第三个参数是一个函数, 返回 `true`表示这个函数的第一个参数应该排在第二个参数的前面.

比如此处, 使用了匿名函数 `[]`, 同时比较 `date`, 当 

```cpp
return a.qso_date < b.qso_date;
```

指的是当前者的时间较小, 应该排在前面, 因此是升序.

> 不要因为语句太长而忘记末尾的 `;`.



### P2

#### 思路分析

- 整体设计:
  - 程序随机生成一组设置, 然后用户开始输入
  - 大概率, 用户不可避免地会失败, 此时应当让用户选择是否继续参与这场游戏, 否则程序生成下一组的设置;
- 房间:
  - monster所在;
  - princess所在
  - lobby;
  - 普通的room
  - 具有毒药的房间(接触后限制之后行动的次数)
  - 解药房间(可以解除中毒的状态)
  - 地图房间(接触后可以通过输入 `map`来显示当前的位置)
- 房间个数: 
  - 每次都将上述的因素都加入, 不太合适, 因此需要用户输入游戏的类型/难度;
  - 经典模式: 只会产生monster, princess,lobby和普通房间. 房间的个数较少;
  - 挑战模式:  加入毒药, 解药和地图因素; 房间个数较多.



## 刷题

- Destructors can not be overloaded.
  - 因为析构函数没有任何的参数和返回类型, 无法重载.

- **类的成员函数可以访问同类的私有成员，即使是其他对象的成员。** 回顾类的私有边界不是相对于对象的, 而是类. 

- cpp中, 构造函数一定不能是 `virtual` 的! 

- 静态变量需要再类的外部定义, 但是静态函数不一定.

- “In C++， struct is actually the same thing as class， except for minor differences in usage.”
  - 这句话是正确的, 注意结构体也是支持继承的. 只是默认的继承方式也是 `public`. 

- In C++, inheritance allows a derived class to directly access all of the functions and data of its base class.（T/F）
  - 错误的. 对于 `public` 类型的继承, 依旧无法直接访问父类的私有成员, 只能通过子类的方法来间接访问.



- 下面的代码段, 能够能够通过编译, 但是存在运行时的错误(越界访问):

  ```c++
  int main() { vector<float> v; v[0] = 2.5; }
  ```

  - 编译器在编译时候, 检查的主要是程序的语法和类型;
  - 越界访问的问题存在于运行时检测.

- 64位系统系统环境, 按照8个字节进行对齐:

  ```c++
  // 参考的类定义方式
  class A1
  {
  public:
      int i;
      void f(){}
  };
  
  // 其他的类型定义
  
  ```

  Output:

  ```c++
  Size of A1 (non - virtual function with int member): 4 bytes
  Size of A2 (virtual function with int member): 16 bytes
  Size of A3 (non - virtual function without int member): 1 bytes
  Size of A4 (virtual function without int member): 8 bytes
  Size of B (derived from A1): 8 bytes
  ```

  主要注意2点:

  1. 没有任何成员的类, 也占用1个字节的大小;

  2. 4+8 将会对齐得到16个字节的空间大小.

     > [!NOTE]
     >
     > `void*` 和 `int*` 的大小相同! (题目通常给出 `int*`的大小, 然后给出虚函数)



- `malloc` 不会调用对象的构造函数, 仅仅分配内存, 并不涉及对象的初始化;

  - `new`会调用构造函数

  - `malloc` 需要显式地类型转换:

    ```c++
    class MyClass {
    public:
        MyClass() { std::cout << "Constructor called!" << std::endl; }
    };
    
    MyClass* p1 = new MyClass();  // 输出 "Constructor called!"
    MyClass* p2 = (MyClass*)malloc(sizeof(MyClass));  // 无输出，构造函数未调用
    ```

- `new` 是CPP的运算符, 可以重载; `malloc`是标准库的函数, 无法重载.
- 每个类最多具有一个析构函数



#### 重载问题

在 `::`, `()` 和 `->`中, 只有 `->`可以被重载:

```c++
#include <iostream>

class MyClass {
public:
    void sayHello() {
        std::cout << "Hello from MyClass!" << std::endl;
    }
};

class MyPtr {
private:
    MyClass* ptr;  // 内部存储一个原生指针

public:
    // 构造函数
    MyPtr(MyClass* p) : ptr(p) {}

    // 重载 -> 运算符
    MyClass* operator->() {
        return ptr;  // 返回原生指针，使得可以继续用 -> 访问成员
    }
};

int main() {
    MyClass obj;
    MyPtr myPtr(&obj);  // 用 MyPtr 包装 MyClass 对象

    myPtr->sayHello();  // 调用 MyClass 的成员函数

    return 0;
}
```

 

#### new与对象转换

```c++
#include <iostream>
using namespace std;

class B;

class A
{
protected:
    int x;

public:
    A(int x = 0) : x(x) {}
    operator B();
    int getx() { return x; }
};

class B : public A
{
public:
    B(int x = 0) : A(x) { this->x++; }
    B(const B &b) : A(b.x) { this->x++; }
};

A::operator B() { return *new B(x + 1); } // 特别注意此处

int main()
{
    A *p1 = new B(3); // new的时候触发B的构造函数, 自增
    A *p2 = new A(9);
    B b0 = *p1; // 发生了A类对象向B类对象的转化, 详见下面两行:
    // *p1 将要转化的时候,  先传入x+1作为B的初始化参数输入,
    // 然后new将触发B的构造函数, 最后返回, 返回结果用于初始化b0, 继续触发拷贝构造函数
    B &r = b0;
    B b1 = b0;  // 发生了 B类对象的拷贝构造, b1的x为8, 但是不会改变b0的x=7
    B b2 = *p2; // 这一步的转换与上面的同理. 也是+3
    cout << p1->getx() << endl;
    cout << p2->getx() << endl;
    cout << b0.getx() << endl;
    cout << r.getx() << endl;
    cout << b1.getx() << endl;
    cout << b2.getx() << endl;
}
```

解题过程中的关键步骤已经写在注释当中, 最后的输出是:

```c++
4
9
7
7
8
12
```

梳理考察的重要知识点:

1. upcast的时候, 是子类的属性赋值给父类, 包括虚函数表. 因此, 此时看作是父类的对象, 但是虚函数能够动态绑定.

2. `new`和`delete`分别自动调用类的构造函数和析构函数;

3. `A::operator B()` 形式表示A类对象如何转换成B类型对象的, 在发生转换的时候自动调用这部分的函数.

   ```c++
   A *p1 = new B(3); // 拷贝构造的时候直接自增为4
   A *p2 = new A(9);
   ```

   注意上面的指针都指向A类的对象.



#### 链式的析构顺序

- 构造顺序: 父类->成员对象-> 自身
- 析构顺序: 自身-> 成员对象-> 父类

下面的这道题目涉及的类的关系如下:

1. P是父类, S是子类;
2. P内部有两个P类型的指针成员.

```c++
#include <iostream>
using namespace std;

// 1. 先声明基类 P
class P
{
public:
    static bool flag; // 静态成员声明
    int x;            // 数据成员
    P *left, *right;  // 指针成员

    // 构造函数
    P(P *left = nullptr, P *right = nullptr)
        : x(0), left(left), right(right) {}

    // 虚析构函数（因为有继承关系，应该是虚函数）
    ~P()
    {
        if (flag)
        {
            if (left != nullptr)
            {
                delete left;
            }
            if (right != nullptr)
            {
                delete right;
            }
        }
        else
        {
            if (right != nullptr)
            {
                delete right;
            }
            if (left != nullptr)
            {
                delete left;
            }
        }
        cout << "P" << x;
    }
};

// 2. 静态成员的定义（必须在类外定义）
bool P::flag = false;

// 3. 派生类 S
class S : public P
{
public:
    // 构造函数，调用基类构造函数
    S(P *left = nullptr, P *right = nullptr) : P(left, right) {}

    // 析构函数
    ~S()
    {
        cout << "S" << x;
    }
};

int main()
{
    S *p1 = new S;
    p1->x = 1; // 设置第一个节点的值
    S *p2 = new S;
    p2->x = 2;   // 设置第二个节点的值
    S s(p1, p2); // 创建根节点，连接p1和p2
    s.x = 3;     // 设置根节点的值/
    return 0;    // 程序结束时析构对象
}

```

最终的输出:

1. 析构s, 首先析构子类自身, 调用s的析构函数, 输出s;
2. 然后析构父类, 父类是P, 析构的时候直接调用自身的析构函数, 一词析构右指针和左指针;
3. 执行到末尾, 输出自身的P3.

Output:

```c++
S3P2P1P3
```







#### 操作符的重载

```c++
#include <iostream>
#include <iomanip>

#define MAXN 110

/* Your answer will be inserted here. Feel free to add anything needed here.*/

class vec
{
private:
    int first, second;

public:
    vec(int a, int b) : first(a), second(b) {}
    //  < 的重载
    bool operator<(const vec &other) const
    {
        return second < other.second;
    }

    // 类型转换的重载
    operator double() const
    {
        return static_cast<double>(first);
    }

    // 输出的重载
    friend std::ostream &operator<<(std::ostream &os, const vec &v)
    {
        return os << "(" << v.first << "," << v.second << ")";
    }
};
void printArrayInfo(vec **arr, int n)
{
    vec *maxv = arr[0], *minv = arr[0];
    double avg = 0;
    for (int i = 0; i < n; ++i)
    {
        vec *val = arr[i];
        if (*val < *minv)
            minv = val;
        if (*maxv < *val)
            maxv = val;
        avg = avg + static_cast<double>(*val);
    }
    avg /= n;
    std::cout << std::fixed << std::setprecision(2) << "min = " << *minv << ", max = " << *maxv << ", avg = " << avg << std::endl;
}

int main()
{
    vec *pool[MAXN];
    int n;
    int a, b;
    std::cin >> n;
    for (int i = 0; i < n; i++)
    {
        std::cin >> a >> b;
        pool[i] = new vec(a, b);
    }
    printArrayInfo(pool, n);
    return 0;
}
```



#### 菱形继承与二义性问题

并不是菱形继承的操作都会导致二义性, 要从本质--访问的操作是否导致无法区分正确的单独对象?

```c++
#include <iostream>
using namespace std;
class A
{
public:
    int x;
    A() : x(6) {}
    int fun()
    {
        return x;
    }
};
class B : public A
{
public:
    int fun()
    {
        return A::fun() + x;
    }
};
class C : public A
{
public:
    int fun()
    {
        return A::fun() + x;
    }
};
class D : public B, public C
{
public:
    int fun()
    {
        return B::fun() + C::fun();
    }
};
int main()
{
    D d;
    cout << d.fun();
    // cout << d.A::fun(); 存在二义性的问题, 因为在D当中分别存在来自于B和C的这个函数!
}
```

在上面代码中, D是菱形继承, 但是可以直接访问d的fun, 因为在d的fun中涉及的是B和C自己的fun. 而后者又能够直接调用自己的继承自A的fun

- 无法直接调用 `d.A::fun()`, 参见注释.

# 最后的枚举







#### 转换运算符

`A::operator B()`的含义： 定义了如何将 `A` 类型的对象转换为 `B` 类型的对象。

- `A::` 表示这是 `A` 类的成员函数
- `operator B` 表示这是一个到 `B` 类型的转换运算符
- `()` 表示这是一个函数

```c++
// A 具有一个成员变量且在构造函数中可以赋值

A::operator B() { return *new B(x + 1); }
```

- `new B(x + 1)` 创建一个新的 `B` 对象，其中 `x` 是 `A` 类的成员变量
- `*` 解引用这个新创建的对象
- 返回这个 `B` 类型的对象

> [!NOTE]
>
> 注意, 这只是决定了B类对象的如何构造, 并不会影响A类对象本身.

调用的场景: 需要将A类型的对象转换为B类型的对象 e.g.——

```c++
A a(5);
B b = a;  // 这里会自动调用 A::operator B()
```



## const相关

### 对象与方法的对应

C++在选择成员函数时会考虑对象的const属性：

- const对象只能调用const成员函数 

- 非const对象优先调用非const版本，如果没有非const版本才会调用const版本

- > [!NOTE]
  >
  > 函数定义的顺序并不会影响调用的选择!

e.g.

```c++
#include <iostream>
using namespace std;
class MyClass
{
public:
    MyClass(int x) : val(x) {}

    void Print()
    {
        cout << 2 << endl
             << val << endl;
    }

    void Print() const { cout << 1 << endl
                              << val << endl; }

private:
    int val;
};
int main()
{
    const MyClass obj1(10);
    MyClass obj2(20);
    obj1.Print();
    obj2.Print();
    return 0;
}
```

output:

```c++
1
10
2
20
```





## 初始化先后的问题

C++中对象的初始化和销毁顺序遵循特定的规则，这对于理解程序行为和避免内存问题至关重要

### 初始化列表

### 类的构造与析构顺序

在C++中，对象的构造和析构顺序遵循以下规则：

1. **构造顺序**：
   
   - 基类先于派生类构造
   - 成员变量按声明顺序构造
   - 基类构造完成后，才执行派生类构造函数体
   - > [!NOTE]
     >
     > 对象在调用构造函数之前, 首先完成内部成员对象的构造
   
2. **析构顺序**：
   
   - 与构造顺序相反
   - 先执行派生类析构函数体
   - 然后按声明顺序的逆序析构成员变量
   - 最后析构基类

#### 示例分析

```c++
int main()
{
    Child c;
}
```

##### 类层次结构

```
X (基础类)
↑
Y (继承自X)

Parent (包含X成员)
↑
Child (继承自Parent，包含Y成员)
```

##### 构造过程分析

当创建`Child`对象时，构造顺序为：

1. 首先构造基类`Parent`
   - 在`Parent`构造前，先构造其成员`x`（调用`X::X()`）
   - 然后执行`Parent`构造函数体（输出"Parent::Parent()"）
2. 基类构造完成后，构造`Child`的成员`y`
   - 在构造`y`前，先构造其基类部分（调用`X::X()`）
   - 然后执行`Y`构造函数体（输出"Y::Y()"）
3. 最后执行`Child`构造函数体（输出"Child::Child()"）

##### 析构过程分析

当`Child`对象离开作用域时，析构顺序为：

1. 首先执行`Child`析构函数体（输出"Child::~Child()"）
2. 然后析构成员`y`
   - 先执行`Y`析构函数体（输出"Y::~Y()"）
   - 然后析构其基类部分（调用`X::~X()`）
3. 最后析构基类`Parent`
   - 先执行`Parent`析构函数体（输出"Parent::~Parent()"）
   - 然后析构其成员`x`（调用`X::~X()`）

#### 预期输出

执行`test.cpp`程序时，预期输出为：

```
X::X()              // Parent的成员x构造
Parent::Parent()    // Parent构造函数体
X::X()              // Y的基类部分构造
Y::Y()              // Y构造函数体
Child::Child()      // Child构造函数体
Child::~Child()     // Child析构函数体
Y::~Y()             // Y析构函数体
X::~X()             // Y的基类部分析构
Parent::~Parent()   // Parent析构函数体
X::~X()             // Parent的成员x析构
```

#### 重要注意事项

1. **虚析构函数**：当使用多态时，基类应该有虚析构函数，确保正确调用派生类析构函数。

2. **成员初始化列表**：推荐使用成员初始化列表而非在构造函数体内赋值，这样可以直接初始化而非先默认构造再赋值。

3. **异常安全**：构造过程中如果抛出异常，已构造的成员会被正确析构，但未完全构造的对象不会调用析构函数。

4. **RAII原则**：资源获取即初始化，在构造函数中获取资源，在析构函数中释放资源，确保资源管理安全。

#### 实际应用

理解对象生命周期对以下场景尤为重要：

1. **资源管理**：确保资源在不再需要时被释放
2. **依赖关系处理**：确保依赖对象在被依赖对象之前构造，之后析构
3. **继承层次设计**：合理设计基类和派生类的构造和析构行为
