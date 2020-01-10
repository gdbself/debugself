---
title: C++11 右值引用、move、emplace、emplace_back原理和用法详解
tags:
  - C++11
url: 54.html
id: 54
categories:
  - C/C++
keywords: C++11,右值引用,move,emplace,emplace_back
description: 'C++11右值引用是什么、move、emplace、emplace_back原理和用法详解'
date: 2017-09-13 23:16:41
---

## 右值引用

C++11引入了右值引用，用&&表示右值引用，如int &&a = add(1,2)。

先了解下什么是左值和右值，简单的说，下面的表达式：
```
int a = 10;
```
等号“=”左边的a为左值，右边的10为右值；当然这只是表面的定义，进一步说，左值是有固定的内存地址，&a即左值的地址，我们可以把&a保存起来，后续通过&a这个地址读取、修改a的内容；而右值是一种临时的值，我们很难获取到右值的地址，如上面的10，10的地址在哪里呢，通过常规的代码是很难获取到10的内存地址的，或者即使获取到右值的地址，该地址可能很快失效了，不能后续使用了。几种常见的右值如下：

* 硬编码的字面量，如int a =10中的10，char *s = "hello world"中的“hello world”都是右值   
* 函数的返回值，如add(1,2)的返回值3也是右值，这里的3是临时值
* 表达式的计算值，如int a =1,b =2;int c = a+b;中的a+b的值也是右值，这里a+b的值也是临时值
    
C++11引入右值引用后，左值和右值又细分为 prvalue, xvalue, lvalue等，有兴趣的可以阅读 [http://en.cppreference.com/w/cpp/language/value_category](http://en.cppreference.com/w/cpp/language/value_category)

还是没搞清楚右值是什么东东？没关系！这并不影响继续阅读下文的内容，你只需要知道&&是右值就行啦。

## 移动构造函数：转移类成员的所有权

**C++11之前已经有复制构造函数了，相比复制构造函数，移动构造函数不是复制，而是直接转移类成员的所有权**

C++11引入了右值引用后，水道渠成的引入了移动构造函数,其参数类型为右值引用，看下面的例子：
```
class A{
public:

A(A &&o){
  cout<<"move constructor"<<endl;
}

A(const A& o) {
  cout<<"copy constructor"<<endl;
};
```
移动构造函数和复制构造函数比较像，它把复制构造函数的&替换为&&了，且没有了const。

移动构造函数有什么用呢，我们顾名思义一下，它是用来移动的，移动什么内容呢，移动的是类内部成员/元素的所有权，看下面的例子:
```
class A{
public:

A(int size) {
  cout<<"constructor"<<endl;
  this->size = size;
  if(size)data = new int\[size\];
  for (int i = 0; i < size; ++i)data\[i\] = i;
}

A(const A& o) {
  cout<<"copy constructor"<<endl;
  this->size=o.size;
  data = new int\[size\];
  memcpy(data,o.data,size*sizeof(int));
}

A(A &&o) {
  cout<<"move constructor"<<endl;
  data=o.data;
  this->size=o.size;
  o.data=nullptr;
  o.size=0;
}

~A(){delete \[\]data;}

private:
  int *data = nullptr;
  int size = 0;
};
```
在上面的复制构造函数中，我们把o.data的内容复制给this->data，这就是大家常说的深拷贝；

而在上面的在移动构造函数中，我们没有复制o.data的元素，而只是把o.data的指针赋值给this->data,这就是大家常用的浅拷贝，但是与浅拷贝不同的是，移动构造函数还把o.data指针置为空，这样执行移动构造函数后，this->data获得了元素的所有权，而o.data不再拥有之前元素的所有权，o.data元素的所有权被移动/过继给this了，这就是移动构造函数的含义。

明白了什么是移动构造函数，那么问题来了，如何才能调用移动构造函数呢?

当我们执行如下的代码
```
A a(10);
A b = a;
//输出结果为：
constructor
copy constructor
```
此时调用的是复制构造函数，因为上述代码是要把a的内容复制给b；而移动构造函数的参数类型是右值引用，要想调用移动构造函数，我们需要传入一个右值引用。前面提到，函数的返回值是临时值，它是右值引用，所以我们编写一个函数，让该函数的返回类型为A，代码如下：
```
A CreateA(int size) {
  A a(size);
  return a;
}
b = CreateA(10);
//输出结果为：
constructor
move constructor
```
顺利调用了移动构造函数！为了更方便的调用移动构造函数，C++11还引入了一个特别有用的函数，即std::move。

## std::move把左值引用“强制转换”为右值引用。

move函数的声明如下：
```
template< class T >
typename std::remove_reference<T>::type&& move( T&& t ) noexcept;
```
从函数声明上看move的返回值，返回值是右值引用，所以通过move，我们可以把左值转换为右值引用。

我们使用move修改上面的代码
```
A a(10);
A b = std::move(a);
//输出结果为：
constructor
move constructor
```
顺利调用了移动构造函数，而且，是不是更简单，更方便了？

## 具有复制功能的移动构造函数

有童鞋问了，上面代码中的移动构造函数的移动功能，完全取决与代码的实现，假如把移动构造函数编码为复制功能，所谓的移动构造函数还算哪门子移动啊？代码如下
```
class A {
public:
   A(A &&o) {
     cout << "moving constructor but execute copyping" << endl;
     this->size = o.size;
     data = new int\[size\];
     memcpy(data, o.data, size * sizeof(int));
   }
｝
```
执行如下的代码
```
A a(10);
A b = std::move(a);
//输出结果为：
constructor
moving constructor but execute copyping
```
对，你没看错，在移动构造函数不执行移动，而执行复制是合法，因为移动构造函数执行移动功能只是约定，而不是强制要求，你完全可以在移动构造函数中执行复制功能！

但是，最好不要这么干（移动构造函数中执行复制功能，简直就是给自己挖坑！），而是遵守通用的约定，比如标准库STL中，移动构造函数都是按照约定，实现为移动功能，看下面的例子：
```
vector<int> v1={1,2,3,4,5};
vector<int> v2=move(v1);
cout<<"v1.size:"<<v1.size()<<endl;
cout<<"v2.size:"<<v2.size()<<endl;
//执行结果为：
v1.size:0
v2.size:5
```
## emplace更高效的向STL容器插入元素
**“移动式”插入元素emplace_back/emplace：更高效的向STL容器插入元素**

先看下vector::push_back插入元素的过程
```
vector<A> vec;
A a(10);
vec.push_back(a);
//输出结果为：
constructor
copy constructor
```
从输出结果看，push\_back过程中除了调用一次构造函数，还额外调用了一次复制构造函数，额外调用复制构造函数甚是浪费时间，假如是A a(10000000)，复制起来可是非常耗时的！如何避免额外的复制呢？emplace\_back来帮助你，其函数声明如下：
```
template< class... Args >
void emplace_back( Args&&... args );
```
从函数声明中，我们看到其参数是右值引用，右值引用可以用来干什么？移动元素的所有权！让我们执行下面的代码：
```
vector<A> vec;
A a(10);
vec.emplace_back(std::move(a));
//输出结果为：
constructor
move constructor
```
从输出结果看，此时调用了一次构造函数，和一次移动构造函数，而移动构造函数基本是不耗时的。很明显，使用emplace\_back比push\_back效率更高。

另外，C++11中，上面的代码可以简化为
```
vector<A> vec;
vec.emplace_back(10);
//输出结果为:
constructor
```
从输出结果看，此时只调用了一次构造函数，连移动构造函数都省掉了，这是因为emplace_back把参数10完美转发给A的构造函数，直接构造了一个元素，而这个元素是直接存放在vector容器中的，为了节省一点执行时间，C++11也是拼了。

简单总结以下，push\_back是“复制式”(即调用复制构造函数)的插入元素，而emplace\_back是“移动式”(即调用移动构造函数)的插入元素；

同理，vector::insert、set::insert、map::insert是复制式的插入元素，他们的移动式插入函数是emplace

两个注意事项：

**调用emplace\_back时需要注意，不要把vec.emplace\_back(std::move(a))，错误的写成vec.emplace_back(ai)**，看下面的例子：
```
vector<A> vec;
A a(10);
vec.emplace_back(a);
//输出的结果为：
constructor
copy constructor
```
从输出结果看，此时调用的是复制构造函数而不是移动构造函数，因为传入的参数a不是右值引用，需要先调用a的复制构造函数生成一个副本，然后把副本的右值引用传递给emplace\_back，最终造成vec.emplace\_back(a)等效与vec.push_back(a)。

**当自定义类A没有移动构造函数时，vec.emplace\_back(std::move(a))也等效与vec.push\_back(a)。**
