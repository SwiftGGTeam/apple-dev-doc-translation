# 诊断运行中应用的外观问题

检查运行中的应用，以调查其显示内容的外观和位置问题。

## 概述

有时，应用的内容可能会出现缺失、位置不当或外观不正确的情况。要识别和诊断这些问题的原因，请连接调试器，重现错误，然后通过检查界面、执行代码和变量状态的变化，缩小其根本原因的范围。如果在配置方案的运行操作的“信息（Info）”标签下勾选了“调试可执行文件（Debug executable）”复选框，应用在使用该配置方案时就会自动附加调试器。要将调试器附加到已运行的进程，请选择“调试（Debug）” → “附加到进程（Attach to Process）”，然后从列表中选择应用进程。

临时覆盖系统设置来控制应用的外观，并揭示只有在这些设置生效时才会出现的问题；通过可视化堆叠层中的视图来了解布局问题；并通过添加视觉叠加来调试沉浸式空间中的内容。

### 调整系统配置以确定它们对视图的影响

有些视觉问题只有在使用特定环境设置配置系统时才会出现。Xcode 在针对 iOS、macOS 和 tvOS 应用时提供了环境覆盖，可帮助你调试这些问题。使用这些环境覆盖可更改界面风格、动态文本尺寸并触发其他辅助选项的效果，这样你就可以了解这些更改对视图布局和视觉外观的影响。

要启用一个或多个覆盖，请单击 Xcode 调试工具栏上的“环境覆盖（Environment Overrides）”按钮，切换覆盖类别旁边的开关，并在类别标题下配置控件。

<p align="center">
    <img src="https://docs-assets.developer.apple.com/published/6ee24e51a4fcc1324c96efe9f29f7a1c/diagnosing-issues-with-your-visual-content-1~dark@2x.png" width="40%">
</p>

外观  
    以浅色或深色外观查看应用内容。使用单选按钮在浅色和深色外观之间进行选择。

动态文本  
    使用不同的动态文本尺寸查看应用内容。使用滑块选择尺寸。有关动态文本的更多信息，请参阅[`DynamicTypeSize`](https://developer.apple.com/documentation/SwiftUI/DynamicTypeSize)。

辅助功能  
    查看各种辅助功能对应用内容的影响。单击复选框切换辅助功能。

### 解决 UIKit 和 SwiftUI 视图布局中的问题

使用视图调试器（针对 iOS、macOS、tvOS 和 watchOS 应用时可用）诊断用户界面中的项目位置错误或尺寸错误的原因。在应用显示视图后（例如在 `viewDidAppear:` 方法中）设置断点，然后当调试器在断点上暂停时，点击调试栏中的“调试视图层次（Debug View Hierarchy）”按钮。或者，在应用显示视图后单击“调试视图层次”按钮。

调试器会在画布中央显示当前视图的 3D 效果图，并在调试导航器中显示视图层次结构。向任意方向拖动视图，就能看到当前视图堆栈的 3D 呈现，还能使用画布底部的控件调整视图和视图之间的间距。

<img src="https://docs-assets.developer.apple.com/published/ecbf24fb595b4cbde72f4c251adbe821/diagnosing-issues-with-your-visual-content-2@2x.png">

在调试导航器的可视化渲染或视图层次中单击选择视图，然后在对象检查器或尺寸检查器中检查详细信息。根据布局类型解决布局问题：

基于框架的布局  
    视图调试器会显示你在尺寸检查器中指定的框架。如果尺寸与预期不符，请逐步检查代码，诊断框架计算中的问题。然后修复并重新测试。

使用自动布局的视图  
    尺寸检查器显示约束。单击一个约束，在视图调试器中将其突出显示。分析影响错位或尺寸错误视图的约束以诊断问题。在代码或“界面构建器（Interface Builder）”中调整约束，然后重新测试。

使用 SwiftUI 构建视图  
    尺寸检查器显示 SwiftUI 如何解析视图的尺寸和位置。利用这些信息，分析和调整 SwiftUI 代码并重新测试。

### 了解沉浸式空间中对象之间的关系

对于在沉浸式空间中添加内容的 visionOS 应用来说，查看坐标轴、边界框和其他通常不可见的信息的可视化表示通常很有帮助。Xcode 的调试工具包含在模拟器或设备上显示这些信息的选项。使用这些工具可确保实体位于你所期望的位置，并以你所期望的方式与其他实体和周围环境进行交互。例如，如果一个实体没有响应事件，请启用“碰撞形状（Collision Shapes）”来确认事件处理所需的实体是否存在，并指示其边界。

<img src="https://docs-assets.developer.apple.com/published/67338eb7f258bc26498c2695617d6bff/diagnosing-issues-in-the-appearance-of-a-running-app-1~dark@2x.png">

要使用叠加层对内容进行注释，请单击 Xcode 调试工具栏上的“调试可视化（Debug Visualization）”按钮，然后选择一个或多个选项：

锚定  
    红色、绿色和蓝色箭头表示每个锚点的 x、y 和 z 轴，实体与其锚点之间有一条黄线。使用此功能来了解实体与空间中其他真实世界物体的位置关系。

轴线  
    红色、绿色和蓝色箭头表示空间中每个物体的 x、y 和 z 轴。使用它可以了解每个物体的方向。

边界  
    绿线表示每个实体的边界边缘。

碰撞形状和轴线  
    实体碰撞形状周围的灰色轮廓，以及表示实体轴线的红色、绿色和蓝色箭头。使用它们来了解事件检测中的问题。

遮挡网格  
    围绕空间中真实物体的多色线框。用它来识别虚拟物体正确或错误地隐藏在真实物体后面的区域。

平面  
    白色边界线和对角线标记了平面。利用它可以了解系统检测到的平面的边界。

> 注意  
> 锚定、轴线和边界选项适用于正在调试的应用实体，而碰撞形状和轴线选项适用于共享空间中的所有实体。遮挡网格和平面选项适用于系统在周围环境中检测到的对象。

---

## 另见

### 调试策略

- [及早诊断内存、线程和崩溃问题](https://developer.apple.com/documentation/xcode/diagnosing-memory-thread-and-crash-issues-early)  
在使用 Xcode 的清理工具进行测试期间，识别 App 中的运行时崩溃和未定义的行为。
- [使用 Instruments 分析 HTTP 流量](https://developer.apple.com/documentation/foundation/url_loading_system/analyzing_http_traffic_with_instruments)  
衡量应用的基于 HTTP 的网络性能和使用情况。
- [检测应用何时访问可能分析用户档案的网域](https://developer.apple.com/documentation/xcode/detecting-when-your-app-contacts-domains-that-may-be-profiling-users)  
使用 Instruments 评估应用或其第三方 SDK 是否访问有可能分析用户档案的网域。