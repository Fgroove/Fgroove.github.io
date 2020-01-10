---
Javalayout: post
title:   "数据处理-OllyDbg"
date:   2019-12-20 08:55:01 +0800
categories: "HWProject"
tag: "malwareAnalysis"

---

* content
{:toc}






# OllyDbg

## 1. 加载恶意代码

OllyDbg可以直接加载可执行文件，甚至加载DLL程序。支持执行DLL中某个函数。

### 1.1 打开可执行文件

默认为情况下，OllyDbg会暂停在程序的入口点位置，即WinMain；如果无法确定入口点位置，会在软件PE头部提供的入口点处中断。

### 1.2 附加调试器到一个运行程序

OllyDbg附加到一个正在运行的进程，附加待调试进城后，OllyDbg会立即暂停这个程序以及它所有线程。

## 2. OllyDbg的接口

OllyDbg的四个窗口

* **反汇编** 显示被调试程序的代码--当前指令前后的一些指令。通常，下一条执行的指令在窗口中高亮显示。空格键修改指令或数据。
* **寄存器** 显示被调试程序寄存器的当前状态。指令修改寄存器值时，寄存器会由黑变红。可以右键修改寄存器的值。
* **栈** 显示堆栈在内存中的当前状态。OllyDbg注释调用一个API之前栈中的存放的参数。
* **内存转储** 显示实时内存转储。`Ctrl + G`可以跳转到任何内存地址。

## 3. 内存映射

内存映射窗口`View->Memory`显示了被调试程序分配的所有内存块。

### 3.1 基地址重定位

基地址重定位是指Windows中的一个模块没有被加载到其预定基地址时发生的情况。

#### 基地址

Windows中的所有PE文件都有一个预定的基地址，它在PE文件中被称为映像基地址。大部分程序预定加载到0x00400000处，是Windows平台下大多数编译器使用的默认地址。

应用程序导入多个DLL，每个DLL都有预定基地址，所以重定位很重要。

#### 绝对地址与相对地址

DLL在`.exe`载入后以任意顺序加载，IDA Pro的话不知道运行时会发生基地址重定位。

## 4. 查看线程和堆栈

恶意代码经常使用多线程。`View->Threads`,调出线程窗口，查看当前线程。该窗口显示线程的内存位置，以及当前活动状态（活动，暂停，挂起）。

OllyDbg是单线程的，需要先暂停所有线程，设置断点后，继续运行程序，确保在一个特定线程内调试。

进程中的每个线程都有自己的栈，可以使用OllyDbg的内存映射，来查看栈中内容。

## 5. 执行代码

| 功能               | 目录         | 热键    | 详述                     |
| ------------------ | ------------ | ------- | ------------------------ |
| Run/Play           | Debug > Run  | F9      |                          |
| Pause              |              |         | 不常用，通常设置断点调试 |
| Run to Selection   | Breakpoint > | F4      | 在到达选择指令前一直运行 |
| Excute till Return | Debug >      | CTRL-F9 | 在当前函数返回时暂停执行 |
|                    |              |         |                          |
|                    |              |         |                          |

如果分析恶意程序期间程序迷失到库代码中，Excute till User Code会很有用。

OllyDbg提供两种单步跟踪方式，即执行一条指令后立即停止运行。

* 单步进入：F7
* 单步跳过：F8

## 6. 断点

汇编窗口按F2添加或移除一个断点。

### 6.1 软件断点

### 6.2 条件断点
