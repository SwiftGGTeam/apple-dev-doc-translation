# 在三维空间中追踪预登记图像

根据已知图像的当前位置在用户周围来放置内容。

## 概述

使用 ARKit 对 2D 图像的跟踪支持，在空间中放置 3D 内容。当图像相对于人移动时，ARKit 会更新图像的位置。如果您在应用程序的资产目录中提供一张或多张参考图像，人们就可以使用该图像在现实世界中的副本在您的应用程序中放置虚拟 3D 内容。例如，如果您设计了一包定制扑克牌，并将这些资产以一副真实扑克牌的形式提供给用户，他们就可以在完全沉浸式的体验中为每张牌放置独特的内容。

下面的示例跟踪了从应用程序资产目录中加载的一组图像：

```swift
let session = ARKitSession()
let imageInfo = ImageTrackingProvider(
    referenceImages: ReferenceImage.loadReferenceImages(inGroupNamed: "playingcard-photos")
)


if ImageTrackingProvider.isSupported {
    Task {
        try await session.run([imageInfo])
        for await update in imageInfo.anchorUpdates {
            updateImage(update.anchor)
        }
    }
}


func updateImage(_ anchor: ImageAnchor) {
    if imageAnchors[anchor.id] == nil {
        // Add a new entity to represent this image.
        let entity = ModelEntity(mesh: .generateSphere(radius: 0.05))
        entityMap[anchor.id] = entity
        rootEntity.addChild(entity)
    }
    
    if anchor.isTracked {
        entityMap[anchor.id]?.transform = Transform(matrix: anchor.originFromAnchorTransform)
    }
}
```

如果你知道所跟踪图像的实际尺寸，可使用 physicalSize 属性来提高跟踪的准确性。estimatedScaleFactor 属性提供了有关跟踪图像的比例与你提供的预期物理尺寸之间差异的信息。

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
- [追踪世界空间中的特定点](https://developer.apple.com/documentation/visionos/tracking-points-in-world-space)  
检索应用在 ARKit 中存储的锚点的位置和方向。