# 将现有应用程序引入 visionOS

使用 visionOS SDK 构建 iPadOS 或 iOS App 版本，并更新代码以适应平台差异。

## 概述

如果您有在 iPadOS 或 iOS 中运行的现有 App，您可以针对 visionOS SDK 构建该 App，以便在平台上运行它。专为 visionOS 构建的应用程序采用标准系统外观，在平台上看起来更自然。更新应用也是添加在平台上运行良好的元素的机会，例如 3D 内容和沉浸式体验。

在大多数情况下，要支持 visionOS，您需要做的就是更新 Xcode 项目的设置并重新编译代码。根据你的应用，你可能需要进行其他更改，以考虑仅在 iOS SDK 中找到的功能。虽然大多数相同的技术在两个平台上都可用，但有些技术没有意义或需要 visionOS 设备上不存在的硬件。例如，人们通常不使用头戴式设备进行非接触式支付，因此使用 ProximityReader 框架的应用在 visionOS 中运行时必须禁用这些功能。

> 备注  
> 如果您在 iOS 应用中使用 ARKit 来创建增强现实体验，则需要进行其他更改才能在 visionOS 中支持 ARKit。有关如何更新此类应用程序的信息，请参阅[将 ARKit 应用程序引入 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)。

### 将 visionOS 添加为应用支持的目标

更新应用程序的第一步是将 visionOS 添加为支持的目标。在项目的设置中，选择应用目标并导航到“常规”选项卡。在“支持的目标”中，点按添加 （+） 按钮以添加新目标位置，然后选择“Apple Vision”选项。通过添加此选项，您可以专门为 visionOS SDK 构建应用程序。

[图片](https://docs-assets.developer.apple.com/published/e86829433550d1659cc49fdb34ea7ff4/bringingExistingAppsToVision~dark@2x.png)

当您将 Apple Vision 添加为目的位置时，Xcode 会对项目的构建设置进行一些一次性更改。添加目标后，您可以修改项目的构建设置和构建阶段，以专门针对 visionOS 自定义构建行为。例如，您可以删除应用的 visionOS 版本的依赖项，或更改要编译的源文件集。

有关如何更新目标配置的更多信息，请参阅[自定义目标的构建阶段](https://developer.apple.com/documentation/Xcode/customizing-the-build-phases-of-a-target)。

### 清理使用已弃用 API 的代码

在为 visionOS 构建之前，修复 iOS 版本代码中的任何弃用警告。当 API 不再相关或存在合适的替代项时，Apple 会将其标记为已弃用。当您编译调用已弃用 API 的代码时，编译器会生成警告，并通常会建议替换供您改用。visionOS 完全删除了许多已弃用的符号，将这些弃用警告变成了平台上的缺失符号错误。在应用的 iOS 版本中进行更改，以查看原始弃用警告和替换详细信息。

除了单个符号之外，以下框架在 iOS 和 visionOS 中均已完全弃用。如果您的应用仍在使用这些框架，请立即停止使用它们。每个框架的参考文档都包含有关如何更新代码的信息。

- Accounts 帐户
- Address Book 通訊錄
- Address Book UI 通讯簿 UI
- Assets Library 资产库
- GLKit GLKit的
- iAd iAd的
- Newsstand Kit 报亭套件
- NotificationCenter 通知中心
- OpenGL ES OpenGL ES的

### 隔离 visionOS 中不可用的功能

iOS SDK 包含许多不适用于 visionOS 的框架，因为它们使用的硬件不可用，或者它们的功能不适用于平台。尽可能移动使用这些框架的代码以分隔源文件，并仅在应用的 iOS 版本中包含这些文件。

如果无法将代码隔离到单独的源文件，请使用条件语句（如下所示）为 visionOS 和 iOS 提供不同的代码路径。以下示例展示了如何在 visionOS 和 iOS 中配置条件语句以执行单独的代码路径：

```swift
#if os(visionOS)
   // visionOS code
#elseif os(iOS)
   // iOS code
#endif
```

以下框架在 iOS SDK 中可用，但在 visionOS SDK 中不可用。

----- | ----- | -----
ActivityKit | AdSupport | AppClip
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

某些框架的行为更改会影响 visionOS 中的应用，而某些框架会在所需硬件不可用时禁用功能。为了帮助您避免将 API 用于缺少的功能，许多框架都提供了 API 来检查这些功能的可用性。继续使用这些 API，并在功能不可用时采取适当的措施。在其他情况下，请准备好框架代码在使用时不执行任何操作或生成错误。

- 方舟。此框架要求您为 iOS 和 visionOS 使用不同的 API。有关更多信息，请参阅[将 ARKit 应用程序引入 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)。
- AutomaticAssessmentConfiguration。如果您尝试在 visionOS 中启动测试，框架将返回错误。
- AVFoundation。捕获界面在 visionOS 中不可用。使用可用性检查来确定存在哪些服务。
- 呼叫套件。您可以继续提供 IP 语音 （VoIP） 服务，但无法提供电话号码验证、呼叫阻止和其他与手机网络相关的服务。
- 时钟套件。此框架的 API 在 visionOS 中不执行任何操作。
- 核心触觉。visionOS 播放音频反馈，而不是触觉反馈。
- CoreLocation。您可以使用标准定位服务请求某人的位置，但大多数其他服务都不可用。使用可用性检查来确定存在哪些服务。“始终”授权级别不可用，并自动变为“使用时”授权。
- 核心运动。气压计数据不可用，但大多数其他传感器可用。使用可用性检查来确定可以使用哪些传感器。
- HealthKit 和 HealthKitUI。运行状况数据不可用。使用可用性检查来确定信息何时可用。
- 地图套件。涉及航向信息的用户跟踪功能不可用。
- 媒体播放器。某些 API 在 visionOS 中不可用。
- MetricKit。您可以收集设备上的诊断日志并生成报告，但无法收集指标。
- 音乐套件。某些 API 在 visionOS 中不可用。
- NearbyInteraction。该框架在 visionOS 中不执行任何操作。使用可用性检查来确定服务何时存在。
- PushToTalk。一键通服务不可用。在创建 [`PTChannelManager`](https://developer.apple.com/documentation/PushToTalk/PTChannelManager) .
- Safari服务。现在显示的 [`SFSafariViewController`](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) 链接会在 Safari 应用程序中打开一个新场景。
- UIKit的。系统报告最多两个同时触摸输入 - 每个人的手一个。所有系统手势识别器都能正确处理这些输入，包括需要多个手指的缩放和旋转手势。如果您有需要两根以上手指的自定义手势识别器，请将其更新为仅在 visionOS 中支持一次或两次触摸。
- 愿景套件。API [`DataScannerViewController`](https://developer.apple.com/documentation/visionkit/datascannerviewcontroller) 不可用，但其他功能仍然可用。
- 监视连接。该框架仅支持 iPhone 和 Apple Watch 之间的连接。使用可用性检查来确定服务何时可用。

有关如何将代码隔离到应用的 iOS 版本的其他信息，请参阅[在特定平台或 OS 版本上运行代码](https://developer.apple.com/documentation/Xcode/running-code-on-a-specific-version)。

### 更新界面以利用 visionOS 功能

现有代码在 visionOS 中正确运行后，请寻找方法来改善您在平台上提供的体验。在 visionOS 中，您不仅可以使用窗口来显示内容。考虑将以下元素合并到界面中的方法：

- 深度。许多 SwiftUI 和 UIKit 视图都使用视觉效果来增加深度。寻找类似的方法，将深度合并到您自己的自定义视图中。
- 3D 内容。考虑一下您可以在哪些位置将 3D 模型和形状合并到您的内容中。使用 RealityKit 实现您的内容，并使用 [`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView) a 从您的应用呈现该内容。请参阅[向应用添加 3D 内容](https://developer.apple.com/documentation/visionos/adding-3d-content-to-your-app)。
- 身临其境的体验。提供一个空间，让某人沉浸在您的应用内容中。空间可让您将内容放置在用户周围环境的任何位置。您还可以创建仅显示应用内容的完全沉浸式体验。请参阅[在应用中创建完全沉浸式体验](https://developer.apple.com/documentation/visionos/creating-fully-immersive-experiences)。
- 与周围环境的互动。使用 ARKit 促进您的内容与周围环境之间的交互。例如，检测平面以用作内容的锚点。请参见 [ARKit](https://developer.apple.com/documentation/arkit)。

如果您使用 UIKit 构建了界面，您仍然可以将 iOS 故事板加载到您的应用中，但您无法为 visionOS 自定义界面或包含 3D 内容。若要在 App 中包含 visionOS 内容，请使用 [`UIHostingController`](https://developer.apple.com/documentation/SwiftUI/UIHostingController) 或 [`UIViewRepresentable`](https://developer.apple.com/documentation/SwiftUI/UIViewRepresentable) 以编程方式添加 SwiftUI 视图。或者，将界面的相关部分迁移到 SwiftUI。将界面移至 SwiftUI 可以减少需要维护的代码，并更轻松地验证界面是否符合您的要求。

有关混合 SwiftUI 和 UIKit 内容的信息，请参阅 [SwiftUI](https://developer.apple.com/documentation/SwiftUI) 中的 [UIKit 集成](https://developer.apple.com/documentation/SwiftUI/UIKit-integration)。有关如何最好地将深度和 3D 元素合并到界面中的指南，请参阅[人机界面指南](https://developer.apple.com/design/human-interface-guidelines)。

### 更新应用的素材资源

将基于矢量的图像或高分辨率图像添加到您的项目中，专门用于支持 visionOS。在 visionOS 中，用户可以从不同的角度和不同的距离查看应用的内容，因此图像像素很少与屏幕像素对齐。基于矢量的图像效果最好，因为它们在任何尺寸下都能保持其细节和清晰度。对于基于位图的图像，请使用高分辨率图像（ @2x 或更好）以确保图像保留不同大小的细节。

有关为应用设计图像的详细信息，请参阅[人机界面指南](https://developer.apple.com/design/human-interface-guidelines)中的[图像](https://developer.apple.com/design/human-interface-guidelines/foundations/images)。

### 决定是否移植您的应用

在某些情况下，将应用移植到 visionOS 可能没有意义。例如，请勿移植以下类型的应用：

- 充当应用扩展信息容器的应用。这包括主要目的是提供自定义键盘扩展、设备驱动程序、贴纸包、短信和彩信过滤扩展、呼叫目录扩展或小组件的应用。
- 基于运动的应用程序。这包括跟踪用户位置变化的应用，例如提供转弯指示或导航的应用。它还包括跟踪身体运动的应用程序。
- 自拍或摄影应用程序。这包括主要目的是从设备的摄像头捕获图像或视频的应用。

如果您的应用使用了不受支持的功能，但可以在没有该功能的情况下运行，您仍然可以将应用引入 visionOS。删除不可用的功能，并专注于将其余内容带到平台上。例如，如果您有一个应用程序，可以让人们写下笔记并拍照以包含在这些笔记中，请在 visionOS 中禁用拍照功能，但允许人们添加文本并合并他们已经拥有的图像。

## 另见

### iOS 迁移和兼容性

- [将您的 ARKit 应用程序引入 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)  
更新使用 ARKit 的 iPadOS 或 iOS App，并在 visionOS 中提供等效体验。
- [检查您现有的应用程序是否与 visionOS 兼容](https://developer.apple.com/documentation/visionos/checking-whether-your-app-is-compatible-with-visionos)  
确定您现有的 iOS 或 iPadOS App 是在 visionOS 中按原样运行，还是需要修改以处理平台差异。
- [使现有应用与 visionOS 兼容](https://developer.apple.com/documentation/visionos/making-your-app-compatible-with-visionos)  
修改您的 iPadOS 或 iOS 应用程序以在 visionOS 中成功运行。
