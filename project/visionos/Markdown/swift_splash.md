# Swift Splash

使用 RealityKit 在 visionOS 中搭建一个交互式的水上游乐设施。

## 概览

Apple Vision Pro 能够将虚拟内容和现实世界无缝结合，从而为诸多类型的虚拟交互体验提供可能。Swift Splash 使用 RealityKit 和 Reality Composer Pro 来构建一个由模块化的滑道部件组成的虚拟水上滑梯。而当建造者完成了他们的游乐设施，就能够释放一只勇于冒险的小金鱼去体验他们的作品。

[Swift Splash 示例视频](https://docs-assets.developer.apple.com/published/e44e0e17b57dacc35293878554ef6e54/Swift-Splash-overview.mp4)

Swift Splash 使用多个 Reality Composer 场景形成预设的实体层次结构，这些结构代表了玩家在构筑游乐设施过程中所需要衔接的每一块滑道部件。它示范了如何依照应用的当前状态去隐藏和展示实体层次结构的部分。举个例子来说，每一片滑道部件包含了一条会游动但隐藏着的鱼，直至启动整个游乐设施装置且小鱼到达指定物件时才会被看到。尽管 Swift Splash 看上去提供了一种有趣的、类似游戏的体验，不过其本质利用预设部分来构造虚拟物件的核心思想也可以被用于生产力或是创造性应用的基础。

Swift Splash 的场景囊括了在 Reality Composer Pro 中内建的 Shader Graph 材质，它们用于在运行时改变游乐设施的外观。每一块滑道部件都可以被设定用来展示三种不同材质中的某一种：金属、木质或塑料。其他风格的 Shader Graph 材质会创建特效，例如水的流动以及起点和终点物件上的闪光灯灯效。甚至其中的一些预设实体内还包含了粒子效果，比如当小金鱼抵达终点线时绽放的礼花。

[修改材质](https://docs-assets.developer.apple.com/published/cc7397c5b310b4e2bc18a40734558735/SS-change-material.mp4)

[礼花](https://docs-assets.developer.apple.com/published/f66b8713ce19ea534c48ec6ddd663779/SS-fireworks.mp4)

### 在 Reality Composer Pro 中构建水上滑道部件

滑道部件是构建 Swift Splash 项目的基础模块。Reality Composer 为每一个物件元素提供了独立的编辑场景。除了组成滑梯的 3D 模型以外，每一个场景还包含了若干个在程序中用于对滑道部件进行动画和定位的其他实体。

![滑道部件](https://docs-assets.developer.apple.com/published/88c564a92a9904ce68b8fa6041bfe2ff/SS-slide-piece@2x.png)

在上方屏幕截图左侧的层级检视面板中，有两个被称作 connect_in 和 connect_out 的变换节点。这两个变换节点会对滑道部件的前后连接做出标记。Swift Splash 就是利用了这些节点从而将新的滑道部件对接到现有内容的末端，也可通过手动移动滑道部件并靠近其他物件的方式将它们吸附在一起。

这个滑道部件窗口展示了在 Swift Splash 中通过运行时去查找实体的两种主要机制。对于一些实体来说，例如 connect_in，Swift Splash 会借助命名规则并在需要使用这些实体时通过匹配名称或后缀来获取它们。在其他场景下，例如名称不唯一或者必须通过配置值取得实体时，Swift Splash 会使用自定义组件来对实体进行标记和检索。

举例来说，当游乐设施运行起来后出现的动画实体会包含一个名为 RideAnimationComponent 的组件。程序借助这个组件来判断这个实体是否为设施在运行状态下播放的动画。该组件还负责存储这个程序实现游乐设施动画所需要的额外状态信息，比如名为 duration 的属性，它用于明确何时去启动下一个已经连接的滑梯组件上的动画。

RideAnimationComponent 组件还有一个名为 isPersistent 的属性。持续的水上游乐设施动画内容是始终可见的，不过只有当设施在运行时才会去执行动画，像是在起点区块上通过动画打开的门。非持续的设施动画，例如小鱼在滑道内游过，只会在设施运行并且小鱼正在通过对应的滑道物件时才会显示出来。

### 通过材质引用避免重复的材质实例

在 Swift Splash 中，许多滑道部件会使用相同的材质元素。举个例子，所有部件都共享着同一个能够将部件从金属转变成木质或是塑料的着色器图形材质。为了避免每一种材质都产生重复的副本，Swift Splash 借助 USD 材质引用特性，做到了在多个场景的多个实体之间共享材质。

Reality Composer Pro 工程中对每一个共享的材质都做了独立的场景设定，这个场景只包含了那一种材质。其他的轨道部件只会建立对这材质的引用。如果你尝试改变原始材质，就会影响所有引用了它的实体。例如，一个名为 M_RainbowLights.usda 的场景包含了 M_RainbowLights 的材质，而 StartPiece.usda 和 EndPiece.usda 两者都引用了这种材质。

![材质引用](https://docs-assets.developer.apple.com/published/142b7f884c9a88d6eb345b400e964505/SS-material-reference@2x.png)

### 并行化的资产加载

为了尽可能提升加载速度以及最高效地运用可用的计算资源，Swift Splash 通过 [TaskGroup](https://developer.apple.com/documentation/Swift/TaskGroup) 并行化地加载 Reality Composer 工程中的所有场景。程序会为需要加载资产的每一个场景创建一个独立的 [Task](https://developer.apple.com/documentation/Swift/Task)。

```swift
await withTaskGroup(of: LoadResult.self) { taskGroup in   
    // Load the regular slide pieces and ride animations.
    logger.info("Loading slide pieces.")
    for piece in pieces {
        taskGroup.addTask {
            do {
                guard let pieceEntity = try await self.loadFromRCPro(named: piece.key.rawValue, 
                                                       fromSceneNamed: piece.sceneName) else {
                    fatalError("Attempted to load piece entity \(piece.name) but failed.")
                }
                return LoadResult(entity: pieceEntity, key: piece.key.rawValue)
            } catch {
                fatalError("Attempted to load \(piece.name) but failed: \(error.localizedDescription)")
            }
        }
    }
    // Continue adding asset load jobs.
    // ...
}
```

程序随后会使用异步迭代器来等待并接收加载结果。

```swift
for await result in taskGroup {
    if let pieceKey = pieces.filter({ piece in
        piece.key.rawValue == result.key
    }).first {
        self.add(template: result.entity, for: pieceKey.key)
        setupConnectible(entity: result.entity)
        result.entity.generateCollisionShapes(recursive: true)
        result.entity.setUpAnimationVisibility()
    }
    // ...
}
```

有关任务组（task groups）的更多信息，详见 [The Swift Programming Language](https://docs.swift.org/swift-book/) 中的 [Concurrency](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html) 相关内容。

每一个已经加载的部件都会被看作是一个模板。当玩家在项目中添加了同类部件时，程序会从 Reality Composer Pro 中复制加载过的部件，然后将该部件添加到场景中。

### 指定透明实体的排序顺序

当遇到多个实体产生超过一个重叠，并且存在非不透明的材质时，RealityKit 的默认深度排序可能会按照错误的顺序绘制这些实体内容。这就导致，从特定角度或是相对于其他透明实体的特定位置上看，有一部分实体可能无法被看到。默认深度排序的计算基于实体边界盒的中心点，当存在多个任何透明度的重叠材质时，可能会引发不正确的绘制顺序。你可以在 Reality Composer Pro 中查看起始部件，或观看下方的视频来了解示例的表现形式。

[多个透明度的重叠材质](https://docs-assets.developer.apple.com/published/e40bd1020f495331174a600d5fc6a333/SS-transparency-issue.mp4)

下方的视频演示了这个问题。假设这三个矩形框分别是三个不同的透明实体的边界盒，其中的圆形则代表着盒子的中心，当摄像机在矩形框周围移动时，距离摄像机最近的圆形就会改变，这就会影响 RealityKit 的默认深度排序算法绘制它们的顺序。

[摄像机位于左侧给三个边界盒造成的影响](https://docs-assets.developer.apple.com/published/38642d83602f9b85aec12394c98a3a69/SS-transparency-sorting-from-left.mp4)

[摄像机位于右侧给三个边界盒造成的影响](https://docs-assets.developer.apple.com/published/49c1493c30441a55d45119a43d2c50de/SS-transparency-sorting-from-right.mp4)

Swift Splash 会为每一个透明实体关联一个 [ModalSortGroupComponent](https://developer.apple.com/documentation/RealityKit/ModelSortGroupComponent) 组件，用于手动去指定相对的深度排序。为了修正上方视频中起始部件的透明度问题，Swift Splash 会命令 RealityKit 首先去绘制鱼的不透明部分，再者是透明的护目镜，第三是水，第四是玻璃球，把选中发光的外壳作为最后的绘制物件。Swift Splash 通过使用相同的但制定了不同顺序的 [ModelSortGroup](https://developer.apple.com/documentation/RealityKit/ModelSortGroup)，并为每一个重叠实体关联 [ModelSortGroupComponent](https://developer.apple.com/documentation/RealityKit/ModelSortGroupComponent) 组件来实现这一点。

```swift
fileprivate func setEntityDrawOrder(_ entity: Entity, _ sortOrder: Int32, _ sortGroup: ModelSortGroup) {
    entity.forEachDescendant(withComponent: ModelComponent.self) { modelEntity, model in
        logger.info("Setting sort order of \(sortOrder) of \(entity.name), child entity: \(modelEntity.name)")
        let component = ModelSortGroupComponent(group: sortGroup, order: sortOrder)
        modelEntity.components.set(component)
    }
}


/// Manually specify sort ordering for the transparent start piece meshes.
func handleStartPieceTransparency(_ startPiece: Entity) {
    let group = ModelSortGroup()
    
    // Opaque fish parts.
    if let entity = startPiece.findEntity(named: fishIdleAnimModelName) {
        setEntityDrawOrder(entity, 1, group)
    }
    if let entity = startPiece.findEntity(named: fishRideAnimModelName) {
        setEntityDrawOrder(entity, 2, group)
    }
    
    // Transparent fish parts.
    if let entity = startPiece.findEntity(named: fishGlassIdleAnimModelName) {
        setEntityDrawOrder(entity, 3, group)
    }
    if let entity = startPiece.findEntity(named: fishGlassRideAnimModelName) {
        setEntityDrawOrder(entity, 4, group)
    }
    
    // Water.
    if let entity = startPiece.findEntity(named: sortOrderWaterName) {
        setEntityDrawOrder(entity, 5, group)
    }
    
    // Glass globe.
    if let entity = startPiece.findEntity(named: sortOrderGlassGlobeName) {
        setEntityDrawOrder(entity, 6, group)
    }
    
    // Selection glow.
    if let entity = startPiece.findEntity(named: startGlowName) {
        setEntityDrawOrder(entity, 7, group)
    }
}
```

### 逐个操作连接的轨道部件

所有独立的滑道部件的根实体都有一个 ConnectableComponent 组件。这个可自定义的组件会将实体标记为允许连接或者吸附到其他可连接的实体。在程序运行时，应用会为每一个添加的部件都新增一个 ConnectableStateComponent 组件。这个组件储存的轨道部件状态信息不需在 Reality Composer Pro 进行编辑修改。在这个组件存储的状态信息中也涵盖了前后部件的连接关系。

[部件连接](https://docs-assets.developer.apple.com/published/e315a90792a0ccf12c5154a11f890020/SS-attach.mp4)

要把整条游乐设施串联起来，略去未对接上的滑道部件，应用程序可以从起始部件获取一个引用，然后遍历直至没有后续部件。这种迭代，类似于遍历一条链表，会在应用中被重复运用很多次。一个例子就是计算已搭建的设施运行时长的函数，它通过遍历整轨上每条单独的部件并将它们执行动画的时长依次累加来完成计算。

```swift
/// Calculates the duration of the built ride by summing up the individual durations.
public func calculateRideDuration() {
    guard let startPiece = startPiece else { fatalError("No start piece found.") }
    var nextPiece: Entity? = startPiece
    var duration: TimeInterval = 0
    while nextPiece != nil {
        // Some pieces have more than one ride animation. Use the longest one to calculate duration.
        var longestAnimation: TimeInterval = 0
        nextPiece?.forEachDescendant(withComponent: RideAnimationComponent.self) { entity, component in
            longestAnimation = max(component.duration, longestAnimation)
        }
        duration += longestAnimation
        nextPiece = nextPiece?.connectableStateComponent?.nextPiece
    }
    // Remove the part of the animation after the goal post.
    rideDuration = duration  / animationSpeedMultiplier + 1.0
}
```

### 体验游乐设施

有两种不同的方式可供玩家在 Swift Splash 中进行交互来构建和编辑游乐设施。他们可以通过 SwiftUI 窗口完成特定的操作，例如新增一段新滑道，或是移除一段已经添加到设施中的滑道部件。玩家还可利用 visionOS 支持的标准手势来操作部件，包括点按、双击、拖动和旋转。玩家点击部件以选择或取消选择它。当玩家双击部件时，他们会选择该部件，而不会取消选中的其他部件。当有人去拖移部件时，它会在沉浸式空间中移动，如果和另一个部件靠得足够近，它们就会自动吸附在一起。使用两指旋转手势会让选中的轨道部件或所有部件沿着 Z 轴进行旋转。

[新增部件](https://docs-assets.developer.apple.com/published/d227ea2e245015ecd44926e4ae73af2b/SS-add-piece.mp4)
*新增部件*

[删除部件](https://docs-assets.developer.apple.com/published/3e1a1770ab82e584cef407bd656d08e1/SS-delete-single.mp4)
*删除部件*

[旋转部件](https://docs-assets.developer.apple.com/published/0f5fe14f9080234b49cee2b7b0d3395e/SS-rotate-single.mp4)
*旋转部件*

[移动部件](https://docs-assets.developer.apple.com/published/937c6d8611b3ea87d90a3cdbc0183e67/SS-drag.mp4)
*移动部件*

Swift Splash 在目标实体上采用标准的 SwiftUI 手势去处理其所有的交互操作。为了能随时给任意手势提供支持，应用内使用 [SimultaneousGesture](https://developer.apple.com/documentation/SwiftUI/SimultaneousGesture) 来进行声明。所有的手势代码都包含在 TrackBuildingView 中，这个视图管理着应用内的沉浸式空间。以下是在应用中定义旋转手势的代码：

```swift
.simultaneousGesture(
    RotateGesture()
        .targetedToAnyEntity()
        .onChanged({ value in
            guard appState.phase == .buildingTrack || appState.phase == .placingStartPiece || appState.phase == .draggingStartPiece else { return }
            handleRotationChanged(value)
        })
        .onEnded({ value in
            guard appState.phase == .buildingTrack || appState.phase == .placingStartPiece || appState.phase == .draggingStartPiece else { return }
            handleRotationChanged(value, isEnded: true)
        })
)
```

因为在相同的 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 上执行的多次点按手势可能带有不同的点击次数，多种手势可能会同时被响应。举个例子来说，如果玩家对一个实体进行双击操作，实际上单击和双击的手势代码都会被调用，最终执行的手势由程序决定。Swift Splash 通过一个布尔状态变量来做这个决定。如果玩家单击了，程序会将此变量（叫做 shouldSingleTap）设置为 true。然后程序会等待一段时间再去执行后续的代码。如果在等待阶段 shouldSingleTap 被设定为 false，那么代码就不会被执行。当 Swift Splash 检测到一个双击手势，它会将 shouldSingleTap 设置为 false，以避免在执行双击代码时触发单击代码。

```swift
.simultaneousGesture(
    TapGesture()
        .targetedToAnyEntity()
        .onEnded({ value in
            guard appState.phase == .buildingTrack else { return }
            Task {
                shouldSingleTap = true
                try? await Task.sleep(for: .seconds(doubleTapTolerance))
                if shouldSingleTap {
```