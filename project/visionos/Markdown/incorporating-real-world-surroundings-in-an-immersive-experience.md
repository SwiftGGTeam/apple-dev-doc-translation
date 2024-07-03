# 在沉浸式体验中融入真实世界的环境

让你的应用内容与当地环境的地形相呼应，从而创造身临其境的体验。

## 概述

场景重建可帮助缩小应用中渲染的三维内容与用户周围环境之间的差距。在 ARKit 中使用场景重建可让你的应用了解用户周围环境的形状，并将你的应用体验带入用户的世界。沉浸式体验--那些使用 [`mixed`](https://developer.apple.com/documentation/SwiftUI/ImmersionStyle/mixed) 空间样式的体验--最适合用于整合这类上下文信息：场景重建只在空间中可用，且不适用于 [`full`](https://developer.apple.com/documentation/SwiftUI/ImmersionStyle/full) 空间样式。

除了提供附近不同物体形状的三维网格外，ARKit 还会对检测到的每个网格面进行分类。例如，它可能会将网格面分类为电器、家具或房间结构信息（如墙壁和地板的位置）的一部分。下面的视频展示了虚拟立方体与场景重建网格的碰撞，这使得立方体看起来像落在了桌子上：

<div>
    <video width="100%" controls="true" src="https://docs-assets.developer.apple.com/published/936efb1057888e3d20b0035cff16a234/visionos-spatial-gesture-direct.mp4">
</div>

### 配置场景重建会话

场景重建需要 [`ARKitSession.AuthorizationType.worldSensing`](https://developer.apple.com/documentation/arkit/arkitsession/authorizationtype/worldsensing) 授权类型和相应的使用说明，你可以在应用的 `Info.plist` 文件中提供这些信息。以下代码会启动一个会话，并随着 ARKit 完善用户周围环境重建不断处理更新：

```swift
RealityView { content in
    content.add(model.setupContentEntity())
}
.task {
    do {
        if model.dataProvidersAreSupported && model.isReadyToRun {
            try await model.session.run([model.sceneReconstruction, model.handTracking])
        } else {
            await dismissImmersiveSpace()
        }
    } catch {
        logger.error("Failed to start session: \(error)")
        await dismissImmersiveSpace()
        openWindow(id: "error")
    }
}
.task {
    await model.processHandUpdates()
}
.task {
    await model.monitorSessionEvents()
}
.task(priority: .low) {
    await model.processReconstructionUpdates()
}
.gesture(SpatialTapGesture().targetedToAnyEntity().onEnded { value in
    let location3D = value.convert(value.location3D, from: .global, to: .scene)
    model.addCube(tapLocation: location3D)
})
```

### 使用碰撞组件添加与真实世界的交互性

你可以让渲染的三维内容与用户周围的物体（如家具和地板）进行物理交互，从而使其更加逼真。使用 RealityKit 的碰撞组件和物理支持在应用中提供这些交互。[`generateStaticMesh(from:)`](https://developer.apple.com/documentation/RealityKit/ShapeResource/generateStaticMesh(from:)-693dx) 方法是场景重建和 RealityKit 物理模拟之间的桥梁。

> 警告  
> 请注意在使用 [`mixed`](https://developer.apple.com/documentation/SwiftUI/ImmersionStyle/mixed) 样式的沉浸式场景中包含内容的多少。占屏幕很多空间的内容，即使它们部分透明，也仍会影响用户认知周围环境中的潜在危险。如果你想让用户沉浸在你的内容中，请使用 [`full`](https://developer.apple.com/documentation/SwiftUI/ImmersionStyle/full) 样式配置你的空间。如需了解更多信息，请参阅[在应用中构建完全沉浸式的体验](https://developer.apple.com/documentation/visionos/creating-fully-immersive-experiences)。

考虑使用低优先级任务生成网格，因为生成网格的计算成本很高。下面代码使用场景重建生成具有碰撞形状的网格实体：

```swift
func processReconstructionUpdates() async {
    for await update in sceneReconstruction.anchorUpdates {
        let meshAnchor = update.anchor


        guard let shape = try? await ShapeResource.generateStaticMesh(from: meshAnchor) else { continue }
        switch update.event {
        case .added:
            let entity = ModelEntity()
            entity.transform = Transform(matrix: meshAnchor.originFromAnchorTransform)
            entity.collision = CollisionComponent(shapes: [shape], isStatic: true)
            entity.components.set(InputTargetComponent())
            
            entity.physicsBody = PhysicsBodyComponent(mode: .static)
            
            meshEntities[meshAnchor.id] = entity
            contentEntity.addChild(entity)
        case .updated:
            guard let entity = meshEntities[meshAnchor.id] else { continue }
            entity.transform = Transform(matrix: meshAnchor.originFromAnchorTransform)
            entity.collision?.shapes = [shape]
        case .removed:
            meshEntities[meshAnchor.id]?.removeFromParent()
            meshEntities.removeValue(forKey: meshAnchor.id)
        }
    }
}
```

> 备注  
> 场景重建网格仅支持 [`PhysicsBodyMode.static`](https://developer.apple.com/documentation/RealityKit/PhysicsBodyMode/static) 物理实体组件模式。

场景重建网格中的每个物体都会独立更新其 [`originFromAnchorTransform`](https://developer.apple.com/documentation/arkit/meshanchor/4293520-originfromanchortransform) 信息，并且需要一个单独的静态网格，因为 ARKit 将其世界表现细分为多个不同的部分。

### 在调试过程中显示场景重建网格

用户在使用运用了场景重建的应用时，通常不需要看到场景重建网格的可视化渲染。系统已经在沉浸式体验中显示了穿透视频。不过，在开发和调试应用时，临时显示场景重建网格会有所帮助。在 Xcode 的调试工具栏中，单击“启用可视化（Enable Visualizations）”按钮并选择“碰撞形状（Collision Shapes）”。由于场景重建网格的每个元素都有一个碰撞组件，因此网格的细节会显示在可视化调试中。有关更多信息，请参阅[诊断运行中应用的外观问题](https://developer.apple.com/documentation/Xcode/diagnosing-issues-in-the-appearance-of-your-running-app)。

---

## 另见

### ARKit

- [快乐光束](https://developer.apple.com/documentation/visionos/happybeam)  
使用 ARKit 在 Full Space 中创建有趣的游戏。
- [设置对 ARKit 数据的访问](https://developer.apple.com/documentation/visionos/setting-up-access-to-arkit-data)  
检查你的应用是否可以使用 ARKit 并尊重用户隐私。
- [在检测到的平面上放置内容](https://developer.apple.com/documentation/visionos/placing-content-on-detected-planes)  
检测水平平面（如桌子和地板）以及垂直平面（如墙壁和门）。
- [追踪世界空间中的特定点](https://developer.apple.com/documentation/visionos/tracking-points-in-world-space)  
检索应用在 ARKit 中存储的锚点的位置和方向。
- [在三维空间中追踪预登记图像](https://developer.apple.com/documentation/visionos/tracking-images-in-3d-space)  
根据已知图像的当前位置在用户周围放置内容。
- [探索 ARKit 的物体追踪](https://developer.apple.com/documentation/visionos/exploring_object_tracking_with_arkit)  
使用 Create ML 训练的参考对象在 visionOS 中查找和跟踪现实世界中的物体。
- [利用房间追踪打造本地体验](https://developer.apple.com/documentation/visionos/building_local_experiences_with_room_tracking)
在 visionOS 中使用房间跟踪功能提供与物理空间的自定义交互。