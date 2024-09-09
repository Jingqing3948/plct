## 前言

Pulse Width Modulation脉冲宽度调制是在惯性系统中，利用数字输出控制模拟电路的一种技术，在测量，通信，功率领域非常常见。

常见嵌入式应用：电机转速控制，LED亮度。

CV180X可以提供一组4路独立的PWM输出，支持连续输出和固定脉冲个数输出两种模式。

## 终端运行PWM

示例来自 MilkV Duo 官方手册。四条语句的作用分别是：指明需要操作的 pwm 编号，设置周期（ns）和占空比（50%），使能。

```shell
echo 1 > /sys/class/pwm/pwmchip0/export

echoo 10000000 > /sys/class/pwm/pwmchip0/pwm1/period

echo 500000 > /sys/class/pwm/pwmchip0/pwm1/duty_cycle

echo 1 > /sys/class/pwm/pwmchip0/pwm1/enable
```

## Arduino运行PWM

![1725598512085](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202409091016159.jpg)

```c
void setup() {
  pinMode(6, OUTPUT);
}

void loop() {
  for(int i = 128; i < 255; i++)
  {
    analogWrite(6,i);
    delay(50);
  }
  for(int i = 255; i > 128; i--)
  {
    analogWrite(6,i);
    delay(50);
  }
}
```

图中的接线连接PIN6，对应PWM5输出。

![1725600022148](https://raw.githubusercontent.com/Jingqing3948/FigureBed/main/mdImages/202409091020587.jpg)

## 参考资料

https://milkv.io/zh/docs/duo/getting-started/arduino#pwm-%E4%BD%BF%E7%94%A8%E7%A4%BA%E4%BE%8B

https://github.com/milkv-duo/duo-files/blob/main/duo/datasheet/CV1800B-CV1801B-Preliminary-Datasheet-full-en.pdf

http://staff.ustc.edu.cn/~llxx/cod/reference_books/RISC-V-Reader-Chinese-v2p12017.pdf

https://community.milkv.io/t/milk-v-duo-pwm/439

https://community.milkv.io/t/pwm-gpio/1517