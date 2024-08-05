# Arduino IDE 简介和开发环境配置

（本视频任务：完成 Arduino 环境配置；连接 MilkV Duo 并开启/关闭 LED 灯闪烁效果。点灯一般被视作嵌入式开发的 Helloworld）

## 简介

提到 Arduino，学习嵌入式的同学肯定不陌生。

Arduino 是一个很流行的开源硬件平台，封装了大量的库函数和示例代码，使得对于没有编程经验的同学来说也可以轻松上手嵌入式开发。，Arduino 社区非常活跃，开发者可以轻松地获取到各种项目教程、文档和支持。

现在，MilkV Duo 系列开发板已经支持 Arduino 开发，本系列课程旨在使用 Arduino IDE 快速上手使用 MilkV Duo。

*本实例使用 `Windows Win 10 and newer, 64 bits` 版本。*

*尝试使用动画讲解原理：Duo 系列 CPU 采用大小核设计，Arduino 固件运行在小核中，大核负责与 Arduino IDE 通讯，接收 Arduino 固件并将其加载到小核中运行。同时，大核中的 Linux 系统也是正常运行的。*

## 环境配置

1. Arduino IDE 下载：访问 Arduino 官网进行下载。
2. IDE 中添加 Duo 的配置文件地址：https://github.com/milkv-duo/duo-arduino/releases/download/config/package_sg200x_index.json。
3. 在开发板管理器中选择 SG200x，点击安装开发板。
4. Duo 固件安装：为 TF 卡刷写 Release 固件。
5. TF卡插入 MilkV Duo，连接电脑。第一次连接需要安装 RNDIS 驱动和串口设备。
6. 关闭 LED 闪烁：终端执行命令，`mv /mnt/system/blink.sh /mnt/system/blink.sh_backup && sync && reboot` 重命名 blink.sh 文件并重启开发板，使得blink.sh文件不会开机自动运行。
7. 开启 LED 闪烁：运行 Arduino 中的 Blink 程序。

## 下期预告

GPIO输出；让指定GPIO周期性输出高低电平，用万用表 & LED 测试输出结果。

## 参考文献

https://www.arduino.cc/en/software

https://github.com/ArielHeleneto/Work-PLCT/tree/master/duo/Arduino

[Releases · milkv-duo/duo-buildroot-sdk (github.com)](https://github.com/milkv-duo/duo-buildroot-sdk/releases)