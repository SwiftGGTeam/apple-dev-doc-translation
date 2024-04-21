# 使你现有的应用与 visionOS 兼容
修改你的 iPadOS 或 iOS 应用，以便在 visionOS 中成功运行。

## 概述

兼容的 iPadOS 或 iOS 应用可链接 iOS SDK 并在 visionOS 中运行。尽管 visionOS 提供了一套完整的 iOS 桥接框架，但由于硬件或使用上的差异，这些框架的某些功能可能无法使用。为确保你的应用能在 visionOS 中正确运行，请优雅地应对可能缺失的功能，并尽可能提供变通方法。

### 使用功能前进行可用性和授权检查

有些框架提供了 API 让你检查框架功能何时可用，或你的应用是否已被授权使用这些功能。在尝试使用相应功能之前，请务必检查这些 API，不要因为存在必要的硬件就认为某项功能可用。设备的配置也会对某些可用性和授权检查的结果产生影响，另外当你的应用在模拟器中运行时，某些功能可能并不存在。如果可用性或授权检查失败，请不要尝试在应用中使用相关功能。

以下框架支持可用性或授权检查：

- **ActivityKit**。检查 [`ActivityAuthorizationInfo`](https://developer.apple.com/documentation/ActivityKit/ActivityAuthorizationInfo) 的 [`areActivitiesEnabled`](https://developer.apple.com/documentation/ActivityKit/ActivityAuthorizationInfo/areActivitiesEnabled) 属性，以确认”实时活动“是否已获得授权。
- **ARKit**。检查配置对象的 [`isSupported`](https://developer.apple.com/documentation/arkit/arconfiguration/2923553-issupported) 属性，以确认增强现实功能是否可用。在 visionOS 中，[`ARView`](https://developer.apple.com/documentation/RealityKit/ARView) 等 ARKit 视图总是不可用，因此请将包含这些视图的接口代码隔离到 iOS 版本的应用中。
- **AVFoundation**。使用 [`AVCaptureDevice.DiscoverySession`](https://developer.apple.com/documentation/avfoundation/avcapturedevice/discoverysession) 类确认哪些摄像头可用。不要假设存在特定的摄像头。
- **自动评估配置**。请在配置 [`AEAssessmentSession`](https://developer.apple.com/documentation/AutomaticAssessmentConfiguration/AEAssessmentSession) 对象时检查错误值。
- **联系人**。使用 [`CNContactStore`](https://developer.apple.com/documentation/Contacts/CNContactStore) 类确认应用的授权状态。
- **核心蓝牙**。使用 [`CBCentralManager`](https://developer.apple.com/documentation/CoreBluetooth/CBCentralManager) 和 [`CBPeripheralManager`](https://developer.apple.com/documentation/CoreBluetooth/CBPeripheralManager) 类确认功能可用性和应用的授权状态。
- **核心触觉**。调用触觉引擎的 [`capabilitiesForHardware()`](https://developer.apple.com/documentation/CoreHaptics/CHHapticEngine/capabilitiesForHardware()) 方法确认可用功能。
- **核心位置**。检查 [`CLLocationManager`](https://developer.apple.com/documentation/corelocation/cllocationmanager) 的属性以确认位置服务的可用性。
- **核心运动**。检查 [`CMMotionManager`](https://developer.apple.com/documentation/coremotion/cmmotionmanager) 的属性，以确认加速计、陀螺仪、磁力计和其他硬件传感器是否可用。
- **核心 NFC**。检查读取器会话的 [`readingAvailable`](https://developer.apple.com/documentation/corenfc/nfcreadersession/3043845-readingavailable) 属性，以确认 NFC 标签读取是否可用。
- **EventKit**。使用 [`EKEventStore`](https://developer.apple.com/documentation/eventkit/ekeventstore) 类确认应用的授权状态。
- **ExposureNotification**。使用 [`ENManager`](https://developer.apple.com/documentation/exposurenotification/enmanager) 类确认应用的授权状态。
- **HealthKit**。使用 [`HKHealthStore`](https://developer.apple.com/documentation/healthkit/hkhealthstore) 类确认健康相关数据是否可用。
- **HomeKit**。检查 [`HMHomeManager`](https://developer.apple.com/documentation/homekit/hmhomemanager) 的属性，确认应用的授权状态。
- **本地身份验证**。使用 [`LAContext`](https://developer.apple.com/documentation/LocalAuthentication/LAContext) 类确认可以使用的身份验证策略。
- **媒体播放器**。使用 [`MPMediaLibrary`](https://developer.apple.com/documentation/mediaplayer/mpmedialibrary) 类确认应用的授权状态。
- **邻近交互**。检查会话的 [`deviceCapabilities`](https://developer.apple.com/documentation/nearbyinteraction/nisession/3951288-devicecapabilities) 属性，确认功能是否可用。
- **PhotoKit**。使用 [`PHPhotoLibrary`](https://developer.apple.com/documentation/photokit/phphotolibrary) 类确认应用的授权状态。
- **ProximityReader**。检查读卡器对象的 [`isSupported`](https://developer.apple.com/documentation/ProximityReader/PaymentCardReader/isSupported) 属性，以确认 iPhone 上的“Tap to Pay”是否可用。
- **ReplayKit**。检查 [`RPScreenRecorder`](https://developer.apple.com/documentation/ReplayKit/RPScreenRecorder) 的 [`isAvailable`](https://developer.apple.com/documentation/replaykit/rpscreenrecorder/isavailable) 属性，以确认屏幕录制功能是否可用。
- **RoomPlan**。检查 [`RoomCaptureSession`](https://developer.apple.com/documentation/RoomPlan/RoomCaptureSession) 对象的 [`isSupported`](https://developer.apple.com/documentation/RoomPlan/RoomCaptureSession/isSupported) 属性，以确认设备上是否支持 3D 激光扫描。
- **SensorKit**。使用 [`SRSensorReader`](https://developer.apple.com/documentation/sensorkit/srsensorreader) 类确认应用的授权状态。
- **语音**。使用 [`SFSpeechRecognizer`](https://developer.apple.com/documentation/speech/sfspeechrecognizer) 类确认语音识别是否可用。
- **用户通知**。使用 [`UNUserNotificationCenter`](https://developer.apple.com/documentation/UserNotifications/UNUserNotificationCenter) 的 [`getNotificationSettings(completionHandler:)`](https://developer.apple.com/documentation/usernotifications/unusernotificationcenter/getnotificationsettings(completionhandler:)) 方法确认应用的授权状态。
- **WatchConnectivity**。调用 [`WCSession`](https://developer.apple.com/documentation/WatchConnectivity/WCSession) 对象的 [`isSupported()`](https://developer.apple.com/documentation/watchconnectivity/wcsession/issupported()) 方法来确认框架是否可用。

### 妥善处理环境差异
Apple 框架在设计时尽可能地采用了无视设备的方式，以减少在不同类型设备上使用时出现的问题。苹果设备的形状和大小各不相同，功能也不尽相同。与其为特定设备构建应用，不如确保应用能适应任何设备，并能从容应对差异。

请在应用的设计过程中考虑到稳健性。请避免可能导致应用在新设备上运行时发生故障的假设，并确保你的应用能动态地适应不同的条件。例如：

- **不要假设设备类型或惯用名总是 iPhone、iPad 或 iPod Touch**。请避免根据当前的惯用名做出决定。如果你确实依赖当前的惯用名，请为未知惯用名提供合理的默认值。
- **将应用设计为能够对应不可用的硬件或功能**。导致特定硬件和功能不可用的原因多种多样。例如，当应用在模拟器中运行时，某个功能或许不可用。请尽可能地执行可用性检查，并从容应对功能缺失。
- **设计能够动态适应的窗口和视图**。使用 SwiftUI 或自动布局来构建界面，以便动态适应任何尺寸。请假设应用的尺寸会动态变化。
- **不要假设设备有特定数量的显示屏**。人们可以将 iPad 和 iPhone 连接到外部显示屏，而 visionOS 设备则使用两个显示屏来创建应用内容的立体版。
- **不要根据可用的框架或符号做出假设**。某个框架或代码符号的存在或缺失不是识别设备类型的可靠方法，而且可能会在今后的软件更新中变化。
- **不要假设你的应用在后台运行**。visionOS 不支持定位、外部访问或蓝牙外设后台执行模式。
- **不要假设所有后台应用都是隐藏的**。在 visionOS 中，后台应用的窗口仍然可见，但在未被观看时会变暗。只有当某个应用呈现出沉浸式空间时，应用窗口才会消失。

当你使用设备详细信息进行决策时，你的应用可能会在未知设备类型上产生不一致或错误的结果，或者彻底崩溃。请寻找依赖环境信息而非设备类型的解决方案。例如，SwiftUI 和 UIKit 使用应用的窗口大小启动布局，而窗口大小并不一定与设备显示屏大小相同。

> **备注**  
> 在绝对必要时可以使用设备特定信息，但要对收到的信息进行验证，并为意外值提供合理的默认行为。

### 审核你的界面代码
为了最大限度地减少干扰，visionOS 会尽可能在与 iPad 相匹配的环境中运行兼容的 iPad 或 iPhone 应用。窗口和视图保留了与 iPadOS 或 iOS 中相同的外观，而且系统会尽可能调整应用窗口的大小以适应 iPad。

在构建应用的界面时，请选择能确保你的应用在 visionOS 中也能正常运行的选项。对于与界面相关的代码，请采用以下最佳实践：

- **在一个应用中支持 iPad 和 iPhone**。创建一个同时支持两种设备类型的应用，而不是为每种设备分别创建应用。SwiftUI 和 UIKit 支持自适应界面，而 Xcode 提供的工具可帮助你在不同的支持尺寸下显示界面。
- **使用场景来组织界面**。场景是管理应用界面的基本工具。使用 SwiftUI 和 UIKit 中的场景类型来组合和管理你在窗口中显示的视图。
- **让界面适应任何尺寸**。将你的界面设计成能自然适应不同尺寸。有关 SwiftUI 视图和布局的介绍，请参阅[声明自定义视图](https://developer.apple.com/documentation/SwiftUI/Declaring-a-Custom-View)。有关在 UIKit 中布局视图的信息，请参阅[视图布局](https://developer.apple.com/documentation/uikit/view_layout)。
- **不要访问屏幕细节**。visionOS 为 [`UIScreen`](https://developer.apple.com/documentation/uikit/uiscreen) 对象提供了合理的值，但请不要使用这些值来做决策。
- **指定支持的界面方向**。在应用的 `Info.plist` 文件中添加 [`UISupportedInterfaceOrientations`](https://developer.apple.com/documentation/bundleresources/information_property_list/uisupportedinterfaceorientations) 键以指定支持的界面方向。请尽可能地支持所有的界面方向。只有当该键存在时，visionOS 才会为你的应用按钮添加界面旋转。
- **更新自定义视图中的悬停效果**。悬停效果用于表明界面中被选中的视图或控件。标准系统视图会根据需要添加悬停效果。对于自定义视图和控件，请确认悬停效果在 visionOS 中看起来是否合适。必要时，为悬停效果添加或更新内容形状。
- **尽可能采用基于矢量的图像**。基于矢量的图像可以很好地缩放至不同尺寸，同时保持清晰的外观。如果你使用基于位图的资源，请将它们生成为所需的精确尺寸。不要使用过大的资源，因为那样需要额外的工作才能将它们显示为正确尺寸。

如果你希望 visionOS 在启动时以特定方向展示应用的界面，请在应用的 `Info.plist` 文件中添加 `UIPreferredDefaultInterfaceOrientation` 键盘。将该键的值设置为应用的 [`UISupportedInterfaceOrientations`](https://developer.apple.com/documentation/bundleresources/information_property_list/uisupportedinterfaceorientations) 键中的值之一。例如，如要指定偏好纵向，请将值设置为 `UIInterfaceOrientationPortrait`。在键名中添加 `~ipad` 或 `~iphone` 以指定特定设备的方向首选项。

### 优雅地应对功能缺失

如果你的应用依赖的框架在 visionOS 中表现不同，请更新你的代码以处理这些差异。可用性检查会在无法使用某项功能时给出明确指示，但某些框架可能会有更微妙的差异。请在你的所有代码中，确保能应对以下异常情况：

- **处理错误情况**。如果函数抛出异常或返回错误，请处理错误。使用错误信息来调整应用行为，或解释无法执行某些操作的原因。
- **优雅地处理 nil 或空值**。在尝试使用对象和返回值之前对其进行验证。
- **更新界面**。当某一功能缺失时，请在界面中提供展示适当的信息，如能干净利落地移除特定功能视图，则请完全移除。不要在功能的原位置留下空视图。

关于在 visionOS 中表现不同的框架的信息，请参阅[检查现有应用是否与 visionOS 兼容](https://developer.apple.com/documentation/visionos/checking-whether-your-app-is-compatible-with-visionos)。

### 移除使用了废弃 API 的代码

如果你的应用当前使用了废弃的 API 或框架，请更新代码以使用适当的替代。废弃符号代表着过时的功能，在某些情况下，调用它们可能什么也做不了。为防止发生潜在的问题，请使用当前的等效符号替换它们，以确保你的代码按预期运行。

在 iPadOS、iOS 和 visionOS 中，以下框架已被完全废弃。如果你的应用仍在使用这些框架，请立即停止使用。每个框架的参考文档都包含有关如何更新代码的信息。
- Accounts
- Address Book
- Address Book UI
- Assets Library
- iAd
- Newsstand Kit
- NotificationCenter
- OpenGL ES

---

## 另见

### iOS 移植和兼容性

- [将现有应用移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-app-to-visionos)  
使用 visionOS SDK 构建当前 iPadOS 或 iOS 应用的新版本，并根据平台差异更新代码。
- [将 ARKit 应用移植到 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-arkit-app-to-visionos)  
更新使用 ARKit 的 iPadOS 或 iOS 应用，并在 visionOS 中提供相同的体验。
- [检查现有应用是否与 visionOS 兼容](https://developer.apple.com/documentation/visionos/checking-whether-your-app-is-compatible-with-visionos)  
确认你现有的 iOS 或 iPadOS 应用是否可在 visionOS 中按原样运行，还是需要进行修改以处理平台差异。