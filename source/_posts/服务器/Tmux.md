---
title: Tmux
date: 2025-01-17 15:56:29
tags:
- 服务器
- 多会话管理
categories: 通用技能
excerpt: 在利用SSH连接远端服务器并需要长期运行程序?  使用Tmux赋予进程以「不死鸟的加护」—— 退出终端窗口后依旧在后台运行~
---
>在利用SSH连接远端服务器并需要长期运行程序?  使用Tmux赋予进程以「不死鸟的加护」—— 退出终端窗口后依旧在后台运行~
<img src = "/img/不死鸟.jpg" width = "70%">

## 什么是 Tmux？

Tmux（Terminal Multiplexer）是一个终端复用器，它允许用户在单个终端窗口中运行和管理多个终端会话。作为一个强大的命令行工具，tmux 特别适合需要长时间运行程序或需要同时管理多个终端窗口的场景。
## 为什么需要 Tmux？

在以下场景中，tmux 特别有用：

1. **远程服务器管理**
   - SSH 连接意外断开时，tmux 会话仍然保持运行
   - 可以随时重新连接到之前的工作环境

2. **多任务管理**
   - 在同一个终端窗口中同时运行多个程序
   - 方便地在不同任务之间切换

3. **结对编程**
   - 多人可以同时连接到同一个 tmux 会话
   - 实时查看和协作编辑

## 基本概念

tmux 采用三层架构：

- **会话（Session）**：最顶层的概念，包含多个窗口
- **窗口（Window）**：类似于浏览器的标签页
- **窗格（Pane）**：窗口内的分割区域

## 常用操作指南

### 1. 安装

不同系统的安装命令：

```bash
# MacOS
brew install tmux

# Ubuntu/Debian
sudo apt install tmux

# CentOS/RHEL
sudo yum install tmux
```

### 2. 会话管理

#### 启动新会话
```bash
# 创建默认会话
tmux

# 创建命名会话
tmux new -s session_name
```

#### 会话操作
```bash
# 断开当前会话
Ctrl+b d

# 列出所有会话
tmux ls

# 连接到指定会话
tmux attach -t session_name

# 关闭指定会话
tmux kill-session -t session_name
```

### 3. 窗口管理

所有命令都需要先按前缀键 `Ctrl+b`：

- `c`: 创建新窗口
- `&`: 关闭当前窗口
- `p`: 切换到上一个窗口
- `n`: 切换到下一个窗口
- `数字键`: 切换到指定编号的窗口

### 4. 窗格操作

同样需要先按前缀键 `Ctrl+b`：

- `%`: 垂直分割窗格
- `"`: 水平分割窗格
- `方向键`: 在窗格之间移动
- `x`: 关闭当前窗格
- `z`: 最大化/还原当前窗格

## 进阶技巧

### 1. 自定义配置

创建 `~/.tmux.conf` 文件来自定义 tmux 配置：

```bash
# 修改前缀键为 Ctrl+a
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# 开启鼠标支持
set -g mouse on

# 设置窗口编号从 1 开始
set -g base-index 1
```

### 2. 复制模式

1. 按 `Ctrl+b [` 进入复制模式
2. 使用方向键移动光标
3. 按 `Space` 开始选择
4. 按 `Enter` 复制选中内容
5. 按 `Ctrl+b ]` 粘贴


---

tmux 是一个强大的终端管理工具，掌握它可以显著提高命令行工作效率。从基本的会话管理到高级的自定义配置，tmux 都提供了灵活而强大的功能。随着使用经验的积累，你会发现它是开发工作中不可或缺的工具之一。

## 参考资源

- [Tmux 官方文档](https://github.com/tmux/tmux/wiki)
- [Tmux 速查表](https://tmuxcheatsheet.com/)
- [Awesome Tmux](https://github.com/rothgar/awesome-tmux)