---
title: C++11 mutable关键字用法详解
tags:
  - C++11
  - mutable
url: 97.html
id: 97
categories:
  - C/C++
keywords: C++11,mutable
description: 'C++11 mutable关键字用法详解'
date: 2017-10-01 00:14:24
---

C++中const关键字表示“不可变的”，C++11引入mutalbe关键字，表示是“可变的，易变的”，引入mutalbe关键字就是为了突破const的限制.

下面总结了三种mutalbe的使用场景。

**场景一**
```
int main()
{
    const struct
    {
        int n1;
        mutable int n2;
    } x = {0, 0};      // const object with mutable member
 
    //  x.n1 = 4; // error: member of a const object is const
    x.n2 = 4; // ok, mutable member of a const object isn't const
}
```
上述代码中x为const变量，正常情况下是不能修改x以及x的成员的，通过mutable ，可以顺利修改x的成员n2了

**场景二**

按引用传递参数时，为了防止参数被修改，经常使用const来限制参数；
```
class Foo {
public:
    void Print() const {    // 使用const声明Print不会修改类成员变量
        cout<<"Print"<<endl;
       count++;           // 此处却需要修改成员变量
    }
private:
    mutable int count= 0;   // 使用mutable声明变量，否则编译错误
};
void FooTest(const Foo &foo){
    foo.Print();
}
```
上述代码中，如果缺少mutable ，是无法编译通过的，添加mutable 后可以顺利修改count的值了

**场景三**

用在Lambda表达式中，突破不能修改按值捕获的变量的限制，见[《C++11 Lambda表达式（匿名函数）用法详解》](/2017/10/01/cpp_lambda/ "C++11 Lambda表达式(匿名函数)用法详解")