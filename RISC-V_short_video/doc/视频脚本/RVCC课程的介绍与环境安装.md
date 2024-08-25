# RVCC课程介绍与环境安装

RVCC（RISC-V C Compiler）课程是一个基于RISC-V架构的编译器构造教程。

该课程是由GitHub用户@sunsahce和@ksco在Rui开发的chibicc项目基础上进行的修改，原始的chibicc是一个为X86架构设计的迷你编译器。RVCC项目通过将chibicc改写为支持RISC-V 64架构，并加入了大量中文注释，使学习者能够逐步理解编译器的工作原理。

该课程以316个commit为教学单元，每个commit对应一个具体的学习阶段，帮助学习者逐步构建一个功能完整的编译器。每个阶段都提供详细的注释和说明，使学习者可以深入理解每一步的设计和实现。

## RVCC环境安装指南

为了搭建RVCC的开发环境，学习者需要进行以下步骤：

1. **克隆RVCC仓库**：首先，用户需要在他们的开发环境中克隆RVCC的GitHub仓库。可以使用以下命令：

   ```
   bash复制代码git clone https://github.com/sunshaoce/rvcc.git
   cd rvcc
   ```

2. **构建编译器**：进入项目目录后，可以使用`make`命令进行构建。对于使用CMake的用户，可以按以下步骤进行：

   ```
   bash复制代码cmake -Bbuild .
   cd build/
   make
   ```

3. **安装RISC-V工具链**：RVCC需要RISC-V工具链来进行编译和测试。用户需要根据他们的操作系统，安装相应的RISC-V工具链(

   [GitHub](https://github.com/sunshaoce/rvcc)

   )。

完成以上步骤后，学习者就可以开始根据课程的指导，逐步实现一个功能完整的RISC-V编译器。

下节课我们更深入地介绍rvcc的内容。