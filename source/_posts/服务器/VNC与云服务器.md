---
title: VNC与云服务器
date: 2025-06-28 18:34:17
tags:
- 服务器
- ssh

categories: 通用技能
excerpt: 在mac上使用VNC实现服务器远程可视化操作与程序运行全流程梳理
thumbnail: https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250628195502186.png?imageSlim
---
在上多智能体短学期课程的时候，碰到了实验——“基于 IsaacGym 的 Unitree G1 机器人平地行走实验”，其要求是：

> 前期准备：Isaac Gym Preview 4 Release 目前仅支持 Linux 操作系统（本地运行安装双系统，请勿使用 WSL，系统版本推荐 Ubuntu 20.04/22.04）

没想到双持用户还是逃不开环境的配置😇 整体上看，有两条解决路径：

1. 在windows上独立安装一个Ubuntu系统
2. 租用云服务器资源，但是需要配置VNC来实现可视化

经过短暂的调研，我发现方案1需要为新系统划分新的磁盘区域，同时下载不小容量的系统。考虑到我大概不会一直在我的windows上用到这个ubuntu，因此我转而采取方案2

整体而言，方案2涉及到的步骤包括：

1. 准备云服务器
2. 安装桌面环境和VNC服务器端
3. 配置VNC

## 准备云服务器

在一众按月计费的产品中，我终于找到了腾讯云服务的按小时计费的计算资源——[高性能应用服务](https://buy.cloud.tencent.com/hai?applicationId=app-gt61aexl&applicationType=sc-8femp43e&regionId=9&bundleId=XL&diskSize=80):

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250628185921988.png?imageSlim"/>

选择合适的基础环境，勾选“按量计费”即可

之后可以在腾讯云-[高性能应用服务](https://console.cloud.tencent.com/hai/instance?rid=1)中管理云服务：

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250628190843586.png?imageSlim"/>

> 首次创建之后默认开机，并且需要“重置密码”来设置一个密码

之后，可以通过ssh连接服务器：

```shell
ssh ubuntu@<公网IP>
```

> 公网IP可能随时间变化，注意参考最新的IP

然后输入之前设置好的密码即可连接

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250628192620957.png?imageSlim"/>

## 安装桌面环境和VNC服务端

**VNC（Virtual Network Computing）**：

- 一种图形化远程控制协议和工具，允许用户通过网络远程访问和操作另一台计算机的桌面环境；
- VNC分为服务器端（VNC Server）和客户端（VNC Viewer）。服务器端运行在被控设备上，客户端运行在需要远程访问的设备上

在服务器领域，尤其是Linux服务器，默认通常只有命令行界面。如果需要图形化桌面环境，可以**安装桌面环境并运行VNC Server**。这样，远程用户就能通过VNC客户端访问服务器的完整图形桌面，进行可视化操作和管理。

因此，我们需要在之前的云服务器上先后安装桌面环境与VNC服务端，然后通过本地的VNC客户端来可视化访问云服务器端

### 安装xfce4

> Xfce4（通常简称 Xfce）是一款轻量级、易于定制的 **Linux/Unix 桌面环境**。它的设计目标是实用、快速且节省系统资源

首先，确保系统是最新配置：

```shell
sudo apt update
```

然后，安装轻量的桌面环境，此处以 `xfce4`为例：

```shell
sudo apt install xfce4
```

### 安装TigerVNC

```shell
sudo apt install tigervnc-standalone-server
```

> 适用于 `Ubuntu 20.04 LTS`及之后的版本

### 配置VNC Server

1. 设置密码：

   ```shell
   vncpasswd
   ```
2. 启动VNC服务：

   ```shell
   vncserver :1
   ```

   > 这会在**5901**端口监听VNC连接
   >

如果启动失败，很可能是vnc脚本的配置问题——`~/.vnc/xstartup`指定了VNC启动的桌面环境。如果脚本内容与之前下载的桌面环境不匹配，就无法正常启动，此时我们需要改写该文件

1. 用nano或vim编辑器打开脚本文件：

```shell
nano ~/.vnc/xstartup
```

2. 将内部的文件改写如下：（以桌面环境**xfce4**为例）

```shell
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
startxfce4 &
```

3. 确保脚本文件有执行权限：

```shell
chmod +x ~/.vnc/xstartup
```

4. 重启VNC服务：

```shell
vncserver -kill :1  # 关闭当前VNC会话
vncserver :1        # 重新启动VNC会话
```

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250628192757374.png?imageSlim"/>

我们可以通过命令 `vncserver -list` 查看当前的VNC对话

## 使用VNC客户端连接服务器

此处以**realvnc**为例——[官方的下载地址](https://www.realvnc.com/en/connect/download/viewer/)

安装完毕之后输入VNC server的 `IP:端口`：

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250628193105645.png?imageSlim"/>

然后按照提示，输入之前在server初始化的时候设置的密码，即可连接

### 通过SSH隧道安全连接

查看5901端口监听的地址：

```shell
netstat -tlnp | grep 5901
```

如果类似输出：

```shell
(base) ubuntu@VM-0-5-ubuntu:~$ netstat -tlnp | grep 5901
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 127.0.0.1:5901          0.0.0.0:*               LISTEN      2583/Xtigervnc    
tcp6       0      0 ::1:5901                :::*                    LISTEN      2583/Xtigervnc    
(base) ubuntu@VM-0-5-ubuntu:~$ 
```

说明此时云服务器端正在监听本机的端口，可以声明：

```shell
vncserver :1 -localhost no
```

此时可以监听所有IP。但是这样需要同步在腾讯云的云服务器处改写规则，允许监听所有IP，存在一定风险

如果担心VNC端口暴露在公网不安全，可以用SSH隧道方式进行加密连接：

1. 本地开启端口转发：

   ```shell
   ssh -L 5901:localhost:5901 用户名@服务器IP
   
   # 启动对话
   vncserver :1
   ```
2. 客户端连接本地端口——在viewer中输入：

   ```shell
   localhost:5901
   ```

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250628194000045.png?imageSlim"/>

至此，我们就可以使用本地的VNC client来操作图形化的云服务器 🎉



## 附录

### 可能遇到的问题

#### 使用浏览器查看实验结果

实验中rl运行的结果在logs文件夹中如图所示：

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250630090112540.png?imageSlim"/>

- 第一个是**TensorBoard日志文件**，由框架自动生成，记录训练过程中的各种指标
  - 为二进制形式，需要使用 Tensorboard 工具查看
  
    ```shell
    # pip install tensorboard
    
    tensorboard --logdir=.
    # 或者指定名称 tensorboard --logdir=output
    ```
  
- 后续以 `pt`结尾的文件是**PyTorch模型权重文件** ，即保存的神经网络模型参数
  - 是模型快照
  - 用PyTorch的 `torch.load()`函数加载，用于测试、继续训练或推理
  
    ```shell
    # 加载pt模型
    import torch
    model = torch.load('xxx.pt')
    ```
  
    

我首先尝试使用scp直接传输日志文件到本地，但是传输之后使用上述工具查看时显示：

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250630090521690.png?imageSlim"/>

> 唔， 我也不知道这是什么情况？可能是传输过程的问题？

于是，我尝试直接在服务器上查看，但是碰到了另一个问题——轻量级的桌面似乎没有自带的浏览器？（我点击下方的浏览器，弹出的却是Terminal），于是有两个方法——

1. 在服务器端安装一个浏览器并查看：

   ```shell
   # 假设在6006端口
   
   sudo apt install chromium-browser
   
   chromium-browser http://localhost:6006
   ```

2. 使用之前提到过的SSH隧道的方法，将6006端口映射到本地：

   ```shell
   ssh -L 6006:localhost:6006 user@server
   ```

   此时我们就可以在本地查看日志：<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250630091320160.png?imageSlim"/>



#### 使用训练完毕的模型

执行`play`文件：

> 假设需要使用的模型快照路径为：`legged_gym/logs/rough_g1/Jul02_14-13-57_rough_g1/model_350.pt`

```bash
python legged_gym/legged_gym/scripts/play.py \
    --task=g1 \
    --sim_device=cuda:0 \
    --rl_device=cuda:0 \
    --experiment_name=rough_g1 \
    --load_run=Jul02_14-13-57_rough_g1 \
    --checkpoint=350 \
    --num_envs=1
```



#### 从一个模型快照继续训练

执行`train`文件：

> 假设使用的模型快照与之前的相同

```bash
python legged_gym/legged_gym/scripts/train.py \
    --task=g1 \
    --rl_device=cuda:0 \
    --sim_device=cuda:0 \
    --headless \
    --resume \
    --experiment_name=rough_g1 \
    --load_run=Jul02_14-13-57_rough_g1 \
    --checkpoint=350
```

> 通过 `--resume` 参数指定继续训练, 

新的日志将会在指定的`experiment_name`目录下生成



### 常用的VNC server命令

```shell
# 启动对话，默认监听本地
vncserver :1

# 启动并监听所有IP（不安全）
vncserver :1 -localhost no

# 停止VNC对话
vncserver -kill :1

# 设置密码
vncpasswd

# 查看当前对话
vncserver -list

# 结束当前所有对话
pkill Xtigervnc
```



### 参考资料

- https://blog.csdn.net/weixin_44262492/article/details/128365248
- https://blog.csdn.net/yinjl123/article/details/136853195
