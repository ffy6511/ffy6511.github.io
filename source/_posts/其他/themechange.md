---
title: React 主题切换
date: 2025-01-23 10:06:34
tags:
categories:
excerpt: 通过CSS变量和上下文管理, 以React框架为例介绍主题的变色设置.
mathjsx: true
---
$\underline{主题切换}$  即通过点击某个组件来切换背景与文字的颜色等 CSS 属性。这要求我们的组件能够控制某个"环境变量", 且`index.css`中的 CSS 应随这个"环境变量"而改变。

要实现这个需求,我们需要解决以下几个问题：

1. 如何定义和管理这个"环境变量"？
2. 组件如何控制这个变量？
3. CSS 如何响应变量的变化？

这些问题的解决方案涉及到几个重要的基础概念,让我们逐一了解。

## 基础知识
### Context 的创建和使用

**Context** 提供了一种在组件树中共享数据的方式,无需手动在每一层传递 props。

```tsx
// 创建 Context
const ThemeContext = createContext<ThemeType | undefined>(undefined);

// 提供 Context
const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState('light');
  
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

// 使用 Context
const ChildComponent = () => {
  const { theme } = useContext(ThemeContext);
  return <div>Current theme: {theme}</div>;
};
```
因此, 我们可以在`src/context/`目录下创建一个`ThemeContext`来定义和管理相关上下文.
- 在其中利用`useState`定义主题反转的函数;
- 在点击按钮组件中使用上述的函数,

### CSS 变量

CSS 变量(也称为自定义属性)允许我们定义可重用的值：

```css
/* 定义变量 */
:root {
  --primary-color: #007bff;
}

/* 使用变量 */
.button {
  background-color: var(--primary-color);
}
```
1. 为了区分CSS的变量与常量, `--xx-y`的命名格式是CSS变量的规范;
2. 通过`var(<c--xx-y>)`的形式使用CSS变量.

### HTML data-* 属性

`document.documentElement.setAttribute('data-theme', theme)` 的作用是在 HTML 根元素上设置一个自定义数据属性：

```js
// JavaScript 设置
document.documentElement.setAttribute('data-theme', 'dark');

// 结果的 HTML
<html data-theme="dark">
  ...
</html>

// 对应的 CSS
[data-theme='dark'] {
  --bg-color: #141414;
}
```

## 主题切换实现

### 主题变量设计

首先设计主题相关的 CSS 变量：

```css
:root {
  /* Light theme variables */
  --bg-color: #ffffff;
  --text-color: #000000;
  --sidebar-bg: #f0f2f5;
  --border-color: #e5e2e2;
  --shadow-color: rgba(0, 0, 0, 0.1);
}

[data-theme='dark'] {
  --bg-color: #141414;
  --text-color: #ffffff;
  --sidebar-bg: #1f1f1f;
  --border-color: #434343;
  --shadow-color: rgba(0, 0, 0, 0.3);
}
```
> 可根据实际需要增减CSS变量.

### 主题状态管理

创建主题 Context 进行状态管理：

```typescript
import React, { createContext, useState, useContext, useEffect } from 'react';

type Theme = 'light' | 'dark';

interface ThemeContextType {
  theme: Theme;
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export const ThemeProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [theme, setTheme] = useState<Theme>(() => {
    const savedTheme = localStorage.getItem('theme');
    return (savedTheme as Theme) || 'light';
  });

  useEffect(() => {
    document.documentElement.setAttribute('data-theme', theme);
    localStorage.setItem('theme', theme);
  }, [theme]);

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
};
```

<br>

在设置上下文组件之后, 我们需要明确其作用的范围. 我们希望主题变色需要在全局范围内生效, 以react框架为例:
```html
<!-- App.tsx -->
import { ThemeProvider } from './context/ThemeContext';

function App(){
    ...
    return (
        <ThemeProvider>
            <div className = "App">
                ...
            </div>
        </ThemeProvider>
    )
}

```
### 主题切换组件

实现主题切换按钮：

```typescript
import React from 'react';
import { Tooltip } from 'antd';
import { useTheme } from '../context/ThemeContext';
import { BsSun, BsMoonStars } from 'react-icons/bs';
import styles from './ThemeToggle.module.css';

const ThemeToggle: React.FC = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <Tooltip title={theme === 'light' ? '切换到暗色模式' : '切换到亮色模式'} placement="right">
      <button 
        className={styles.themeToggle} 
        onClick={toggleTheme}
        aria-label="Toggle theme"
      >
        {theme === 'light' ? <BsMoonStars /> : <BsSun />}
      </button>
    </Tooltip>
  );
};

export default ThemeToggle;
```
1. 此处使用`antd`的`Tooltip`, 用于在鼠标悬浮按钮组件时显示文字提示;
2. `aria-label="Toggle theme"` 在`<button>`内设置这个属性不是必要的, 但是可以帮助屏幕阅读器读出 "Toggle theme“.


### 组件样式应用
在组件内部使用CSS变量的方式已经在 [CSS变量](#CSS-变量) 中介绍, 在此给出示例:
```css
.sidebar_container {
    background-color: var(--sidebar-bg);
    color: var(--text-color);
}

.icon_button {
    color: var(--text-color);
    background-color: transparent;
}

.icon_button:hover {
    background-color: var(--sidebar-hover-color);
}
```

<br>

如果某个CSS不需要作为变量进行统一管理, 可以直接使用 **属性选择器** 进行单独设置:
```css
[data-theme='dark'] .icon_example{
  --bg-color: #141414;
}
``` 

## 总结与参考
通过以上实现,我们构建了一个完整的主题切换系统。关键点包括：

1. 使用 CSS 变量管理主题样式
2. 通过 Context API 实现状态管理
3. 利用 data-theme 属性切换主题
4. 本地存储保持主题持久化

### 参考 🔗
1. [React Context API](https://react.dev/reference/react/useContext)
2. [CSS Custom Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)
3. [HTML data-* Attributes](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes)