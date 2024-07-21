## 前言

OpenCV是一种开源的计算机视觉和机器学习软件库，旨在提供一组通用的计算机视觉工具。它用于图像处理、目标识别、人脸识别、机器学习等领域，广泛应用于计算机视觉任务。

OpenCV-Mobile是OpenCV库的轻量版本，专为移动平台（Android、iOS和ARM Linux）而设计。该项目旨在在移动设备上支持计算机视觉、机器学习和图像处理应用，使开发者能够在移动平台上利用OpenCV的功能。

> ChatGPT: 如果OpenCV是一本巨大的百科全书，提供各种计算机视觉工具，那么OpenCV-Mobile就像是一本精简的手册，专注于在移动设备上提供基本的视觉功能。

opencv-mobile 能直接享受上游的 RVV 优化. RISC-V 扩展集，在汇编层面上进行并行运算，加速处理。

我们本次实验期望：在 MilkV 开发板上运行 opencv-mobile，加载图片、缩放、保存图片。

**非常感谢opencv-mobile原作者[nihui (github.com)](https://github.com/nihui)！我们本次实验主要是学习nihui老师的实验实现。**

**原实验地址：[opencv-mobile (迷你版opencv库)在 milkv-duo 上的移植和应用 - CV1800B(Duo 中文论坛) - Sophgo](https://forum.sophgo.com/t/opencv-mobile-opencv-milkv-duo/217)，侵删！**

## 实验环境

- ubuntu 虚拟机或 WSL
- milkV 开发板
- 2GB+ SD卡（用于烧录运行 MilkV 系统）
- USB-TypeC 烧录线

## 实验步骤

步骤主要如下：

1. 下载 opencv-mobile 程序包并进行基础配置。
2. 下载 MilkV 工具链，编译生成 MilkV 平台上的 opencv-mobile 目标文件。
3. 导入目标文件和待处理图片到开发板上，运行程序进行图片缩放。
4. 获取并查看最终处理结果。

### MilkV 交叉编译工具链烧写

首先，我们在本地 ubuntu 编译 opencv-mobile 程序。程序的目标运行地点在 MilkV 上，在 x86 架构上编译得到的目标文件并不能直接在 RISC-V 架构上运行，因此我们需要 通过MilkV 的专有工具链，在本机上交叉编译。

下载地址：https://sophon-file.sophon.cn/sophon-prod-s3/drive/23/03/07/16/host-tools.tar.gz

下载完成后解压到 ubuntu 系统目录中，路径不要包括中文。

解压完成后，我们修改 ~/.bashrc 文件，在结尾添加如下语句，添加环境变量：

```shell
$ export RISCV_ROOT_PATH=$HOST_TOOL_PATH/gcc/riscv64-linux-musl-x86_64
```

`$HOST_TOOL_PATH` 是你个人解压的 host-tools 路径，比如我的路径是 `/home/jingqing3948/tools/host-tools`

保存文件后通过 `source ~/.bashrc` 或重新打开终端使环境变量生效。

### 下载配置 opencv-mobile

接下来我们下载 opencv-mobile 源码程序，配置编译选项并编译。

```shell
$ git clone https://github.com/nihui/opencv-mobile.git
$ cd opencv-mobile

$ wget -q https://github.com/opencv/opencv/archive/4.8.1.zip
$ unzip -q opencv-4.8.1.zip
$ cd opencv-4.8.1

$ truncate -s 0 cmake/OpenCVFindLibsGrfmt.cmake
$ rm -rf modules/gapi
下面三条指令略有不同。旧版本中 .patch 文件确实位于 opencv-mobile 文件夹中，新版本 .patch 文件整合到了 opencv-mobile/patch/文件夹中，需要自行修改。
$ patch -p1 -i ../opencv-4.8.1-no-rtti.patch
$ patch -p1 -i ../opencv-4.8.1-no-zlib.patch
$ patch -p1 -i ../opencv-4.8.1-link-openmp.patch
$ rm -rf modules/highgui
$ cp -r ../highgui modules/

$ mkdir build
$ cd build
$ cmake -DCMAKE_TOOLCHAIN_FILE=../../toolchains/riscv64-unknown-linux-musl.toolchain.cmake -DCMAKE_C_FLAGS="-fno-rtti -fno-exceptions" -DCMAKE_CXX_FLAGS="-fno-rtti -fno-exceptions" -DCMAKE_INSTALL_PREFIX=install -DCMAKE_BUILD_TYPE=Release `cat ../../opencv4_cmake_options.txt` -DBUILD_opencv_world=OFF -DOPENCV_DISABLE_FILESYSTEM_SUPPORT=ON ..
$ make -j16
$ make install
```

> nihui 老师：特别提一下，编译过程中会看到 cmake 成功检查并开启了 riscv vector 支持，这对 milkv-duo 芯片是有加速效果的
>
> ```txt
> -- Performing Test HAVE_CPU_RVV_SUPPORT (check file: cmake/checks/cpu_rvv.cpp)
> -- Performing Test HAVE_CPU_RVV_SUPPORT - Success
> ```
>
> opencv-mobile 附带的 `toolchains/riscv64-unknown-linux-musl.toolchain.cmake` 全局开启了 c906 内核相关的编译参数，并设置为 c906 优化，这些参数会自动应用在 opencv-mobile 所有模块的编译中，提供最佳性能。

### 使用 opencv-mobile milkv-duo 优化版实现图片缩放

在克隆的 `opencv-mobile/test/` 里有一个示例程序，加载、缩放、保存图片。

```shell
jingqing3948@jingqing:~/Develop/opencv-mobile/test$ ls 
build  CMakeLists.txt  main.cpp
```



![image-20231116225842183](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212246264.png)

程序自动查找当前目录下的 `in.jpg`，将其大小重置后输出保存文件 `out.jpg` .

我们在 test 文件夹下新建终端窗口，进行该项目的编译，**注意修改 `$HOST_TOOL_PATH` 为自己的路径。**

```shell
$ export RISCV_ROOT_PATH=$HOST_TOOL_PATH/gcc/riscv64-linux-musl-x86_64

$ cd opencv-mobile/test

$ mkdir build
$ cd build
$ cmake -DCMAKE_TOOLCHAIN_FILE=../../toolchains/riscv64-unknown-linux-musl.toolchain.cmake -DCMAKE_BUILD_TYPE=Release -DOpenCV_DIR=/home/nihui/dev/opencv-mobile/opencv-4.8.0/build/install/lib/cmake/opencv4 ..
$ make
```

成功编译后，在 build 文件夹里可以看到可执行程序 `opencv-mobile-test`

```shell
jingqing3948@jingqing:~/Develop/opencv-mobile/test/build$ ls
CMakeCache.txt  CMakeFiles  cmake_install.cmake  Makefile  opencv-mobile-test
```

(可选) 编译出来的二进制可以用交叉编译工具链中的 strip 进一步瘦身.

```shell
$ $HOST_TOOL_PATH/gcc/riscv64-linux-musl-x86_64/bin/riscv64-unknown-linux-musl-strip opencv-mobile-test
```

从工具链里找到 `$HOST_TOOL_PATH/gcc/riscv64-linux-musl-x86_64/sysroot/lib64v0p7_xthead/lp64d/libgomp.so.1.0.0` ，复制文件。我们要上传到开发板上的程序文件就是 `opencv-mobile-test` 和 `libgomp.so.1.0.0` 这两个。

以及，准备一张需要缩放的图片。大小尽量不要超过 500KB。重命名为 in.jpg

### 在 MilkV 上成为点灯大师

首先我们给 sd 卡烧写 MilkV 系统，并将 sd 卡插入 MilkV，通过数据线连接电脑。

系统烧写使用的工具可以从指南中根据指引下载，我们的工具压缩包中也包含了官方镜像和 balenaEtcher_win 安装程序。

烧写系统指南：[启动 Duo | Milk-V (milkv.io)](https://milkv.io/zh/docs/duo/getting-started/boot)

安装 RNDIS 驱动指南：[Install RNDIS driver | Milk-V (milkv.io)](https://milkv.io/zh/docs/duo/getting-started/windows-rndis-dirver)

打开电脑终端，通过 ssh 方式连接到开发板。*opencv-mobile 程序编译部分是在 ubuntu 上跑的，ssh 连接则不局限于什么系统，只要能将文件上传到开发板即可。*

```shell
> ssh root@192.168.42.1
```

密码：milkv

成功登录如下图：

![1700148302785](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212246209.png)

可以查看系统信息：

```shell
[root@milkv]~# uname -a

[root@milkv]~# df -h

[root@milkv]~# free
```

![1700148587637](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212246379.png)

当前路径位于 `/root/` 文件夹下。我们将输入图片、运行程序均拷贝到此文件夹下运行。首先，我们尝试一个点亮 led 灯的脚本程序。

led.sh 代码如下：

```shell
#!/bin/sh

LED_GPIO=/sys/class/gpio/gpio440

if test -d $LED_GPIO; then
    echo "GPIO440 already exported"
else
    echo 440 > /sys/class/gpio/export
fi

echo out > $LED_GPIO/direction

while true; do
    echo 1 > $LED_GPIO/value
done
```

内容很简单，给 LED GPIO 端口不断赋值为1使得 LED 灯常亮.

在本机重新打开一个终端窗口，通过 scp 命令拷贝文件：

```shell
> scp led.sh root@192.168.42.1:/root
```

![1700149061911](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212247158.png)

在开发板中可以找到并执行该程序：

```shell
[root@milkv]~# chmod 777 led.sh

[root@milkv]~# ./led.sh
```

![image-20231116233819427](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212247002.png)

此时程序循环执行点亮 LED 灯，可以注意到开发板上的蓝灯变成了常亮状态。

![image-20231117001939364](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212246284.png)

在终端窗口中按下 Ctrl+C 强制停止程序执行。

### 在 MilkV 上运行 opencv-mobile 程序

同样的操作步骤，我们将 `in.jpg`， `opencv-mobile-test` 和 `libgomp.so.1.0.0` 文件上传到开发板。

```shell
> scp .\libgomp.so.1.0.0 .\opencv-mobile-test .\in.jpg root@192.168.42.1:/root
```

![1700149458340](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212246847.png)

```shell
[root@milkv]~# chmod 777 -R .
```

![1700149489127](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212246132.png)

将 `libgomp.so.1.0.0` 重命名为 `libgomp.so.1`

```shell
[root@milkv]~# mv libgomp.so.1.0.0 libgomp.so.1
```

![1700149637767](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212246787.png)

执行如下指令：

```shell
[root@milkv]~# LD_LIBRARY_PATH=. ./opencv-mobile-test
```

可以发现当前目录下新生成了 `out.jpg` 文件。

![1700149900145](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212246253.png)

在本机通过如下命令将 out.jpg 拷贝到本地，并对比处理前后的图片。

![image-20231116235720822](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212246154.png)

![在这里插入图片描述](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202407212246097.png)


图片压缩成功！