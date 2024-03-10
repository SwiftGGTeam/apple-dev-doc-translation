# 将 ARKit 应用引入 visionOS

更新使用 ARKit 的 iPadOS 或 iOS 应用，并在 visionOS 中提供相同的体验。

## 概述

如果你使用 ARKit 在 iPhone 或 iPad 上创建增强现实体验，那么在将你的应用引入 visionOS 时，你需要重新考虑对该技术的使用。在 iPadOS 和 iOS 中，ARKit 在向显示屏交付内容方面发挥着至关重要的作用。而在 visionOS 中，你只能使用 ARKit 来获取有关用户周围环境的数据，而且使用的是一套不同的 API。

在 visionOS 中，你不需要一个特殊的视图来显示增强现实界面。你只需使用 SwiftUI 或 UIKit 构建包含应用内容的窗口。当你显示这些窗口时，visionOS 会为你将它们放置在用户的周围环境中。如果你想控制用户的周围环境中任意 2D 或 3D 内容的位置，请使用 SwiftUI 和 RealityKit 构建你的内容。

在将应用迁移到 visionOS 时，请尽可能多地复用应用的当前内容。visionOS 支持与 iOS 相同的大多数技术，因此你可以重复使用项目素材、3D 模型和大多数自定义视图。但不要复用应用的 ARKit 代码或任何依赖于 visionOS 平台不支持技术的代码。

有关如何将应用移植到 visionOS 的一般指导，请参阅[将你的现有应用引入 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-app-to-visionos)。

### 采用 iOS 和 visionOS 兼容的技术

要创建一个既可在 iOS 又可以在 visionOS 中运行的应用，请使用两个平台上兼容的技术。iOS 中的 ARKit 可让你使用多种不同的技术创建界面，而 visionOS 中的首选技术是 SwiftUI 和 RealityKit。如果你目前没有使用 RealityKit 来制作 3D 内容，请考虑在开始添加 visionOS 支持之前切换到 RealityKit。如果你的 iOS 应用中保留了使用旧技术的代码，那么在迁移到 visionOS 时，你可能需要使用 RealityKit 重新创建其中的大部分代码。

如果你使用 [Metal](https://developer.apple.com/documentation/metal) 绘制应用的内容，你可以将代码引入 visionOS，为 2D 视图创建内容或创建完全沉浸式体验。你不能使用 Metal 创建与用户周围环境相融合的 3D 内容。这一限制可防止应用对用户周围环境的像素进行采样，因为这些像素可能包含敏感信息。有关如何使用 Metal 创建完全沉浸式体验的信息，请参阅[使用 Metal 绘制完全沉浸式内容](https://developer.apple.com/documentation/compositorservices/drawing_fully_immersive_content_using_metal)。

## 将 3D 素材转换为 USDZ 格式
iOS 和 visionOS 中 3D 素材的推荐格式是 USDZ。这种格式为所有内容（包括模型、纹理、行为、物理、锚点等）提供了一个紧凑的单一文件。如果你的素材不使用这种格式，请使用 Xcode 自带的 Reality Converter 工具为你的项目进行转换。

在为 visionOS 构建 3D 场景时，请使用 Reality Composer Pro 创建包含 USDZ 素材的场景。使用 Reality Composer Pro，你可以导入你的 USD 文件，并进行无损的原装编辑。如果你的 iOS 应用为你的素材添加了自定义材质，请在应用中将这些材质转换为着色器图形。

虽然你可以使用 USDZ 文件将模型和材质引入项目，但你无法引入使用 Metal 编写的自定义着色器。请使用 MaterialX 着色器替换任何自定义着色器代码。许多数字内容创建工具都支持 MaterialX 标准，可让你创建动态着色器并将其与 USDZ 文件一起保存。Reality Composer Pro 和 RealityKit 均支持 MaterialX 着色器，并将它们与其他 USDZ 素材内容结合在一起。有关 MaterialX 的更多信息，请参阅 https://materialx.org。

### 更新界面以支持 visionOS

在 visionOS 中，你负责管理应用的内容，而系统会负责处理这些内容与用户周围环境的整合。这是与 iOS 不同的一点：在 iOS 中，你使用特殊的 ARKit 视图将你的内容与实时摄像头内容融合在一起。因此，将你的界面引入 visionOS 意味着你需要移除这个特殊的 ARKit 视图，并只专注于你的内容。

如果你可以使用 SwiftUI 或 UIKit 视图来显示应用的内容，那么就用这些视图构建一个窗口，并从你的 visionOS 应用中将其呈现出来。如果你使用其他技术将 2D 或 3D 内容融入用户周围的环境，请在你的应用的 visionOS 版本中进行以下替换。

如果你使用了以下框架创建 AR 体验： | 更新为：
----- | -----
RealityKit 和 [`ARView`](https://developer.apple.com/documentation/RealityKit/ARView) | [RealityKit](https://developer.apple.com/documentation/RealityKit) 和 [`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView)
SceneKit 和 [`ARSCNView`](https://developer.apple.com/documentation/arkit/arscnview) | [RealityKit](https://developer.apple.com/documentation/RealityKit) 和 [`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView)
SpriteKit 和 [`ARSKView`](https://developer.apple.com/documentation/arkit/arskview) | [RealityKit](https://developer.apple.com/documentation/RealityKit) 或 [SwiftUI](https://developer.apple.com/documentation/SwiftUI)

[`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView) 是一种 SwiftUI 视图，用于管理使用 RealityKit 和 Reality Composer Pro 创建的内容和动画。你可以将 [`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView) 添加到应用的任何窗口中，以显示 2D 或 3D 内容。你还可以将视图添加到 [`ImmersiveSpace`](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) 场景中，用来将 RealityKit 内容整合到用户的周围环境中。

> 注意  
> 你可以将 iOS 故事板加载到 visionOS 应用中，但不能为 visionOS 定制界面或加入 3D 内容。如果你想在 iOS 和 visionOS 之间共享界面文件，请采用 SwiftUI 视图或以编程方式创建界面。

有关如何使用 [`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView) 以及如何响应与内容交互的更多信息，请参阅[在应用中添加 3D 内容](https://developer.apple.com/documentation/visionOS/adding-3d-content-to-your-app)。

### 替换 ARKit 代码

ARKit 为 iOS 和 visionOS 提供了不同的 API，在这两个平台上使用 ARKit 服务的方式也有所不同。在 iOS 中，你需要使用 ARKit 将内容投至屏幕上，也可以用它来管理内容与用户周围环境之间的交互。而在 visionOS 中，系统会将内容投至屏幕，因此你仅使用 ARKit 管理与周围环境的交互。由于这种使用方式比较有限，有些应用在 visionOS 中完全不需要 ARKit。

只有在需要以下服务时，你才会在 visionOS 中使用 ARKit：
- 平面检测
- 图像追踪
- 场景重建
- 手部追踪
- 世界追踪和设备姿态预测

使用平面检测、图像追踪和场景重建来促进应用的虚拟内容与现实世界物品之间的交互。例如，使用平面检测来检测放置内容的桌面。使用世界追踪来记录在应用重启时需要保存的锚点。如果你的应用需要基于手的自定义输入，使用手部追踪。

要在应用中启动 ARKit 服务，请创建一个 [`ARKitSession`](https://developer.apple.com/documentation/arkit/arkitsession) 对象，并与每个服务的数据提供源一起运行。与 iOS 中的 ARKit 不同，visionOS 中的服务彼此独立，你可以随时启动或停止单个服务。下面的示例展示了如何检测水平和垂直平面。数据提供源使用异步序列传递新的信息。

```swift
let session = ARKitSession()
let planeData = PlaneDetectionProvider(alignments: [.horizontal, .vertical])


Task {
    try await session.run([planeData])
    
    for await update in planeData.anchorUpdates {
        switch update.event {
        case .added, .updated:
            // Update plane representation.
            print("Updated planes.")
        case .removed:
            // Indicate plane removal.
            print("Removed plane.")
        }
    }
}
```

如果你使用了 visionOS 中的世界追踪数据提供源，ARKit 会自动保存你添加到应用内容中的锚点。你无需自己保存这些锚点。

有关如何使用 ARKit 的更多信息，请参阅 [ARKit](https://developer.apple.com/documentation/arkit)。

### 隔离在 visionOS 中不可用的 ARKit 功能

如果你的应用使用了 visionOS 中没有的 ARKit 功能，请将该代码隔离到 iOS 版本的应用中。以下功能在 iOS 中可用，但在 visionOS 中没有相应的功能：

- 面部追踪
- 身体追踪
- 地理追踪和使用经纬度放置锚点
- 物体检测
- 应用剪辑代码检测
- 视频帧后处理

虽然 visionOS 中不提供全身追踪功能，但可以追踪佩戴设备用户的双手。在 visionOS 中，手势是与内容交互的重要方式。SwiftUI 可处理常见类型的交互，如点击和拖动，但你也可以使用自定义手势追踪来实现应用支持的复杂手势。

如果你在 iOS 中使用 ARKit 射线来检测与用户周围环境中的物体的交互，那么你可能不需要在 visionOS 中使用该代码。SwiftUI 和 RealityKit 可在 3D 空间中处理与应用内容的直接和间接交互，因此在很多情况下都无需进行射线检测。在其他情况下，你可以使用 ARKit 和 RealityKit 的功能来管理与内容的交互。例如，你可以使用 ARKit 的手部追踪功能来确定用户在场景中的指向，然后使用场景重建功能来构建一个网格，并将其集成到 RealityKit 内容中。

---

## 另见

### iOS 迁移和兼容性

- [将你的现有应用引入 visionOS](https://developer.apple.com/documentation/visionos/bringing-your-app-to-visionos)  
使用 visionOS SDK 为你的 iPadOS 或 iOS 应用构建一个新版本，并更新代码以适应平台差异。

- [检查现有应用是否与 visionOS 兼容](https://developer.apple.com/documentation/visionos/checking-whether-your-app-is-compatible-with-visionos)  
确定你现有的 iOS 或 iPadOS 应用是否可在 visionOS 中原封不动地运行，还是需要进行修改以处理平台差异。

- [使现有应用与 visionOS 兼容](https://developer.apple.com/documentation/visionos/making-your-app-compatible-with-visionos)  
修改你的 iPadOS 或 iOS 应用，以便在 visionOS 中成功运行。