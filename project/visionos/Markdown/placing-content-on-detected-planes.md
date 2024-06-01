# 在检测到的平面上放置内容

检测水平平面（如桌子和地板）以及垂直平面（如墙壁和门）。

## 概述

平面是在 visionOS 上使用“完整空间”的应用中放置内容的理想场所。它们为虚拟 3D 内容提供了与用户周围环境共存的场所。使用 ARKit 中的平面检测功能可检测到这类表面，并根据你的应用可能需要的条件（如平面大小、与用户的距离或所需的平面方向）过滤可用的平面。

<video controls src="https://docs-assets.developer.apple.com/published/364b575a694972747bab316a6e17615d/plane-detection.mp4" title="录像"></video>

### 使用 RealityKit 锚点实体进行基本的平面锚定

如果你的应用不需要特定的平面，而且你在使用 RealityKit 渲染应用的 3D 内容，那么你可以使用锚点实体（[`AnchorEntity`](https://developer.apple.com/documentation/RealityKit/AnchorEntity)）作为代替。这种方法可以让你将 3D 内容添加到一个平面上，且无需提示用户获得世界感知权限，也无需去了解该平面相对于用户的位置。

下代码显示了一个锚点，你可以用它将实体添加到桌子上：

```swift
AnchorEntity(.plane(.horizontal, classification: .table, minimumBounds: [0.5, 0.5]))
```

锚点实体不允许你选择人物周围环境中的特定平面，而是允许你请求具有某些特征的平面。当你需要选择更具体的平面或有关平面在世界中位置和方向的实时信息时，请使用 `ARKitSession` 和 `PlaneDetectionProvider。`

### 为平面检测配置 ARKit 会话

平面检测信息来自配置了 [`PlaneDetectionProvider`](https://developer.apple.com/documentation/arkit/planedetectionprovider) 的 [`ARKitSession`](https://developer.apple.com/documentation/arkit/arkitsession)。你可以选择检测水平或垂直平面或两者。ARKit 检测到的每个平面都有一个分类，如 [`PlaneAnchor.Classification.table`](https://developer.apple.com/documentation/arkit/planeanchor/classification/table) 或 [`PlaneAnchor.Classification.floor`](https://developer.apple.com/documentation/arkit/planeanchor/classification/floor)。你可以使用这些分类来进一步选择用哪类平面来应用中呈现内容。平面检测需要 [`ARKitSession.AuthorizationType.worldSensing`](https://developer.apple.com/documentation/arkit/arkitsession/authorizationtype/worldsensing) 授权。

下面代码启动的会话可检测水平和垂直平面，但会过滤掉被归类为窗口的平面：

```swift
let session = ARKitSession()
let planeData = PlaneDetectionProvider(alignments: [.horizontal, .vertical])


Task {
    try await session.run([planeData])
    
    for await update in planeData.anchorUpdates {
        if update.anchor.classification == .window {
            // Skip planes that are windows.
            continue
        }
        switch update.event {
        case .added, .updated:
            await updatePlane(update.anchor)
        case .removed:
            await removePlane(update.anchor)
        }
        
    }
}
```

### 创建并更新与平面关联的实体

如果你在显示需要绑定至特定平面的内容，请在收到来自 ARKit 的新信息时更新这些内容。当用户周围环境中的某个平面不再可用时，ARKit 会发送移除事件。请通过移除与平面相关的内容来响应移除事件。

下面代码展示了更新平面过程：向用户周围的每个平面上放置文本实体；文本实体显示的是 ARKit 检测到的平面类型：

```swift
@MainActor var planeAnchors: [UUID: PlaneAnchor] = [:]
@MainActor var entityMap: [UUID: Entity] = [:]


@MainActor
func updatePlane(_ anchor: PlaneAnchor) {
    if planeAnchors[anchor.id] == nil {
        // Add a new entity to represent this plane.
        let entity = ModelEntity(mesh: .generateText(anchor.classification.description))
        entityMap[anchor.id] = entity
        rootEntity.addChild(entity)
    }
    
    entityMap[anchor.id]?.transform = Transform(matrix: anchor.originFromAnchorTransform)
}


@MainActor
func removePlane(_ anchor: PlaneAnchor) {
    entityMap[anchor.id]?.removeFromParent()
    entityMap.removeValue(forKey: anchor.id)
    planeAnchors.removeValue(forKey: anchor.id)
}
```

---

## 另见

### ARKit

- [快乐光束](https://developer.apple.com/documentation/visionos/happybeam)  
利用 ARKit 创建一个有趣的游戏。
- [设置对 ARKit 数据的访问](https://developer.apple.com/documentation/visionos/setting-up-access-to-arkit-data)  
检查你的应用是否可以使用 ARKit 并尊重用户隐私。
- [在沉浸式体验中融入真实世界环境](https://developer.apple.com/documentation/visionos/incorporating-real-world-surroundings-in-an-immersive-experience)  
让你的应用内容与环境的地形相呼应，从而创造身临其境的体验。
- [追踪世界空间中的特定点](https://developer.apple.com/documentation/visionos/tracking-points-in-world-space)  
检索应用在 ARKit 中存储的锚点的位置和方向。
- [在三维空间中追踪预登记图像](https://developer.apple.com/documentation/visionos/tracking-images-in-3d-space)  
根据已知图像的当前位置在用户周围放置内容。