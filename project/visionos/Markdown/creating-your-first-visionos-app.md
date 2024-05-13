# 创建第一个 visionOS 应用程序

使用 SwiftUI 构建新的 visionOS 应用程序，并添加特定于平台的功能。

## 概述

如果您是 visionOS 的新手，请从一个新的 Xcode 项目开始，了解平台功能，并熟悉 visionOS 的内容和技术。当您为 visionOS 构建应用程序时，SwiftUI 是一个很好的选择，因为它能让您完全使用 visionOS 的功能。虽然您也可以使用 UIKit 构建部分应用程序，但您需要使用 SwiftUI 来实现该平台独有的许多功能。

> 注意  
> 为 visionOS 开发需要配备 Apple 芯片的 Mac。

<video controls src="https://docs-assets.developer.apple.com/published/40f2eeb392d88ba3d5475db92289a1e3/multiple-apps-overview.mp4" title="视频"></video>

在任何 SwiftUI 应用程序中，您都可以使用场景在屏幕上放置内容。场景包含要在屏幕上显示的视图和控件。场景还定义了这些视图和控件在屏幕上显示时的外观。在 visionOS 中，您可以在同一场景中包含 2D 和 3D 视图，还可以将这些视图显示在窗口中或作为人周围环境的一部分。

column1 | column2
----- | -----
有窗户的场景 | 带有窗口和 3D 物体的场景

从一个新的 Xcode 项目开始，添加功能以熟悉 visionOS 的内容和技术。在模拟器中运行应用程序，验证您的内容是否符合您的预期，然后在设备上运行，看看您的 3D 内容是否栩栩如生。

围绕一个或多个场景组织内容，这些场景管理应用程序的界面。每个场景都包含您要显示的视图和控件，而场景类型则决定您的内容是采用 2D 还是 3D 外观。SwiftUI 专门为 visionOS 添加了 3D 场景类型，还为所有场景类型添加了 3D 元素和布局选项。

### 创建 Xcode 项目

在 Xcode 中选择 "文件">"新建">"项目"，创建一个新项目。导航至模板选择器的 visionOS 部分，然后选择应用程序模板。出现提示时，为项目指定名称和其他选项。

创建新的 visionOS 应用程序时，您可以在配置对话框中配置应用程序的初始场景类型。如果要在初始场景中主要显示 2D 内容，请选择 "窗口 "作为初始场景类型。如果主要显示 3D 内容，则选择 "卷轴"。您还可以添加一个身临其境的场景，将您的内容置于人的周围环境中。

![截图](https://docs-assets.developer.apple.com/published/8ce394114263dbab766500aa0ae209a6/xcode-template-options~dark@2x.png)

当你想创建 3D 资产或场景以在你的应用程序中显示时，请包含一个 Reality Composer Pro 项目文件。使用此项目文件可从原始形状和现有的 USDZ 资产创建内容。你还可以用它来为你的内容构建和测试自定义的 RealityKit 动画和行为。

### 修改现有窗口

使用标准 SwiftUI 视图构建初始界面。视图为你的界面提供了基本内容，你可以使用 SwiftUI 修改器自定义视图的外观和行为。例如，.background 修饰符可在内容后面添加部分透明的色调：

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
               .background(.black.opacity(0.8))
        }


        ImmersiveSpace(id: "Immersive") {
            ImmersiveView()
        }
    }
}
```

要进一步了解如何使用 SwiftUI 创建和配置界面，请参阅 SwiftUI 要点。

### 在视图中处理事件

许多 SwiftUI 视图会自动处理交互，您只需在交互发生时提供要运行的代码即可。您还可以在视图中添加 SwiftUI 手势识别器，以处理轻点、长按、拖动、旋转和缩放手势。系统会自动将以下类型的输入映射到您的 SwiftUI 事件处理代码中：

**间接输入**。人的眼睛指示互动的目标。要开始互动，人的一只手或两只手的拇指和食指会碰在一起。手指和手掌的其他动作决定了手势的类型。

**直接输入**。当人的手指与屏幕上的项目占据相同空间时，系统就会报告一次交互。手指和手掌的其他动作可定义手势类型。

**键盘输入**。人们可以使用连接的鼠标、触控板或键盘与项目互动，触发菜单命令并执行手势。

有关在 SwiftUI 视图中处理交互的更多信息，请参阅 [SwiftUI Essentials](https://developer.apple.com/tutorials/swiftui) 教程中的[处理用户输入](https://developer.apple.com/tutorials/swiftui/handling-user-input)。

### 构建并运行应用程序

在模拟器中构建并运行应用程序，看看它的外观如何。visionOS 模拟器有一个虚拟背景，作为应用程序内容的背景。使用键盘和鼠标或触控板在环境中导航，并与应用程序进行交互。

轻点并拖动应用程序内容下方的窗口栏，可调整窗口在环境中的位置。将指针移至窗口栏旁边的圆圈上，显示窗口的关闭按钮。将光标移至窗口的某个角落，可将窗口栏变成一个调整大小的控件。

> 备注  
> 应用程序无法控制窗口在空间中的位置。系统会将每个窗口放置在其初始位置，并根据与应用程序的进一步交互更新该位置。

有关如何在模拟器中与应用程序交互的更多信息，请参阅[在 visionOS 模拟器中与应用交互](https://developer.apple.com/documentation/Xcode/interacting-with-your-app-in-the-visionos-simulator)。

## 另见

