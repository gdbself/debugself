---
title: Erlang中binary和bitstring的含义和区别
tags:
  - Erlang
url: 120.html
id: 120
categories:
  - Erlang
keywords: Erlang,binary,bitsting
description: 'Erlang中binary和bitstring的含义和区别'
date: 2017-11-17 22:51:02
---

binary是二进制类型，bitstring是位串类型；binary和bitstring都是二进制类型的数据

两者的定义都是
```
<<>>

<<E1,...,En>>

Ei = Value |

Value:Size |

Value/TypeSpecifierList |

Value:Size/TypeSpecifierList
```
每个元素En，可以指该元素占用的位数、字节序、类型等，如 <<111:16/signed-big-integer>>，含义是元素的值为111，在内存中占16位，无符号整型，使用大端字节序存储。

Size表示前一个Value数据存储的位数，默认是8位，也就是一个字节。

TypeSpecifierList可以是以下几种类型及其组合，组合以 - 相连

Type = integer | float | binary | bytes |bitstring | bits | utf8 | utf16 | utf32

默认值是integer。bytes是binary的简写，bits是bitsring的简写

Signedness = signed | unsigned

只有当type为integer时有用，默认是unsigned

Endianness = big | little | native

当type为integer，utf16，utf32，float有用，默认是big

Unit = unit:IntegerLiteral

有效范围是1-256，integer、float和bitstring默认是1，binary默认是8

X=<<15,25,35>> ，默认每一个元素Size为8位(也即一个字节)，等效于<<15:8,25:8,35:8>>，通过byte_size(X)可以得到X在内存中共占用3字节，内存中的内容为0x0F1923

X=<<15,25,35:7>>，shell中显示为<<15,25,35:7>>,X在内存中占用小于3个字节（虽然此时byte_size(X)返回值依旧是3）

X=<<15,25,35:9>>，shell中显示为<<15,25,17,1:1>>,内存中占用大于3个字节，此时byte_size(X)返回值是4

通过上面的例子，得出binary和bitstring的定义：

当X占用内存正好是整数个字节时（即bit位数为8的倍数），X的类型为binary；

当X占用内存不是整数个字节时（即bit位数不是8的倍数），X的类型为bitstring，且binary也是bitstring的一种