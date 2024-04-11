# 检查现有应用程序是否与 visionOS 兼容

确定您现有的 iOS 或 iPadOS 应用程序是否能在 visionOS 中正常运行，还是需要进行修改以处理平台差异。

## 概述
visionOS 支持大多数与 iOS 相同的技术，因此许多专为在 iPad 或 iPhone 上运行而开发的应用程序都可以不经修改地在 visionOS 设备上运行。当兼容的应用程序在 visionOS 中运行时，它将保留在 iPadOS 或 iOS 中的外观，其内容将显示在用户周围的窗口中。

如果你在 iOS App Store 上下载了一款应用程序，请尝试下载并在 Apple Vision Pro 上运行。如果遇到问题，请使用 Xcode 进行识别和修复。如果您使用 iOS SDK 构建了应用程序，Xcode 15 及更高版本会自动为您的项目添加 Designed for iPad 运行目标。使用此目标运行您的应用程序并测试其在 visionOS 中的兼容性。您可以在模拟器中测试应用程序的大部分核心功能，但有些功能只能在设备上使用。

## 确定缺失的功能是否会影响您的应用程序
visionOS 包含与 iPadOS 和 iOS 大部分相同的技术，但也存在一些差异。在某些情况下，由于硬件差异或人们使用 visionOS 设备的方式不同，您在应用程序中使用的某项功能可能无法使用。作为测试的一部分，请考虑任何缺失功能对应用程序整体体验的影响。在可能的情况下，通过禁用缺失功能或提供访问相同内容的替代方法来解决这些问题。

在 visionOS 中，以下功能在兼容的 iPad 和 iPhone 应用程序中不可用。请使用框架 API 来确定这些功能何时可用。

- 核心运动服务
- 气压计和磁力计数据
- 除标准服务外的所有定位服务
- HealthKit 数据
- 视频或照片捕捉
- 自动对焦或闪光灯等相机功能
- 后置（自拍）摄像头

在某些情况下，当您的应用程序在 visionOS 中运行时，框架或功能的行为会有所不同。当您的应用程序在 visionOS 中运行时，请做好处理这些差异的准备。

- **AirPlay**。visionOS 隐藏了系统界面中的 AirPlay 共享按钮，因此您无法使用兼容应用中的 AirPlay 功能。
- **应用程序扩展**。VisionOS 不加载 App Clips、设备驱动程序、设备活动监视器、键盘扩展、信息应用程序扩展、照片编辑应用程序扩展、短信和通话报告扩展或小部件。
- **Apple Watch 功能**。VisionOS 会忽略 iOS 或 iPadOS 应用程序中的 watchOS 应用程序和 WatchKit 扩展。[Watch Connectivity 框架](https://developer.apple.com/documentation/WatchConnectivity)[不可用。ClockKit](https://developer.apple.com/documentation/clockkit) 中的面部共享在 visionOS 中不起作用。
- **音频和视频**。visionOS 不支持画中画或 AV 路由功能。在使用视频功能前，请检查其可用性。请做好准备，当您的应用程序移至后台时，音频播放会自动停止。
- **课堂功能**。使用[自动评估配置](https://developer.apple.com/documentation/AutomaticAssessmentConfiguration)启动测试时会报错。
- **蜂窝电话**。蜂窝电话服务不可用。您仍可使用 [CallKit](https://developer.apple.com/documentation/CallKit) 和 [Core Telephony](https://developer.apple.com/documentation/coretelephony) 实施 Voice-over-IP (VoIP) 服务。
- **设备管理**。对 [ManagedSettings](https://developer.apple.com/documentation/ManagedSettings) 和 [ManagedSettingsUI](https://developer.apple.com/documentation/ManagedSettingsUI) 框架的调用不起作用。
- **游戏控制器**：VisionOS 只有在有人正在查看应用程序时才会发送游戏控制器事件。要要求将游戏控制器作为应用程序的输入设备，请将带有 `visionOS` 值的 [`GCRequiresControllerUserInteraction`](https://developer.apple.com/documentation/bundleresources/information_property_list/gcrequirescontrolleruserinteraction) 关键字添加到应用程序的 `Info.plist` 中。
- **接力**。visionOS 不会尝试将用户活动移交给其他设备。
- **触感反馈**。visionOS 播放声音而非触觉。
- **HomeKit**。你无法使用二维码从 visionOS 设备添加配件。
- **度量**。您可以使用 [MetricKit](https://developer.apple.com/documentation/MetricKit) 收集设备上的诊断日志并生成报告，但无法收集指标。
- **多点触控**。系统最多可同时报告两个触摸输入--每个人的双手各一个。所有系统手势识别器都能正确处理这些输入，包括需要多个手指的缩放和旋转手势。如果您有需要两个以上交互点的自定义手势识别器，请将其更新为仅支持 visionOS 中的一个或两个触摸。
- **家长控制**。调用 [FamilyControls](https://developer.apple.com/documentation/FamilyControls) 框架什么也做不了。
- **PencilKit**。visionOS 不会报告 [`UITouch.TouchType.pencil`](https://developer.apple.com/documentation/uikit/uitouch/touchtype/pencil) 类型的触摸，但会报告其他类型的触摸。
- **一键通调**。用 [Push to Talk](https://developer.apple.com/documentation/PushToTalk) 框架不会产生任何结果。
- **Safari 服务**。显示 [`SFSafariViewController`](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) 的链接会打开 Safari 场景。
- **屏幕时间**。对 [Screen Time](https://developer.apple.com/documentation/ScreenTime) 框架的调用不起作用。
- **传感器相关功能**。调用 [SensorKit](https://developer.apple.com/documentation/sensorkit) 框架不执行任何操作。
- **社交媒体**。调用 [Social](https://developer.apple.com/documentation/Social) 框架时不会执行任何操作。
- **系统接口**。授权提示、登录 Apple 提示和其他系统提供的接口会在应用程序进程之外异步运行。由于这些接口不在您的应用中以模式运行，因此您的应用可能无法收到即时响应。
- **车辆功能**。系统不会调用您应用程序的 [CarPlay](https://developer.apple.com/documentation/CarPlay) 代码。您使用 [CarKey](https://developer.apple.com/documentation/CarKey) 进行的调用不起任何作用。
- **视觉**。数据扫描仪在 [VisionKit](https://developer.apple.com/documentation/VisionKit) 中不起作用。

iOS 中的 ARKit 版本与 visionOS 中的版本不兼容，因此 visionOS 无法显示包含 ARKit 视图的窗口。有关如何将 ARKit 应用程序引入 visionOS 的信息，请参阅[将 ARKit 应用引入 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)。

有关如何处理代码中缺失功能的详细信息，请参阅[使现有应用程序与 visionOS 兼容](https://developer.apple.com/documentation/visionos/making-your-app-compatible-with-visionos)。

## 在上传应用程序之前测试特定场景

当您的应用程序在 visionOS 中运行时，iOS 的以下 App Store 功能将继续工作：

- 应用内购买和订阅
- 应用程序功能和权限
- 按需资源
- 应用程序精简

当您使用应用程序精简功能为不同的设备和操作系统优化您的应用程序时，App Store 会选择最适合 visionOS 设备的资源和内容。然后，它会移除任何其他资源，以创建一个精简的应用程序安装。当您从 Xcode 15 或更高版本导出应用程序时，可以使用 visionOS 虚拟精简目标测试精简支持。

当您准备分发应用程序时，请创建一个存档，然后使用 "Ad-Hoc "或 "Development "分发方法导出。在导出过程中，Xcode 会创建一个经过适当签名的应用程序，供您分发给测试人员。有关详细信息，请参阅[将应用程序分发到注册设备](https://developer.apple.com/documentation/Xcode/distributing-your-app-to-registered-devices)。

### 在 App Store Connect 中更新应用程序信息

在您签署更新的 Apple Developer Program License Agreement 后，App Store 会自动在 visionOS 中提供兼容的 iPad 和 iPhone 应用程序。如果您不想让应用程序在 Apple Vision Pro 上运行，请在 App Store Connect 中更改应用程序的可用性。

1. 在 App Store Connect 中选择您的应用程序。
2. 导航至定价和可用性信息。
3. 禁用 "在 Apple Vision Pro 上提供此应用 "选项。

当您删除应用程序在 Apple Vision Pro 上的可用性时，App Store 就会停止为 visionOS 提供 iOS 应用程序。已经下载过你的 iOS 应用的用户仍然可以在 visionOS 中运行它，但他们无法再次下载。此设置不会影响使用 visionOS SDK 构建的应用程序版本。

## 另见

### iOS 迁移和兼容性
- [将现有应用程序移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-app-to-visionos)  
使用 visionOS SDK 构建 iPadOS 或 iOS 应用程序的一个版本，并根据平台差异更新代码。
- [将 ARKit 应用程序移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)  
更新使用 ARKit 的 iPadOS 或 iOS 应用程序，并在 visionOS 中提供同等体验。
- [使现有应用程序与 visionOS 兼容](https://developer.apple.com/documentation/visionos/making-your-app-compatible-with-visionos)  
修改您的 iPadOS 或 iOS 应用程序，以便在 visionOS 中成功运行。