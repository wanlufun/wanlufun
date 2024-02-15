---
title: 算法基础 - C++ 语法
tags:
  - C++
cover: 'https://images.wanlu.fun/picgo/202402152237615.png'
abbrlink: f24d757b
date: 2024-02-15 22:35:28
---

## 选择 C++ 的原因：性能高

1. 低级访问和控制： C 和 C++ 提供了对硬件的直接访问和控制能力。例如，你可以直接管理内存、使用指针操作等，这在许多高级语言中是不可能或受限的。
2. 编译时优化： C 和 C++ 编译器能够在编译时进行广泛的优化，如内联函数、循环展开、常量传播等，这些优化有助于提高运行时性能。
3. 无运行时开销：与需要运行时环境（如Java的JVM或Python解释器）的语言相比， C 和 C++ 编译的程序直接转换为机器码，无需额外的运行时支持，这减少了执行时的开销。
4. 确定性的资源管理： C 和 C++ 允许程序员精确控制资源的使用，如手动管理内存分配和释放。这种控制能力使得资源利用更加高效，尤其在资源受限的环境下。
5. 标准库的效率：C 和 C++ 的标准库（如STL）高效且经过优化，提供了快速的数据结构和算法实现。
6. 硬件依赖优化：C 和 C++ 代码可以针对特定硬件平台进行优化，利用特定的指令集和硬件特性来提高性能。

## 输入、输出和语句控制

```cpp
#include <cstdio>
printf / scanf 所需头文件

#include <iostream>
cin / cout 所需头文件

// 优化 cin / cout 速度
std::ios::sync_with_stdio(false);
std::cin.tie(0);  // 如果编译开启了 C++11 或更高版本，建议使用 std::cin.tie(nullptr);
```

### 输入优化 - 快读

> getchar 是用来读入 1 byte 的数据并将其转换为 char 类型的函数，且速度很快

```cpp
int read() {
  int x = 0, w = 1;
  char ch = 0;
  while (ch < '0' || ch > '9') {  // ch 不是数字时
    if (ch == '-') w = -1;        // 判断是否为负
    ch = getchar();               // 继续读入
  }
  while (ch >= '0' && ch <= '9') {  // ch 是数字时
    x = x * 10 + (ch - '0');  // 将新读入的数字「加」在 x 的后面
    // x 是 int 类型，char 类型的 ch 和 '0' 会被自动转为其对应的
    // ASCII 码，相当于将 ch 转化为对应数字
    // 此处也可以使用 (x<<3)+(x<<1) 的写法来代替 x*10
    ch = getchar();  // 继续读入
  }
  return x * w;  // 数字 * 正负号 = 实际数值
}
```

### 输出优化

> putchar() 用来将数字输出为单个字符的函数

```cpp
void write(int x) {
  static int sta[35];
  int top = 0;
  do {
    sta[top++] = x % 10, x /= 10;
  } while (x);
  while (top) putchar(sta[--top] + 48);  // 48 是 '0'
}
```

**变量类型**

| 变量类型  | 值                       | 所需字节 |
| --------- | ------------------------ | -------- |
| bool      | true / false             | 1 byte   |
| char      | 'a' 、'b'                | 1 byte   |
| int       | -2147483648 ~ 2147483647 | 4 byte   |
| float     | 1.23 2.5 6-7 位有效数字  | 4 byte   |
| double    | 15 - 17 位有效数字       | 8 byte   |
| long long | -2 ^ 63 ~ 2 ^ 63 - 1     | 8 byte   |

**格式输出符号**

| 输出类型    | 格式输出 |
| ----------- | -------- |
| int         | %d       |
| double      | %lf      |
| long double | %llf     |
| float       | %f       |
| char        | %c       |
| string      | %s       |

## 判断语句

```cpp
if (flag)
{
    statement;
}
else if (flag)
{
    statement;
}
else
{
    statement;
}
```

## 循环结构

```cpp
while (flag)
{
    statement;
}
```

当 flage 满足条件时，执行 statement 语句。

```cpp
do
{
    statement;
}
while (flag);
```

先执行一次 statement 语句，再进行判断。

```cpp
for(init-statement; confident; expression)
{
    statement;
}
```

- init-statement 可以为空
- confident 可以为空，默认为 true
- expression 可以为空

**跳转语句**

- break 退出循环
- continue 进入下一轮循环

## STL 容器与算法

```cpp
vector 变长数组
string 字符串 substr(), c_str()
queue 队列 push() front() pop()
priority_queue 优先队列 push() top() pop()
stack 栈 push() top() pop()
deque 双端队列 
set map multiset multimap 给予平衡二叉树（红黑树），动态维护有序序列
unordered_set unordered_map unordered_multiset unordered_multimap 哈希表
bitset 压位
```

系统为某一程序分配空间时，所需时间与空间大小无关，与申请次数有关。

### 容器共同函数

- size() ：返回容器里元素的数量
- empty() ：返回容器是否为空
- clear() ：清空所有元素（队列、优先队列、栈不支持）

### pair

```cpp
pair<int, int> p;

// 创建一个 pair
p = {a, b};
p = make_pair(a, b);

p.first 第一个元素
p.second 第二个元素
```

### vector

- front() ：返回第一个元素
- back() ：返回最后一个元素
- push_back() ：在容器最后添加一个元素
- pop_back() ：删除容器最后一个元素

### string

- substr() 返回一个子串
- c_str() 返回一个可以 %d 输出的 char * 字符串

### queue

> 先进先出

- push() 向队尾插入一个元素
- front() 返回队头第一个元素
- back() 返回队尾最后一个元素
- pop() 弹出队头第一个元素

### deque

- size()
- empty()
- clear()
- front()
- back()
- push_back()
- pop_back()
- push_front()
- pop_front()

### priority_queue 默认大根堆

- push()
- top()
- pop()

```cpp
priority_queue(int, vector<int>, greater<int>> heap; 小根堆
    第一个参数：堆中元素的类型
    第二个参数：堆由什么结构实现。默认 vector
    第三个参数：如何排序的
```

### stack

> 先进后出

- push()
- top()
- pop()

### set / multiset

> set 不能存在重复元素 / multiset 可以存在重复元素

- insert()
- find()
- count()
- erase()
  - 输入一个数：删除所有这个数
  - 输入迭代器：删除迭代器
- lower_bound() 返回大于等于 x 最小的数的迭代器
- upper_bound() 返回大于 x 最小的数的迭代器

### map / multimap

> 可以向使用数组一样，使用 map

- insert() 插入一个 pair
- erase() 
- find()

### bitset

- ~ & | ^

- \>> <<

- == !=

- []

- count)(

- any() 判断是否有一个 1

- none() 判断是否都为0

- set()

- reset 

- flip() 取反

### 迭代器

通过统一的接口，对不同的容器进行遍历，类似与指针，可以进行解引用。

- begin() ：指向容器中第一个元素
- end() ：指向容器中最后一个的后一个位置（属于越界范围）

注：绝大多数容器均可以通过迭代器遍历容器里的元素，除了队列、栈、优先队列（因为他们本身用于不同的规则，即不需要遍历元素，以破坏这个规则）。

### 位运算符

- `&` ：与
- `|` ：或
- `^` ：异或
- `~` ：取反

### C++ <algorithm> 库

1. std::sort(first, last) 对 [first, last) 容器进行排序。左闭右开。first / last 为迭代器。

```cpp
// 默认从小到大排序
vector<int> nums = {2, 4, 3, 5};
sort(nums.begin(), nums.end());

int a[20];
sort(a, a+20);

// 从大到小
sort(s.begin(), s.end(), greater<int>());
```

2. std::unique() 删除连续的重复项

```cpp
// 去重（先排序，在 unique ）
vector<int> nums = {1, 2, 1, 2, 3, 4, 5, 5};
sort(nums.begin(), nums.end());
auto last = unique(nums.begin(), nums.end());
nums.erase(last, nums.end());
```

3. random_shuffle() 打乱
4. lower_bound / upper_bound 二分

```cpp
lower_bound(ForwardIt first, ForwardIt last, const T& value, Compare comp) 返回大于等于 x 最小的数的迭代器

upper_bound(ForwardIt first, ForwardIt last, const T& value, Compare comp) 返回大于 x 最小的数的迭代器

// 第四个参数可选
```

5. next_permutation() 排序

```cpp
next_permutation(a, a+n)
```

6. swap() 交换两个数

### C \<cstring> 库

- strlen() : 返回给定字符串的长度。

```cpp
int strlen(const char* str);
```

- strcpy() : 复制字符串。

```cpp
char* strcpy(char* dest, const char* src );
```

- strcmp() : 按字典比较两个字符串。

```cpp
int strcmp(const char* lhs, const char* rhs );
```

- strchr() : 查找字符。

```cpp
const char* strchr(const char* str, int ch );
```

- strstr() : 

```cpp
const char* strstr( const char* haystack, const char* needle );
```

- strcat() : 拼接字符串。

```cpp
char* strcat( char* dest, const char* src );
```

- memset() : 设置一块内存的默认值

```cpp
void* memset( void* dest, int ch, std::size_t count );
```

## 数据生成

- srand(time(NULL))

## 调试代码模板

```cpp
#define DEBUG
#ifdef DEBUG
// do something when DEBUG is defined
#endif
// or
#ifndef DEBUG
// do something when DEBUG isn't defined
#endif
```

## 注意事项

1. `printf / scanf`效率一般比 `cin / cout`高
2. 万能头文件 `#include <bits/stdc++.h` （通用头文件，缺点编译慢）。
3. 使用 `scanf("%c", &n)`时，会读取 `\n`等空白符。
4. 当使用 cout 输出一个浮点数时，如果这个数的大小超过了 cout 的默认精度（通常为 6 位数字），它可能会自动转换为科学计数法来表示。这是为了避免输出过长的数字，同时保持一定的精度。
5. 由于浮点数在计算机中的存储方式导致的，它们是以二进制形式表示的，而某些十进制小数在二进制中是无限循环的，不能精确表示。
6. 使用 std::getline 函数，从输入流中读取一行。它会读取直到遇到换行符 \n，但不会将换行符添加到结果字符串中。**换行符会被从缓冲区丢弃**
