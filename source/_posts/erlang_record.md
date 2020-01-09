---
title: Erlang中记录Record通过模式匹配为变量赋值
tags:
  - Erlang
  - Record
url: 116.html
id: 116
categories:
  - Erlang
keywords: Erlang,Record
description: 'Erlang中记录Record的一种特殊技巧，通过模式匹配为变量赋值'
date: 2017-10-27 12:04:25
---

在定义Record时，可以指定默认值，比如
```
record(post, {title=Dafault1, slug=Dafault2, body, author}).
```
但是下面的代码，是通过模式匹配，为Title、Slug赋值，而不是为title和slug指定默认值，这两者意思完全相反啊，没想到啊没想到。
```
%% record(post, {title, slug, body, author}).
Post = #post{title = "Pattern Match in Erlang",
             slug = "pattern-match-in-erlang",
             body = "Bla bla bla...",
             author = sloger}.
#post{title = Title, slug = Slug} = Post.
Title.                                % "Pattern Match in Erlang"
Slug.                                 % "pattern-match-in-erlang"
```
  

参考资料

http://www.jb51.net/article/59401.htm
