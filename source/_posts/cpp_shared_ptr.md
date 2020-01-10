---
title: C++11智能指针shared_ptr、weak_ptr、unique_ptr用法详解
url: 89.html
id: 89
categories:
  - C/C++
keywords: C++11,智能指针,shared_ptr,weak_ptr,unique_ptr
description: 'C++11智能指针用法教程'
date: 2017-09-24 23:02:24
tags:
---

## 什么是智能指针

智能指针，是一种特殊的指针，它可以自动释放new出来的指针，不需要程序员手动调用delete即可释放；

## 智能指针的原理

智能指针是一个包装类，内部包装了真正的数据指针（即new出来的内存地址）和一个引用计数。

当构造智能指针时（即智能指针的构造函数或者复制构造函数被调用时），引用计数会加1；

当析构智能指针时(即析构函数被调用时)引用计数会减1，并判断引用计数是否为0，为0时调用delete删除真正的数据指针；

智能指针重载了->操作符，当调用智能指针的->时，内部会转换为真正的数据指针的解引用；所以智能指针使用起来，和使用普通指针基本一致。

C++11的定义了多种智能指针，他们都包含在#include <memory>头文件中

## shared_ptr的用法

从名字上看shared\_ptr是共享指针，意味着我们可以复制shared\_ptr，复制出的智能指针指向同一个内部数据指针（即被智能指针包装的真正数据）。

### 构造shared_ptr

有多种方法可以构造shared_ptr，下面代码中有4种构造方式：
```
int *p = new int(1);
shared\_ptr<int> sp1(p);// 通过普通指针p构造shared\_ptr
shared_ptr<int> sp2 = sp1;// 复制，sp2也指向p
shared\_ptr<int> sp3 = make\_shared<int>(2);// 通过make\_shared构造，make\_shared内部调用了new int(2);
sp3.reset(new int(3));// 通过reset重置内部数据指针
sp3.reset();// 通过reset重置内部数据指针为空
```
注意，不能构造两个独立的智能指针，且指向同一个内部数据指针
```
int *p = new int(1);
shared_ptr<int> sp1(p);
shared_ptr<int> sp2(p);
```
上述代码中，p最终会被释放两次，从而造成错误！当需要sp2也包装p时，请使用sp2=sp1；

### 判断智能指针是否为空

可以直接用if、!操作符判断智能指针是否为空
```
shared_ptr<int> sp;
if(!sp){
    cout<<"sp is null"<<endl;
}
sp.reset(new int(0));
if(sp){
    cout<<"sp is not null"<<endl;
}
//输出结果
sp is null
sp is not null
```
### 判断智能指针是否相等

直接用==操作符判断智能指针是否相等，因为智能指针类内部重载了==操作符
```
shared\_ptr<int> sp1=make\_shared<int>(3);
auto sp2 = sp1;
if(sp2 == sp1){
    cout<<"true"<<endl;
}
else{
    cout<<"false"<<endl;
}
//输出结果
true
```
### 通过*引用智能指针

智能指针重载了*操作，通过*可以获取智能指针的内部数据指针
```
shared\_ptr<int> sp1 = make\_shared<int>(1);
cout<<*sp1<<endl;
//输出结果
1
```
### 通过get引用智能指针

get返回了智能指针的内部数据的指针
```
class Foo{
public:
    Foo(int i,int j){
        this->i=i;
        this->j=j;
    }
    void print(){cout<<i<<j<<endl;}
private:
    int i;
    int j;
};
shared\_ptr<Foo> sp2 = make\_shared<Foo>(1,2);
Foo *pf = sp2.get();
pf->print();
//输出结果
12
```
### 通过->引用智能指针的成员

智能指针重载了->操作，通过->可以直接引用智能指针的内部数据指针
```
shared\_ptr<Foo> sp2 = make\_shared<Foo>(1,2);
sp2->print();
//输出结果
12
```
注意，上述有多种引用智能指针的方式，其中的区别如下：

* 调用shared_ptr类本身的函数时，用.操作符，如sp2.reset();
* 调用shared_ptr内部数据的函数时，用->操作符，如sp2->print();

**use_count()获取有多少个智能指针共享同一个内部数据指针**
```
shared\_ptr<int> sp1 = make\_shared<int>(1);
cout<<sp1.use_count()<<endl;
auto sp2 = sp1;
cout<<sp1.use_count()<<endl;
//输出结果
1
2
```

## weak_ptr的用法

从名字上看，weak\_ptr是弱指针，即它比shared\_ptr要弱一点。weak\_ptr可以看做shared\_ptr的助手，weak\_ptr要和shared\_ptr配套一起使用。当创建一个weak\_ptr时，要用一个shared\_ptr来初始化它。

我们知道，复制shared\_ptr是会增加内部数据的引用计数，但是复制weak\_ptr时，以及由shared\_ptr构造weak\_ptr时，是不会增加引用计数的；且weak\_ptr没有重载*、->操作符，所以不能通过*、->操作符操作智能指针的内部数据，这就是weak\_ptr弱的原因吧，汗。

因为weak\_pt不增加引用计数，我们可以任意构造weak\_ptr，任意释放weak\_ptr，却不会影响智能指针中内部数据的释放(内部数据何时释放，只取决于shared\_ptr)。那么weak\_ptr有什么用呢？weak\_ptr可以用来监看shared_ptr：

* weak\_ptr::use\_count()查看有多少个shared_ptr共享同一个内部数据  
* weak\_ptr::expired判断shared\_ptr是否有效，即shared_ptr内部数据是否被释放

weak\_ptr是否可以监看shared\_ptr中的内部数据呢？因为weak\_ptr是弱指针，所以不能直接访问，但是可以通过weak\_ptr::lock间接访问。
```
weak_ptr::lock
```
weak\_ptr::lock返回构造weak\_ptr的shared\_ptr，当shared\_ptr已经被释放时，返回的是空shared\_ptr；注意，因为weak\_ptr::lock返回了shared\_ptr，而shared\_ptr会增加引用计数，进而影响内部数据指针的释放，这也是lock的含义所在，想通过weak\_ptr访问shared\_ptr中的内部数据，需要先lock，返回一个shared_ptr，这相当于把weak\_ptr“转换”为shared\_ptr，然后通过shared_ptr随便访问去吧。
```
int main()   
{   
    std::weak_ptr<int> wp0;   
    std::cout << "wp0.expired() == " << std::boolalpha   
        << wp0.expired() << std::endl;   
  
    std::shared_ptr<int> sp1(new int(5));   
    std::weak_ptr<int> wp1(sp1);   
    std::cout << "*wp1.lock() == "   
        << *wp1.lock() << std::endl;   
  
    std::weak_ptr<int> wp2(wp1);   
    std::cout << "*wp2.lock() == "   
        << *wp2.lock() << std::endl;   
  
    return (0);   
 }
// 输出结果
wp0.expired() == true  
*wp1.lock() == 5  
*wp2.lock() == 5
```

## unique_ptr的用法

unique\_ptr是一个独占的智能指针,即unique\_ptr不支持复制,但是支持通过move转移内部指针
```
unique_ptr<T> myPtr(new T);      // ok
unique_ptr<T> otherPtr = myPtr;    // 编译错误
unique_ptr<T> otherPtr = move(myPtr);    // ok
```
同shared\_ptr,unique\_ptr也不能多个unique_ptr指向同一个内部数据指针；
```
int *p = new int(0);
unique_ptr<int> up1(p);
unique_ptr<int> up2(p);// 错误，up2和up1都指向了p，将来p会被释放两次，造成错误
```

## 智能指针的使用场景

分享几种工作中使用到智能指针的场景。

### 观察者模式

现在有一个设备模块，该模块从设备获取一条条数据（这里用Record表示一条数据)，并通过观察者模式把Record分发给所有(观察了该数据的)观察者。观察者一般都是视图，如视图A得到数据后，通过表格显示数据的内容，而视图B上得到数据后，通过趋势图显示数据的内容。

当用户关闭了所有视图，意味没有视图再使用数据Record了，这时就可以释放掉Record；但是只要有一个视图在使用record，就不能释放Record。

如何管理Record的释放呢？这种情况使用智能指针，可以做到所有视图关闭后，自动释放Record。

### 工厂模式

假如你编写了一个工厂类，它提供一个接口，根据配置产生各种对象(内部调用new新建对象)。

由于该类很NewBee，它被封装为动态库提供给其他同事，当其他同事调用动态库得到新建对象后，新建对象将来由谁负责释放呢？如果没有统一且明确的沟通确认，很容易出现双方都忘记释放新建对象，或者同一个新建对象被双方都释放了一次的情况！

这时候你甩出了一个智能指针，然后宣布：大家都不用关心谁来释放了，让指针自己释放去吧。

### 避免代码发生异常时的内存泄露

如下的代码：
```
void foo(){
try{
    int *p = new int(0);
    do_something(p);
    delete p;
}
catch(...){
}
}
```
如果do\_something抛出异常，delete p是不会被执行的，从而造成内存泄露。使用智能指针的话，即使do\_something发生异常，在栈上申请的局部变量依旧会被销毁，当指针指针被销毁时，它的析构函数会自动释放内存。

## 是否所有使用场景都可以用智能指针代替普通指针

非也，比如某个时刻，你发现程序内存占用非常大，你想手动delete释放之前申请的内存空间，如果你使用了智能指针，因为智能指针的释放是依赖智能指针的析构函数，但是我们又不能手动调用析构函数，这意味着你无法手动释放智能指针，这种场景中，就需要使用普通指针。