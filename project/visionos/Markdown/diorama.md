# Diorama

使用 Reality Composer Pro 设计 visionOS 应用程序中的场景

[下载本文的示例项目](https://docs-assets.developer.apple.com/published/de92f635b991/Diorama.zip)

## 概览


VisionOS 应用程序使用 RealityKit 展示的虚拟现实内容，可以通过 Reality Composer Pro 来构建，编辑，预览。在 Reality Composer Pro 项目中，可以创建多个场景，每个场景通过层次结构组织虚拟对象（也就是实体），这样你的应用程序可以高效率地加载显示虚拟现实内容。

另外，在 Reality Composer Pro 中也可以为场景中的实体（ Entity ）增加和配置组件（ Component ）—— 也包括自定义的组件，这样可以帮助你构建实体的层次结构。

![Diorama overview](https://docs-assets.developer.apple.com/published/c106f0741656e1984ef4ebafc62f03ef/Diorama-overview.mp4)

你也可以使用 *Shader Graph* 设计实体的虚拟外观，Shader Graph 也是一个基于节点的视觉工具，用来创造 RealityKit 可以使用的素材（Materials）。Shader Graph 提供了丰富的表面细节和形状可以操作的实体。你也可以创建有动画效果的材质( animated materials )和动态的材质（ dynamic materials ），这些素材可以根据应用程序的状态或用户的输入而改变。

Diorama 项目展示了 RealityKit 和 Reality Composer Pro 许多特性。这个项目展示了一个可交互的虚拟徒步地形图，模拟了现实世界中国家公园的登山路径和护林员站点的地形图。虚拟地图上有标志性景点，可以点击获取详细信息。也可以在 Yosemite 和 Catalina Island 这两个虚拟徒步地图之间丝滑的切换。

### 导入 Asset 来构建场景


<p style="width: 100%; padding:10px 0px;" >
<img src="https://docs-assets.developer.apple.com/published/68c8d792b69ef000f6c70a0b8724ee44/rcpro-library-cropped@2x.png" align = "right" width="50%" height="auto"/>
Reality Composer Pro 项目包含了应用程序用来构建场景的各种数字资源（ Asset ）。Diorama 项目中有多个 Asset ，包括许多 3D 模型，比如仿真桌子，模拟的徒步地形图，几只鸟儿和云朵，以及环境音和一些图像。Reality Composer Pro 提供了一系列内置 3D 模型。可以点击窗口右上角的“ Add (+) ”按钮，在右边弹出的工具栏中可以看到这些 3D 模型。选择相应的对象就将它们导入到项目中。 
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
</p>


Diorama 使用的是自定义的 Asset。为了在 Reality Composer Pro 的场景中使用自定义的 Asset ，有三种方式将 Asset 导入到项目中：直接将它们拖拽到 Reality Composer Pro 项目的 Project Browser 窗口中；点击菜单栏中“ File > Import ”选择相应的资源文件( Asset )导入；将资源文件( Asset ) 拷贝到项目关联的 Swift package 的 `.rkassets` Bundle中。


![Rcpro Assets](https://docs-assets.developer.apple.com/published/e31f4da3e3aadabb15f50ace406f6b81/rcpro-assets@2x.png)


> 注意
> 
> 即使可以在 VisionOS 系统中直接加载 USDZ 文件，以及其他的资源文件( Asset )，但是 RealityKit 会将 Reality Composer Pro 项目中的资源文件( Asset )编译为二进制格式，这比加载单独的文件要快得很多。


### 创建包含应用程序实体的场景

单独的 Reality Composer Pro 项目中可以有很多个场景。场景是实体的层次结构，在项目中以 .usda 类型的文件存储场景，可以通过 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 加载显示。你可以使用 Reality Composer 的“ Scenes ”可以构建完整的 RealityKit 场景，也可以用于存储可重用的实体层次结构——在运行时作为构建块创建其他场景——类似于 Diorama 项目中那样使用。可以根据需要，在菜单栏中选择“ File > New > Scene ”，或者点击“ ⌘N ”，在项目中添加不同场景。

在 Reality Composer Pro 窗口的顶端显示当前所有打开场景的标签 Tab 。 Project Browser 窗口中双击 .usda 文件来打开场景。选中场景的标签 tab ，这样就可以编辑场景——在 Hierarchy Viewer 窗口（在左侧），3D 内容展示（中间），Inspector 属性窗口（在右侧）可以对场景做修改。

![Rcpro Scene Tabs](https://docs-assets.developer.apple.com/published/7b08ca4b0e489767d821e0ae7b79d895/rcpro-scene-tabs@2x.png)

### 在场景中添加资源 Asset

RealityKit 只支持场景中包含实体，但它无法将 Reality Composer Pro 支持的所有类型的资源 Asset 转化为实体使用。当你将某些 Asset——例如3D模型，置于场景中时，Reality Composer Pro 会自动将 Asset 转化为一个实体。Reality Composer Pro 则会间接使用其他类型的资源 Asset。例如，它主要使用图像文件描绘模型实体的表面细节。

Diorama 使用多个场景将资源 Asset 整合在一起，这样运行时可以将这些场景整合为一个完整的沉浸式体验环境。例如，DioramaAssembled 场景中包含了桌子，徒步地形图作为桌面，以及地形图上的路线。在地形图上飞翔的鸟群和漂浮的云朵也分作独立的场景。

<p style="width: 100%; padding:10px 0px;" >
<img src="https://docs-assets.developer.apple.com/published/03bdd6cd0d9b758ce7dc87d4f9122844/rcpro-manipulator@2x.png" align = "left" style="width: 50%;height: auto; padding: 0px 15px 0px 0px;" />
将 Asset 从 Project Browser 窗口拖拽到场景层次结构中或 3D 内容展示区域中，这样就在场景中添加了实体。如果你拖拽的 Asset 可以作为实体的类型，Reality Composer Pro 就会将它添加到场景中。在场景层次结构 或者 3D 内容展示区域中选中任何一个 Asset ，可以在右侧的 Inspector 属性窗口中，或者3D 内容展示区域使用操纵器 Manipulator ，调整 Asset 的位置，旋转角度，或者缩放操作。
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
</p>


### 给实体添加组件

RealityKit 遵循“实体-组件-系统”（ ECS ）的设计原则。在 ECS 架构的应用程序中，你就可以使用组件存储实体额外的数据，使用组件中的数据编写系统逻辑实现实体的行为。Reality Composer Pro 中可以为实体添加和设置组件，组件类型包括像 PhysicsBodyComponent 这样自带的组件，也可以自定义的组件——位于 Reality Composer Pro 项目的 Swift Package 包中的 Sources 文夹件下。你也可以在 Reality Composer Pro 中创建新的组件，并且可以在 Xcode 中修改它们。更多关于 ECS 的信息，请查阅 [Understanding RealityKit’s modular architecture](https://developer.apple.com/documentation/visionos/understanding-the-realitykit-modular-architecture) 。

Diorama 使用自定义组件辨认那些标志性景点的变换操作 transform ，标记鸟儿让它们成群结队的飞翔，或者指定两张地图中的一个的组件以控制实体的透明度。

在层次结构或 3D 内容展示区域中选择其中的实体，这时展示在右侧 Inspector 属性窗口种，点击最下面的 Add Component 按钮来为选中的实体添加组件。这时，屏幕中间会出现一个可用的组件列表，列表的第一行是“ New Component ”。这个选项是创建一个新的组件类别，也可以选择创建一个新的系统类别，同时把这个新创建的组件添加到了选中的实体中。

<p style="width: 100%; padding:10px 0px;" >
<img src="https://docs-assets.developer.apple.com/published/5b880670ece2e4d51e0343bb9592f26e/rcpro-new-component@2x.png" align = "right" style="width: 50%;height: auto; padding: 0px 0px 0px 15px;" />
<p style="color:transparent;"> - </p>
在组件列表中，可以看到 Diorama 项目中用来辨认标志性景点的变换操作 Transform 的 `PointOfInterestComponent` 组件。如果选中实体没有包含 `PointOfInterestComponent` 组件，选择把这个组件添加到选中的实体中。每个实体有且仅有一个某种类型组件的实例。你可以在 Inspector 属性窗口编辑现存组件的数值，这些会改变标志性景点被点击时的显示。
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
</p>

### 使用 Transform 去做标注点

在 Reality Composer Pro 中，`transform` 是标记空间的点的虚实体。Transform 包含位置信息 Position，旋转信息 Rotation，缩放信息 Scale，同时他的子类实体会继承这些信息。但是，Transform 没有视觉表述，它们本身没有独立的功能。Transform 是用来标记场景中位置，或者用来组织实体层次结构。比如，你会将需要一起移动的实体设置为有相同 Transform 的子实体，这样就可以通过移动父实体的 Transform 的同时将子实体们一起移动。

<p style="width: 100%; padding:10px 0px;" >
<img src="https://docs-assets.developer.apple.com/published/481feec7fa1f1ce0bb84e6b0236d729f/rcpro-point-of-interest-component@2x.png" align = "right" style="width: 30%;height: auto; padding: 0px 0px 0px 15px; vertical-align: middle;" />
<p style="color:transparent;"> - </p>
Diorama 使用 `PointOfInterestComponent` 组件和 Transform 一起标示地图上的标志性景点。在应用程序运行时，Transform 会标记展示地理位置名称的悬浮标牌的位置。点击标牌使其展开显示详细信息。将 `PointOfInterestComponent` 组件添加到 Transform 虚实体中，应用程序查找有 `PointOfInterestComponent` 类型组件的 Transform 虚实体，将其转化为可交互的视图。因为 Transform 除了包含位置数据 Position，方向数据 Rotation，缩放数据 Scale 之外没有其他的数据，所以 Transform 使用 PointOfInterestComponent 组件来存储标牌显示所需要的数据。在 Reality Composer Pro 打开名为 DioramaAssembled 场景，点击名为 `Cathedral_Rocks` 的 Transform，这时在 Inspector 属性窗口就能看到 `PointOfInterestComponent` 组件。
<p style="color:transparent;"> - </p>
<p style="color:transparent;"> - </p>
</p>

### 程序运行时加载场景

为了更方便快捷地加载 Reality Composer Pro 场景，Reality Composer Pro Swift package 定义了一个常量，这个常量就是 Reality Composer Pro 项目名称后面追加 “Bundle” 字符。在调用 [load(named:in:)](https://developer.apple.com/documentation/RealityKit/Entity/load(named:in:)) ，将场景的名称字符串作为 `named` 参数，` {{ProjectName}}Bundle ` 常量作为 `in` 参数，就可以加载 Reality Composer Pro 项目 Bundle 中的场景。下面的例子中，项目名是 `RealityKitContent` ，所以常量就是 `RealityKitContentBundle` 。下面代码就是在 Diorama 项目中通过 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 初始化方法加载地图桌面场景：

```swift
let entity = try await Entity.load(named: "DioramaAssembled", 
                                   in: RealityKitContent.RealityKitContentBundle)
```

当从一个异步上下文中调用时 [load(named:in:)](https://developer.apple.com/documentation/RealityKit/Entity/load(named:in:)) 方法时，这个方法就会被异步执行的。因为 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 初始化方法中的 content 闭包时异步执行，那么在闭包中加载场景也是异步的。注意异步调用 [load(named:in:)](https://developer.apple.com/documentation/RealityKit/Entity/load(named:in:)) 须要使用 `await` 关键字。


### 创建悬浮视图

Diorama 将 `PointOfInterestComponent` 组件添加到 Transform 用以显示景点的详细信息。地图上每个景点的位置上面都悬浮着显示景点名字的视图。点击悬浮视图时，便会展开显示详细信息，这些信息就是来自 `PointOfInterestComponent` 组件存储的数据。创建 SwiftUI 视图展示景点的详细信息，通过 `PointOfInterestComponent` 组件查询相应的实体，使用 `ImmersiveView.swift` 中声明的查询：

```swift
static let markersQuery = EntityQuery(where: .has(PointOfInterestComponent.self))
```

Diorama 项目中，在 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 初始化方法中查询获取那些标志性景点实体，并将它们传递给 `createLearnMoreView(for:)` 函数，这个函数创建详情视图，并将它存储下来，在点击的时候在展示它。


```swift
subscriptions.append(content.subscribe(to: ComponentEvents.DidAdd.self, componentType: PointOfInterestComponent.self, { event in
    createLearnMoreView(for: event.entity)
}))
```

### 为标志性景点创建附属视图

Diorama 用 `LearnMoreView` 视图展示 `PointOfInterestComponent` 组件中储存的详细信息，首先将 LearnMoreView 存储为附属视图。*Attachments 附属视图*是 SwiftUI 视图，同时也是 RealityKit 实体，而在 RealityKit 场景中使用的是附属视图作为实体并设定位置。例如，Diorama 设置的是附属视图（ Attachment ）的位置，让它们悬浮在每个标志景点的上方。

> 译者注：将 Attachment 译作“附属视图”，attachment 就像是链接 SwiftUI 环境 和 RealityKit 环境 的一个中间件。

应用程序首先会检查实体是否有 `PointOfInterestRuntimeComponent` 组件。如果没有，会创建新的 PointOfInterestRuntimeComponent 组件并将这个组件添加到实体中。这个组件存储的是只有在运行时使用的数值，无需用 Reality Composer Pro 编辑。

在运行时将数值放在单独的组件中，并把组件添加到实体中，Reality Composer Pro 不会在 Inspector 属性窗口中显示出这个运行时组件。`PointOfInterestRuntimeComponent` 用 `attachmentTag`属性存储附属视图的唯一标识符，这样应用程序会在合适的时候通过 `attachmentTag` 获取相应视图并显示出来。

```swift
struct PointOfInterestRuntimeComponent: Component {
    let attachmentTag: ObjectIdentifier
}
```

接下来， Diorama 创建 `LearnMoreView` 类型的 SwiftUI 视图，显示 `PointOfInterestComponent` 中的信息，接下来设置详细视图的 `tag` 值，同时使用相同的 `tag` 值设置 P`ointOfInterestRuntimeComponent` 的 `attachmentTag` 属性。最后，讲这些详细视图存储在 `AttachmentProvider` 中，`AttachmentProvider` 是用来维护持有附属视图引用的自定义类，以防这些视图因没有添加在场景中而被释放。

```swift
let tag: ObjectIdentifier = entity.id


let view = LearnMoreView(name: pointOfInterest.name,
                         description: pointOfInterest.description ?? "",
                         imageNames: pointOfInterest.imageNames,
                         trail: trailEntity,
                         viewModel: viewModel)
    .tag(tag)
entity.components[PointOfInterestRuntimeComponent.self] = PointOfInterestRuntimeComponent(attachmentTag: tag)


attachmentsProvider.attachments[tag] = AnyView(view)
```

### 展示标志景点的附属视图

将视图添加到 AttachmentProvider 中时，并不会立即显示在场景中。可以选择 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 有名为 `attachments` 的视图构建器（ ViewBuilder）参数的初始化方法，这个视图构建器创建的附属视图指定给了 attachments 参数。

```swift
ForEach(attachmentsProvider.sortedTagViewPairs, id: \.tag) { pair in
    pair.view
}
```

当 RealityKit 视图内容发生变化的时候，会调用初始化时指定 update 闭包，在这个闭包中会通过 `PointOfInterestRuntimeComponent` 类型查询相应的实体，通过组件的 attachmentTag 属性获取对应的附属视图，然后将附属视图添加到地图上并设置它的位置。（译者注：详细请参阅 RealityView 的初始化方法）。

```swift
viewModel.rootEntity?.scene?.performQuery(Self.runtimeQuery).forEach { entity in


    guard let attachmentEntity = attachments.entity(for: component.attachmentTag) else { return }
    
    if let pointOfInterestComponent = entity.components[PointOfInterestComponent.self] {
        attachmentEntity.components.set(RegionSpecificComponent(region: pointOfInterestComponent.region))
        attachmentEntity.components.set(OpacityComponent(opacity: 0))
    }
    
    viewModel.rootEntity?.addChild(attachmentEntity)
    attachmentEntity.setPosition([0, 0.2, 0], relativeTo: entity)
}
```

### 使用 Shader Graph 创建自定义的素材


Diorama 通过滑动条控制两张地图之间渐变地切换。为了达到这种效果，先在地图上绘制等高翔，`DioramaAssembled` 场景中的 `FlatTerrain` 实体使用的 *Shader Graph 素材*。 Shader Graph 是基于节点（ node-base ）的素材编辑器，它已经集成到 Reality Composer Pro 。使用 Shader Graph 可以创建运行时改变的动态素材。在 Reality Composer Pro 推出之前，实现动态素材唯一办法就是创建自定义素材 [CustomMaterial](https://developer.apple.com/documentation/RealityKit/CustomMaterial) 并通过编写 Metal Shader 实现必要逻辑。

Diorama 项目中的 `DynamicTerrainMaterialEnhanced` 做了两件事情：根据地图模型的位移贴图中存储高度数据来描绘等高线和平面圆形上地形外表的顶点；通过在两个不同的高度地图之间进行插值。应用程序实现了在两组有不同的高度数据的地图之间平滑切换。


<p style="width: 100%; padding:15px 0px;" >
<img src="https://docs-assets.developer.apple.com/published/de06e6ab95eaf46129dab538dfec72c4/rcpro-shader-graph-output-node@2x.png" align = "right" style="width: 25%;height: auto; padding: 0px 0px 0px 15px; vertical-align: middle; "/>
在创建 Shader Graph 素材时，你可以在Swift代码中通过 *Promote Inputs* 参数给 Shader Graph 素材传递数据。这样可以实现编写 Metal Shader 所需要的逻辑。使用编辑器创建的素材后，可以使用素材中自定义外表的输出节点（ custom surface output ）作用与实体的外观，这等价于编写 Fragment Shader 的 Metal 代码；也可以使用素材中集合修改器的输出（ geometry modifier output ）摆放顶点的位置，这等价于编写 Vertex Shader 的 Metal 代码。
</p>

节点图（ Node graph ）可以包*含子图（ Subgraph ）*，这类似函数。它们包含一系列可重用的输入和输出节点。子图（ Subgraph ）包含绘制等高线和调整顶点的逻辑。双击子图（ Subgraph ）进行编辑操作。更多关于使用 Shader Graph 创建素材，请参照 [Explore Materials in Reality Composer Pro](https://developer.apple.com/wwdc23/10202) 。

### 运行时更新 Shader Graph 素材

`DynamicTerrainMaterialEnhanced` 有名为 `Progress` 的 Promoted Input 参数。如果将参数设置为 1.0 ，则会显示 Catalina Island。如果见参数设为 0 ，则会显示 Yosemite。如果将参数设置为 0 到 1 之间的数值，则显示两个地图切换时的中间状态。当用户操作滑动条时，应用程序会用滑动条当前的数值更新输入参数。

> 要点
>
>Shader Graph 素材参数的名称是区分大小写的。如果大写和小写的拼写不正确，你的代码就无法更新材料。


应用程序会在 Slider 的 `.onChanged` 回调闭包中调用 `handleMaterial()` 函数，在函数中获取 terrain 实体中的 [ShaderGraphMaterial](https://developer.apple.com/documentation/RealityKit/ShaderGraphMaterial) 素材，然后调用素材的 [setParameter(name:value:)](https://developer.apple.com/documentation/RealityKit/ShaderGraphMaterial/setParameter(name:value:)) 的方法去更新素材的输入参数。

```swift
private func handleMaterial() {
    guard let terrain = viewModel.rootEntity?.terrain,
            let terrainMaterial = terrainMaterial else { return }
    do {
        var material = terrainMaterial
        try material.setParameter(name: materialParameterName, value: .float(viewModel.sliderValue))
        
        if var component = terrain.modelComponent {
            component.materials = [material]
            terrain.components.set(component)
        }
        
        try terrain.update(shaderGraphMaterial: terrainMaterial, { m in
            try m.setParameter(name: materialParameterName, value: .float(viewModel.sliderValue))
        })
    } catch {
        print("problem: \(error)")
    }
}
```
