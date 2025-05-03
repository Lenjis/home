---
title: "VSCode ARM开发解决 Identifier “XXX” undefined问题"
tags: [VSCode, ARM, CN]
style: border # fill / border
color: info # primary / secondary / success / danger / warning / info / light / dark
description: 
---

最近在写飞行控制课程代码时，VSCode中会大量报错
![](https://lenjis.github.io/assets/images/Identifier_undefined.png)
但是用右键转到定义，又可以正常跳转，且项目可以正常编译。

查找网上的解决方案大多是解决代码本身的问题，但是编译器没有给出类似的报错，说明是`Intellisence`出了问题。

打开`C/C++: Edit configurations (UI)`，在默认的`Win32`下新建一个Configuration，`IntelliSense mode`选择`windows-clang-arm`，然后在工具栏选择对应的`Configuration`，问题解决。
![](https://lenjis.github.io/assets/images/configuration.png)

如果右键也无法找到定义，需要修改`C/C++`插件的`include path`设置，添加自己项目中的库文件目录。
