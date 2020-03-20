---
title: 关于言
type: docs
bookToc: false
---

# 关于"言"

言(Yan)是一款面向编译原理教学的实践平台。该项目致力于改善"初次接触编译原理课程的同学"编写第一个编译器&解释器时的编程体验，并为学生与老师提供一个功能较为完善的自动评测系统。

该项目包含数个子项目:

1. [Yan Foundation](https://github.com/yan-lang/yan)(Working in Progress)是一个编写编译器&解释器的框架，它封装了许多编写任意语言编译器时都需要的一些实用模块，如命令行参数处理，文本IO，交互式命令行(REPL)，格式化输出工具和出错处理等。通过该框架，学生能够在学习完理论知识后，结合框架编写少量代码，即可迅速完成一个“比较那么回事”的一个编译器ˉ
2. [Yan Language](https://github.com/yan-lang/yan)(Working in Progress)是为了阐述Foundation用法设计的一个微型编程语言，我们利用Foundation框架为Yan语言编写了一个简易的编译器和解释器。

3. [Token util](https://github.com/yan-lang/token-util)是一个协助老师为词法分析器生成统一的Token类型的工具。

该项目目前正在开发之中，目前已经基本完成词法以及语法分析器框架部分的开发，欢迎下载我们的Beta版本进行体验。

