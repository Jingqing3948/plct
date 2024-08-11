# MilkV Duo cvi-mmf 硬件加速 JPG 解码性能测试

## 前言

本文是基于 nihui 老师的 opencv-mobile 对其支持 milkv-duo cvi-mmf 硬件加速 JPG 解码的测试。
nihui 老师原文章如下：[opencv-mobile 现已支持 milkv-duo cvi-mmf 硬件加速 JPG 解码](https://zhuanlan.zhihu.com/p/673940312) 
opencv-mobile 仓库地址如下：[nihui/opencv-mobile: The minimal opencv for Android, iOS, ARM Linux, Windows, Linux, MacOS, WebAssembly (github.com)](https://github.com/nihui/opencv-mobile)

仅供学习交流使用，侵删！

> 1. opencv-mobile highgui 模块在运行时动态加载 cvi 库，JPG 硬件解码
> 2. 无需修改代码，`cv::imread()` 与 `cv::imdecode()` 自动支持
> 3. 支持EXIF自动旋转，支持直接解码为grayscale
> 4. 因为只测试验证了 milkv-duo/milkv-duo-256m，白名单暂时只有 milkv-duo/milkv-duo-256m
>
> ——nihui 老师

## 编译 opencv-mobile

这次的尝试同样是编译 opencv-mobile 和 opencv-mobile-test 程序并烧录到 milkv duo 开发板上使用，下载编译等流程基本一致（注意我的上篇文章中提到的 patches 文件夹小问题）。

另外，此次测试我尝试使用了内存更大的 Milkv duo 256 版本。使用区别仅在于需要使用新版镜像。

![3d40c4941f72eed4a249a1e82f05dbe](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/3d40c4941f72eed4a249a1e82f05dbe.jpg)

基本流程和上一篇文章一样。

git pull 获取最新版本 opencv-mobile 源码后，首先需要检查修改 test 文件夹内的  CMakeLists.txt 文件为：

```cmake
project(opencv-mobile-test)
cmake_minimum_required(VERSION 3.5)
set(CMAKE_CXX_STANDARD 11)

set(OpenCV_DIR "${CMAKE_CURRENT_SOURCE_DIR}/opencv-mobile-4.8.1-milkv-duo/lib/cmake/opencv4")
find_package(OpenCV REQUIRED)

add_executable(opencv-mobile-test main.cpp)

target_link_libraries(opencv-mobile-test ${OpenCV_LIBS})
```

然后仍然是引入编译 opencv-4.8.1。自己编译的方式如下：

```shell
$ cd opencv-mobile

$ wget -q https://github.com/opencv/opencv/archive/4.8.1.zip
$ unzip -q opencv-4.8.1.zip
$ cd opencv-4.8.1

$ truncate -s 0 cmake/OpenCVFindLibsGrfmt.cmake
$ rm -rf modules/gapi
$ patch -p1 -i ../patches/opencv-4.8.1-no-rtti.patch
$ patch -p1 -i ../patches/opencv-4.8.1-no-zlib.patch
$ patch -p1 -i ../patches/opencv-4.8.1-link-openmp.patch
$ rm -rf modules/highgui
$ cp -r ../highgui modules/

$ mkdir build
$ cd build
$ cmake -DCMAKE_TOOLCHAIN_FILE=../../toolchains/riscv64-unknown-linux-musl.toolchain.cmake -DCMAKE_C_FLAGS="-fno-rtti -fno-exceptions" -DCMAKE_CXX_FLAGS="-fno-rtti -fno-exceptions" -DCMAKE_INSTALL_PREFIX=install -DCMAKE_BUILD_TYPE=Release `cat ../../opencv4_cmake_options.txt` -DBUILD_opencv_world=OFF -DOPENCV_DISABLE_FILESYSTEM_SUPPORT=ON ..
$ make -j16
$ make install
```

不过项目主页上 nihui 老师已经放上了下载编译好的版本，可以直接下载到 opencv-mobile 文件夹中，省略上一步。下载链接：https://github.com/nihui/opencv-mobile/releases/latest/download/opencv-mobile-4.8.1.zip

![img](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/v2-d4315e881ca166d1a4ffc0d2c56a9ec0_1440w.webp)

编译 opencv-mobile-test 文件。

```shell
$ export RISCV_ROOT_PATH=$HOST_TOOL_PATH/gcc/riscv64-linux-musl-x86_64

$ cd opencv-mobile/test

$ mkdir build
$ cd build
$ cmake -DCMAKE_TOOLCHAIN_FILE=../../toolchains/riscv64-unknown-linux-musl.toolchain.cmake -DCMAKE_BUILD_TYPE=Release -DOpenCV_DIR=/home/nihui/dev/opencv-mobile/opencv-4.8.0/build/install/lib/cmake/opencv4 ..
$ make
```

## 运行结果

这次因为使用了 256MB Milkv Duo，所以应该可以尝试大一点的图片处理。我选用了一张3600KB的图片，opencv-mobile-test 仍然是简单的图片压缩处理（->200*200 大小）。

旧版程序：

```shell
[root@milkv-duo]~# time sh -c 'LD_LIBRARY_PATH=. ./opencv-mobile-test'
this device is not
real    0m 2.11s
user    0m 1.82s
sys     0m 0.27s
[root@milkv-duo]~# time sh -c 'LD_LIBRARY_PATH=. ./opencv-mobile-test'
this device is not
real    0m 2.11s
user    0m 1.82s
sys     0m 0.28s
[root@milkv-duo]~# time sh -c 'LD_LIBRARY_PATH=. ./opencv-mobile-test'
this device is not
real    0m 2.11s
user    0m 1.78s
sys     0m 0.31s
[root@milkv-duo]~# time sh -c 'LD_LIBRARY_PATH=. ./opencv-mobile-test'
this device is not
real    0m 2.10s
user    0m 1.78s
sys     0m 0.31s
[root@milkv-duo]~# time sh -c 'LD_LIBRARY_PATH=. ./opencv-mobile-test'
this device is not
real    0m 2.11s
user    0m 1.82s
sys     0m 0.27s
```

新版程序：

```shell
[root@milkv-duo]~# time sh -c 'LD_LIBRARY_PATH=. ./opencv-mobile-test'
this device is not whitelisted for jpeg encoder rkmpp
Command terminated by signal 11
real    0m 0.36s
user    0m 0.10s
sys     0m 0.15s
[root@milkv-duo]~# time sh -c 'LD_LIBRARY_PATH=. ./opencv-mobile-test'
this device is not whitelisted for jpeg encoder rkmpp
Command terminated by signal 11
real    0m 0.36s
user    0m 0.11s
sys     0m 0.14s
[root@milkv-duo]~# time sh -c 'LD_LIBRARY_PATH=. ./opencv-mobile-test'
this device is not whitelisted for jpeg encoder rkmpp
Command terminated by signal 11
real    0m 0.36s
user    0m 0.12s
sys     0m 0.13s
[root@milkv-duo]~# time sh -c 'LD_LIBRARY_PATH=. ./opencv-mobile-test'
this device is not whitelisted for jpeg encoder rkmpp
Command terminated by signal 11
real    0m 0.36s
user    0m 0.09s
sys     0m 0.17s
[root@milkv-duo]~# time sh -c 'LD_LIBRARY_PATH=. ./opencv-mobile-test'
this device is not whitelisted for jpeg encoder rkmpp
Command terminated by signal 11
real    0m 0.36s
user    0m 0.12s
sys     0m 0.13s
```

整体速度提升了5倍以上。