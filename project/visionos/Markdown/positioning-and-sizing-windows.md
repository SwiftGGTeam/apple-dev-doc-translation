# 定位和调整窗口大小

影响应用呈现的窗口的初始几何位置和大小。

## 概述

visionOS 和 macOS 允许用户移动和调整窗口大小。在某些情况下，你的应用可以使用场景修饰符来影响窗口在这些平台上的初始几何体，以及指定系统用于在窗口上设置最小和最大尺寸限制的策略。这种配置会影响窗口和立体窗口（具有 [`volumetric`](https://developer.apple.com/documentation/SwiftUI/WindowStyle/volumetric) 窗口样式的窗口）。

配置窗口大小和位置的能力受以下限制：

- 系统可能无法满足你的请求。例如，如果指定的默认大小超出了窗口大小的可调整范围，则系统会收缩受影响的尺寸以使其保持在合理范围内。
- 虽然你可以更改窗口的内容，但不能在窗口出现后直接操作窗口位置或大小。这确保了用户可以完全控制他们的工作空间。
- 在应用状态恢复的时候，系统会将窗口恢复到其以前的位置和大小。

> 备注  
> iPadOS 中的窗口占据全屏，或在“侧拉”（Slide Over）或“分屏浏览”（Split）中与另一个窗口共享屏幕。你无法以编程方式影响该平台上的窗口几何位置和大小。

### 指定窗口初始位置

在 macOS 中，当你的 App 首次从特定场景声明中打开窗口时，系统会默认将该窗口置于屏幕中央。对于支持多个同时并存窗口的场景类型，系统会将每个新添加的窗口偏移一小段距离，以避免完全遮挡现有窗口。

你可以通过应用 [`defaultPosition(_:)`](https://developer.apple.com/documentation/SwiftUI/Scene/defaultPosition(_:)) 场景修饰器来指示窗口相对于屏幕边界的放置位置，从而覆盖 macOS 中首个窗口的默认位置。例如，你可以请求系统在屏幕的右下角放置一个新窗口：

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .defaultPosition(.bottomTrailing)
    }
}
```

系统将窗口中与指定 [`UnitPoint`](https://developer.apple.com/documentation/SwiftUI/UnitPoint) 点相对应的点与屏幕中与同一单位点相对应的点对齐。你可以使用内置单位点（如上例所示的 [`bottomTrailing`](https://developer.apple.com/documentation/SwiftUI/UnitPoint/bottomTrailing)），也可以定义一个自定义单位点。

你不能在 visionOS 中使用 [`defaultPosition(_:)`](https://developer.apple.com/documentation/SwiftUI/Scene/defaultPosition(_:))。相反，系统会根据情况自动放置新窗口：

- 当用户首次从主页视图启动应用时，系统会相对于主页视图放置应用的窗口。
- 当正在运行的应用打开新窗口时，系统会将新窗口放在应用的现有窗口之一的前面。

### 指定窗口初始大小

你可以通过应用默认大小场景修饰器的其中一个（如 defaultSize(width:height:) ）来指示系统从 Scene 声明中创建的新窗口的默认初始大小。例如，你可以请求 WindowGroup 生成的新窗口在 x 维度中占据 600 磅，在 y 维度中占据 400 磅：

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .defaultSize(CGSize(width: 600, height: 400))
    }
}
```

系统可能会根据窗口的内容和可调整大小设置来限制窗口的实际大小。

### 指定窗口大小的可调整性

macOS 和 visionOS 都提供相应的界面控件，使用户能够在一定限制内调整窗口大小。例如，用户可以使用查看 visionOS 窗口角落时出现的控件来调整该系统平台上的窗口大小。

<video src="https://docs-assets.developer.apple.com/published/ea86e172731efbfe9b260375be987e5e/resizing-window.mp4" width="100%">
</video>


你可以指定系统对窗口尺寸调整的限制。所有场景的默认可调整性为 [`automatic`](https://developer.apple.com/documentation/SwiftUI/WindowResizability/automatic)。应用该策略时，[`Settings`](https://developer.apple.com/documentation/SwiftUI/Settings) 窗口使用该 [`contentSize`](https://developer.apple.com/documentation/SwiftUI/WindowResizability/contentMinSize) 策略， 其中最小和最大窗口尺寸都与窗口所包含内容的最小和最大尺寸相匹配。其他场景类型默认使用 [`contentMinSize`](https://developer.apple.com/documentation/SwiftUI/WindowResizability/contentMinSize) 策略 ，它保留了最小尺寸限制，但并不限制最大尺寸。

你可以通过向场景添加 [`windowResizability(_:)`](https://developer.apple.com/documentation/SwiftUI/Scene/windowResizability(_:)) 场景修改器来显式指定某个特定的可调整尺寸策略。例如，用户可以将窗口的大小从以下窗口组调整为两个维度的 100 到 400 磅之间，因为 frame 修饰器会将这些边界强加在内容视图上：

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
                .frame(
                    minWidth: 100, maxWidth: 400,
                    minHeight: 100, maxHeight: 400)
        }
        .windowResizability(.contentSize)
    }
}
```

你可以更进一步，为具有固定大小的内容的窗口强制指定尺寸。

### 指定一个立体窗口的大小

当你创建一个立体窗口（具有 [`volumetric`](https://developer.apple.com/documentation/SwiftUI/WindowStyle/volumetric) 窗口样式的窗口）时，你可以使用三维的默认尺寸修饰器（如 [`defaultSize(width:height:depth:in:)`](https://developer.apple.com/documentation/SwiftUI/Scene/defaultSize(width:height:depth:in:))）。以下代码创建一个边长为 1 米的立体窗口：

```swift
WindowGroup(id: "globe") {
    Globe()
}
.windowStyle(.volumetric)
.defaultSize(width: 1, height: 1, depth: 1, in: .meters)
```

立体窗口将在其整个生命周期内保持此大小。用户无法在运行时更改立体窗口的大小。

虽然你可以以“磅”为单位指定立体窗口的大小，但通常最好使用物理单位，如上面的代码，它以米为单位指定大小。这是因为与常规窗口不同，系统以固定缩放而不是动态缩放的形式来呈现立体窗口，这意味着立体窗口看起来更像是物理对象，而不是用户界面。有关不同类型缩放的信息，请参阅[空间布局](https://developer.apple.com/design/Human-Interface-Guidelines/spatial-layout)。

---

## 另见

### SwiftUI

- [你好，世界](https://developer.apple.com/documentation/visionos/world)  
使用窗口、体量和沉浸式空间让用户了解地球。
- [展示窗口和空间](https://developer.apple.com/documentation/visionos/presenting-windows-and-spaces)  
打开和关闭构成应用界面的场景。