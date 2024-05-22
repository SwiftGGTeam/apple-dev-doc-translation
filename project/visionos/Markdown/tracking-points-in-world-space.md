# 追踪世界空间中的特定点

检索应用在 ARKit 中存储锚点的位置和方向。

> [下载](https://docs-assets.developer.apple.com/published/c52a77b613db/ObjectPlacementExample.zip)

visionOS 1.0+ | Xcode 15.1+
----- | -----

## 概述

将世界锚点与 ARKit 会话的 [`WorldTrackingProvider`](https://developer.apple.com/documentation/arkit/worldtrackingprovider) 结合使用，可以随着佩戴设备用户的移动，跨设备、跨会话地追踪你感兴趣的世界点。例如，用户可能会将一个 3D 物体放置在桌子的特定位置，并希望下次使用设备时它再次出现在同一位置。

<video controls width="100%" src="https://docs-assets.developer.apple.com/published/5672970f612c5ca66cbbd4dbd11bb8a3/world-tracking.mp4" title="视频"></video>

ARKit 会为应用创建的每个世界锚点收录一个唯一标识符，当用户在同一地点返回你的应用时，ARKit 会自动将这些锚点放回空间。世界追踪提供程序还会提供用户佩戴设备的位置。

### 使用世界追踪启动 ARKit 会话

使用为世界追踪配置过的 [`ARKitSession`](https://developer.apple.com/documentation/arkit/arkitsession) 开始接收应用放置的世界锚点的更新。下面代码展示了应用之前使用方法 [`addAnchor(_:)`](https://developer.apple.com/documentation/arkit/worldtrackingprovider/4108569-addanchor) 注册的世界锚点的更新：

```swift
let session = ARKitSession()
let worldInfo = WorldTrackingProvider()


Task {
    try await session.run([worldInfo])
    
    for await update in worldInfo.anchorUpdates {
        switch update.event {
        case .added, .updated:
            // Update the app's understanding of this world anchor.
            print("Anchor position updated.")
        case .removed:
            // Remove content related to this anchor.
            print("Anchor position now unknown.")
    }
}
```

> **重要**  
> 如果用户重新定位当前空间（例如按住数码表冠），世界锚点更新将开始更新其相对于新世界原点的位置。例如，放置在桌子上的世界锚点仍会报告桌子的位置信息，但这些位置是相对于更新后的世界原点的。

### 创建和添加世界锚点

启动世界追踪 ARKit 会话后，你可以在应用的世界坐标系中为任何感兴趣的点创建世界锚点。例如，你可能会追踪到用户将某件物品放置在空间内距桌子有特定位移的某一位置：

```swift
let anchor = WorldAnchor(originFromAnchorTransform: deskPlane.originFromAnchorTransform + offset)
try await worldInfo.addAnchor(anchor)
```

使用 [`addAnchor(_:)`](https://developer.apple.com/documentation/arkit/worldtrackingprovider/4108569-addanchor) 方法将世界锚点添加到应用的追踪提供程序后，当前和未来运行的应用会话中的 [`anchorUpdates`](https://developer.apple.com/documentation/arkit/worldtrackingprovider/4180469-anchorupdates) 序列会持续更新该新锚点的当前位置。

### 跨会话保留世界锚点

ARKit 保留的你应用中世界锚点的唯一信息是它们的 [`UUID`](https://developer.apple.com/documentation/foundation/uuid)（[`WorldAnchor`](https://developer.apple.com/documentation/arkit/worldanchor) 实例的 [`id`](https://developer.apple.com/documentation/arkit/worldanchor/4108561-id) 属性）以及在特定空间中的位置。你的应用负责保留其他信息，譬如每个锚点的含义。例如，你可以保存用户放在桌子上的自定义 3D 台灯模型的本地数据。

当用户从一个城市移动到另一个城市或从一个房间移动到另一个房间时，应用不会从被使用过的每个地方接收所有的世界锚点更新。相反， [`anchorUpdates`](https://developer.apple.com/documentation/arkit/worldtrackingprovider/4180469-anchorupdates) 序列仅为附近的物体提供世界锚点更新。

### 追踪设备在世界中的位置

使用 Compositor Services 框架和 [`WorldTrackingProvider`](https://developer.apple.com/documentation/arkit/worldtrackingprovider) 类的 [`queryDeviceAnchor(atTimestamp:)`](https://developer.apple.com/documentation/arkit/worldtrackingprovider/4293525-querydeviceanchor) 方法在空间中获取有关用户设备当前和未来预测姿势的低延迟信息。更多信息，请参阅[使用 Metal 绘制完全沉浸式内容](https://developer.apple.com/documentation/compositorservices/drawing_fully_immersive_content_using_metal)。

---

## 另见

### ARKit

- [快乐光束](https://developer.apple.com/documentation/visionos/happybeam)  
使用 ARKit 在 Full Space 中创建有趣的游戏。
- [设置对 ARKit 数据的访问](https://developer.apple.com/documentation/visionos/setting-up-access-to-arkit-data)  
检查你的应用是否可以使用 ARKit 并尊重用户隐私。
- [在沉浸式体验中融入真实世界的环境](https://developer.apple.com/documentation/visionos/incorporating-real-world-surroundings-in-an-immersive-experience)  
让你的应用内容与当地世界的地形相呼应，从而创造身临其境的体验。
- [在检测到的平面上放置内容](https://developer.apple.com/documentation/visionos/placing-content-on-detected-planes)  
检测水平平面（如桌子和地板）以及垂直平面（如墙壁和门）。
- [在三维空间中追踪预登记图像](https://developer.apple.com/documentation/visionos/tracking-images-in-3d-space)  
根据已知图像的当前位置在用户周围放置内容。