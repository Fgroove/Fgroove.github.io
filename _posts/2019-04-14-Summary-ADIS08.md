---
layout: post
title:  "Automatically Identifying Trigger-based Behavior in Malware"
date:   2019-04-10 09:55:01 +0800
categories: 基于动态的恶意软件分析
tag: 论文调研
---
* content
{:toc}


# Automatically Identifying Trigger-based Behavior in Malware(ADIS'08)

---

## 问题

分析基于触发的恶意行为经常是手工分析，论文提出一种自动化分析的方法。

## 贡献

- 我们开发了混合执行二进制文件的技术，并将它们应用于分析基于触发器的行为。以前关于混合执行的工作需要源代码。对二进制文件进行混合执行可能也没有其他用途。

- 我们在名为MineSweeper的工具中实现我们的想法。特别是，MineSweeper自动：

  a）检测是否存在特定触发类型的基于触发器的行为，

  b）查找触发条件，

  c）查找满足触发条件的输入值，何时可以解决触发条件，以及

  d）向程序提供触发器值，使其显示触发器行为，以便可以在受控环境中对其进行分析。

  在我们的实验中，执行自动分析基于触发器的行为的所有步骤的端到端时间通常不到30分钟。

- MineSweeper不需要源代码，可以处理未经修改的二进制程序。分析二进制文件的能力对于恶意软件分析是一种现实的方法，是绝对必要的。由于我们动态检测代码以便即时执行混合执行，因此我们也能够处理混淆和打包的代码，如我们的实验所示。此外，我们的框架是可扩展的，以适应许多不同的触发器类型。

## 原理

1. 步骤1：当给出恶意程序时，用户**首先选择感兴趣的触发类型**。触发类型可以是**时间，系统事件，网络输入或任何库或系统调用**。我们提供了恶意软件常用的触发器类型列表。用户可以从提供的列表中进行选择，也可以定义自己感兴趣的触发器类型。
2. 步骤2：给定感兴趣的触发器类型，然后我们的方法**迭代地执行混合具体和符号执行**，以探索依赖于触发器输入的不同执行路径并观察基于触发器的行为。特别是，**触发器输入将以符号形式表示**，混合的具体和符号执行在路径沿着路径建立符号表达式和约束。当它根据符号输入命中下一个条件跳转时，它将生成两个路径谓词：一个用于继续真正分支的当前路径，另一个用于继续伪分支的当前路径。因此，路径谓词是触发输入的条件，它使程序执行沿着该路径行进。
3. 步骤3：然后**将两个路径谓词提供给求解器**，以查看是否可以满足每个公式，指示路径是否可行。然后，每个可行的分支将指示一个新的可行路径以进一步探索。然后将可行路径添加到要进一步探索的路径集中。对于每个可行路径，求解器还将分配返回到触发输入以使公式为真，即触发值。
4. 步骤4.a：我们的方法然后**从可行路径集中选择下一个路径**以进一步探索。 然后，该过程返回到步骤2，以沿着所选路径继续混合固定和符号执行。 执行将继续执行，直到它遇到依赖于触发输入的下一个条件跳转，如步骤2中所述。以这种方式，我们可以**强制程序执行任何可行路径**，从而能够根据触发输入迭代地探索不同的执行路径。
5. 步骤4.b：然后我们的方法使用步骤3中求解器返回的触发值具体执行程序，以观察受控环境中基于触发器的行为。

## 设计与实现

### 1. 指定触发器类型

MineSweeper提供了恶意软件常用的触发器类型。

用户自定义触发器类型，也必须指定触发器输入在内存中的存储位置。

### 2. 混合执行引擎

使用的是全系统模拟(QEMU)和动态二进制插桩。

通过hook仿真器，我们的系统会通知在原始程序中执行的每个指令，何时我们插入代码来进行混合执行。

要执行混合执行，对于要在原始程序中执行的每条指令，我们需要插入代码来做两件事：

（1）检查指令是否会读取任何触发输入，如果是，我们需要创建新的符号变量表示触发输入; 

（2）根据指令，具体执行指令（如果所有操作数都是具体的）或符号执行指令（如果至少有一个操作数是符号的）。 

### 3. Solver

论文用的是STP工具。

### 4. 路径选择器

路径选择器将要探索的当前发现的可行路径集作为输入，并输出选择要探索的下一个路径。路径选择器可以使用不同的启发式方法来决定从可行路径集中选择哪条路径。

例如，它可以使用**广度优先搜索**，**深度优先搜索**或其他策略。理想情况下，我们希望有一个策略来帮助我们尽早发现基于触发器的行为。
在我们的方法中，我们的策略是探索尽可能多的依赖于触发输入的条件跳转。因此，我们采用类似BFS的方法，我们将始终尝试探索在重新访问循环体之前从未见过的依赖于触发器的分支。

当MineSweeper遇到一个循环时，它最初会尝试探索循环头的两个分支（循环头是条件跳转，一个分支执行循环体，另一个分支离开循环）。这个模拟执行一次循环。其他循环迭代将添加到路径选择队列的末尾。我们发现这种策略在我们的真实世界示例中快速发现恶意行为方面最有效。

**但是，由于保存，管理和恢复所有状态的大小和复杂性，我们通过简单地多次运行混合执行引擎来模拟此行为。**

### 5. 运行器

Runner将触发值作为输入，并在受控环境中使用触发值执行程序。

在我们的设计中，Runner拦截对指定触发器类型的任何调用，并用给定的触发器值替换返回的答案。

由于每个触发器输入在混合执行中都有一个新的符号变量，因此我们将能够区分哪个触发器值为哪个函数返回提供。例如，求解器可以为触发器类型的函数调用返回的第一次和第二次指定不同的赋值;在这种情况下，Runner将根据相关函数返回的第一次或第二次来提供不同的触发值。在我们的运行示例中，假设求解器输出的时间应该是10:06时的11/9（实际上，求解器将返回值到触发器输入的赋值，例如，指定的字节1-14的值触发类型）。 Runner将拦截GetLocalTime调用并用提供的时间11/9 10:06替换返回的14个字节。

## 优缺点

优：自动化处理。对二进制文件（非源代码）符号执行。

缺：使用符号参数的系统调用很困难；处理任意长度的输入是一个难题；不处理依赖于触发值的间接跳转。

## 启发

* 符号执行部分不知是否可以转换为目前先进的符号执行器，甚至fuzzer。
* 通过符号执行获得可疑路径，在可控环境下强制执行路径。
