---
title: MX6ULL使用实践
date: 2024-04-26 16:21:42
tags: 嵌入式
---

# 任务目标

本次任务目标是在MX6ULL上用ffmpeg采集摄像头数据，通过opencv进行图像处理，将处理后的图像传输给显示屏。

# 实现过程

## 工作流

首先PC机运行windows11系统，在虚拟机中运行ubuntu系统，在虚拟机中编写代码并进行交叉编译，将可执行文件通过

## 串口通信

串口在linux系统中就是一个终端。

### 分类

本地终端：显示屏、键盘鼠标等设备。位置在/dev/ttyX。

用串口连接的远程终端：用串口线连接PC机，PC机运行一个终端模拟程序。位置在/dev/ttymxcX。

基于网络的远程终端：通过ssh、Telnet协议登录到一个元册灰姑娘主机。位置在/dev/pts/X。

### 实践过程

#### 查看终端

- 首先查看终端设备ls /dev/ttymxc*，如果该串口并没有注册，需要编写驱动注册该串口。

- 通过who命令查看哪些终端正在被使用

#### 串口应用编程

终端应用程序有一套标准API叫做termios API。

终端的应用程序主要包括配置和读写。

- 配置：struct termios结构体描述了终端的配置信息。
- 读写：read(),write()函数

代码示例

```C
#define _GNU_SOURCE //在源文件开头定义_GNU_SOURCE 宏
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/ioctl.h>
#include <errno.h>
#include <string.h>
#include <signal.h>
#include <termios.h>
typedef struct uart_hardware_cfg {
    unsigned int baudrate; /* 波特率 */
    unsigned char dbit; /* 数据位 */
    char parity; /* 奇偶校验 */
    unsigned char sbit; /* 停止位 */
} uart_cfg_t;
static struct termios old_cfg; //用于保存终端的配置参数
static int fd; //串口终端对应的文件描述符
/**
** 串口初始化操作
** 参数 device 表示串口终端的设备节点
**/
static int uart_init(const char *device)
{
    /* 打开串口终端 */
    fd = open(device, O_RDWR | O_NOCTTY);
    if (0 > fd) {
        fprintf(stderr, "open error: %s: %s\n", device, strerror(errno));
    return -1;
	}
    /* 获取串口当前的配置参数 */
    if (0 > tcgetattr(fd, &old_cfg)) {
        fprintf(stderr, "tcgetattr error: %s\n", strerror(errno));
        close(fd);
        return -1;
    }
        return 0;
}
/**
** 串口配置
** 参数 cfg 指向一个 uart_cfg_t 结构体对象
**/
static int uart_cfg(const uart_cfg_t *cfg)
{
    struct termios new_cfg = {0}; //将 new_cfg 对象清零
    speed_t speed;
    /* 设置为原始模式 */
    cfmakeraw(&new_cfg);
    /* 使能接收 */
    new_cfg.c_cflag |= CREAD;
    /* 设置波特率 */
    cfg->baudrate
    speed = B115200;
    if (0 > cfsetspeed(&new_cfg, speed)) {
    fprintf(stderr, "cfsetspeed error: %s\n", strerror(errno));
    return -1;
	}
    /* 设置数据位大小 */
	new_cfg.c_cflag &= ~CSIZE; //将数据位相关的比特位清零
    new_cfg.c_cflag |= CS7;
    /* 设置奇偶校验 */
    new_cfg.c_cflag &= ~PARENB;
    new_cfg.c_iflag &= ~INPCK;
    /* 设置停止位 */
    new_cfg.c_cflag &= ~CSTOPB;
    /* 将 MIN 和 TIME 设置为 0 */
    new_cfg.c_cc[VTIME] = 0;
    new_cfg.c_cc[VMIN] = 0;
    /* 清空缓冲区 */
    if (0 > tcflush(fd, TCIOFLUSH)) {
    fprintf(stderr, "tcflush error: %s\n", strerror(errno));
    return -1;
	}
    /* 写入配置、使配置生效 */
    if (0 > tcsetattr(fd, TCSANOW, &new_cfg)) {
        fprintf(stderr, "tcsetattr error: %s\n", strerror(errno));
        return -1;
    }
    /* 配置 OK 退出 */
    return 0;
}
int main(int argc, char *argv[])
{
    uart_cfg_t cfg = {0};
    char *device = NULL;
    int rw_flag = -1;
    unsigned char w_buf[10] = {0x11, 0x22, 0x33, 0x44,
    0x55, 0x66, 0x77, 0x88}; //通过串口发送出去的数据
    int n;
    /* 解析出参数 */
    for (n = 1; n < argc; n++) {
    if (!strncmp("--dev=", argv[n], 6))
    device = &argv[n][6];
    else if (!strncmp("--brate=", argv[n], 8))
    cfg.baudrate = atoi(&argv[n][8]);
    else if (!strncmp("--dbit=", argv[n], 7))
    cfg.dbit = atoi(&argv[n][7]);
    else if (!strncmp("--parity=", argv[n], 9))
    cfg.parity = argv[n][9];
    else if (!strncmp("--sbit=", argv[n], 7))
    cfg.sbit = atoi(&argv[n][7]);
    else if (!strncmp("--type=", argv[n], 7)) {
    if (!strcmp("read", &argv[n][7]))
        rw_flag = 0; //读
        else if (!strcmp("write", &argv[n][7]))
        rw_flag = 1; //写
    }
    else if (!strcmp("--help", argv[n])) {
        show_help(argv[0]); //打印帮助信息
        exit(EXIT_SUCCESS);
    }
}
    if (NULL == device || -1 == rw_flag) {
        fprintf(stderr, "Error: the device and read|write type must be set!\n");
        show_help(argv[0]);
        exit(EXIT_FAILURE);
	}
    /* 串口初始化 */
	if (uart_init(device))
   		exit(EXIT_FAILURE);
    /* 串口配置 */
    if (uart_cfg(&cfg)) {
        tcsetattr(fd, TCSANOW, &old_cfg); //恢复到之前的配置
        close(fd);
        exit(EXIT_FAILURE);
    }
    /* 读|写串口 */
    switch (rw_flag) {
    case 0: //读串口数据
    	async_io_init(); //我们使用异步 I/O 方式读取串口的数据，调用该函数去初始化串口的异步 I/O
    	for ( ; ; )
    	sleep(1); //进入休眠、等待有数据可读，有数据可读之后就会跳转到 io_handler()函数
    	break;
    case 1: //向串口写入数据
    	for ( ; ; ) { //循环向串口写入数据
   			write(fd, w_buf, 8); //一次向串口写入 8 个字节
    		sleep(1); //间隔 1 秒钟
    	}
    	break;
    }
    /* 退出 */
    tcsetattr(fd, TCSANOW, &old_cfg); //恢复到之前的配置
    close(fd);
    exit(EXIT_SUCCESS);
}
```

