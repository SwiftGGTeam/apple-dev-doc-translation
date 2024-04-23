# 在检测到的平面上放置内容

检测水平平面（如桌子和地板）以及垂直平面（如墙壁和门）。

## 概述

平面是在 visionOS 中使用 "全空间 "的应用中定位内容的理想场所。它们为虚拟 3D 内容提供了与人的周围环境共存的场所。使用 ARKit 中的平面检测功能可检测到这些类型的表面，并根据你的应用可能需要的标准（如平面的大小、与某人的距离或所需的平面方向）过滤可用的平面。

<video controls src="https://docs-assets.developer.apple.com/published/364b575a694972747bab316a6e17615d/plane-detection.mp4" title="录像"></video>

### 使用 RealityKit 锚点实体进行基本的平面锚定

如果你的应用中不需要特定的平面，而且你是在 RealityKit 中渲染应用的 3D 内容，那么你可以使用锚实体（[`AnchorEntity`](https://developer.apple.com/documentation/RealityKit/AnchorEntity)）来代替。这种方法可让你将 3D 内容附加到一个平面上，而无需提示用户获得世界感应权限，也无需特别了解该平面相对于用户的位置。

下图显示了一个锚点，你可以使用它将实体附加到表格上：

```swift
AnchorEntity(.plane(.horizontal, classification: .table, minimumBounds: [0.5, 0.5]))
```

锚点实体不会让你选择人物周围环境中的特定平面，而是让你请求具有特定特征的平面。当你需要更具体的平面选择或关于平面在世界中的位置和方向的实时信息时，请使用 ARKitSession 和 PlaneDetectionProvider。

### 为平面检测配置 ARKit 会话

平面检测信息来自配置为使用 [`PlaneDetectionProvider`](https://developer.apple.com/documentation/arkit/planedetectionprovider) 的 [`ARKitSession`](https://developer.apple.com/documentation/arkit/arkitsession)。你可以选择检测水平面、垂直面或两者。ARKit 检测到的每个平面都有一个分类，如 [`PlaneAnchor.Classification.table`](https://developer.apple.com/documentation/arkit/planeanchor/classification/table) 或 [`PlaneAnchor.Classification.floor`](https://developer.apple.com/documentation/arkit/planeanchor/classification/floor)。你可以使用这些分类来进一步细化你的应用使用哪种平面来呈现内容。平面检测需要 [`ARKitSession.AuthorizationType.worldSensing`](https://developer.apple.com/documentation/arkit/arkitsession/authorizationtype/worldsensing) 授权。

下面启动的会话可检测水平和垂直平面，但会过滤掉被归类为窗口的平面：

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

### 创建和更新与每个平面相关联的实体

如果你要显示需要附加到特定飞机上的内容，请在从 ARKit 收到新信息时更新你的内容。当人的周围环境中不再有飞机时，ARKit 会发送移除事件。通过移除与飞机相关的内容来响应这些事件。

下面显示的是将文本实体放在人周围环境中的每架飞机上的飞机更新；文本实体显示 ARKit 检测到的飞机类型：

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
- [在身临其境的体验中融入真实世界的环境](https://developer.apple.com/documentation/visionos/incorporating-real-world-surroundings-in-an-immersive-experience)  
让你的应用内容与当地世界的形状相呼应，从而创造身临其境的体验。
- [追踪世界空间中的特定点](https://developer.apple.com/documentation/visionos/tracking-points-in-world-space)  
检索应用在 ARKit 中存储的锚点的位置和方向。
- [在三维空间中追踪预登记图像](https://developer.apple.com/documentation/visionos/tracking-images-in-3d-space)  
根据已知图像的当前位置在用户周围来放置内容。