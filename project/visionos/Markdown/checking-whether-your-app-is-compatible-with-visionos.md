# 检查现有应用是否与 visionOS 兼容

确认你现有的 iOS 或 iPadOS 应用能否原封不动地在 visionOS 中正常运行，还是需要进行修改以处理平台差异。

## 概述

visionOS 支持大多数与 iOS 相同的技术，因此许多专为在 iPad 或 iPhone 上运行而开发的应用都可以不经修改地在 visionOS 设备上运行。当兼容的应用在 visionOS 中运行时，它将保留在 iPadOS 或 iOS 中的外观，并且其内容会显示在用户周围环境的窗口中。

如果你在 iOS App Store 上发布了一款应用，请尝试下载并在 Apple Vision Pro 上运行。如果遇到问题，请使用 Xcode 进行识别和修复。如果你使用 iOS SDK 构建了应用，Xcode 15 及更高版本会自动将“专为 iPad 设计”运行时目标添加到您的项目中。使用此目标运行你的应用并测试其在 visionOS 中的兼容性。你可以在模拟器中测试应用的大部分核心功能，但有些功能仅在设备上可用。

### 确认缺失的功能是否会影响你的应用

visionOS 包含与 iPadOS 和 iOS 相同的大部分技术，但也存在一些差异。在某些情况下，由于硬件差异或人们使用 visionOS 设备的方式不同，你在应用中使用的某项功能可能无法使用。在测试的过程中，请考虑任何缺失功能对整体应用体验的影响。尽可能通过禁用缺失功能或提供访问相同内容的方法来替代缺失的功能。

以下功能在兼容 visionOS 的 iPad 和 iPhone 应用中不可用。请使用框架 API 来确认这些功能何时可用。

- 核心运动服务
- 气压计和磁力计数据
- 除标准服务外的所有定位服务
- HealthKit 数据
- 视频或静态照片捕获
- 自动对焦或闪光灯等相机功能
- 前置（自拍）摄像头

在某些情况下，当你的应用在 visionOS 中运行时，框架或功能的行为会有所不同。当你的应用在 visionOS 中运行时，请准备好处理这些差异。

- **AirPlay**。visionOS 隐藏了系统界面中的 AirPlay 共享按钮，并且你无法使用兼容应用中的 AirPlay 功能。
- **应用扩展**。VisionOS 不会加载 App Clips、设备驱动程序、设备活动监视器、键盘扩展、信息应用扩展、照片编辑应用扩展、短信和通话报告扩展或小组件。
- **Apple Watch 功能**。VisionOS 会忽略 iOS 或 iPadOS 应用中的 watchOS 应用和 WatchKit 扩展。[Watch Connectivity](https://developer.apple.com/documentation/WatchConnectivity) 框架不可用。[ClockKit](https://developer.apple.com/documentation/clockkit) 中的面部共享在 visionOS 中不起作用。
- **音频和视频**。visionOS 不支持画中画或 AV 路由功能。在使用视频功能前，请检查其可用性。请准备好在应用移至后台时会自动停止音频播放。
- **课堂功能**。使用[自动评估配置](https://developer.apple.com/documentation/AutomaticAssessmentConfiguration)启动测试时会报错。
- **蜂窝电话**。蜂窝网络服务不可用。你仍可使用 [CallKit](https://developer.apple.com/documentation/CallKit) 和 [Core Telephony](https://developer.apple.com/documentation/coretelephony) 实施 IP语音（VoIP）服务。
- **设备管理**。调用 [ManagedSettings](https://developer.apple.com/documentation/ManagedSettings) 和 [ManagedSettingsUI](https://developer.apple.com/documentation/ManagedSettingsUI) 框架没有效果。
- **游戏控制器**。visionOS 只有在有人正在查看应用时才会发送游戏控制器事件。如要将游戏控制器作为应用的输入设备，请将带有 `visionOS` 值的 [`GCRequiresControllerUserInteraction`](https://developer.apple.com/documentation/bundleresources/information_property_list/gcrequirescontrolleruserinteraction) 键添加到应用的 `Info.plist` 中。
- **接力**。visionOS 不会尝试将用户活动移交给其他设备。
- **触感反馈**。visionOS 播放声音而非触觉。
- **HomeKit**。你无法使用二维码从 visionOS 设备添加配件。
- **指标**。你可以使用 [MetricKit](https://developer.apple.com/documentation/MetricKit) 收集设备上的诊断日志并生成报告，但无法收集指标。
- **多点触控**。系统最多可同时报告两个触摸输入--用户的双手。所有系统手势识别器都能正确处理这些输入，包括需要多个手指的缩放和旋转手势。如果你的自定义手势识别器需要两个以上的交互点，请将其更新为在 visionOS 中仅支持一或两个触控。
- **家长控制**。调用 [FamilyControls](https://developer.apple.com/documentation/FamilyControls) 框架不执行任何操作。
- **PencilKit**。visionOS 不会报告 [`UITouch.TouchType.pencil`](https://developer.apple.com/documentation/uikit/uitouch/touchtype/pencil) 类型的触摸，但会报告其他类型的触摸。
- **一键通话**。调用 [Push to Talk](https://developer.apple.com/documentation/PushToTalk) 框架不执行任何操作。
- **Safari 服务**。显示 [`SFSafariViewController`](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) 的链接会打开 Safari 场景。
- **屏幕时间**。调用 [Screen Time](https://developer.apple.com/documentation/ScreenTime) 框架不执行任何操作。
- **传感器相关功能**。调用 [SensorKit](https://developer.apple.com/documentation/sensorkit) 框架不执行任何操作。
- **社交媒体**。调用 [Social](https://developer.apple.com/documentation/Social) 框架时不执行任何操作。
- **系统界面**。授权提示、“使用 Apple 登录”提示和其他系统提供的界面会在应用进程之外异步运行。由于这些界面不在你的应用中以强制模式运行，因此你的应用可能无法收到即时响应。
- **车辆功能**。系统不会调用你应用的 [CarPlay](https://developer.apple.com/documentation/CarPlay) 代码。你的 [CarKey](https://developer.apple.com/documentation/CarKey) 调用不执行任何操作。
- **视觉**。数据扫描仪在 [VisionKit](https://developer.apple.com/documentation/VisionKit) 中不执行任何操作。

iOS 中的 ARKit 版本与 visionOS 中的版本不兼容，因此 visionOS 无法显示包含 ARKit 视图的窗口。有关如何将 ARKit 应用移植到 visionOS 的信息，请参阅[将 ARKit 应用移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)。

有关如何处理代码中缺失功能的详细信息，请参阅[使现有应用与 visionOS 兼容](https://developer.apple.com/documentation/visionos/making-your-app-compatible-with-visionos)。

### 在上传应用之前测试特定场景

当你的应用在 visionOS 中运行时，以下适用于 iOS 的 App Store 功能将继续有效：

- 应用内购买和订阅
- 应用权限和授权
- 按需资源
- 应用精简

当你使用应用精简功能为不同的设备和操作系统优化你的应用时，App Store 会选择最适合 visionOS 设备的资源和内容。然后，它会移除任何其他资源，以创建一个精简的应用安装包。当你从 Xcode 15 或更高版本导出应用时，可以使用 visionOS 虚拟精简目标来测试应用精简功能。

当你准备分发应用时，请创建一个存档，然后使用“Ad-Hoc”或“Development”分发方法导出。在导出过程中，Xcode 会创建一个经过适当签名的应用，供你分发给测试人员。详细信息请参阅[将应用分发到已注册设备](https://developer.apple.com/documentation/Xcode/distributing-your-app-to-registered-devices)。

### 在 App Store Connect 中更新应用信息

在你签署更新的 Apple Developer Program License Agreement 后，App Store 会自动在 visionOS 中提供兼容的 iPad 和 iPhone 应用。如果你不想让你的应用在 Apple Vision Pro 上运行，请在 App Store Connect 中更改应用的可用性。

1. 在 App Store Connect 中选择你的应用。
2. 导航至定价和可用性信息。
3. 禁用“在 Apple Vision Pro 上提供此应用”选项。

当你移除应用在 Apple Vision Pro 上的可用性后，App Store 就会停止提供适用于 visionOS 的 iOS 应用。已经下载过你的 iOS 应用的用户仍然可以在 visionOS 中运行它，但他们无法再次下载。此设置不会影响使用 visionOS SDK 构建的应用版本。

---

## 另见

### iOS 迁移和兼容性
- [将现有应用移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-app-to-visionos)  
使用 visionOS SDK 构建 iPadOS 或 iOS 应用版本，并根据平台差异更新代码。
- [将 ARKit 应用移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)  
更新使用 ARKit 的 iPadOS 或 iOS 应用，并在 visionOS 中提供等效体验。
- [使现有应用与 visionOS 兼容](https://developer.apple.com/documentation/visionos/making-your-app-compatible-with-visionos)  
修改你的 iPadOS 或 iOS 应用，以便在 visionOS 中成功运行。