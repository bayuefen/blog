---
title: 【Python】标准库unittest基本应用
date: 2019-04-17 17:36:29
tags:
    - Python
    - unittest
    - 日记
---

[`unittest`](https://docs.python.org/3.7/library/unittest.html)的入门基本应用记录。

概念
`unittest`是`python`自带的标准库之一，是`python`版的`junit`，主要用于维护执行自动化测试框架的用例。

<!-- more -->

## 核心工作原理
核心应有有：`TestCase` `TestSuite` `TestLoader` `TestRunner` `TestFixture` 等构造函数。

- `TestCase`: 测试用例。一个完整的测试单元包括 测试前准备环境的搭建(`setUp`)，执行测试代码(`run`)，以及测试后环境的还原(`tearDown`)。一系列具有完整测试流程的测试单元组成测试用例。
- `TestSuite`: 测试用例合集。多个`TestCase`集合组成`TestSuite`。且`TestSuite`可以嵌套其他`TestSuite`。
- `TestLoader`: 测试用例装载器。主要用于加载`TestCase`值`TestSuite`。通过`loadTestsFrom__()`方法加载`TestCase`，并创建其实例，然后将该实例添加至`TestSuite`中，返回`TestSuite`实例。
- `TestRunner`: 测试用例执行器。执行器中的`run(test)`方法会自动执行`TestCase`、`TestSuite`中的`run(result)`方法，并将执行的测试结果保存至`TextTestResult`实例中，用于统计测试用例执行结果合计。
- `TestFixture`: 测试环境构建与销毁维护。在测试用例运行初始化前使用`setUp()`准备初始环境，在测试用例执行完毕后使用`tearDown()`执行销毁，还原运行测试环境。


参考链接

https://blog.csdn.net/luanpeng825485697/article/details/79459771

