# 在三维空间中追踪预登记图像

根据已知图像的当前位置在用户周围放置内容。

## 概述

使用 ARKit 对二维图像的追踪支持，在空间中放置三维内容。当图像相对于用户移动时，ARKit 会提供图像位置的更新。如果你在应用的资源目录中提供了一张或多张参考图片，用户就可以使用该图像的真实副本在应用中放置虚拟的三维内容。例如，如果你设计了一副定制纸牌,并以真实的纸牌包的形式将这些资源提供给用户，他们就可以在完全沉浸式的体验中为每张牌添加独特的内容。

下面的代码展示了如何追踪从应用资源目录中加载的一组图像：

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

如果你知道所追踪图像的实际尺寸，可使用 [`physicalSize`](https://developer.apple.com/documentation/arkit/referenceimage/4108519-physicalsize) 属性来提高追踪精度。[`estimatedScaleFactor`](https://developer.apple.com/documentation/arkit/imageanchor/4108428-estimatedscalefactor) 属性提供了追踪图像的实际尺寸与你预期的物理尺寸之间的差异信息。

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