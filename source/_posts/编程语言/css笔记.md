---
title: css笔记
date: 2025-01-08 18:33:32
tags:
- 前端
- CSS
index_img: /img/Aniya.png
categories: 学习笔记
excerpt: css属性好多好复杂😶‍🌫️
---
> [在线编辑平台](https://jsfiddle.net/?locale=zh_CN)


### 伪元素
使用伪元素`before`和`after`可以为元素添加内容.
```html
<div class="author">待抉</div>

<style>
.author:before {
  content: "Author: ";
  font-weight: bold; /* 加粗字体 */
  color: blue; /* 文本颜色为蓝色 */
}
.author:after {
  content: " ✍️";
  font-size: 1.2em; /* 调整图标大小 */
  margin-left: 5px; /* 添加左边距 */
}
</style>
```


`:nth-child`可用于对序列中不同的元素进行有序的样式设计:
```html
<div class="index-card">内容1</div>
<div class="index-card">内容2</div>
<div class="index-card">内容3</div>
<div class="index-card">内容4</div>

<style>
.index-card:nth-child(odd) {
  flex-direction: row-reverse;
}
</style>
```
- 选择所有类名为 "index-card" 的元素;
- `row-reverse` 会让 flex 容器中的项目从右到左排列;
> 同理, `even`选择偶数位置的元素; `3n+1`等函数表达式也可以起作用.

### 基本的元素属性
#### 布局属性
- `margin`: 控制元素的外边距;
- `padding`: 控制元素的内边距;

#### 文本属性
- `font-size`: 控制字体大小;
- `font-weight`: 控制字体粗细;
- `text-align`: 控制文本的对齐方式;
- `text-decoration`: 控制文本的装饰;
- `line-height`: 控制行高;
- `letter-spacing`: 控制字符间距;
- `text-transform`: 控制文本的大小写;

#### 边框属性
- `border`: e.g. `border: 1px solid red;` 简写属性;
- `border-radius`: 控制元素的圆角;
- `border-style`: solid, dashed, dotted...

#### 其他
- `overflow`: visible, hidden, scroll, auto;
- `opacity`: 控制元素的透明度;
- `cursor`: pointer, default, move, not-allowed...

### 动感魔法
#### hover效果
通过为class设置`:hover`伪类，可以为元素添加鼠标悬停时的效果。
```css
.my-element:hover {
  background-color: green;
  color: red;
  transform: scale(1.1);
  font-size:20px;
}
```
> 空格将不会被忽略, 需要确保类名与`:hover`之间不存在空格;

其他的一些常用的伪类:
```css
a:hover {
      color: red;
    }
    button:active {
      background-color: green;
    }
    input:focus {
      border-color: blue;
    }
    a:visited {
      color: purple;
    }
    p:first-child {
      font-weight: bold;
    }
    p:last-child {
      font-style: italic;
    }
    li:nth-child(2) {
      color: red;
    }
    p:only-child {
      color: green;
    }
    div:empty {
      background-color: yellow;
    }
```

### Flex
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    /* 定义Flex容器 */
    .container {
      display: flex; /* 将容器设置为Flexbox容器 */
      
      /* flex-direction: 定义主轴方向 */
      /* row: 水平从左到右（默认） */
      /* row-reverse: 水平从右到左 */
      /* column: 垂直从上到下 */
      /* column-reverse: 垂直从下到上 */
      flex-direction: row; /* 可以改为row-reverse, column, column-reverse */

      /* flex-wrap: 定义是否换行 */
      /* nowrap: 不换行（默认） */
      /* wrap: 换行 */
      /* wrap-reverse: 反向换行 */
      flex-wrap: wrap; /* 可以改为nowrap, wrap-reverse */

      /* flex-flow: flex-direction 和 flex-wrap 的简写 */
      /* flex-flow: <flex-direction> <flex-wrap>; */
      flex-flow: row wrap; /* 可以改为column nowrap等组合 */

      /* justify-content: 定义在主轴上的对齐方式 */
      /* flex-start: 起始对齐（默认） */
      /* flex-end: 末端对齐 */
      /* center: 居中对齐 */
      /* space-between: 两端对齐，项目之间间隔相等 */
      /* space-around: 项目之间间隔相等，项目两边有半个间隔 */
      /* space-evenly: 项目之间间隔相等，项目两边有完整间隔 */
      justify-content: space-between; /* 可以改为flex-start, flex-end, center, space-around, space-evenly */

      /* align-items: 定义在交叉轴上的对齐方式 */
      /* stretch: 拉伸适应容器（默认） */
      /* flex-start: 交叉轴起始对齐 */
      /* flex-end: 交叉轴末端对齐 */
      /* center: 交叉轴居中对齐 */
      /* baseline: 项目基线对齐 */
      align-items: center; /* 可以改为flex-start, flex-end, stretch, baseline */

      /* align-content: 定义多行的内容在交叉轴上的对齐方式（适用于多行时） */
      /* stretch: 拉伸适应容器（默认） */
      /* flex-start: 交叉轴起始对齐 */
      /* flex-end: 交叉轴末端对齐 */
      /* center: 交叉轴居中对齐 */
      /* space-between: 多行两端对齐，行之间间隔相等 */
      /* space-around: 多行之间间隔相等，行两边有半个间隔 */
      align-content: space-between; /* 可以改为flex-start, flex-end, center, space-around */
      
      height: 100vh; /* 设置容器高度 */
      background-color: #f0f0f0; /* 设置容器背景颜色 */
    }
    
    /* 定义Flex项目 */
    .item {
      flex: 1; /* 设置项目的flex属性，项目平分空间 */
      padding: 20px; /* 设置项目内边距 */
      background-color: lightblue; /* 设置项目背景颜色 */
      margin: 10px; /* 设置项目外边距 */
      text-align: center; /* 设置项目文本居中 */
      flex-grow: 1;/*尝试向flex容器扩展空间*/
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item">Item 1</div>
    <div class="item">Item 2</div>
    <div class="item">Item 3</div>
  </div>
</body>
</html>
```

