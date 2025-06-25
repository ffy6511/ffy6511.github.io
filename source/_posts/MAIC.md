---
title: MAIC
date: 2025-05-17 19:35:35
tags:
- swift
excerpt: 记录swift学习和开发的过程
categories: 
- 开发记录
thumbnail: https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250625150337542.png?imageSlim
---

### 主题'CSS'的设计

#### AppTheme

为了方便统一管理颜色，我们可以创建一个主题相关的结构体，并设置主题相关的环境变量：

```swift
import SwiftUI

struct AppTheme {
    // 通过 Assets.xcassets 定义的颜色
    let primaryColor = Color("PrimaryColor")
    let secondaryColor = Color("SecondaryColor")

    // 直接在代码中定义颜色
    let backgroundColor = Color(UIColor.systemGray6)

    // 字体
    let largeTitleFont = Font.system(size: 34, weight: .bold)
    let bodyFont = Font.system(size: 17, weight: .regular)

    // 间距
    let paddingSmall: CGFloat = 8
    let paddingMedium: CGFloat = 16
    let paddingLarge: CGFloat = 24

    // 圆角
    let cornerRadius: CGFloat = 12
}
```

> 注意不能设置此处的变量为 `static`， 否则无法在其他文件中使用.

- 在Assets中定义颜色：

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250517193908985.png?imageSlim"/>

#### 导入主文件

在主文件中导入主题相关的结构体，并设置环境变量：

```swift
import SwiftUI

@main
struct MAICApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environment(\.appTheme, AppTheme()) // 将 AppTheme 注入到环境中
        }
    }
}
```

#### 使用主题

在其他文件中，可以通过环境变量来访问主题相关的变量：

```swift
import SwiftUI
struct ContentView: View {
    @Environment(\.appTheme) var appTheme: AppTheme // 从环境中获取 AppTheme

    }
```

### 设置底部导航栏

#### 基础的Tab视图

```swift
struct MainView: View {
    var body: some View {
        TabView{
            HomeView()
                .tabItem {
                    Label("花园",systemImage: "house.circle")
                }
          
            HealthView()
                .tabItem {
                    Label("健康",systemImage: "heart.circle")
                }
          
            MeditationView()
                .tabItem{Label("冥想",systemImage: "figure.mind.and.body.circle")
                }
          
            SettingsView()
                .tabItem{
                    Label("设置",systemImage: "gear.circle")
                }
          
        }
    }
}
```

- 导入各个定义好的视图；
- `Label`设置导航栏的文字；
- `systemImage`设置导航栏的图标

然后在项目入口中指定这个视图：

```swift
@main
struct MAICApp: App {
    var body: some Scene {
        WindowGroup {
            MainView()
                .environment(\.appTheme, AppTheme()) // 将 AppTheme 注入到环境中
        }
    }
}
```

#### 设置触感反馈

为了在切换底部导航栏时，提供触感反馈，我们可以设置一个状态变量，记录当前的Tab：

```swift
@State private var selectedTab = 0

var body: some View {
    TabView(selection: $selectedTab){
        HomeView()
                    .tabItem {
                        Label("花园",systemImage: "house.circle")
                    }
                    .tag(0)
    ...
    }
    .sensoryFeedback(.selection, trigger: selectedTab)
```

- `selection` 是一种标准的系统触觉反馈，通常用于表示用户在选择器、列表或其他 UI 元素中进行了选择或状态改变
