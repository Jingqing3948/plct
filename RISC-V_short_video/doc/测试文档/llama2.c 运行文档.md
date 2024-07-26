# llama2.c 运行文档

## 简介

`llama2.c`是一个用纯C编写的Llama 2模型的推理代码项目。极简的、单文件C代码实现，可以加载并推理Llama 2模型。

## 运行

1. 访问 [karpathy/llama2.c: Inference Llama 2 in one file of pure C (github.com)](https://github.com/karpathy/llama2.c) 仓库。

2. 点击 "Open in Colab" 在线上服务器运行，或在本地按照指令克隆代码运行。

   <img src="https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202406150723783.png" alt="image-20240615072306685" style="zoom:67%;" />

3. 克隆 llama2.c 代码。

   <img src="https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202406150724310.png" alt="image-20240615072402258" style="zoom:50%;" />

4. 编译构建可执行文件。

   <img src="https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202406150724613.png" alt="image-20240615072444213" style="zoom:50%;" />

5. 选定模型，三个模型的参数量和生成文本能力逐渐增加。

   ![image-20240615073022429](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202406150730485.png)

6. 给定提示词，设置参数，开始生成后续文本。

   ![image-20240615073306084](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202406150733153.png)