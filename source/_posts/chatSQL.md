---
title: 𝐜𝐡𝐚𝐭𝐒𝐐𝐋
date: 2025-05-08 23:30:56
tags: 奇思妙想
categories: 前后端开发
thumbnail: https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250508164908220.png?imageSlim
excerpt: ~~你的下一款minisql, 何必是minisql.~~ chatSQL 是一个交互式 SQL 学习平台，通过人工智能技术生成个性化的 SQL 练习题，帮助用户从入门到精通 SQL 查询语言。平台结合了直观的数据库可视化工具、智能代码编辑器和即时反馈系统，为用户提供沉浸式学习体验。
---
<p align="center" style="font-size: 1.2em; margin: 20px 0;">
  <a href="https://chat-sql-hazel.vercel.app/" target="_blank" style="font-size: 1.2em; font-weight: bold;">Website</a>
  <a href="https://github.com/ffy6511/chatSQL" target="_blank" style="font-size: 1.2em; font-weight: bold;">Github Repo</a>
</p>
<p align="center">
  <a href="https://deepwiki.com/ffy6511/chatSQL"><img src="https://deepwiki.com/badge.svg" alt="Ask DeepWiki" /></a>
</p>

**什么是chatSQL**:

chatSQL 是一个交互式 SQL 学习平台，通过人工智能技术生成个性化的 SQL 练习题，帮助用户从入门到精通 SQL 查询语言。平台结合了直观的数据库可视化工具、智能代码编辑器和即时反馈系统，为用户提供沉浸式学习体验。无论您是 SQL 初学者还是希望提升查询技能的开发者，chatSQL 都能根据您的水平定制适合的学习内容，让 SQL 学习变得更加高效和有趣。

## 产生背景
在初学sql的时候, 总觉得在纸上谈兵的感觉不太切实, ~~总是不知道自己胡乱写的sql语句是否正确~~. 偶然的机会看到了一个在线的[自学sql网站](http://xuesql.cn/lesson/select_queries_with_constraints), 提供了数据库结构,元组信息以及sql编辑平台, 供用户实时查询:

![](https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250508234644726.png?imageSlim)

然而该网站的习题内容对免费用户十分有限, 不足以sql学习者反复的练习. 于是我想到, 为什么不自己做一个呢? 

### sql题目的生成
gemini免费的api + dify上免费的工作流 = 无穷无尽的sql题目~

由于有过dify平台上搭建工作流的经验, 我还是选择用dify来针对用户的需求生成不同难度和类型的sql题目. 

### schema的可视化
在写数据库作业的时候, 意外发现了[chartDB](https://chartdb.io/)这个网站, 其中的数据库结构的可视化十分优雅.经过对其源码的"探测", 我挖掘到了xyflow这个组件库.
![](https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250509000938549.png?imageSlim)

同时, 我决定直接用MUI组件库中的表格作为数据库元组的展示, 让元组的信息更加直观.

### sql语句的执行
这部分可谓是项目中最难实现的任务了.

其实一开始并没有立刻开始做chatSQL, 就是因为我没有现成的在虚拟/实际数据库中执行sql语句的方法. 

然而, 某天浏览数据库教材的时候, 意外发现了官网上存在一个前端执行sql语句查询的demo! 发现叫做[sql.js](https://sql.js.org/#/)的库似乎就可扮演在前端的sql引擎角色. 因此, 我觉得万事俱备了! 开始设计前后端的字段, 布局排版, 然后实现基础的UI...

然而然而, 当我准备开始做sql引擎的时候, 发现sql.js竟然与react高版本框架和一些依赖不兼容! 然而我只剩下sql引擎的执行部分了... 也不好就此放弃. 那么就~~借助augment的力量~~自己写一个吧~

1. 首先, 从monaco editor入手, 建立一个类似于vscode风格的代码编辑区;
2. 然后, 利用[node-sql-parser](https://www.npmjs.com/package/node-sql-parser)库, 我们将标准的sql语句传入给解析组件, 获得ast语法树;
3. 最后, 我们编写自己的sql执行引擎, 利用ast语法树执行sql语句.

> 下面基本是项目仓库中README的部分.



## ✨ 特性

- 🤖 AI 生成练习：提供两种方式的习题来源
  - 通过预设的教程, 循序渐进地练习`select`, `join`, 聚合操作与嵌套子查询等知识点.
  - 与dify工作流交互, 输入难度,标签与描述自动生成 SQL 练习题.

- 📊 数据库结构可视化：直观展示表关系和字段信息, 外检约束等信息一目了然;
- ⌨️ Monaco编辑器与schema的补全整合：
  - 支持sql语法高亮和悬浮的语法提示
  - 针对当前schema信息提供`tab`的自动补全

- 📝 即时结果验证：实时验证查询结果
  - 由构建于前端的sql引擎0延迟地处理sql查询结果.
  - 支持将查询结果与期望结果进行比较, 评价查询结果是否正确.




## 🖥 界面预览

### 初始化界面

![](https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250508164908220.png?imageSlim)

- 点击侧边栏中的“初始化教程”, 可以同预设的数据库表结构进行交互;
- 点击侧边栏中的“帮助”, 可以查看基本的操作演示.

### 数据库结构可视化

![](https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250508165221364.png?imageSlim)

- 默认显示数据库结构的可视化视图;
- 可在左下角切换元组视图.

### SQL 编辑器演示

<img src="/img/edit.gif" alt="编辑器演示" width="80%" />

对应快捷键:

- `command+enter` : 执行查询
- `command+j`: 检测查询结果是否匹配;
- `command+k`: 搜索历史记录.

## 🛠 技术栈

<p align="left">
  <img src="https://img.shields.io/badge/Next.js-black?style=for-the-badge&logo=next.js" alt="Next.js" />
  <img src="https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black" alt="React" />
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" alt="TypeScript" />
  <img src="https://img.shields.io/badge/Ant%20Design-0170FE?style=for-the-badge&logo=antdesign&logoColor=white" alt="Ant Design" />
  <img src="https://img.shields.io/badge/Material--UI-007FFF?style=for-the-badge&logo=mui&logoColor=white" alt="Material-UI" />
  <img src="https://img.shields.io/badge/Monaco%20Editor-DD1100?style=for-the-badge&logo=visualstudiocode&logoColor=white" alt="Monaco Editor" />
  <img src="https://img.shields.io/badge/XY%20Flow-22C55E?style=for-the-badge&logo=diagram&logoColor=white" alt="XY Flow" />
</p>

- **框架**: [Next.js](https://nextjs.org/) 15.3.0
- **UI 组件**:
  - [Ant Design](https://ant.design/) 5.24.6
  - [Material-UI](https://mui.com/) 7.0.2
- **编辑器**: [Monaco Editor](https://microsoft.github.io/monaco-editor/)
- **流程图**:
  - [XY Flow](https://reactflow.dev/) (@xyflow/react)
  - 用于数据库表关系可视化
  - 支持自定义节点和边的样式
  - 提供图表交互操作
  - 基于 D3.js 的缩放和拖拽功能
- **AI 集成**: [Dify.ai](https://dify.ai/)
- **类型检查**: [TypeScript](https://www.typescriptlang.org/)

## 🚀 快速开始
> 得益于~~预制课~~教程系列的完善, 您可以直接clone仓库后, 通过`npm install`安装依赖, `npm run dev`启动前端即可进行基本教程的学习, 而无需配置dify工作流. 😌

### 前置要求

- Node.js 18.0 或更高版本
- npm 包管理器
- Dify.ai 账号和 API 密钥

### 安装步骤

1. 克隆仓库

```bash
git clone https://github.com/ffy6511/chatSQL.git
cd chatSQL/chat-sql
```

2. 安装依赖

```bash
npm install
```

3. 配置环境变量

```bash
touch .env
```

编辑 `.env` 文件并添加你的 Dify API 密钥：

```
NEXT_PUBLIC_DIFY_API_KEY=your_api_key_here
```

4. 启动开发服务器

```bash
npm run dev
```

5. 更新git日志: 如果您希望更新自己的"更新日志"界面, 请执行

```bash
npm run generate-git
```
### Dify 工作流配置

1. 在 [Dify 平台](https://dify.ai) 创建新应用（选择工作流）
2. 导入工作流配置：
   - 从项目中下载 `public/chatSQL.yml` 文件
   - 在 Dify 平台中导入该配置文件
   - ![](https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefineddify.png?imageSlim)
3. 获取 API 密钥并在个人设置中配置（工作流默认使用 Gemini，可根据需要修改）

## 🤝 贡献

欢迎提交 Pull Request 和 Issue！



---

## 重要更新

### 25-04-30

- feat: 在主页的侧边栏增加了"初始化教程"功能, 提供不同难度的教程系列;

- feat: 增加分享链接, 可以导出当前的历史记录, 通过粘贴地址在不同设备之间共享(_notice_: 可能覆盖当前已有的记录)

### 25-05-11

- **feat**: 在"初始化教程"中增加了教材Database System Concepts中schema, 根据25春夏DB的PPT的字段要求在官网基础上调整得到.

- feat: 优化了代码编辑的体验(根据上下文修改补足的建议)

- style: 增加了loading的动画, 优化更新日志的页面
