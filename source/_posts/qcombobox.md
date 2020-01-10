---
title: 设置QComboBox的Item自动宽度
tags:
  - QComboBox
  - 自动宽度
url: 113.html
id: 113
categories:
  - QT
keywords: QT,QComboBox,自动宽度
description: '设置QComboBox的Item自动宽度'
date: 2017-10-26 17:22:47
---

问题：如何设置QComboBox控件中Item自动宽度呢

答案：在QT设计器中，设置minimumContentsLength >0,并设置sizeAdjustPolicy 为AdjustToContents，此后当添加的item非常长时，QComboBox会自己变宽


参考资料

[https://forum.qt.io/topic/14676/how-can-i-keep-a-qcombobox-from-changing-width-due-to-contents/13](https://forum.qt.io/topic/14676/how-can-i-keep-a-qcombobox-from-changing-width-due-to-contents/13)

I have actually found a solution. If you set the QComboBox minimumContentsLength property to some small value greater than 0, and the sizeAdjustPolicy to AdjustToContents, the combo box will try to adjust its size, but will fit inside the layout requirements.  
If minimumContentsLength is 0, it will enforce a fixed size to fit all its contents, and the layout will never size it down, as you have noticed.