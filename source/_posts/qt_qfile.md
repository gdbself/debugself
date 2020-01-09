---
title: 'QT QFile::remove失败/无效的解决方法'
tags:
  - QT
url: 91.html
id: 91
categories:
  - QT
date: 2017-09-27 15:08:31
---

windows上，QT中执行QFile::remove(filePath);总是失败，无法删除文件

经过一番折腾，终于发现是因为文件是只读的，解决方法：

QFile::setPermissions(filePath,QFile::ReadOther | QFile::WriteOther);

QFile::remove(filePath);

上述方法可以正确删除只读文件。