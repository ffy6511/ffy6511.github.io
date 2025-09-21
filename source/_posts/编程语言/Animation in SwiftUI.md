---
title: Animation in SwiftUI
date: 2025-09-21 18:32:0
tags:
  - swift
  - SwiftUI
categories: 简单的备忘录
excerpt: 记录SwiftUI中的动画设置, 包括原生实现、拓展以及使用第三方库Lottie
mathjax: false
thumbnail: https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250921183549494.png?imageSlim
---

### Custom Transition

扩展 `AnyTransition`：

```swift
extension AnyTransition {
    static var slideAndFade: AnyTransition {
        let insertion = AnyTransition.move(edge: .trailing)
                        .combined(with: .opacity)
        let removal = AnyTransition.scale
                        .combined(with: .opacity)
        return .asymmetric(insertion: insertion, removal: removal)
    }
}
```

然后直接在视图中使用：

```swift
MyView()
    .transition(.slideAndFade)
```

#### Animation Sequence

我们可以通过定义多个 `animation` 修饰符来创建一个复杂的动画序列：

```swift
struct ContentView: View {
    @State private var isAnimating = false

    var body: some View {
        VStack {
            Button("Animate") {
                isAnimating.toggle()
            }

            Circle()
                .frame(width: 100, height: 100)
                .foregroundColor(.blue)
                .scaleEffect(isAnimating ? 1.5 : 1)
                .opacity(isAnimating ? 0.5 : 1)
                .rotationEffect(.degrees(isAnimating ? 360 : 0))
                .animation(.easeInOut(duration: 1), value: isAnimating)
                .animation(.easeOut(duration: 1).delay(0.5), value: isAnimating)
                .animation(.linear(duration: 1).delay(1), value: isAnimating)
        }
    }
}
```

#### Gesture-Driven

> [link](https://diegoperezsalas.medium.com/mastering-animations-in-swiftui-creating-smooth-and-engaging-interactions-eea741869628)

```swift
struct DraggableCircleView: View {
    @GestureState private var dragOffset = CGSize.zero

    var body: some View {
        Circle()
            .frame(width: 100, height: 100)
            .foregroundColor(.green)
            .offset(dragOffset)
            .gesture(
                DragGesture()
                    .updating($dragOffset, body: { (value, state, transaction) in
                        state = value.translation
                    })
            )
            .animation(.spring(), value: dragOffset)
    }
}
```

### 自定义的动画效果

我们可以使用本地变量，结合 `onAppear`修饰符，自定义页面出现之后元素的状态变化：

首先我们创建一个本地变量：

```swift
// 结构体内部的属性默认是 immutable不可变 的，因此必须使用 @State 来声明
@State private var isAnimating = false
```

然后我们在页面初次加载的时候将其设置为 `true`：

```swift
ZStack {
    // 背景
    CustomAngularGradient()

    ScrollView {
			...
        }
    }
    .scrollContentBackground(.hidden)
}
.onAppear{
    isAnimating = true
}
.navigationBarBackButtonHidden(true)
```

其中，`onAppear` 修饰符的完整写法为：

```swift
.onAppear(perform: {
    isAnimating = true
})
```

> 参数 `perform` 是一个 `() -> void`的函数

最后，我们可以组件上配合基本的 `opacity`, `offset`, 以及 `animation`修饰符完成动画效果的定义：

```swift
.opacity(isAnimating ? 1: 0)
.offset(y: isAnimating ? 0: 40)
.animation(.easeOut(duration: 0.5), value: isAnimating)
```

其中， `value: isAnimating` 表示当对应的值发生变化时，采取第一个参数指定的动画效果对涉及的 UI 进行变换

我们可以借助 `onChange`修饰符，在改变特定属性的时候，重新渲染动画：

```swift
.onChange(of: viewModel.selectedPeriod) {
    isAnimating = false
    // 数据变化时重新播放动画
    withAnimation(.easeInOut(duration: 1.0)) {
        isAnimating = true
    }
}
```

> 先重置状态，然后再用 1s 的时间将状态改变（easeInOut 适用于没有设置动画效果的 UI 变化）

```swift
withAnimation(.easeOut(duration: 0.5)) {
    showSuccessAnimation = true
    showTrackingResult = true
}
```

#### 延迟播放

如果我们不希望立即执行 `onAppear` 的代码，可以使用下面的方式延迟执行：

```swift
DispatchQueue.main.asyncAfter(deadline: .now() + delay) {
    // 延迟执行的代码
}
```

e.g.

```swift
.onAppear {
    DispatchQueue.main.asyncAfter(deadline: .now() + 3.0) {
        isAnimating = true
    }
}
```

#### repeat

如果希望重复播放动画，只需要加上对应的修饰符：

```swift
.animation(
	Animation
    .easeInOut(duration: 2)
    .repeatForever(),
value: isAnimating)
```

#### 几何匹配过渡

```swift
func matchedGeometryEffect<ID>(id: ID, in namespace: Namespace.ID,
    properties: MatchedGeometryProperties = .frame,
    anchor: UnitPoint = .center,
    isSource: Bool = true) -> some View where ID: Hashable
```

- `id`: 唯一标识符，要匹配的视图使用相同的 id；
- `namespace`: 使用 `@Namespace` 声明，用于将配对的视图绑定在一起；
- `properties`: 可选，默认 .frame，还可设置为 .size 或 .position，控制哪些属性参与动画；
- `anchor`: 动画参考点，默认 .center；
- `isSource`: 布尔值，指定该视图是否为动画的起始源（source）

> 只能用来处理位置和大小的变化，无法过渡颜色

#### 缩放导航过渡

在 `NavigationStack`中定义页面跳转的动画

```swift
NavigationStack {
    NavigationLink {
        DetailView()
            .navigationTransition(.zoom(sourceID: "zoom", in: namespace))
    } label: {
        SourceView()
            .matchedTransitionSource(id: "zoom", in: namespace)
    }
}
```

源视图与目标视图通过相同 `id` 和 `namespace` 链接，形成缩放动画。因此在 `ForEach`情况下，我们需要根据每个元素的标识符来设置缩放的 `id`：

```swift
ForEach(items) { item in
    NavigationLink {
        DetailView(item: item)
            .navigationTransition(.zoom(sourceID: item.id, in: namespace))
    } label: {
        RoundedRectangle(cornerRadius: 8)
            .fill(item.color)
            .frame(width: 80, height: 80)
            .overlay(Text(item.name).foregroundColor(.white))
            .matchedTransitionSource(id: item.id, in: namespace)
    }
}
```

### 使用 Lottie

> [link](https://www.youtube.com/watch?v=u9xo-XdcRV8)

#### 前言

文件类型分为 `json` 和 `dot`，后者是新出现的、性能更优、占比更小，但是不支持同步

**组件库：**

```swift
import Lottie
```

**基本使用：**

```swift
struct LottieBootcamp: View｛
  var body: some View｛
    LottieView（animation：.named（"StarAnimation.json")）
			.playbackMode(.playing(.toProgress(1, loopMode: .playOnce)))
			.animationDisFinish{ completed in
        // some coidngs here after animation
      }
  ｝
｝
```

#### 通用封装组件

定义一个通用的包装，方便我们直接调用：

```swift
//
//  LottieHelperView.swift
//  CarbonCoin
//
//  Created by Zhuo on 2025/9/4.
//

import SwiftUI
import Lottie

struct LottieHelperView: View {
    var fileName: String = "success.json"
    var contentMode: UIView.ContentMode = .scaleAspectFill
    var playLoopMode: LottieLoopMode = .playOnce
    var onAnimationDidFinish : (() -> Void)? = nil

    var body: some View {
        LottieView(animation: .named(fileName))
            .configure({ lottieAnimationView in
                lottieAnimationView.contentMode = contentMode
            })
            .playbackMode(.playing(.toProgress(1, loopMode: playLoopMode)))
            .animationDidFinish{completed in
                onAnimationDidFinish?()
            }
    }
}

#Preview {
    LottieHelperView()
}

```

我们实际调用的时候可以使用尾随闭包， 但是注意参数的顺序：

```swift
// 例如新增进度

var playLoopMode: LottieLoopMode = .playOnce
var animationProgress: AnimationProgressTime = 1

var onAnimationDidFinish : (() -> Void)? = nil
```

```swift
// 动画视图
if showSuccessAnimation {
    LottieHelperView(
        fileName: "success.json",
        playLoopMode: .playOnce,
        animationProgress: 0.5
    ) {
        showSuccessAnimation = false // 动画结束后隐藏
    }
    .frame(maxWidth: .infinity, maxHeight: .infinity)
    .background(Color.black.opacity(0.5)) // 添加背景遮罩
    .ignoresSafeArea()
}
```

因为只有当传入参数的最后一个是函数的时候，我们才可以直接用 `{ }` **尾随闭包**的形式。因此新定义的进度参数也应该放在前面
