---
title: "Linux Process Management"
date: 2022-01-29T18:04:55+08:00
categories: ["original"]
tags: ["linux"]
draft: false
---

## 进程简介

进程是正在执行的一个程序或命令，每个进程都是一个运行的实体，都有自己的地址空间，并占用一定的系统资源

## 进程管理的作用

- 判断服务器健康状态

- 查看系统中的所有进程

- 杀死进程

### 查看系统中所有进程

- `ps aux` 查看系统中所有进程，使用BSD操作系统格式

- `ps -el`查看系统中所有进程，使用Linux标准命令格式

- `ps -Lf 进程号`查看指定进程的线程

### 进程信息说明

- `USER`该进程是哪个用户产生

- `PID`进程ID号

- `%CPU`该进程占用的CPU资源百分比

- `%MEM`该进程占用的物理内存百分比

- `VSZ`该进程占用的虚拟内存大小，单位kb

- `RSS`该进程占用实际物理内存的大小，单位kb

- `TTY`该进程在哪个终端中运行的，其中`tty1-tty7`代表本地控制台终端，`tty1-tty6`是本地的字符界面终端，`tty7`是图形终端。`pts/0-255代表虚拟终端(远程)

- `STAT` 进程状态。常见状态：
  - `R` 运行
  
  - `S` 睡眠
  
  - `T` 停止状态
  
  - `s` 包含子进程
  
  - `+` 位于后台
  
- `START` 该进程的启动时间

- `TIME` 该进程占用CPU的运算时间，注意不是系统时间

- `COMMAND` 产生此进程的命令名

### 查看系统健康状态

```
top [选项]
```

- `-d` 秒数：指定top命令每隔几秒更新。默认3秒

#### top命令的交互模式中可以执行的命令：

- `?/h` 显示交互模式的帮助

- `P` 按CPU使用率排序，默认就是此项

- `M` 以内存的使用率排序

- `N` 以PID排序

- `q` 退出top

#### top命令的显示信息

##### 第一行信息为任务队列信息

|             内容             |                             说明                             |
| :--------------------------: | :----------------------------------------------------------: |
|           12:26:46           |                         系统当前时间                         |
|       up 1day, 13.:32        |           系统的运行时间。本机已经运行了1天13小时            |
|           2 users            |                      当前登录了两个用户                      |
| load average: 0.00,0.00,0.00 | 系统在之前1分钟、5分钟、15分钟的平均负载。一般认为小于1时，负载较小。如果大于1，系统已经超出负荷。 |



##### 第二行为进程信息

| 内容            | 说明                                      |
| --------------- | ----------------------------------------- |
| Tasks: 95 total | 系统中的进程总数                          |
| 1 running       | 正在运行的进程数                          |
| 94 sleeping     | 睡眠的进程                                |
| 0 stopped       | 正在停止的进程                            |
| 0 zombie        | 僵尸进程。如果不是0，需要手工检查僵尸进程 |



##### 第三行为CPU信息

内容说明:

|     内容     |                             说明                             |
| :----------: | :----------------------------------------------------------: |
| Cpu(s)0.1%us |                   用户模式占用的CPU百分比                    |
|    0.1%sy    |                   系统模式占用的CPU百分比                    |
|    0.0%ni    |            改变过优先级的用户进程占用的CPU百分比             |
|   99.7%id    |                       空间CPU的百分比                        |
|    0.1%wa    |               等待输入/输出的进程占用的百分比                |
|    0.0%hi    |                硬中断请求服务占用的CPU百分比                 |
|    0.1%si    |                软中断请求服务占用的CPU百分比                 |
|   0.0%stst   | (steal time)虚拟时间百分比。就是当有虚拟机时，虚拟CPU等待实际CPU的时间百分比 |

##### 第四行为物理内存信息

|        内容        |          说明          |
| :----------------: | :--------------------: |
| Mem: 625344k total |  物理内存总量，单位kb  |
|    571504k used    | 已经使用的物理内存总量 |
|    53840k free     |   空闲的物理内存数量   |
|   65800k buffers   |   作为缓冲的内存数量   |

##### 第五行为交换分区(swap)信息

|        内容         |                说明                |
| :-----------------: | :--------------------------------: |
| Swap: 524280k total | 内容说明交换分区(虚拟内存)的总大小 |
|       0k used       |      已经使用的交互分区的大小      |
|    524280k free     |         空闲交换分区的大小         |
|   409280k cached    |      作为缓存的交互分区的大小      |

#### 查看进程树

```bash
pstree [选项]
```

- `-p` 显示进程的PID

- `-u` 显示进程的所属用户

### 终止进程

#### kill命令

- `kill -l` 查看可用的进程信号

- `kill -9 1234`强制终止pid为1234的进程

##### 常用信号

| Signal Num | Signal Name |                         Description                          |
| :--------: | :---------: | :----------------------------------------------------------: |
|     1      |   SIGHUP    |        该信号让进程关闭，然后重新读取配置文件之后重启        |
|     9      |   SIGKILL   | 用来立即结束程序的运行。本信号不能被阻塞、处理和忽略。一般用于强制终止进程 |
|     15     |   SIGTERM   | 正常结束进程的信号。kill命令的默认信号。有时如果进程已经发生问题，这个信号是无法正常终止进程的，这时需要用信号9 |

#### killall命令

按照进程名杀死进程

- `killall [选项] [信号] 进程名`

##### 选项

- `-i` 交互式，询问是否要杀死某个进程

- `-I` 忽略进程名的大小写

#### pkill命令

按照进程名终止进程

- `pkill [选项] [信号] 进程名`

##### 选项

- `-t` 终端号：按照终端号踢出用户
