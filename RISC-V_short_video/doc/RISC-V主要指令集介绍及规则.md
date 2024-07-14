# RISC-V主要指令集介绍及规则

## 推荐资料

RISC-V Reader / RISC-V开放架构设计之道，适合新手阅读。

## 概述

RISC-V的模块化到底是如何实现的呢？

核心部分：RV32I，代表32位字长的整型指令集（Integer），包含了许多整型指令如load指令把数据加载入寄存器中，add sub指令将两个寄存器中的数据求和或求差，store指令将数据从寄存器写回内存中。这个部分是RISC-V的固定部分，RV芯片必须包含并实现这一部分。

其他模块是可选模块，开发者按需实现。如RV32M乘除扩展，RV32F/RV32D包括float和double浮点数处理扩展，RV32A用于一些原子扩展，RV32V用于并行操作的高性能向量扩展，适合用于需要大规模并行数据处理的应用，如机器学习和大数据分析。

除32位字长指令集之外，还有一些扩展版，如RV64M,RV64F,RV64D,用于进行64位字长数据/寄存器操作。

## 指令集结合规则

RV+字长+所有包含的扩展

`RV [32, 64, 128]` `I, M, A, F, D, G, Q, L, C, B, J, T, P, V, N`

比如：我想要实现一个支持I，M，A，D，F扩展的RV芯片，合起来被叫做：RV32IMAFD。IMAFD是一个比较常用的组合，因此也叫做RV32G。

## 参考资料

[RISC-V-Reader-Chinese-v2p12017.pdf (ustc.edu.cn)](http://staff.ustc.edu.cn/~llxx/cod/reference_books/RISC-V-Reader-Chinese-v2p12017.pdf)

[RISC-V Architecture: A Comprehensive Guide to the Open-Source ISA (wevolver.com)](https://www.wevolver.com/article/risc-v-architecture)

[Standard Extensions - RISC-V - WikiChip](https://en.wikichip.org/wiki/risc-v/standard_extensions)