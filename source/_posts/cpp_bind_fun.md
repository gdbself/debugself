---
title: c++11 function、bind用法详解
tags:
  - bind
  - C++11
  - function
url: 86.html
id: 86
categories:
  - C/C++
  - C++11
date: 2017-09-20 22:51:18
---

std::function用法详解

function用来保存C++中各种可调用对象，什么是可调用对象呢？简单的说，就是支持()操作的，最常见的就是函数、以及重载了operator()的类对象，还有C++11中的lambda表达式，bind表达式等都是可调用的对象。

定义一个function对象后，通过function()即可调用function中保存的可以调用对象，所以

function的功能和回调函数非常类似，下面是一个回调函数的例子，通过回调函数来动态修改函数的行为：我们先定义一个Foo类，并打印出Foo的num成员，但是具体如何打印是通过回调函数动态完成的。

typedef void(*Callback_Fun)(int);

class Foo {
public:
    void Print() {
        if (print\_fun)print\_fun(num);
    }
    void SetPrintCb(Callback_Fun cb) {
        print_fun = cb;
    }
private:
    Callback\_Fun print\_fun = nullptr;
    int num = 10;
};

// 原样打印
void print_single(int i) {
    cout << i << '\\n';
}

// 双倍打印
void print_double(int i) {
    cout << i * 2 << '\\n';
}

int main(){
    Foo foo;
    // 原样打印
    foo.SetPrintCb(print_single);
    foo.Print();
    // 双倍打印
    foo.SetPrintCb(print_double);
    foo.Print();
}
//输出结果:
10
20

print\_single函数只是简单的把数值原样的打印出来，当print\_single被设置为打印的回调函数时，foo的成员num被原样打印出来了；

而print\_double函数会把数值双倍后再打印，当print\_double被设置为打印的回调函数时，打印出的结果是num的双倍；

通过设置不同的回调函数，我们动态修改了打印的行为!

把上面的回调函数修改为使用function，改动非常简单：

#include <functional>
//typedef void(*Callback_Fun)(int);
typedef function<void(int)> Callback_Fun;

function<void(int)>：function在<>内声明回调的类型，其中void是函数的返回值类型，int是参数类型。

只需要修改回调的类型声明，其他代码不用做任何修改，重新编译后，代码可以正确运行，输出结果也正确！

至此，除了回调的类型声明不同，function和回调函数的用法是完全一样的！而且，function的功能比回调函数更强大，function还可以保存lambda表达式，用法如下：

#include <functional>
typedef function<void(int)> Callback_Fun;
int main(){
    Foo foo;
    foo.SetPrintCb( \[\](int i){ cout << i << '\\n'; });
    foo.Print();
   return 0;
}
//输出结果:10

通过lambda表达式，foo.SetPrintCb的代码更简洁了。

而function最最重要的功能是，保存bind表达式。

std::bind用法详解

bind可以根据当前已有的可调用对象，构造出一个新的可调用对象，有了bind，我们可以实现“动态生成新的函数”的功能。

由普通函数构造bind对象

我们使用前面的print_single来构造bind对象，代码如下：

typedef function<void(int)> Callback_Fun;
int main(){
    Foo foo;
    foo.SetPrintCb(bind(print\_single,std::placeholders::\_1));
    foo.Print();
    return 0;
}

//输出结果:10

上述代码中，bind(print\_single,std::placeholders::\_1)构造了一个bind对象，这个bind对象作为function的参数来构造function对象，最终bind对象被保存到中function对象中；

bind的第一个参数print_single，是用来构造bind对象的普通函数；

bind的第二个参数std::placeholders::\_1，是占位符，它占谁的位置呢？它对应了print\_single中的第一个参数的位置，同时也对应了调用print\_single时第一个参数，在上面的例子中，我们通过调用print\_fun最终调用了print\_single，所以占位符也对应了调用print\_fun时的实参num

void Print() {
    if (print\_fun)print\_fun(num);
}

通过placeholders::\_1占位符，告诉bind当调用函数时，把调用函数时的第一个参数(即print\_fun的num)传递给print_single的第一个参数。

函数有多个参数时，请同理使用placeholders::\_2、placeholders::\_3等

通过修改普通函数来构造bind对象

使用bind来构造function对象

foo.SetPrintCb(bind(print\_single,std::placeholders::\_1));

和直接通过函数构造function对象

foo.SetPrintCb(print_single)

是等效的，那直接使用函数构造function对象即可，为什么还要多此一举使用bind呢？因为bind的功能更强大，它可以修改已有的函数。假如我们已经编写好下面的函数

void print_add(int i,int j) {
    cout << i+j << '\\n';
}

现在我想直接复用print\_add，而不想再另外写print\_single(浪费代码啊~~~)，这时候就必须使用bind了

foo.SetPrintCb(bind(print\_add,std::placeholders::\_1,0));

由于print_add需要两个参数，在上面的bind中，直接把第二个参数固定为0，等效于

void print_add(int i,int j=0) {
    cout << i+0 << '\\n';
}

这样我们省去print\_single的代码，同时完成了和print\_single一样的效果

由类成员函数构造bind对象

bind最常用的功能之一，是由类成员函数构造bind对象；想想看，如何由类成员函数(非static成员函数)构造回调函数？答案是很难，而通过bind，却可以很容易做到。

class MyTest{
public:
    void print(int i) { cout << i << '\\n'; }
};
int main(){
    Foo foo;
    MyTest test;
    foo.SetPrintCb(bind(&MyTest::print,&test,std::placeholders::_1));
    foo.Print();
    return 0;
}

//输出结果:10

上面代码中：

bind的第一个参数&MyTest::print，指明要绑定的是MyTest的类成员函数print；

我们知道，要调用类的成员函数，必须要有类的实例，所以bind的第二个参数&test，指明了要绑定的类实例，即test这个实例；

bind的第三个参数std::placeholders::_1，对应了MyTest::print的第一个参数。

结论：std::function、std::bind结合起来使用，完成了类似回调函数的功能，但是比回调函数更灵活，扩展性更强。