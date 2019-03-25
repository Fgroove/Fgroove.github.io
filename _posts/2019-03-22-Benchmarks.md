---
layout: post
title:  Benchmarks
date:   2019-03-22 8:55:01 +0800
categories: "QEMU-KVM系统"
tag: 系统评估
---
* content
{:toc}


# Benchmarks for Windows

## CineBench
---
CineBench提供了最全面，最值得信赖的CPU基准测试之一。 它使图像渲染成为一项主要由CPU承担的任务 - 并将其与其他实际测试进行比较，以衡量CPU的性能。

这是真实世界：虽然其他基准测试将测试您的整体PC性能或CPU和GPU的组合，但CineBench专门测试CPU的所有可用处理器内核。

运行测试后，您的处理器将按分数进行分级：分数越高，CPU的性能输出就越强。

QEMU(with kvm)的CINEBENCH SCORES: 158pts

```shell
Your System: 
Rendering: 158pts
Rendering (Single Core): 158pts
Multi Processor Ratio: 1.00x
1 Core, 1 Thread @ 2.81 GHz
QEMU Virtual CPU version 2.5+
Windows 7, 64 bit, Professional Edition Service Pack 1 (build 7601)
CINEBENCH Info:
CINEBENCH Version: 15.037 RC83328demo 64 Bit
```

QEMU(no kvm)的CINEBENCH SCORES: 5pts

```shell
Your System: 
Rendering: 5pts
Rendering (Single Core): 5pts
Multi Processor Ratio: 1.00x
1 Core, 1 Thread @ 2.81 GHz
QEMU Virtual CPU version 2.5+
Windows 7, 64 bit, Professional Edition Service Pack 1 (build 7601)
CINEBENCH Info:
CINEBENCH Version: 15.037 RC83328demo 64 Bit
```

## RealBench

RealBench是实际CPU基准测试的另一个例子。 它使用了四个测试，都涉及一些容量的渲染：图像编辑，H.264视频编码，OpenCL和重型多任务处理。

您可以将您的发现上传到RealBench网站，以比较您与其他基准硬件配置的位置。 可能RealBench的最佳方面是它模拟常规课程负载; 没有压力测试将CPU推到最大，以便衡量其性能。 当然，压力测试也是RealBench中的一项可用功能。

RealBench无法在原生TCG模式下运行。

## Microsoft Windows benchmarks
### BAPCo: MobileMark, SYSmark, WebMark
---
**收费**

BAPCo，Business Applications Performance Corporation，是一个非营利性联盟（成立于1991年），其章程旨在为基于流行软件应用程序和操作系统的个人计算机开发和分发一套客观的性能基准。

BAPCo目前的成员包括宏碁，ARCIntuition，ChinaByte，CNET，仁宝电子，戴尔，惠普，日立，英特尔，LC未来中心，联想，微软，西部数据，纬创，三星，索尼，东芝，佐尔。

2011年6月21日，AMD公司宣布，在未能通过SYSmark 2012基准测试后，它已从BAPCo组织辞职[1]。 Nvidia和VIA也在几周后离开了。

### Futuremark: 3DMark, PCMark
#### PCMark: [Result](https://www.3dmark.com/pcm7/1173102#)


```shell
SETTINGS
Default settings used Yes Repeat count3
```

#### 3DMark
破解版 失败 收费才是正道

### PiFast
手动输入参数
### SuperPrime
### Super PI
### UserBenchmark
### Whetstone
### Windows System Assessment Tool, included with Windows Vista and later releases, providing an index for consumers to rate their systems easily
### Worldbench (discontinued)