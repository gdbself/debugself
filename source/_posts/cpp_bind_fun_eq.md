---
title: 'C++11如何判断std::bind,std::function对象是否相等'
tags:
  - C++11
url: 75.html
id: 75
categories:
  - C/C++
  - C++11
date: 2017-09-18 01:01:37
---

如何判断两个bind对象是否相等  

-------------------

先上结论：无法判断

bind函数返回一个bind对象，这个bind对象具体是什么类型呢？查找下bind的参考文档，比如MSDN中bind函数定义如下

template<class Fty, class T1, class T2, ..., class TN>
    unspecified bind(Fty fn, T1 t1, T2 t2, ..., TN tN);
template<class Ret, class Fty, class T1, class T2, ..., class TN>
    unspecified bind(Fty fn, T1 t1, T2 t2, ..., TN tN);

纳尼？返回值类型为unspecified ？返回值类型未定义！没关系，自己写个Demo来获取bind的返回类型。

void print_add(int i, int j) {
    cout << i + j << '\\n';
}
int main(){
    auto f1 = bind(print\_add, std::placeholders::\_1, std::placeholders::_2);
    auto f2 = bind(print\_add, std::placeholders::\_1, 20);
    cout << typeid(decltype(f1)).name() << endl;
    cout << typeid(decltype(f2)).name() << endl;
    return 0;
}
//VS2015的输出为：
class std::\_Binder<struct std::\_Unforced,void (\_\_cdecl&)(int,int),struct std::\_P
h<1> const &,struct std::_Ph<2> const &>
class std::\_Binder<struct std::\_Unforced,void (\_\_cdecl&)(int,int),struct std::\_P
h<1> const &,int>

上面的Demo中，decltype可以获取表达式的类型，通过decltype我们最终得到了bind返回值类型为std::\_Binder，\_Binder下划线告诉我们这个类是一个内部类，并不开放给开发者![](http://img.baidu.com/hi/tsj/t_0015.gif)。

由于std::\_Binder并没有重载==运算符，所以无法判断两个bind对象是否相等；std::\_Binder也没有提供获取构造bind对象时的函数指针(如无法通过f1获取到构造f1的print_add地址)的接口，所以也不能判断bind对象内部函数指针是否相等。

如何判断两个function对象是否相等
--------------------

function提供了function::target函数获取function内部保存的可调用对象的指针.

void print(int i) {
    cout << i << '\\n';
}
typedef function<void(int)> Callback_Fun;

int main(){
    Callback_Fun f1 = print;
    auto p = f1.target<void(*)(int)>();
    if(*p == print){
        cout<<"true"<<endl;
    }
    else{
        cout<<"false"<<endl;
    }
}
//输出结果
true

需要注意的是，上面的代码中f1.target<void(*)(int)>中指明target的类型是void(*)(int)，这个类型即print的函数指针，如果类型填写错误，target的返回值为空。

int main(){
    Callback_Fun f1 = print;
    auto p = f1.target<void(*)(int,int)>();
    if(p == nullptr){
        cout<<"null"<<endl;
    }
}
//输出结果
null

之所以类型填写错误，target的返回值为空，是因为target函数内部会先获取function内部可调用对象的真正类型，然后对比该类型和f1.target<void(*)(int)>指定的类型是否不一致，不一致时直接返回空指针。 所以，要判断function内部指针是否相等，前提条件是f1.target的类型必须相同！像下面的两个function对象

Callback_Fun f1 = print;
Callback\_Fun f2 = bind(print,std::placeholders::\_1);

虽然f1和f2都是由print函数构造出来的,但是f1和f2是必定不相等的，因为f1的内部target类型是一个函数，而f2的内部target类型是一个bind对象，两者的类型是不一样的，target的返回值必定也是不一样的。 即使两个function对象内部都是bind对象，如下

Callback\_Fun f1 = bind(print,std::placeholders::\_1)
Callback\_Fun f2 = bind(print,std::placeholders::\_1);

尽管f1.target、f2.target类型相同，也可以获取到两者的可调用对象的地址，但是因为f1.target、f2.target的返回值为bind对象，且无法判断两个bind对象是否相等，所以依旧无法判断f1和f2是否相等。

**结论**：只有当function对象内部是函数指针时，才可以判断function对象是否相等，function内部是bind对象或者lambda对象时，都是无法判断function对象是否相等的。 无法判断两个bind，function对象是否相等，如果非要判断，只能自己来判断了，比如自己把构造多个bind对象的函数指针保存起来，然后自己对比函数指针是否相等。