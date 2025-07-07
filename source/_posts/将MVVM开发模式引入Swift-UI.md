---
title: 将MVVM开发模式引入Swift-UI
date: 2025-07-07 11:07:39
tags:
- swift
- 编程语言

categories: 开发记录
excerpt: 以MAIC中的古琴设计模块为例, 梳理MVVM模式在Swift UI中的使用
thumbnail: https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250707112933696.png?imageSlim
---
**引言**

最近在以赛促学地使用swift语言开发一款ios端的应用。汲取了web端开发的一些经验和教训，开发的初期我意识到了类似于全局设置的“CSS”变量的重要性，以及框架结构的重要性。本篇文章将以开发过程中的“古琴设计”这一模块为例，介绍Swift UI中的MVVM开发模式的实践

### MVVM 模式

#### 什么是MVVM

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250706195118887.png?imageSlim"/>

> from 维基百科.

**MVVM**, i.e. Model–view–viewmodel 是一种软件架构模式, 将图形界面与业务逻辑的开发相分离:

- Model 模型: 数据和业务逻辑层, 包含对象基本的属性;
- view 视图: 用户界面层，负责展示内容，比如 SwiftUI 的各种界面组件;
- viewModel 视图模型: 连接 Model 和 View 的桥梁，负责处理数据、业务逻辑，并将数据以适合 View 展示的方式提供给 View

因此, MVVM框架的核心思想是**Model 负责定义底层数据, viewModel负责数据处理, view负责元素的显示**

Swift UI中数据绑定的功能, 比如如 `@State`, `@ObservedObject`, `@StateObject`, `@EnvironmentObject`, 使得不同模块之间的通信变得十分容易, 从而方便在Swift UI框架下实现MVVM风格的开发.

e.g.

```swift
struct ContentView: View {
    @StateObject var viewModel = ContentViewModel()
    var body: some View {
        Text(viewModel.title)
    }
}

class ContentViewModel: ObservableObject {
    @Published var title = "Hello, MVVM!"
}
```

#### MVVM的好处

作为一种分层架构模式, 它使得在Swfit UI框架下的开发更加接近web端开发的**前后端分离**. 一方面，通过MVVM将界面显示和数据更新的操作解耦，便于维护和测试；另一方面，Swfit语法的特性使得视图很容易自动响应视图模型的数据变化，界面和数据同步比React中更加简单

### 从一个开发需求实践MVVM

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250706200519943.png?imageSlim"/>

假设我们的开发目标如图所示——古琴的设计分为琴身和琴弦两大整体：

- 琴身内部的样式细分为形制、材质、铭文；
- 琴弦内部的样式细分为弦数、材质和音准

在交互方面，我们希望用户可以专注于选择其中一个古琴特征进行配置，并且可以保存当前的全局配置

---

#### Model

从上述的描述中，我们可以分析得到对应的数据模型，比如可以直接为固定的形制类型设计一个枚举及其内部对应的计算属性:

```swift
enum GuqinShape: String, CaseIterable, Identifiable, Codable {
    case fuxi = "伏羲式"
    case hundun = "混沌式"
    case zhenghe = "正和式"
    case zhongni = "仲尼式"
    case liezi = "列子式"

    var id: String { self.rawValue }

    /// 形制图片名称
    var imageName: String {
        switch self {
        case .fuxi:
            return "guqin_fuxi"
        ...
    }
```

> Codable 使得设计配置可以方便地以JSON格式加密和解密或者存储

分别为古琴的特征设计在model层面的数据模型之后，从视图的角度出发，我们需要根据用户选择的特征来显示对应的选项内容，因此需要额外定义类别的枚举：

```swift
enum CustomizationCategory: String, CaseIterable, Identifiable {
    case shape = "形制"
    case material = "材质"
    case inscription = "铭文"
    case stringsCount = "弦数"
    case stringsMaterial = "弦材质"
    case tuning = "音准"

    var id: String { self.rawValue }
    }
```

为了存储配置，以及显示当前的配置信息等要求，我们定义一个当前配置的结构体：

```swift
struct GuqinConfiguration: Codable, Equatable {
    var shape: GuqinShape
    var material: GuqinMaterialType
    var inscription: GuqinInscription
    var stringsCount: GuqinStringsCount
    var stringsMaterial: GuqinStringsMaterial
    var tuning: GuqinTuning   

    /// 默认配置
    // 使用 ' 避免成为关键字处理
    static let `default` = GuqinConfiguration(
        shape: .fuxi,
        material: .blackLacquer,
        inscription: .default,
        stringsCount: .sevenStrings,
        stringsMaterial: .nylon,
        tuning: .defaultSevenStrings,
        isShowingBack: false
    )

    /// 配置名称（用于保存和显示）
    var displayName: String {
        return "\(shape.rawValue) · \(material.rawValue) · \(stringsCount.rawValue)"
    }

    /// 根据弦数更新音准设置
    mutating func updateTuningForStringsCount() {
        tuning = GuqinTuning.defaultTuning(for: stringsCount)
    }
}
```

> `mutating`用于声明该方法可以改写结构体或者枚举的属性，因为出于数据安全的角度考虑， swift默认禁止值类型修改自身的属性

**总结**：模型层是纯粹的数据和业务规则的定义，它不关心 UI 如何展示这些数据，也不直接处理用户交互

#### viewmodel

视图模型是数据与逻辑的桥梁，让 UI 能够响应用户的操作并展示最新的数据

在 SwiftUI 中，视图模型通常是一个遵循 `ObservableObject` 协议的 `class`，并通过 `@Published` 属性发布其状态变化

```swift
// MARK: - Published Properties
    @Published var currentConfiguration: GuqinConfiguration = .default
    @Published var selectedCategory: CustomizationCategory = .shape
    @Published var isPreviewMode: Bool = false
```

**状态管理**：

* `@Published var currentConfiguration: GuqinConfiguration`：这是视图模型的核心。它持有 `GuqinConfiguration` 的实例，代表了用户当前定制古琴的实时状态。当 `currentConfiguration` 的任何属性发生变化时，由于 `@Published` 的存在，所有观察它的视图都会自动刷新，从而实现 UI 的响应式更新
* `@Published var selectedCategory: CustomizationCategory`：管理用户当前选择的定制类别（如形制、材质），这直接影响到 UI 中显示哪些具体的定制选项

**业务逻辑与方法**：我们在视图模型中定义一系列的公共方法，类似于oop中的方法，供其他对象调用（在这里是高层的视图）

* `selectShape(_ shape: GuqinShape)`、`selectMaterial(_ material: GuqinMaterialType)` 等方法是视图模型**暴露给视图的公共接口**。当用户在 UI 中选择一个形制或材质时，视图会调用 `viewModel` 中对应的方法。这些方法负责更新
  `currentConfiguration`，并可能包含额外的**业务逻辑**（如触觉反馈、动画触发）。

  ```swift
  func selectMaterial(_ material: GuqinMaterialType) {
          withAnimation(.easeInOut(duration: 0.3)) {
              currentConfiguration.material = material
          }
  
          // 触觉反馈
          let impactFeedback = UIImpactFeedbackGenerator(style: .medium)
          impactFeedback.impactOccurred()
      }
  ```

  > `withAnimation` 作为动画包裹器，接受一个动画参数和闭包作为输入，为闭包内部相关的UI变化自动赋予动画效果
  >

**数据转换（计算属性）**：通过使用计算属性，比如上述model的琴身形制中的 `imageName`返回在assests中的实际图片名称，此时在view中定义的方法很简单：

```swift
var currentShapeImageName: String {
        return currentConfiguration.shape.imageName
}
```

如果我们希望在view中使用默认的SF符号，可以在view中定义计算属性：

```swift
private var systemIconName: String {
        switch category {
        case .shape:
            return "rectangle.3.group"
        case .material:
            return "paintbrush.fill"
        case .inscription:
            return "text.word.spacing"
        case .stringsCount:
            return "waveform"
        case .stringsMaterial:
            return "fiberchannel"
        case .tuning:
            return "tuningfork"
        }
    }
```

然后可以直接在当前结构体（view本身也是结构体）中调用：

```swift
Image(systemName: systemIconName)
  .foregroundColor(isSelected ? .accentColor : .secondary)
```

* `currentShapeImageName`、`currentMaterialTextureImageName` 等计算属性：这些属性将 `currentConfiguration` 中的原始模型数据（如枚举值）转换为视图可以直接使用的格式（如图片名称字符串、`Color` 类型），避免视图直接处理复杂的模型逻辑，保持视图的简洁性

为了判断某个选择项是否被选中，比如下方“伏羲式”是选中的，需要处理额外的视觉、触觉处理：

<img src="https://my-blog-img-1358266118.cos.ap-guangzhou.myqcloud.com/undefined20250706220413622.png?imageSlim"/>

我们可以使用泛型的思想定义一个函数：

```swift
func isSelected<T: Equatable>(_ option: T, in category: CustomizationCategory) -> Bool {
    switch category {
    case .shape:
        if let shape = option as? GuqinShape {
            return currentConfiguration.shape == shape // 如果输入的参数类型正确，与当前的配置比较
        }
    ...
    default:
        break
    }
    return false
}
```

> 以shape为例，首先尝试将输入的泛型参数option转换为形制的类型；
>
> `as`是条件类型转换运算符：如果转换成功，返回转换之后的值；否则返回 `nil`
>
> 此处配置中的shape同样是一个枚举 `GuqinShape`，枚举值就是具体的形制名称 。所以上述的比较就是

我们也可以使用泛型约束的方式来改写上述检查：

```swift
func isSelected<T: Equatable>(_ option: T, in category: CustomizationCategory) -> Bool {
    switch category {
    case .shape:
        guard let shape = option as? GuqinShape else {return false}
      	return currentConfiguration.shape == shape
    ...
    default:
        break
    }
    return false
}
```

**总结**：视图模型是应用逻辑的核心。它从模型中获取数据，处理用户交互，更新模型状态，并通过 `@Published` 属性将最新的状态通知给视图。它充当了模型和视图之间“翻译官”和“协调者”。

---

#### view

视图层是用户直接看到和交互的部分。在 SwiftUI 中，视图是轻量级的 `struct`，它们声明式地描述了 UI 的外观，并响应视图模型发布的状态变化:

* **主视图 (`GuqinCustomizationView.swift`)**：

  * **职责**：作为古琴定制功能的主入口和布局容器, 不直接处理复杂的业务逻辑，而是将这些职责委托给视图模型和子视图。
  * **状态绑定**：通过 `@StateObject private var viewModel = GuqinCustomizationViewModel()` 实例化并持有视图模型。`@StateObject` 确保了视图模型在视图生命周期内只被创建一次，并且其 `@Published` 属性的变化能够驱动视图的刷新。

    ```swift
    @StateObject private var viewModel = GuqinCustomizationViewModel()
    ```
  * **UI 结构**：它将整个界面划分为几个主要区域：

    * `centerPreviewArea`：用于显示古琴的实时预览;
    * `categoryScrollView`：横向滚动的类别选择器;
    * `CustomizationOptionSelector`：底部的定制选项面板;
  * **交互委托**：视图中的按钮（如切换预览模式、重置）直接调用 `viewModel` 中对应的方法，将用户操作传递给视图模型处理。
* **组件（Components）——可重用的 UI 模块**：

  * **职责**：这些是更小、更专注的 UI 单元，它们封装了特定的 UI 元素和行为，提高了代码的复用性。
  * **`GuqinPreviewView`**：
    * **职责**：专门负责古琴的视觉渲染。它接收来自 `GuqinCustomizationViewModel` 的各种参数（如 `shapeImageName`、`materialTextureImageName`、`stringsImageName`、`inscription` 等），并利用 `ZStack`、`Image`、`mask`、`blendMode` 等 SwiftUI 修饰符，将这些图像层叠加组合，最终呈现出定制后的古琴
    * **状态传递**：它通过属性接收数据，而不是直接观察视图模型，这使得它成为一个“哑视图”（**Dumb View**），只负责展示.
  * **`OptionThumbnailButton`**：
    * **职责**：通用的选项缩略图按钮。
    * **状态传递**：接收 `thumbnailImage`、`title` 和 `isSelected` 等参数。`isSelected` 参数决定了按钮的视觉状态（如边框颜色、缩放效果），这个值通常来自视图模型。
    * **交互**：通过 `action` 闭包将点击事件回调给父视图（通常是 `CustomizationOptionSelector`），再由父视图调用视图模型的方法。

子视图中, 如果需要观察对应的视图模型, i.e. :

```swift
struct CustomizationOptionSelector: View {
    @ObservedObject var viewModel: GuqinCustomizationViewModel
  
    var body: some View {
        VStack(spacing: 12) {   
            // 选项滚动视图
            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 16) {
                    switch viewModel.selectedCategory {
                    case .shape:
                        ForEach(GuqinShape.allCases) { shape in
                          ...
                        }
```

> 使用视图模型中存储的“当前选中类别”的信息, 选择显示对应的选项滚动视图

此时, 由于我们已经在外层的视图创建并管理了一个视图模型的实例, 只需要将这个实例作为参数输入给子视图:

```swift
struct GuqinCustomizationView: View {
    @StateObject private var viewModel = GuqinCustomizationViewModel()
    @Environment(\.dismiss) private var dismiss
  
    var body: some View {
        NavigationView {
            GeometryReader { geometry in
                VStack(spacing: 0) {
                   ...

                    // 底部选择面板
                    CustomizationOptionSelector(viewModel: viewModel)
                        .frame(height: 140)
                }
            }
            ...
        }
```

* **节（Sections)** : 用于组织复杂视图
  * **职责**：将一个大视图分解成逻辑上独立的、可重用的“节”。每个节可以有自己的视图模型或直接观察父视图模型的一部分

---

### 状态变量的使用

我们之前提到过, Swift UI之所以适合使用MVVM模式, 是因为内置的一系列状态变量的优势. 此处补充总结几个状态变量的用途和示例:

* **`@State`**：

  * **用途**：用于管理视图内部的、简单的、私有的状态。当 `@State` 变量的值改变时，SwiftUI 会自动重新渲染使用该变量的视图。
  * **示例**：在 `InscriptionEditor` 中，`@State private var inscriptionText: String` 和 `@State private var fontSize: CGFloat` 就是典型的 `@State` 变量，它们管理着铭文编辑器的临时输入和字体大小。这些状态只在 `InscriptionEditor` 内部有意义，当它们需要影响到全局的古琴配置时，会通过调用 `viewModel.updateInscription()` 来更新 `viewModel` 中的 `currentConfiguration`。
* **`@StateObject`**：

  * **用途**：用于在视图的生命周期内创建并持有 `ObservableObject` 的实例。当视图首次出现时，`@StateObject` 会创建并初始化一个视图模型实例，并在视图的整个生命周期中保持这个实例。
  * **示例**：在 `GuqinCustomizationView` 中，`@StateObject private var viewModel = GuqinCustomizationViewModel()` 就是这样使用的。它确保了 `GuqinCustomizationViewModel` 在 `GuqinCustomizationView` 存在期间唯一，并且其 `@Published` 属性的变化能够驱动 `GuqinCustomizationView` 及其子视图的刷新;
* **`@ObservedObject`**：

  * **用途**：用于在**子视图中观察**父视图或其他来源传递过来的 `ObservableObject` 实例。它不负责创建实例，只负责观察。
* **`@Binding`**：

  * **用途**：用于在父视图和子视图之间创建双向绑定。子视图可以读取和修改绑定值，而这些修改会反映到父视图的原始数据源上。
  * **示例**：在 `TuningEditor` 中，`Slider` 的 `value` 参数使用了 `Binding`：

    ```swift
    Slider(
        value: Binding(
            get: { viewModel.currentConfiguration.tuning.stringTunings[index] },
            set: { newValue in
                var tuning = viewModel.currentConfiguration.tuning
                tuning.stringTunings[index] = newValue
                viewModel.updateTuning(tuning)
            }
        ),
        in: 0.0...1.0
    )
    ```

    这里创建了一个临时的 `Binding`，它从 `viewModel.currentConfiguration.tuning.stringTunings[index]` 获取值，并在值改变时调用 `viewModel.updateTuning()` 来更新视图模型中的数据。

 {% note info %} `Binding`通常用于修改父视图的部分属性, 不会持有或者管理数据, 只是引用父视图的数据 {%endnote %}

* **`@Environment`**：
  * **用途**：用于访问 SwiftUI 环境中提供的共享数据，如 `\.dismiss`（用于关闭视图）、`\.colorScheme` 等。

如果希望避免视图模型实例的层层嵌套的传递, 我们可以在创建和管理该实例的view中将其作为环境对象注入到子视图中:

```swift
struct GuqinCustomizationView: View {
    @StateObject private var viewModel = GuqinCustomizationViewModel() // 保持不变, 创建和管理实例
    @Environment(\.dismiss) private var dismiss
  
    var body: some View {
        NavigationView {
            GeometryReader { geometry in
                VStack(spacing: 0) {
                    // 古琴预览区域
                    centerPreviewArea
                        .frame(maxHeight: .infinity)

                    // 类别选择滚动条
                    categoryScrollView
                        .frame(height: 80)
                        .padding(.vertical, 8)

                    // 底部选择面板
                    CustomizationOptionSelector // 不再需要显式给出参数
                        .frame(height: 140)
                }
                .environmentObject(viewModel) // 注入环境对象, 供子视图直接访问
            }
```

对应的, 我们在需要访问该视图模型的子视图中声明观察:

```swift
struct CustomizationOptionSelector: View {
    @EnvironmentObject var viewModel: GuqinCustomizationViewModel // <-- 更改为 @EnvironmentObject

    var body: some View {
        // ... existing code ...
        // 内部使用 viewModel 的方式不变
        switch viewModel.selectedCategory {
        // ...
        case .inscription:
            // 传递给 InscriptionEditor 也不再需要显式参数
            InscriptionEditor() // <-- 不再需要 viewModel: viewModel
        case .tuning:
            // 传递给 TuningEditor 也不再需要显式参数
            TuningEditor() // <-- 不再需要 viewModel: viewModel
        }
        // ... existing code ...
    }
}
```

{% note success %} 只需要在顶层视图注入一次, 由此避免了繁琐的参数传递 {% endnote %}

---

### 数据流与事件流

现在，让我们把所有模块串联起来，看看它们是如何协同工作的：

**数据流向**：

* **自上而下（数据流）**：`ViewModel` 发布状态变化 (`@Published`) -> `View` 观察 (`@StateObject`/`@ObservedObject`) -> `View` 刷新 UI，并将数据传递给子视图（通过属性或 `@Binding`）。
* **自下而上（事件流）**：用户在 `View` 中交互 -> `View` 调用 `ViewModel` 的方法 -> `ViewModel` 更新 `Model` 状态 -> `ViewModel` 发布状态变化，循环开始。

这种模式使得代码高度解耦，每个模块只关注自己的职责

