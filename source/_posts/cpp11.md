---
title: C++11常用新特性使用经验总结
tags:
  - bind
  - C++11
  - function
  - Lambda
url: 100.html
id: 100
categories:
  - C/C++
keywords: C++11,新特性,经验总结
description: 'C++11新增特性，常用新特性使用经验总结'
date: 2017-10-12 22:59:02
---

## 更方便的就地初始化

在定义类成员变量时直接初始化，如下：
```
class Device{
private:
    int device_id = 0;
}
```
是不是非常方便？妈妈再也不用担心我的变量忘记初始化了

另外，还可以直接初始化结构体成员：
```
typedef struct _Channel{
    int id;
    string name;
    int other;
}Channel;
Channel ch{1,"CH1",128};
Channel *ch_ptr = new Channel{1,"CH1",128};
```
也可以直接初始化类中的结构体成员
```
class Device{
private:
    int device_id = 0;
    Channel ch;
}
Device dev{ch{1,"CH1",128}};
```
也可以直接初始化各种STL容器
```
vector<int> vec = {1,2,3};
vector<int> vec{1,2,3};
map<string, int> m = { { "debug", 1}, {"self", 2}};
```
上面的例子中，初始化vector等容器的元素的个数是不固定的，这个如何做到的呢？有兴趣的话可以进一步研究初始化列表std::initializer\_list，使用std::initializer\_list可以初始化自定义的类型，这样就可以用多个个数不固定的元素，来初始化自定义类型。

## auto关键字:自动推导变量类型

下面的代码
```
map<string,int>::iterator it = m.find("debug");
```
可以简化为
```
auto it = find("debug");
```
烦人的长串map<string,int>::iterator终于可以拜拜了

当然C++的是强类型语言，所以并不存在auto这种类型，变量it在编译时期被编译器自动推导为真正的类型，it的类型依旧是map<string,int>::iterator

## 更方便的for循环

使用新的for特性，可以更方便遍历数组或者各种容器中的元素
```
int array\[10\]{0,1,2,3,4,5,6,7,8,9};
for(auto it:array)cout<<it<<" ";

vector<int> vec{0,1,2,3,4,5,6,7,8,9};
for(auto it:vec)cout<<it<<" ";

map<string, int> m = { { "debug", 1}, {"self", 2}};
for(auto it:m)cout<<it.firse<<":"<<it.second<<endl;
```
当需要修改容器中的元素时，请用下面的方式：

vector<int> vec{0,1,2,3,4,5,6,7,8,9};
for(auto &it:vec)it++;

注意，for(auto it:vec)中的it是一个元素的副本，当产生副本比较耗时间或者耗空间时，请使用for(auto &it:vec)的方式，而不是使用for(auto it:vec)的方式

## tuple元组

元组类似具有多列的记录，举例，保存人员信息时，先定义如下结构体
```
typedef struct _Person{
int id;
string name;
int age;
}Person;
```
修改为使用tuple，可以如下定义
```
typedef tuple<int,string,int> Person;
```
操作元组
```
Person person = make_tuple(1, "DebugSelf", 18); // 构造元组
cout<<"ID:"<<get<0>(person)<<endl;              // 读取元组的成员
cout<<"Name:"<<get<1>(person)<<endl;
cout<<"Age:"<<get<2>(person)<<endl;
get<2>(person) = 30;                            // 修改元组的成员
```

## nullptr关键字:更安全的空指针

C++中使用NULL表示空指针，而NULL的定义如下
```
#define NULL 0
```
因为NULL实质是0，当遇到如下的重载函数时
```
void fun(int a, int b);
void fun(int a, int *b);
fun(a,NULL);
```
我们的本意是想调用第二个fun，但是因为NULL本质是0，最终调用的是第一个fun；

C++新增nullptr关键字，用来表示空指针，他的类型就是指针，而不是int类型的0，调用fun(a,nullptr)会正确调用第二个fun。

为了代码更安全，请使用nullptr代替NULL。

## 新增类型转换函数std::to_string

在C++11之前，要想把int转换为string，需要
```
int value=10;
char buff_temp\[100\]={0};
sprintf(buff_temp,"%d",value);
string str = buff_temp;
```
每次写这段转换代码时，心里都在暗骂，MD这个功能不应该是标准库自带的吗？C++11后终于引入了to_string把int转换为string，上述代码简化为：
```
string str=to_string(value);
```
to_string其实功能还比较弱，连转换格式控制都不提供，不过总比没有强吧，汗。

另外，std::stoi/stol/stoll是把string转换为数值。

## 更方便更安全的智能指针shared_ptr

见[《C++11智能指针shared\_ptr、weak\_ptr、unique_ptr用法详解》](http://www.debugself.com/archives/89 "C++11智能指针shared_ptr、weak_ptr、unique_ptr用法详解")

## 更方便的函数:lambda表达式

使用lambda可以就地创建一个函数，写法更简洁，提高了编码效率

见[《C++11 Lambda表达式(匿名函数)用法详解》](http://www.debugself.com/archives/94 "C++11 Lambda")

## 功能更强的回调函数:function和bind

见[《c++11 function、bind用法详解》](http://www.debugself.com/archives/86 "c++11 function、bind用法详解")

## 效率更高的右值引用

见[《C++11 右值引用、 move、emplace、emplace_back原理和用法详解》](http://www.debugself.com/archives/54 "C++11 右值引用、 move、emplace、emplace_back原理和用法详解")

## for_each

使用for_each，可以针对容器中的元素，依次调用指定的函数
```
void print(int& elem) {
    cout << elem << endl;
}
int main() {
    vector<int> vec{0,1,2,3,4,5,6,7,8,9};
    for_each(vec.begin(), vec.end(), print);
}
```
## 原始字符串R"()"

原始字符串中,是不存在字符转义的问题的，比如Windows上文件路径C:\\Users\\debugself\\Desktop\\notepad2\\Notepad2.txt要写在代码中，需要写成
```
string path=“C:\\\Users\\\debugself\\\Desktop\\\notepad2\\\Notepad2.txt”;
```
使用原始字符，就可以直接写为
```
string path=R“(C:\\Users\\debugself\\Desktop\\notepad2\\Notepad2.txt)”;
```

## 新增无序容器

unordered\_map/unordered\_multimap、unordered\_set/unordered\_multiset

无序容器，内部通过Hash Table实现，比map和set的红黑树效率更高

## mutable关键字:突破const关键字的限制

见[《C++11mutable关键字用法详解》](http://www.debugself.com/archives/97 "C++11 mutable关键字用法详解")

## 其他

* C++11还引入了很多操作系统相关的库，如：
* 线程：线程这个很方便，可以跨平台，而且比CreateThread、pthread都简洁方便
* 处理日期和时间的chrono库，包括计时器timer，记录时长duration
* regular expression：正则表达式
* 变参数模板：STL库的实现很多都基于可变参数模板实现的。C++11大量使用了模板各种奇淫技巧，模板的用法真是无穷无尽，有了模板的存在，谁还敢说精通C++?![](http://img.baidu.com/hi/face/i_f31.gif)。
    
其实C++11中新引入的功能，之前boost库已经早实现过了（所以大家都叫boost为准标准库，哈哈），boost烦人的地方就是库太大，版本太多，部分模块必须编译为共享库才能使用，每次使用boost都得附加一堆文件，现在好了，直接使用C++11吧。


参考资料

[http://en.cppreference.com/](http://en.cppreference.com/ "c++参考手册")