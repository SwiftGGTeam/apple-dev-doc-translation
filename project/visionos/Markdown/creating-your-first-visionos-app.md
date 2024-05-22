# 创建你的第一个 visionOS 应用

使用 SwiftUI 构建新的 visionOS 应用，并添加平台特有的功能。

## 概述

如果你是 visionOS 的新手，请从一个新的 Xcode 项目开始，了解平台功能，并熟悉 visionOS 的内容和技术。当你为 visionOS 构建应用时，SwiftUI 是一个绝佳的选择，因为它能让你完全发挥 visionOS 的功能。虽然你也可以使用 UIKit 构建部分应用，但你需要使用 SwiftUI 来实现许多该平台的特有功能。

> 注意  
> 为 visionOS 开发需要配备 Apple 芯片的 Mac。

<video controls src="https://docs-assets.developer.apple.com/published/40f2eeb392d88ba3d5475db92289a1e3/multiple-apps-overview.mp4" width="100%" title="视频"></video>

在任何 SwiftUI 应用中，你都可以使用场景在屏幕上放置内容。场景包含了要在屏幕上显示的视图和控件。场景还定义了这些视图和控件在屏幕上显示时的外观。在 visionOS 中，你可以在同一场景中包含二维和三维视图，也可以将这些视图呈现在窗口或在用户周围环境中。

![img](https://docs-assets.developer.apple.com/published/2128a03dea1096ca3ba51b13db64a4af/2D-window@2x.png) | ![img-2](https://docs-assets.developer.apple.com/published/6adf1416ed7edab76dc30986f976e45f/2D-and-3D-window@2x.png)
----- | -----
包含窗口的场景 | 包含窗口和三维物体的场景

从一个新的 Xcode 项目开始，添加功能以熟悉 visionOS 的内容和技术。在模拟器中运行应用，验证内容是否符合你的预期，然后在设备上运行以见证栩栩如生的三维内容。

围绕一个或多个场景组织内容，场景决定了应用的界面。每个场景都包含了你要显示的视图和控件，而场景类型则决定你的内容采用二维还是三维外观。SwiftUI 专门为 visionOS 添加了三维场景类型，还为所有场景类型添加了三维元素和布局选项。

### 创建 Xcode 项目

在 Xcode 中选择 "文件">"新建">"项目"，创建一个新项目。导航至模板选择器的 visionOS 部分，然后选择应用模板。出现提示时，为项目指定名称和其他选项。

创建新的 visionOS 应用时，你可以在配置对话框中配置应用的初始场景类型。如果要在初始场景中主要显示二维内容，请选择 "窗口 "作为初始场景类型。如果主要显示三维内容，则选择 "体量"。你还可以添加一个沉浸式场景，将内容置于用户的周围环境中。

![截图](https://docs-assets.developer.apple.com/published/8ce394114263dbab766500aa0ae209a6/xcode-template-options~dark@2x.png)

当你想创建显示在应用中的三维素材或场景时，请包含一个 Reality Composer Pro 项目文件。使用此项目文件可从原始形状和现有的 USDZ 素材创建内容。你还可以用它为你的内容构建并测试自定义的 RealityKit 动画和行为。

### 修改现有窗口

使用标准 SwiftUI 视图构建初始界面。视图为界面提供了基本内容，你可以使用 SwiftUI 修饰符自定义视图的外观和行为。例如，`.background` 修饰符可在内容后方添加半透明的色调：

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

要进一步了解如何使用 SwiftUI 创建和配置界面，请参阅 [SwiftUI Essentials](https://developer.apple.com/tutorials/swiftui)。

### 处理视图中的事件

许多 SwiftUI 视图会自动处理交互，你只需在交互发生时提供要运行的代码即可。你还可以在视图中添加 SwiftUI 手势识别器，以处理轻点、长按、拖动、旋转和缩放手势。系统会自动将以下类型的输入映射到你的 SwiftUI 事件处理代码中：

![img](https://docs-assets.developer.apple.com/published/36b549ead5a549703b625119431fa6be/indirect-input@2x.png) | <span style="font-weight:normal">**间接输入**。用户眼睛指示出了互动目标。要开始互动，用户会将一只或两只手的拇指和食指并拢。其他的手指和手掌动作决定了手势的类型。</span> 
----- | :-----
![img-2](https://docs-assets.developer.apple.com/published/62fae83577ad55c14f38e0a51739f0f5/direct-input@2x.png) | **直接输入**。当用户手指与屏幕上的项目占据相同空间时，系统会报告一次交互。其他的手指和手掌动作决定了手势的类型。
![img-3](https://docs-assets.developer.apple.com/published/f94b7c0a6f4e2c59ab5b6d90d99eccc6/keyboard-input@2x.png) | **键盘输入**。用户可以使用连接的鼠标、触控板或键盘与项目互动，触发菜单命令并执行手势。

有关在 SwiftUI 视图中处理交互的更多信息，请参阅 [SwiftUI Essentials](https://developer.apple.com/tutorials/swiftui) 教程中的[处理用户输入](https://developer.apple.com/tutorials/swiftui/handling-user-input)。

### 构建并运行应用

在模拟器中构建并运行应用来查看外观。visionOS 模拟器有一个虚拟背景，作为应用内容的背景。使用键盘，鼠标或触控板在环境中导航，并与应用进行交互。

轻点并拖动应用内容下方的窗口栏，可调整窗口在环境中的位置。将指针移至窗口栏旁边的圆圈上，显示窗口的关闭按钮。将光标移至窗口的某个角落，可将窗口栏变成一个调整大小的控件。

> 备注  
> 应用无法控制窗口在空间中的位置。系统会将每个窗口放置在其初始位置，并根据与应用的进一步交互更新该位置。

有关如何在模拟器中与应用交互的更多信息，请参阅[在 visionOS 模拟器中与应用交互](https://developer.apple.com/documentation/Xcode/interacting-with-your-app-in-the-visionos-simulator)。

## 另见

### 创建应用

- [为应用添加 3D 内容](https://developer.apple.com/documentation/visionos/adding-3d-content-to-your-app)  
为你的 visionOS 应用添加深度和维度，并了解如何将应用的内容融入用户周围环境中。
- [在应用中构建完全沉浸式的体验](https://developer.apple.com/documentation/visionos/creating-fully-immersive-experiences)  
将 RealityKit 或 Metal 创建的内容与空间结合，构建完全沉浸式的体验。
- [在 visionOS 中绘制基于图层的清晰内容](https://developer.apple.com/documentation/visionos/drawing-sharp-layer-based-content)  
在 visionOS 中，从自定义的 Core Animation 图层中以多种分辨率提供文本和矢量图像。