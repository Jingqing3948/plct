# 短视频剪辑教学2：AI克隆配音使用教学

研究了一天累鼠我了。

配音一般是教学视频中不可或缺的元素，配合字幕将信息直接呈现给观众。

目前视频配音我们主要尝试两种方式，人工配音和AI配音。优劣对比如下：

|      | 人工配音   | AI配音                                     |
| ---- | ---------- | ------------------------------------------ |
| 优点 | 感情丰富   | 清晰度高，快捷方便，已经可以很精确模仿人声 |
| 缺点 | 花费时间长 | 感情略显平淡；部分词语念不准确             |

所以我觉得更方便的处理方式是使用AI配音批量配音后，对于部分不妥当的配音使用人工配音替换即可。

本文主要是对于剪映AI克隆配音的使用方法介绍总结。

## 软件准备

剪映5.x版本（旧版本剪映克隆配音效果一致且免费；6.x新版本剪映隆重推出SVIP，克隆配音功能按字收费，若组里有富哥富姐可vivo50看看实力）。

不过新版本项目的剪映无法使用旧版本打开，比如我之前有一个编辑了一半的6.0版本的项目，想用5.5版本打开重新配音是做不到的。只能重新创建一个项目了。

下载链接：https://pan.baidu.com/share/init?surl=cPgxqhDLbv3DxNUhdGcJGg&pwd=qiji

## 克隆流程

1. 打开项目，选中想要配音的字幕。

![image-20240730190048248](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407301900415.png)

2. 点击“朗读”-“克隆音色”，这里显示限免，说明版本正确。

![image-20240730190138074](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407301901109.png)

3. 点击+号创建克隆音色，弹出一个窗口，里面有一段需要念的文字，录制朗读文字。注意，克隆音色只根据你的这一段音频录制，所以要尽可能地清晰、有感情地朗读。

![image-20240730190232069](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407301902125.png)

4. 完成录制后，在克隆音色里就可以找到刚刚录制的音色，选中后点击“开始朗读”按钮即可生成音频。

## 剪映字幕导出

对于demo视频一般都是交给张老师配音，所以比较方便的是导出视频文件和 srt / lrc 字幕文件，有字幕文件配音起来会比较方便。

不过剪映里直接插入的文字是无法导出为配音的，比如我新建了一个文本块，放到轨道上，这个文本块（可能是被判定为图片了？很奇怪）无法导出为字幕。

想要导出字幕，就需要以字幕的形式插入字幕，不能新建文本。怎样插入字幕？

点击“智能字幕”-选中“文稿匹配”-输入一段字幕-点击确定插入。

![image-20240730191436656](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407301914692.png)

![image-20240730191546807](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407301915848.png)

这样插入的文本才是“字幕”，非常奇葩。或者直接导入本地字幕文件也可以。

![image-20240730191626352](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407301916387.png)

选中这些字幕，可以看到窗口里有“字幕选项”可以批量管理所有字幕，说明这个文本可以被成功识别为字幕。我们可以在这个窗口里批量编辑字幕内容，快捷添加删除字幕，也可以在文本窗口里调整字幕样式。

![image-20240730191719627](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407301917666.png)

完成字幕编辑后点击导出，勾选字幕导出，就可以获得字幕文件。

![image-20240730191818770](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407301918824.png)

## 问题解决

### 1. 部分词语朗读方式错误

比如”milkv"，可能会读作“秒克呜”。拼音和英文没法很好区分。

解决方式是灵活使用谐音字或空格断开，比如“milk v"就可以获得正确的朗读效果；或者使用中文”秒克微“替代，完成朗读后取消勾选”朗读随文本更新“，再修改字幕为正确内容。

如果使用中文导致发音变长（如：RISC-V 我通常使用“瑞斯克five”，这个“克”的发音就有点长），可以适当裁剪音频，获得“瑞思k five”的效果。

### 2. 字幕导出选项是灰色的，无法导出字幕？

这说明此项目中的文本不是“字幕”格式。可以参考前面的“剪映字幕导出”了解如何插入“字幕”格式的文本以及导出。

剪映这个问题做的不太好，很怪。