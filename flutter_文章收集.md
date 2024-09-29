flutter渲染机制三棵树原理：
什么是三棵树？
在Flutter中和Widgets一起协同工作的还有另外两个伙伴：Elements和RenderObjects；由于它们都是有着树形结构，所以经常会称它们为三棵树。

Widget：Widget是Flutter的核心部分，是用户界面的不可变描述。做Flutter开发接触最多的就是Widget，可以说Widget撑起了Flutter的半边天；
Element：Element是实例化的 Widget 对象，通过 Widget 的 createElement() 方法，是在特定位置使用 Widget配置数据生成；
RenderObject：用于应用界面的布局和绘制，保存了元素的大小，布局等信息；

> https://cloud.tencent.com/developer/article/1771780

---
flutter运行原理：
Understanding How Flutter Works: A Comprehensive Guide

---
如何在遮罩层切一个洞
How to cut a hole in an overlay
> https://www.flutterclutter.dev/flutter/tutorials/how-to-cut-a-hole-in-an-overlay/2020/510/

