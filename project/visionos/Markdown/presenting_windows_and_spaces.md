# 展示窗口和空间

打开和关闭组成应用界面的场景。


## 概述

一个应用程序的场景，包括了用户与之进行交互的视图，它可以有多样的形式。例如，场景可以被一个窗口、一个窗口中的选项卡或一整个其他场景所填满。有些场景甚至可以将视图置于用户的周围环境中。场景的出现方式取决于其类型、系统平台和上下文。

当用户启动你的 app 时，SwiftUI 会在你的 app 声明中查找首个 [WindowGroup](https://developer.apple.com/documentation/SwiftUI/WindowGroup) 、 [Window](https://developer.apple.com/documentation/SwiftUI/Window) 或 [DocumentGroup](https://developer.apple.com/documentation/SwiftUI/DocumentGroup) ，然后打开该类型的场景，通常会用一个新窗口或整个屏幕填充它，具体取决于系统平台。例如，在 macOS 中运行的以下应用会显示一个包含 MailViewer 视图的窗口：

```swift
@main
struct MailReader: App {
    var body: some Scene {
        WindowGroup(id: "mail-viewer") {
            MailViewer()
        }


        Window("Connection Status", id: "connection") {
            ConnectionStatus()
        }
    }
}
```

在 visionOS 中，您也可以将 app 配置为打开 app 声明的首个 [ImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) 。在任何情况下，特定的平台和配置都允许您一次打开多个场景。在这些条件下，您可以使用环境中出现的操作人为地打开和关闭应用中的场景。

### 检查多场景支持

如果您在不同系统平台上共享一套代码，并且需要在运行时了解当前系统是否支持显示多个场景，请读取 [supportsMultipleWindows](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/supportsMultipleWindows) 环境变量。以下代码创建了一个按钮，只有在 app 支持多个窗口时按钮才会显示：
    
```swift
struct NewWindowButton: View {
    @Environment(\.supportsMultipleWindows) private var supportsMultipleWindows
    @Environment(\.openWindow) private var openWindow


    var body: some View {
        Button("Open New Window") {
            openWindow(id: "mail-viewer")
        }
        .opacity(supportsMultipleWindows ? 1 : 0)
    }
}
```
    
你读取到的值由平台和你的 app 配置共同决定：

- 在 macOS 中，对于使用 SwiftUI app 生命周期的任何 app，该值返回 true 。
- 在 iPadOS 和 visionOS 中，对于使用 SwiftUI app 生命周期且 info plist 中键 [UIApplicationSupportsMultipleScenes](https://developer.apple.com/documentation/bundleresources/information_property_list/uiapplicationscenemanifest/uiapplicationsupportsmultiplescenes) 设为 true 的任何 app，此属性返回 true ，否则返回 false 。
- 对于所有其他系统平台和配置，该值返回 false 。

如果您的 app 仅在其中一种情况下运行，则可以默认其采取对应的行为，而无需检查该值。

### 同时启用多个共存场景

您始终可以在 macOS 中呈现多个场景。若要使 iPadOS 或 visionOS 上的 app 能够同时显示多个场景（包括 visionOS 中的 [ImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) 场景），请在 app 中 info.plist 里的 [UIApplicationSceneManifest](https://developer.apple.com/documentation/bundleresources/information_property_list/uiapplicationscenemanifest) 字典中添加值为 true 的 [UIApplicationSupportsMultipleScenes](https://developer.apple.com/documentation/bundleresources/information_property_list/uiapplicationscenemanifest/uiapplicationsupportsmultiplescenes) 键。在 Xcode 中，选择你 app 对应的一个 target 的 info 标签页以添加此键：

(Xcode 中的 info 标签页)[https://docs-assets.developer.apple.com/published/2f3b9f249fac0c2b9c0c720f4e49e253/presenting-windows-and-stages-1~dark@2x.png]

其他平台上的 app 在其生命周期内只能显示一个场景。

### 以编程方式打开窗口

某些系统平台提供内置控件，使用户能够打开 app 自定义的窗口样式的场景的实例。例如，在 macOS 中，用户可以从菜单栏中选取 File > New Window 以打开新窗口。SwiftUI 还为您提供了以编程方式打开新窗口的方法。

为此，请从环境中获取 [openWindow](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/openWindow) 操作，并使用标识符、值或同时使用两者调用它，以指示要打开的窗口类型以及要打开的窗口（可选）数据。当有人单击或轻按该按钮时，以下视图将打开之前定义的邮件查看器窗口的新实例：

```swift
struct NewViewerButton: View {
    @Environment(\.openWindow) private var openWindow


    var body: some View {
        Button("New Mail Viewer") {
            openWindow(id: "mail-viewer")
        }
    }
}
```

当该操作在支持多个场景的系统上运行时，SwiftUI 会在 app 声明中查找具有匹配标识符的窗口，并创建该类型的新场景。

> 重要
> 如果在 [supportsMultipleWindows](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/supportsMultipleWindows) 为 false 的情况下，您尝试打开一个新窗口，SwiftUI 会忽略该操作并记录一个运行时错误。

除了打开 app 主窗口的更多实例（如上例所示）之外，还可以打开 app 的 body 内声明的其他窗口类型。例如，您可以打开显示连接信息的 [Window](https://developer.apple.com/documentation/SwiftUI/Window) 实例：

```swift
Button("Connection Status") {
    openWindow(id: "connection")
}
```

### 以编程方式打开空间

在 visionOS 中，您可以打开一个沉浸式空间（可用于在用户周围环境中呈现无限内容的场景），其方式与打开窗口的方式大致相同，只需使用 [openImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/openImmersiveSpace) 操作。该操作以异步方式运行，因此在调用它时需使用 await 关键字，并且通常在一个 [Task](https://developer.apple.com/documentation/Swift/Task) 闭包内部执行此操作：

```swift
struct NewSpaceButton: View {
    @Environment(\.openImmersiveSpace) private var openImmersiveSpace


    var body: some View {
        Button("View Orbits") {
            Task {
                await openImmersiveSpace(id: "orbits")
            }
        }
    }
}
```

由于当您打开 [ImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) 场景时，您的应用在 ImmersiveSpace 中运行，因此您一次只能打开一个此类场景。如果尝试在空间已存在时再次打开空间，系统将记录一个运行时错误。

你的 app 可以在一个沉浸式空间中显示任意数量的窗口。。但是，当您从 app 中打开空间时，系统会隐藏属于其他应用程序的所有窗口。关闭您的 app 的空间后，其他应用的窗口会重新显示。同样地，如果另一个 app 打开沉浸式空间，系统会隐藏您的 app 的窗口。

### 将空间指定为 app 的主界面

当 visionOS 启动一个 app 时，它会打开 app 的 body 中声明的第一个窗口组、窗口或文档场景，就像在其他系统平台上一样。即使您先声明的是一个空间，也是如此。但是，如果要直接在沉浸式空间中打开 app，请将 [UIApplicationPreferredDefaultSceneSessionRole](https://developer.apple.com/documentation/bundleresources/information_property_list/uiapplicationpreferreddefaultscenesessionrole) 键添加到 app 的 info.plist 中并将其值设置为 UISceneSessionRoleImmersiveSpaceApplication ，从而将空间指定为 app 的默认场景。在这种情况下，visionOS 会打开它在 app 声明中找到的第一个空间。

> 重要
> 在以沉浸式空间启动 app 时，避免用户产生不知所措的感觉。有关设计指南，请参阅[《沉浸式体验》](https://developer.apple.com/design/Human-Interface-Guidelines/immersive-experiences)。

### 以编程方式关闭窗口

用户可以使用系统自带的控件关闭窗口，例如 macOS 窗口上面系统默认的关闭按钮。您当然也可以以编程方式关闭窗口。从环境中获取 [dismissWindow](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/dismissWindow) 操作，并使用要关闭的窗口的标识符来调用它：

```swift
private struct ContentView: View {
    @Environment(\.dismissWindow) private var dismissWindow


    var body: some View {
        Button("Done") {
            dismissWindow(id: "connection")
        }
    }
}
```

在 iPadOS 和 visionOS 中，如果要关闭是当前 app 唯一打开的场景，系统会忽略该操作。

### 以编程方式关闭空间

若要关闭空间，请调用 [dismissImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/dismissImmersiveSpace) 操作。与相应的打开空间的操作类似，关闭操作是异步执行的，并且需要 await 关键字：

```swift
private struct ContentView: View {
    @Environment(\.dismissImmersiveSpace) private var dismissImmersiveSpace


    var body: some View {
        Button("Done") {
            Task {
                await dismissImmersiveSpace()
            }
        }
    }
}
```

您无需为此操作指定标识符，因为一次只能打开一个空间。与窗口一样，你不能关闭 app 唯一打开的场景的空间。

### 在窗口和空间之间过渡

由于无法以编程方式关闭 visionOS app 中上次打开的窗口或沉浸式空间，因此请务必在关闭旧场景之前打开新场景。在窗口和沉浸式空间之间移动时，请特别注意事件发生的顺序，因为空间的打开和关闭操作是异步运行的。

例如，考虑一个国际象棋游戏，它首先在窗口中显示一个开始按钮。当有人点击按钮时，应用程序会关闭窗口并打开一个显示棋盘的沉浸式空间。以下按钮演示了一个正确的事件顺序--先打开空间然后关闭窗口：

```swift
Button("Start") {
    Task {
        await openImmersiveSpace(id: "chessboard")
        dismissWindow(id: "start") // Runs after the space opens.
    }
}
```

在上面的代码中，务必将 [dismissWindow](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/dismissWindow) 操作放在一个 Task 中，以便它会等待 [openImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/openImmersiveSpace) 操作完成后执行。如果将操作放在 Task 之外（无论是在 Task 之前还是之后），它可能会在异步打开操作完成之前执行，此时窗口仍是唯一打开的场景。在这种情况下，系统会打开空间，但不会关闭窗口。

## 另见

#### SwiftUI

- [Hello World](https://developer.apple.com/documentation/visionos/world)
使用窗口、立体空间和沉浸式空间向人们介绍地球。
- [Positioning and sizing windows](https://developer.apple.com/documentation/visionos/positioning-and-sizing-windows)
影响你的 app 所呈现窗口的初始几何布局。
