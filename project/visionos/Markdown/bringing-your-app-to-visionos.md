# 将现有应用移植到 visionOS

使用 visionOS SDK 为你的 iPadOS 或 iOS 应用构建一个新版本，并更新代码以适应平台差异。

## 概述

如果你有一个在 iPadOS 或 iOS 上运行的应用，你可以使用 visionOS SDK 构建该应用程序，以便在该平台上运行。为 visionOS 专门构建的应用采用标准系统外观，在该平台上看起来更自然。同时，更新应用也是添加在该平台上吸引眼球的元素（如 3D 内容和沉浸式体验）的好机会。

在大多数情况下，你只需更新 Xcode 的项目设置并重新编译代码即可支持 visionOS。根据你的应用，你可能需要进行其他更改，以解决那些仅在 iOS SDK 中支持的功能。虽然在两个平台上多数技术都是通用的，有些技术在 visionOS 设备上并不合理，或者需要的硬件并不存在。例如，用户通常不使用头戴式设备进行电子支付，因此使用 ProximityReader 框架的应用在 visionOS 中运行时必须禁用这些功能。

> 备注  
> 如果你在 iOS 应用中使用了 ARKit 来实现增强现实体验，则需要进行额外的更改才能在 visionOS 中支持 ARKit。有关如何更新此类应用，请参阅[将 ARKit 应用移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)。

### 将 visionOS 添加为应用的支持终端（supported destination）

更新应用的第一步是将 visionOS 添加为支持终端。在你的项目设置中，选择应用目标并导航到 General 选项卡。在 Supported Destinations 选项中，单击添加 （+） 按钮以添加新的终端，并选择 Apple Vision 选项。添加该选项后，你就可以专门为 visionOS SDK 构建应用了。

[支持终端面板](https://docs-assets.developer.apple.com/published/e86829433550d1659cc49fdb34ea7ff4/bringingExistingAppsToVision~dark@2x.png)

当第一次 Apple Vision 添加为终端时，Xcode 会对项目的构建设置进行一些一次性更改。添加终端后，你可以修改项目的构建设置和构建阶段，以定制针对 visionOS 的构建行为。例如，你可以移除 visionOS 版应用的依赖项，或更改要编译的源文件集。

有关如何更新目标配置的更多信息，请参阅[自定义目标的构建阶段](https://developer.apple.com/documentation/Xcode/customizing-the-build-phases-of-a-target)。

### 清理使用了废弃 API 的代码

在为 visionOS 构建之前，修复 iOS 版本代码中的任何弃用警告。当 API 不再适用或存在合适的替代品时，Apple 就会将其标记为废弃。当你编译调用已废弃 API 的代码时，编译器会生成警告，并经常建议你使用替代品。visionOS 完全删除了许多已废弃的符号，这些废弃警告也因此变成了该平台上的符号缺失错误。请在 iOS 版本的应用查看原始的弃用警告和替换详情。

除个别符号外，以下框架在 iOS 和 visionOS 中均已被完全弃用。如果你的应用仍在使用这些框架，请立即停止使用。每个框架的参考文档都包含有关如何修改代码的详情。

- Accounts
- Address Book
- Address Book UI
- Assets Library
- GLKit
- iAd
- Newsstand Kit
- NotificationCenter
- OpenGL ES

### 隔离 visionOS 中不可用的功能

iOS SDK 包含许多不适用于 visionOS 的框架，这可能是因为框架使用的硬件不可用，也可能是因为框架功能功能不适用于该平台。尽可能将使用这些框架的代码移到独立的源文件中，并只在 iOS 版本的应用中包含这些文件。

如果无法将代码隔离到单独的源文件中，可使用条件语句（如下面的语句）为 visionOS 和 iOS 提供不同的代码路径。下面的示例展示了如何配置条件语句，以便在 visionOS 和 iOS 中执行不同的代码路径：

```swift
#if os(visionOS)
   // visionOS code
#elseif os(iOS)
   // iOS code
#endif
```

以下框架在 iOS SDK 中可用，但在 visionOS SDK 中不可用。

ActivityKit | AdSupport | AppClip
--- | --- | ---
AutomatedDeviceEnrollment | BusinessChat | CarKey
CarPlay | Cinematic | ClockKit
CoreLocationUI | CoreMediaIO | CoreNFC
CoreTelephony | DeviceActivity | DockKit
ExposureNotification | FamilyControls | FinanceKit
FinanceKitUI | ManagedSettings | ManagedSettingsUI
Messages | MLCompute | NearbyInteraction
OpenAL | ProximityReader | RoomPlan
SafetyKit | ScreenTime | SensorKit
ServiceManagement | Social | Twitter
WidgetKit | WorkoutKit

有些框架的行为变化会影响你的 visionOS 应用，有些框架会在所需硬件在当前平台不可用时禁用功能。为了帮助你避免使用缺失功能的 API，许多框架都提供了检查功能可用性的 API。继续使用这些 API，并在功能不可用时采取适当的措施。而在使用没有检查可用性的框架时，请做好框架代码失效或报错的准备。

- **ARKit**。此框架要求你为 iOS 和 visionOS 使用不同的 API。有关详细信息，请参阅[将 ARKit 应用移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)。
- **AutomaticAssessmentConfiguration**。如果你尝试在 visionOS 中启动测试，该框架会返回错误。
- **AVFoundation**。截屏接口在 visionOS 中不可用。请使用可用性检查来确定哪些服务可用。
- **CallKit**。你可以继续提供 Voice-over-IP (VoIP) 服务，但电话号码验证、呼叫阻断和其他蜂窝相关服务不可用。
- **ClockKit**。该框架的 API 在 visionOS 中不起作用。
- **CoreHaptics**。visionOS 使用音频反馈而非触觉反馈。
- **CoreLocation**。 你可以使用标准定位服务请求某人的位置，但大多数其他服务都不可用。请使用可用性检查确认哪些服务可用。“始终”授权级别不可用，会自动变为“使用中”授权。
- **CoreMotion**。 气压计数据不可用，但大多数其他传感器可用。使用可用性检查来确认哪些传感器可以使用。
- **HealthKit** 和 **HealthKitUI**。健康数据不可用。使用可用性检查确认信息何时可用。
- **MapKit**。涉及航向信息的用户跟踪功能不可用。
- **MediaPlayer**。某些 API 在 visionOS 中不可用。
- **MetricKit**。你可以收集设备上的诊断日志并生成报告，但无法收集指标。
- **MusicKit**。某些 API 在 visionOS 中不可用。
- **NearbyInteraction**。该框架在 visionOS 中不起作用。使用可用性检查来确认服务是否存在。
- **PushToTalk**。一键通话服务不可用。请检查创建 [PTChannelManager](https://developer.apple.com/documentation/PushToTalk/PTChannelManager) 时的错误。
- **SafariServices**。显示 [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) 的链接会在 Safari 应用程序中打开一个新场景。
- **UIKit**。 系统最多可同时反馈两个触控输入 - 双手各一个。所有系统手势识别器都能正确处理这些输入，包括需要多手指操作的缩放和旋转手势。如果你有需要两个手指以上的自定义手势，请在 visionOS 中将其更新为只有一个或两个触控的手势。
- **VisionKit**。 [DataScannerViewController](https://developer.apple.com/documentation/VisionKit/DataScannerViewController) API 不可用，但其他功能仍然可用。
- **WatchConnectivity**。该框架仅支持 iPhone 与 Apple Watch 之间的连接。使用可用性检查确认服务何时可用。

有关如何将代码隔离到 iOS 应用版本的更多信息，请参阅[在特定平台或操作系统版本上运行代码](https://developer.apple.com/documentation/Xcode/running-code-on-a-specific-version)。

### 更新界面以更好地利用 visionOS 功能

当你的现有代码在 visionOS 中正常运行后，请设法改善你在该平台上提供的体验。在 visionOS 中，你可以使用窗口之外的方式来显示内容。请考虑如何将以下元素融入你的界面：

- **深度**。许多 SwiftUI 和 UIKit 视图都使用视觉效果来增加深度。请寻找类似的方法，将深度元素融入进你的自定义视图中。
- **3D 内容**。想想在哪些内容中可以加入 3D 模型和形状。使用 RealityKit 实现你的内容，并使用 [`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView) 从你的应用中展示这些内容。请参阅[在应用中添加 3D 内容](https://developer.apple.com/documentation/visionos/adding-3d-content-to-your-app)。
- **沉浸式体验**。显示一个空间，让用户沉浸在你的应用内容中。空间可以让你将内容呈现在用户周围的任何地方。你还可以创建只显示当前应用内容的完全沉浸式体验。请参阅[在应用中创建完全沉浸式体验](https://developer.apple.com/documentation/visionos/creating-fully-immersive-experiences)。
- **与周围环境的互动**。使用 ARKit 可促进你的内容与周围环境之间的互动。例如，检测平面以用作内容的锚点。请参阅 [ARKit。](https://developer.apple.com/documentation/arkit)

如果你使用 UIKit 构建了界面，你仍可将 iOS 故事板加载到你的应用中，但无法为 visionOS 定制界面或包含 3D 内容。要在应用中包含 visionOS 内容，请使用 [`UIHostingController`](https://developer.apple.com/documentation/SwiftUI/UIHostingController) 或 [`UIViewRepresentable`](https://developer.apple.com/documentation/SwiftUI/UIViewRepresentable) 以编程方式添加 SwiftUI 视图。又或者，将界面的相关部分迁移到 SwiftUI。将界面迁移到 SwiftUI 会减少需要维护的代码，并更容易验证你的界面是否按你的需求运行。

关于同时使用 SwiftUI 和 UIKit 内容的更多信息，请参阅 [SwiftUI](https://developer.apple.com/documentation/SwiftUI) 中的 [UIKit 集成](https://developer.apple.com/documentation/SwiftUI/UIKit-integration)。有关如何最好地在界面中融入深度和 3D 元素的指南，请参阅[人机界面指南](https://developer.apple.com/design/human-interface-guidelines)。

### 更新应用的素材资源

在你的项目中添加基于矢量或高分辨率的图像，以专门支持 visionOS。在 visionOS 中，用户可以从不同角度和不同距离观看应用的内容，因此图像像素很少与屏幕像素对齐。基于矢量的图像效果最佳，因为它们在任何尺寸下都能保持细节和清晰度。对于位图图像，请使用高分辨率图像（@2x 或更高），以确保图像在不同尺寸下都能保持细节。

有关为应用设计图像的详细信息，请参阅[人机界面指南](https://developer.apple.com/design/human-interface-guidelines)中的[图像](https://developer.apple.com/design/human-interface-guidelines/foundations/images)章节。

### 决定是否移植应用

在某些情况下，将应用移植到 visionOS 可能并不合理。例如，请勿移植以下类型的应用：

- **作为应用扩展信息容器的应用**。这包括了主要目的是提供自定义键盘扩展、设备驱动程序、贴纸包、短信和彩信过滤扩展、通话目录扩展或小组件的应用。
- **基于运动的应用**。这包括了追踪用户位置变化的应用程序，如提供转弯指示或导航的应用程序。也包括了追踪身体运动的应用程序。
- **自拍或摄影类应用**。这包括以通过设备摄像头捕捉图像或视频为主要目的的应用程序。

如果你的应用使用了不支持的功能，但在没有该功能的情况下也能正常运行，你仍然可以将应用移植到 visionOS 中。移除不支持的功能，并专注于将其他内容移植到该平台。例如，如果你有一款应用可以让用户写下笔记并拍照放在笔记中，那么你可以在 visionOS 中禁用拍照功能，但允许用户添加文字并整合已有的图片。

## 另见

### iOS 迁移和兼容性

- [将 ARKit 应用移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)  
更新使用了 ARKit 的 iPadOS 或 iOS 应用，并在 visionOS 中提供接近的体验。
- [检查现有应用是否与 visionOS 兼容](https://developer.apple.com/documentation/visionos/checking-whether-your-app-is-compatible-with-visionos)  
确定你现有的 iOS 或 iPadOS 应用是在 visionOS 中按原样运行，还是需要修改以处理平台差异。
- [使现有应用与 visionOS 兼容](https://developer.apple.com/documentation/visionos/making-your-app-compatible-with-visionos)  
修改你的 iPadOS 或 iOS 应用以在 visionOS 中成功运行。
