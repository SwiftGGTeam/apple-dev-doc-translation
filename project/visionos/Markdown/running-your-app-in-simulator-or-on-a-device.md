# 在模拟器或设备上运行应用

在模拟的 iOS、tvOS、watchOS 或 visionOS 设备上，或在与 Mac 相连的设备上启动应用。

## 概述

要测试应用，请在模拟或真实设备上构建并运行应用。使用模拟设备在你无法直接访问的各种硬件上调试应用。但由于模拟设备在 Mac 上的模拟器应用中运行，所以无法复制实际设备的性能或功能。要验证你的应用是否按预期运行，请在一台或多台真实设备上运行。你可以使用连接线线将真实设备连接到 Mac，而对于 iOS、tvOS 或 visionOS 应用，可在将设备与 Xcode 配对后通过 Wi-Fi 进行连接。

通过 SwiftUI 预览，你可以在不构建和运行应用的情况下查看应用的界面。有关这些动态预览的更多信息，请参阅 [在 Xcode 中预览](https://developer.apple.com/documentation/SwiftUI/Previews-in-Xcode)。

### 选择构建方案和运行目标

在构建和运行应用之前，请选择包含应用目标的构建方案。*构建方案*是项目详细信息和设置的集合，用于告诉 Xcode 如何从你的项目构建和运行产品。Xcode 会根据你选择的方案确定生成的产品可以在何处运行，并在工具栏的运行目标菜单中填充可用设备列表。例如，如果方案仅包含一个 tvOS 应用，则Xcode 将只把 tvOS 模拟器和设备作为潜在的运行目标。

想了解有关构建方案的更多信息，请参阅[自定义项目的构建方案](https://developer.apple.com/documentation/xcode/customizing-the-build-schemes-for-a-project)。

> 重要  
> 在模拟器中运行应用时，某些限定硬件的功能可能不可用。提供设备特定功能访问权限的框架也会提供 API来告知你这些功能何时可用。请调用这些 API 并处理功能不可用的情况。要测试功能本身，请在真实设备上运行代码。

### 配置模拟设备列表

在 Xcode 的“设备和模拟器（Devices and Simulators）”窗口中管理真实和模拟设备。要查看此窗口，请选择窗口（Window） > 设备和模拟器（Devices and Simulators）。在“模拟器”选项卡中查看和配置模拟设备。

<img src="https://docs-assets.developer.apple.com/published/95df5341d1a7b94ac6027cbaa55709c5/running-your-app-in-the-simulator-or-on-a-device-2~dark@2x.png">

要添加新的模拟设备，请单击模拟器列表底部的加号 (+) 按钮，然后指定所需的配置。你可以添加新的模拟器并指定与默认设置不同的设备类型或操作系统版本。要从列表中删除模拟器，请选择该模拟器并按 Delete 键。

> 注意  
> Xcode 需要构建和运行模拟器的每个平台和系统版本的模拟器运行时。如果 Xcode 不显示某个平台的设备类型，则可能需要安装该平台的模拟器运行时。有关安装的更多信息，请参阅[安装和管理模拟器运行时](https://developer.apple.com/documentation/xcode/installing-additional-simulator-runtimes)。

### 将真实设备连接到 Mac

要查看和管理与真实设备的连接，请在 Xcode 的“设备和模拟器”窗口中选择“设备（Devices）”选项卡。“设备”选项卡显示当前已连接和断开连接的设备，可帮助你诊断可能出现的问题。例如，如果某个设备运行的操作系统版本不支持你的应用，Xcode 可能会将其显示为不可用。它还会显示可与 Xcode 配对的新设备。将设备与 Xcode 配对，以便将其包含在项目的运行目标列表中。

要通过物理连接配对设备，请使用适当的连接线将设备连接到 Mac。解锁设备，并按照 Xcode 或设备上出现的任何指示操作。

要在没有物理连接的情况下配对 Apple Vision Pro 或 Apple TV：

1. 确保 Mac 和要连接的设备位于同一个 Wi-Fi 网络中。Wi-Fi 网络必须与 Bonjour 兼容。

2. 通过本地网络将设备广播至目标 Mac。对于 visionOS 设备，请选择“设置（Settings）” > “常规（General）” > “远程设备（Remote Devices）”；对于 tvOS 设备，请选择“设置（Settings）” > “远程控制和设备（Remote and Devices）” > “远程应用和设备（Remote App and Devices）”。

3. 从 Xcode 的“设备和模拟器（Devices and Simulators）”窗口列表中选择设备，然后点击配对按钮，目标设备上会出现一个代码。

4. 在 Mac 上输入代码，完成配对过程。

配对完成后，设备将显示在 Xcode 中“设备和模拟器”窗口的已连接设备下。在安装和运行应用时，无需将配对设备与 Mac 保持物理连接。如果你的设备连接到与 Mac 处于同一网络的 Wi-Fi，Xcode 就可以使用该链接来安装和运行你的应用。

要将 Apple Watch 与 Mac 配对，请使用数据线将其配套的 iPhone 与 Mac 连接，并确保 iPhone 已配对以进行开发。完成此步骤后，请按照 Apple Watch 上的指示信任 Mac。当通过运行 iOS 17 或更高版本的 iPhone 配对时，Xcode 会通过 Wi-Fi 连接到 Apple Watch。5 系列和更旧型号的 Apple Watch 还要求 Apple Watch 和 Mac 与同一个兼容 Bonjour 的 Wi-Fi 网络相关联。当通过运行旧版 iOS 的 iPhone 配对时，Xcode 要求 iPhone 与 Mac 保持连接，以便在任何型号的 Apple Watch 上进行开发。

在安装应用之前，请执行几个额外的步骤：

- 在 Xcode 的“帐户（Account）”偏好选项中指定你的 Apple ID。
- 在项目的“签名和功能（Signing & Capabilities）”窗格中指定一个有效的团队。
- 如果 macOS 应用包含需要代码签名的功能，请对其进行代码签名；请参阅[向应用添加功能](https://developer.apple.com/documentation/xcode/adding-capabilities-to-your-app)。
- 如果你属于 [Apple Developer Program](https://developer.apple.com/programs/)，请向你的团队注册设备。
- 按照[在设备上启用开发者模式](https://developer.apple.com/documentation/xcode/enabling-developer-mode-on-a-device)中的描述，在 iOS、watchOS 或 visionOS 设备上启用开发者模式。

> 注意  
> 运行 macOS 应用无需配置 Mac 设备。同样，要运行 MacOS 版本的 iPad 应用，请选择“我的 Mac”（正在运行 Xcode 的 Mac）作为设备。

### 运行应用

单击工具栏中的运行按钮或选择“产品（Product）” > “运行（Run）”，在选定的模拟或真实设备上构建并运行应用。在工具栏的活动区域查看构建状态。

如果构建成功，Xcode 会运行应用并在调试区域打开调试会话。使用调试区域中的控件逐步查看代码、检查变量并与调试器交互。

如果构建不成功，请单击活动区域中的指示器以阅读“问题”导航器中的错误或警告信息。或者选择“视图（View）” > “导航器（Navigators）” > “显示问题导航器（Show Issue Navigator）”查看信息。

完成应用测试后，单击工具栏中的“停止”按钮。

### 与模拟环境交互

如果选择模拟设备作为运行目标，模拟器会启动并显示一个与模拟环境相对应的窗口。对于某些设备，模拟器会用一个与目标设备相似的外壳环绕屏幕内容。在 visionOS 中，模拟器会显示一个合成空间，以模拟佩戴设备时的体验。

每个设备外壳和空间都有特定的控件来支持交互。有关特定设备的详细信息，请参阅有关交互参考资料。

- [在 iOS 和 iPadOS 模拟器中与应用交互](https://developer.apple.com/documentation/xcode/interacting-with-your-app-in-the-ios-or-ipados-simulator)
- [在 tvOS 模拟器中与应用交互](https://developer.apple.com/documentation/xcode/interacting-with-your-app-in-the-tvos-simulator)
- [在 watchOS 模拟器中与应用交互](https://developer.apple.com/documentation/xcode/interacting-with-your-app-in-the-watchos-simulator)
- [在 visionOS 模拟器中与应用交互](https://developer.apple.com/documentation/xcode/interacting-with-your-app-in-the-visionos-simulator)

---

## 另见

### 精要

- [在设备上启用开发者模式](https://developer.apple.com/documentation/xcode/enabling-developer-mode-on-a-device)  
授予或拒绝本地安装的应用在 iOS、iPadOS、visionOS 和 watchOS 设备上运行的权限。