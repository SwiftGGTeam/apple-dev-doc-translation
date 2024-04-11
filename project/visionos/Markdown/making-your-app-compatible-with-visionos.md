# 让您现有的应用程序与 visionOS 兼容
修改您的 iPadOS 或 iOS 应用程序，以便在 visionOS 中成功运行。

## 概述

兼容的 iPadOS 或 iOS 应用程序可链接 iOS SDK 并在 visionOS 中运行。尽管 visionOS 提供了一套完整的 iOS 框架用于链接，但由于硬件或使用上的差异，这些框架的某些功能可能无法使用。为确保您的应用程序能在 visionOS 中正确运行，请从容应对任何缺失的功能，并尽可能提供变通方法。

### 使用功能前进行可用性和授权检查

有些框架提供 API，可让您确定框架功能何时可用，或您的应用程序是否已获授权使用这些功能。在尝试使用相应功能之前，请务必检查这些 API，不要因为存在必要的硬件就认为某项功能可用。设备的配置也会对某些可用性和授权检查的结果产生影响，当您的应用程序在模拟器中运行时，功能可能并不存在。如果可用性或授权检查失败，请不要尝试在应用程序中使用相关功能。

以下框架支持可用性或授权检查：

- **ActivityKit**。检查 [ActivityAuthorizationInfo](https://developer.apple.com/documentation/ActivityKit/ActivityAuthorizationInfo) 的 [`areActivitiesEnabled`](https://developer.apple.com/documentation/ActivityKit/ActivityAuthorizationInfo/areActivitiesEnabled) 属性，以确定实时活动是否已获授权。
- **ARKit**。检查配置对象的 [`isSupported`](https://developer.apple.com/documentation/arkit/arconfiguration/2923553-issupported) 属性，以确定增强现实功能是否可用。在 visionOS 中，[`ARView`](https://developer.apple.com/documentation/RealityKit/ARView) 等 ARKit 视图永远不可用，因此请将包含这些视图的接口代码隔离到 iOS 版本的应用程序中。
- **AVFoundation**。使用 [`AVCaptureDevice.DiscoverySession`](https://developer.apple.com/documentation/avfoundation/avcapturedevice/discoverysession) 类确定哪些摄像头可用。不要假设存在特定的摄像头。
- **自动评估配置**。配置 [`AEAssessmentSession`](https://developer.apple.com/documentation/AutomaticAssessmentConfiguration/AEAssessmentSession) 对象时检查错误值。
- **联系人**。使用 [`CNContactStore`](https://developer.apple.com/documentation/Contacts/CNContactStore) 类确定应用程序的授权状态。
- **核心蓝牙**。使用 [`CBCentralManager`](https://developer.apple.com/documentation/CoreBluetooth/CBCentralManager) 和 [`CBPeripheralManager`](https://developer.apple.com/documentation/CoreBluetooth/CBPeripheralManager) 类确定功能可用性和应用的授权状态。
- **核心触觉**。调用触觉引擎的 [`capabilitiesForHardware()`](https://developer.apple.com/documentation/CoreHaptics/CHHapticEngine/capabilitiesForHardware()) 方法确定可用功能。
- **核心位置**。检查 [`CLLocationManager`](https://developer.apple.com/documentation/corelocation/cllocationmanager) 的属性以确定位置服务的可用性。
- **核心运动**。检查 [`CMMotionManager`](https://developer.apple.com/documentation/coremotion/cmmotionmanager) 的属性，以确定加速计、陀螺仪、磁力计和其他硬件传感器是否可用。
- **核心 NFC**。检查读取器会话的 [`readingAvailable`](https://developer.apple.com/documentation/corenfc/nfcreadersession/3043845-readingavailable) 属性，以确定 NFC 标签读取是否可用。
- **EventKit**。使用 [`EKEventStore`](https://developer.apple.com/documentation/eventkit/ekeventstore) 类确定应用程序的授权状态。
- **ExposureNotification**。使用 [`ENManager`](https://developer.apple.com/documentation/exposurenotification/enmanager) 类确定应用程序的授权状态。
- **HealthKit**。使用 [`HKHealthStore`](https://developer.apple.com/documentation/healthkit/hkhealthstore) 类确定健康相关数据是否可用。
- **HomeKit**。检查 [`HMHomeManager`](https://developer.apple.com/documentation/homekit/hmhomemanager) 的属性，确定应用的授权状态。
- **本地身份验证**。使用 [`LAContext`](https://developer.apple.com/documentation/LocalAuthentication/LAContext) 类确定可以使用的身份验证策略。
- **媒体播放器**。使用 [`MPMediaLibrary`](https://developer.apple.com/documentation/mediaplayer/mpmedialibrary) 类确定应用程序的授权状态。
- **附近交互**。检查会话的 [`deviceCapabilities`](https://developer.apple.com/documentation/nearbyinteraction/nisession/3951288-devicecapabilities) 属性，确定功能是否可用。
- **PhotoKit**。使用 [`PHPhotoLibrary`](https://developer.apple.com/documentation/photokit/phphotolibrary) 类确定应用程序的授权状态。
- **ProximityReader**。检查读卡器对象的 [`isSupported`](https://developer.apple.com/documentation/ProximityReader/PaymentCardReader/isSupported) 属性，以确定 iPhone 上的 "Tap to Pay "是否可用。
- **ReplayKit**。检查 [`RPScreenRecorder`](https://developer.apple.com/documentation/ReplayKit/RPScreenRecorder) 的 [`isAvailable`](https://developer.apple.com/documentation/replaykit/rpscreenrecorder/isavailable) 属性，以确定屏幕录制支持是否可用。
- **RoomPlan**。检查 [`RoomCaptureSession`](https://developer.apple.com/documentation/RoomPlan/RoomCaptureSession) 对象的 [`isSupported`](https://developer.apple.com/documentation/RoomPlan/RoomCaptureSession/isSupported) 属性，以确定设备上是否支持激光雷达扫描。
- **传感器套件**.使用 [`SRSensorReader`](https://developer.apple.com/documentation/sensorkit/srsensorreader) 类确定应用程序的授权状态。
- **语音**。使用 [`SFSpeechRecognizer`](https://developer.apple.com/documentation/speech/sfspeechrecognizer) 类确定语音识别是否可用。
- **用户通知**。使用 [`UNUserNotificationCenter`](https://developer.apple.com/documentation/UserNotifications/UNUserNotificationCenter) 的 [`getNotificationSettings(completionHandler:)`](https://developer.apple.com/documentation/usernotifications/unusernotificationcenter/getnotificationsettings(completionhandler:)) 方法确定应用程序的授权状态。
- **WatchConnectivity**。调用 [`WCSession`](https://developer.apple.com/documentation/WatchConnectivity/WCSession) 对象的 [`isSupported()`](https://developer.apple.com/documentation/watchconnectivity/wcsession/issupported()) 方法来确定框架是否可用。

### 妥善处理环境差异
Apple 框架尽可能采用与设备无关的方法，以减少在不同类型设备上使用时出现的问题。苹果设备的形状和大小各不相同，功能也不尽相同。与其为特定设备构建应用程序，不如确保它能适应任何设备，并能从容应对差异。

在设计过程中为您的应用程序建立稳健性。避免可能导致应用程序在新设备上运行时发生故障的假设，并确保您的应用程序能动态适应不同的条件。例如

- **不要假设设备类型或习惯用语总是 iPhone、iPad 或 iPod Touch**。避免根据当前习惯用语做出决定。如果您确实依赖于当前习惯用法，请为未知习惯用法提供合理的默认值。
- **设计应用程序以处理不可用的硬件或功能**。特定硬件和功能不可用的原因多种多样。例如，当应用程序在模拟器中运行时，某个功能可能不可用。尽可能执行可用性检查，并从容应对功能缺失。
- **设计动态适应的窗口和视图**。使用 SwiftUI 或自动布局来构建界面，以便动态适应任何尺寸。假设应用程序的大小会动态变化。
- **不要假设设备有特定数量的显示屏**。人们可以将 iPad 和 iPhone 连接到外部显示屏，而 visionOS 设备可以使用两个显示屏来创建立体版本的应用内容。
- **不要根据可用的框架或符号做出假设**。框架或代码符号的存在与否是识别设备类型的不可靠方法，而且可能在以后的软件更新中发生变化。
- **请勿假定您的应用程序在后台运行**。visionOS 不支持定位、外部附件或蓝牙外设后台执行模式。
- **不要以为后台程序是隐藏的**。在 visionOS 中，后台应用程序的窗口仍然可见，但在无人查看时会变暗。只有当一个应用程序呈现出一个身临其境的空间时，应用程序窗口才会消失。

当您使用设备详细信息进行决策时，您的应用程序可能会在未知设备类型上产生不一致或错误的结果，或者完全失败。寻找依赖环境信息而非设备类型的解决方案。例如，SwiftUI 和 UIKit 使用应用程序的窗口大小启动布局，而窗口大小并不一定与设备显示屏大小相同。

> **注意事项**
> 设备特定信息可在绝对需要时使用，但要对收到的信息进行验证，并为意外值提供合理的默认行为。

### 审核您的界面代码
为了最大限度地减少干扰，visionOS 会尽可能在与 iPad 相匹配的环境中运行兼容的 iPad 或 iPhone 应用程序。窗口和视图保留了与 iPadOS 或 iOS 中相同的外观，而且系统会尽可能调整应用程序窗口的大小以适应 iPad。

在构建应用程序的界面时，请选择能确保您的应用程序在 visionOS 中也能良好运行的选项。对于与界面相关的代码，请采用以下最佳实践：

- **在同一个应用程序中支持 iPad 和 iPhone**。创建一个同时支持两种设备类型的应用，而不是为每种设备分别创建应用。SwiftUI 和 UIKit 支持可调整的界面，Xcode 提供的工具可帮助您在不同的支持尺寸下可视化界面。
- **使用场景组织界面场景是管理应用程序界面的基本工具**。使用 SwiftUI 和 UIKit 中的场景类型来组合和管理您在窗口中显示的视图。
- **让界面适应任何尺寸设计您的界面**。使其自然适应不同尺寸。有关 SwiftUI 视图和布局的介绍，请参阅[声明自定义视图](https://developer.apple.com/documentation/SwiftUI/Declaring-a-Custom-View)。有关在 UIKit 中布局视图的信息，请参阅[视图布局](https://developer.apple.com/documentation/uikit/view_layout)。
- **不要访问屏幕细节**。visionOS 为 [`UIScreen`](https://developer.apple.com/documentation/uikit/uiscreen) 对象提供了合理的值，但不要使用这些值来做决定。
- **指定支持的界面方向**。在应用程序的 · 文件中添加 [`UISupportedInterfaceOrientations`](https://developer.apple.com/documentation/bundleresources/information_property_list/uisupportedinterfaceorientations) 关键字，以指定其支持的界面方向。只有在该键存在时，visionOS 才会为你的应用程序按钮添加界面旋转。
- **更新自定义视图中的悬停效果**。悬停效果可传达界面中的重点视图或控件。标准系统视图会根据需要应用悬停效果。对于自定义视图和控件，请确认悬停效果在 visionOS 中看起来是否合适。必要时，为悬停效果添加或更新内容形状。
- **尽可能采用基于矢量的图像**。基于矢量的图像可以很好地缩放至不同尺寸，同时保持清晰的外观。如果您使用基于位图的资产，请将它们制作成所需的精确尺寸。不要使用过大的资产，因为它们需要额外的工作才能以正确的尺寸显示。

如果希望 visionOS 在启动时以特定方向显示应用程序的界面，请在应用程序的 `Info.plist` 文件中添加 `UIPreferredDefaultInterfaceOrientation` 密钥。将该键的值设置为应用程序的 [`UISupportedInterfaceOrientations`](https://developer.apple.com/documentation/bundleresources/information_property_list/uisupportedinterfaceorientations) 键中的值之一。例如，要指定纵向方向的偏好，请将值设置为 `UIInterfaceOrientationPortrait`。在键名中添加 `~ipad` 或 `~iphone` 以指定特定设备的方向首选项。

### 优雅地应对功能缺失

如果您的应用程序依赖的框架在 visionOS 中表现不同，请更新您的代码以处理这些差异。可用性检查会在无法使用某项功能时给出明确指示，但某些框架可能会有更微妙的行为。在你的整个代码中，确保你能应对异常情况：

- **处理错误条件**。如果函数抛出异常或返回错误，请处理错误。使用错误信息来调整应用程序的行为，或解释无法执行某些操作的原因。
- **优雅地处理 nil 或空值**。在尝试使用对象和返回值之前对其进行验证。
- **更新界面**。当某一功能缺失时，在界面中提供适当的消息，或者如果可以干净利落地移除特定功能视图，则完全移除。不要在有功能的地方留下空视图。

有关在 visionOS 中表现不同的框架的信息，请参阅[检查现有应用程序是否与 visionOS 兼容](https://developer.apple.com/documentation/visionos/checking-whether-your-app-is-compatible-with-visionos)。

### 删除使用过时 API 的代码

如果您的应用程序当前使用了过时的 API 或框架，请更新代码以使用适当的替换。过时的符号代表过时的功能，在某些情况下，当您调用它们时可能什么也做不了。为防止出现潜在问题，请使用现代等效符号替换它们，以确保您的代码按预期运行。

在 iPadOS、iOS 和 visionOS 中，以下框架已被完全废弃。如果您的应用程序仍在使用这些框架，请立即停止使用。每个框架的参考文档都包含有关如何更新代码的信息。
- 账户
- 地址簿
- 地址簿用户界面
- 资产库
- 广告
- 报摊套件
- 通知中心
- OpenGL ES

---

## 另见

### iOS 迁移和兼容性

- [将现有应用程序移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-app-to-visionos)  
使用 visionOS SDK 构建 iPadOS 或 iOS 应用程序的一个版本，并根据平台差异更新代码。
- [将 ARKit 应用程序移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)  
更新使用 ARKit 的 iPadOS 或 iOS 应用程序，并在 visionOS 中提供同等体验。
- [检查现有应用程序是否与 visionOS 兼容](https://developer.apple.com/documentation/visionos/checking-whether-your-app-is-compatible-with-visionos)  
确定您现有的 iOS 或 iPadOS 应用程序是否可在 visionOS 中按原样运行，还是需要进行修改以处理平台差异。