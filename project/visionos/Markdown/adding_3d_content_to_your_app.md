# 为 App 增添 3D 内容

在 VisionOS App 中添加“深度”——这个第三个维度（ z 轴），以及了解如何将你的应用程序融入到用户的周围环境中。

> 译者注： depth ，这里翻译成“深度”，即为空间深度，也叫纵深。是物体在空间距离下，产生的视觉焦点，并导致所有物体围绕这个焦点聚拢的聚焦方式。通俗讲就是人在一副画面中感觉到的强烈的远近对比。所以，人们通过纵深来营造画面的立体感。

## 概述

立体显示可以让人感受到更真实的 3D 内容。 3D 内容增添让人感觉更加真实的视觉深度，人们可以从不同角度观看，仿佛这些内容真的就在他们的面前。

在构建 visionOS 应用程序，就要考虑在你的应用程序中添加深度（ z 轴）这个纬度。系统提供了多种方式展示 3D 内容，包括在已有的 2D 窗口（ window ），立体窗口（ volume ），或者虚拟空间（ ImmersiveSpace ）。 根据你 App 中内容选择最适合展示方式。

|![Window](https://docs-assets.developer.apple.com/published/344cec64a4a472f1241823d96af51e75/window@2x.png)|![Volume](https://docs-assets.developer.apple.com/published/5dfa0705cd9c1f7db9f980d1201e530c/volume@2x.png)|![Immersive space](https://docs-assets.developer.apple.com/published/fbaf256af4a956be1818b5005f40bfae/immersive-space@2x.png)|
| :----: | :----: | :----: |
| Window | Volume | Immersive space |

### 在二维平面窗口添加深度（ z 轴）维度

二维平面窗口（ window ）是应用程序界面的重要组成部分。 visionOS 系统下的应用软件界面可自动获得 visionOS 系统风格元素，并且可紧紧跟随用户手和眼的移动，灵活地调整窗口的大小，同时突出显示你的自定义控件。

[3D Objects Detail.](https://docs-assets.developer.apple.com/published/d30560f74c5b7b932652bf510cc97d64/3D-Objects-detail.mp4)

根据需求在你的自定义视图中添加 深度效应，并使用下面这些 3D 布局选项在你的二维平面窗口（ window ）中摆设视图（ view ）。

- 使用 [shadow(color:radius:x:y:)](https://developer.apple.com/documentation/SwiftUI/View/shadow(color:radius:x:y:)) 或者 [visualEffect(_:)](https://developer.apple.com/documentation/SwiftUI/View/visualEffect(_:)) 修改视图（ view ）显示外观，使其更具有深度效果。

- 使用 [hoverEffect(_:isEnabled:)](https://developer.apple.com/documentation/SwiftUI/View/hoverEffect(_:isEnabled:)) 方法，使视图（ view ）能够以突出移动或高亮的方式响应用户视线关注。

- 使用 [ZStack](https://developer.apple.com/documentation/SwiftUI/ZStack) 在 Z 轴(深度维度)布局视图（ view ）。

- 使用 [transform3DEffect(_:)](https://developer.apple.com/documentation/SwiftUI/View/transform3DEffect(_:)) ，为视图（ view ）变化添加 3D 动画效果。'

- 使用 [rotation3DEffect(_:axis:anchor:anchorZ:perspective:)](https://developer.apple.com/documentation/SwiftUI/View/rotation3DEffect(_:axis:anchor:anchorZ:perspective:)) 来旋转视图。

另外，你也可以在二维平面窗口（ window ）添加静态 3D 模型，来为 2D 视图（ view ）增加视觉深度。 Model3D 视图类型可以加载 USDZ 文件，或者以 Asset 方式加载，并在窗口（ window ）以文件的固有尺寸展示。通过这种方式展示 3D 内容，前提是你已经有了可展示的模型数据，模型数据可以是放在 App 本地，或者通过网络加载。例如，电商类的 App 可以使用 Model3D 类型视图来展示商品的 3D 效果。


### 使用 RealityKit 展示动态的 3D 场景

RealityKit 是苹果为构建 3D 模型，以及在屏幕中动态更新场景，而开发的框架。在 visionOS 系统中，可以同时使用 RealityKit 和 SwiftUI 将 App 中 2D 和 3D 内容丝滑的融合起来。加载现有的 USDZ Assets，或者使用 Reality Composer Pro，为你的 App 内容，创建一个富有动画，符合物理特性，有光影效果，音效，和自定义行为的场景。以 Swift Package 方式将 Reality Composer Pro project 添加关联到你的 Xcode 项目中，然后在你的 Xcode 项目 Swift 代码中，使用 import ModuleName 方式引用。(和使用和苹果其他 Framework 一样，用 ModuleName 来引用)。更多关于怎样管理项目文件，请参照 [Managing files and folders in your Xcode project](https://developer.apple.com/documentation/Xcode/managing-files-and-folders-in-your-xcode-project) 。

> 译者推荐：了解更多请参阅[Creating 3D Content with Reality Composer](https://developer.apple.com/documentation/realitykit/creating-3d-content-with-reality-composer#Add-the-Composition-to-Your-App)文档和[Meet Reality Composer Pro](https://developer.apple.com/videos/play/wwdc2023/10083/?time=75)视频。

![Dynamic 3D Scene](https://docs-assets.developer.apple.com/published/1ed1e6e2b884fbf653f4e9450e90bf1d/dynamic-3D-scene@2x.png)

使用 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 在用户界面中展示 3D 内容。这个 SwiftUI 就像是一个盛放 RealityKit 内容(就是 3D 内容)的容器，接下来你可以使用熟悉的 SwiftUI 技术更新 3D 内容。

下面的示例代码展示了在视图（ view ）中使用 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 展示一个 3D 球体。在视图（ view ）的闭包中创建一个表面光滑的 RealityKit 球体，然后把球体添加到视图（ view ）中。

```swift
 struct SphereView: View {
    var body: some View {
        RealityView { content in
            let model = ModelEntity(
                         mesh: .generateSphere(radius: 0.1),
                         materials: [SimpleMaterial(color: .white, isMetallic: true)])
            content.add(model)
        }
    }
}
```

当 SwiftUI 要显示 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 时，它只会执行一次闭包的代码来创建实体和其他内容。因为 3D 实体创建相当的消耗性能，所以视图（ view ）只会执行一次创建代码。当需要更新 3D 实体显示状态时，那么就改变相应视图（ view ）的显示状态，并通过更新闭包来实现显示内容的变化。下面的示例代码，在更新闭包中，通过改变模型的 transform.scale 属性来改变球体的大小：

```swift
struct SphereView: View {
    var scale = false


    var body: some View {
        RealityView { content in
            let model = ModelEntity(
                         mesh: .generateSphere(radius: 0.1),
                         materials: [SimpleMaterial(color: .white, isMetallic: true)])
            content.add(model)
        } update: { content in
            if let model = content.entities.first {
                model.transform.scale = scale ? [1.2, 1.2, 1.2] : [1.0, 1.0, 1.0]
            }
        }
    }
}
```

更多关于如何使用 RealityKit 创建 3D 内容的，请参照 [RealityKit](https://developer.apple.com/documentation/RealityKit) 。

### 如何响应与 RealityKit 内容的交互

处理与 RealityKit 场景里实体的交互：

- 创建手势识别器 TapGesture ，然后通过 [targetedToAnyEntity()](https://developer.apple.com/documentation/SwiftUI/Gesture/targetedToAnyEntity()) 修改手势识别器参数，并将手势识别器添加到 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 上。

- 在实体或者父实体中添加 [InputTargetComponent](https://developer.apple.com/documentation/RealityKit/InputTargetComponent) 实例。

- 给 RealityKit 实体添加触摸形状，让它支持交互。

 [targetedToAnyEntity()](https://developer.apple.com/documentation/SwiftUI/Gesture/targetedToAnyEntity()) 修改器是链接手势识别器和 RealityKit 内容的桥梁。例如，创建 [DragGesture](https://developer.apple.com/documentation/SwiftUI/DragGesture) 手势，通过手势修饰符设置参数和响应闭包，然后把手势添加到实体上，这样实体就可以响应用户的拖拽。当实体有了手势识别器， SwiftUI 执行提供的响应闭包中的代码。'

下面的示例代码是在前面的例子的基础上给 SphereView 添加手势识别器。同时也在模型中添加 [InputTargetComponent](https://developer.apple.com/documentation/RealityKit/InputTargetComponent) 和相应触摸形状的 [CollisionComponent](https://developer.apple.com/documentation/RealityKit/CollisionComponent) ，这样实体便能够进行交互。如果不添加这些组件，视图（ view ）不会把交互事件分发到相应的实体上。

```swift
struct SphereView: View {
    @State private var scale = false


    var body: some View {
        RealityView { content in
            let model = ModelEntity(
                mesh: .generateSphere(radius: 0.1),
                materials: [SimpleMaterial(color: .white, isMetallic: true)])


            // Enable interactions on the entity.
            model.components.set(InputTargetComponent())
            model.components.set(CollisionComponent(shapes: [.generateSphere(radius: 0.1)]))
            content.add(model)
        } update: { content in
            if let model = content.entities.first {
                model.transform.scale = scale ? [1.2, 1.2, 1.2] : [1.0, 1.0, 1.0]
            }
        }
        .gesture(TapGesture().targetedToAnyEntity().onEnded { _ in
            scale.toggle()
        })
    }
}
```

### 在三维立体窗口( volume )中展示 3D 内容

三维立体窗口( volume )是一种有三个维度窗口(即 volumetric window)，它符合显示 3D 内容需求。二维平面窗口（ window ）和三维立体窗口( volume )均可容纳 2D 和 3D 内容，而且它们有许多相似之处。然而，二维平面窗口（ window ）会把超出显示区域太多的 3D 内容剪切掉，所以，对于以显示 3D 为主的内容，三维立体窗口( volume )是更好的选择。

在App中添加 [WindowGroup](https://developer.apple.com/documentation/SwiftUI/WindowGroup) Scene，并将 windowStyle 设置为 [volumetric](https://developer.apple.com/documentation/SwiftUI/WindowStyle/volumetric) ，这样就创建了一个三维立体窗口( volume )。这种 windowStyle 指定 SwiftUI 创建一个显示 3D 内容的 window 。三维立体窗口( volume )既可以包含 2D 视图，也可以包含 3D 视图。所以，你可以使用 RealityKit 创建 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 实例，来构造 App 内容。下面的例子代码，使用应用包( App Bundle )中的 balloons 静态 3D 模型为内容创建一个三维立体窗口( volume )：

```swift
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            Model3D("balloons")
        }.windowStyle(style: .volumetric)
    }
}
```

使用二维平面窗口（ window ）和三维立体窗口( volume )展示有限的 2D 和 3D 内容是一种很便捷的方式，但是同时，你就不能在控制你要显示的内容在用户周围环境中的位置。系统会在要显示二维平面窗口（ window ）和三维立体窗口( volume )的时候设置其初始位置。系统也会为其添加一个工具栏( window bar )，以便用户移动或者缩放窗口。

![Window Example](https://docs-assets.developer.apple.com/published/4e51011416061f44436a165e3e3d400b/window-examples@2x.png)

了解更多关于“什么情况下使用三维立体窗口( volume )”，参照 [Human Interface Guidelines > Windows](https://developer.apple.com/design/human-interface-guidelines/windows#visionOS)。

### 在用户周围环境中的展示 3D 内容

当你需要定义 App 内容所要展示的位置，那么就使用 [ImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) 展示相应内容。沉浸式空间( immersive space )提供无限区域来展示你的 3D 内容，并且在这个空间里你可以控制 3D 内容的大小和位置。在获得用户许可后，你还可以将 ARKit 与沉浸式空间( immersive space )结合使用，将内容整合到用户周围的环境中。例如，你可以使用 ARKit 场景重建来获取家具和附近物体的三维网格结构，并让你的内容与该三维网格结构进行相互融合。

 [ImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) 是一种特定类型的 Scene ，可以和 App 中其他的 Scene 一起创建。下面的例子展示了包含了沉浸式空间( immersive space )和普通窗口（ window ）的 App 的代码：

```swift
@main
struct MyImmersiveApp: App {
    var body: some Scene {
        WindowGroup() {
            ContentView()
        }


        ImmersiveSpace(id: "solarSystem") {
            SolarSystemView()
        }
    }
}
```

如果在创建 [ImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) 时没有制定类型，系统会默认创建一个 [mixed](https://developer.apple.com/documentation/SwiftUI/ImmersionStyle/mixed) 类型的空间。这个类型会将你的内容和贯穿用户周围环境的内容融合在一起展示。其他沉浸类型会不同程度的隐藏周围环境内容。调用 [immersionStyle(selection:in:)](https://developer.apple.com/documentation/SwiftUI/Scene/immersionStyle(selection:in:)) 指定你的空间支持什么沉浸类型。如果指定了多个样式，则可以使用修改器的 selection 参数在样式之间切换。

> 警告
> 
> 请注意一下在沉浸式场景( immersive scenes )中有多少内容使用 [mixed](https://developer.apple.com/documentation/SwiftUI/ImmersionStyle/mixed) 类型。因为如果 3D 内容占满屏幕很大一部分的，即使可以看见部分周围环境，也可能使用户完全无法看到周围环境中的潜在危险。如果想让用户完全沉浸在你的内容中，请使 [full](https://developer.apple.com/documentation/SwiftUI/ImmersionStyle/full) 配置你的空间。如需了解更多信息，请参阅 [Creating fully immersive experiences in your app](https://developer.apple.com/documentation/visionos/creating-fully-immersive-experiences) 。

记得在 [ImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) 中设置实体的位置。 SwiftUI 中，通过相应的修改器 position(x:y:) 、 position(_:CGPoint) 设置视图的位置，对于 RealityKit 实体，则是通过设置 transform 相关的属性，来设置实体的位置。SwiftUI 会以用户所站的位置作为空间原点初始位置，但是原点位置可以随其他事件而改变。例如，当需要同时展示有用户虚拟角色的 SharePlay Activity 和 你的 3D 内容时，系统会平移你的空间原点的位置。如果需要对 SwiftUI 视图和 RealityKit 实体进行相对定位，请使用 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 和 RealityViewContent 的参数和方法来实现所需的坐标转换。


> 译者注：这里将 Spatial Personas 翻译为“用户虚拟角色”，是用户的拟真形象，是苹果的一项新技术。更多关于 SharePlay activity 和 Spatial Personas，可以通过 [Design spatial SharePlay experiences](https://developer.apple.com/videos/play/wwdc2023/10075?time=23) 视频了解。'

在 SwiftUI 环境中，通过 [openImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/openImmersiveSpace) 方法，显示你的 [ImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) Scene。系统会异步执行这个方法，并通过已有的信息初始化你的 Scene 。下列实例代码展示了点击按钮，通过 solarSystem 的唯一标识符打开这个虚拟空间：

```swift
Button("Show Solar System") {
    Task {
        let result = await openImmersiveSpace(id: "solarSystem")
        if case .error = result {
            print("An error occurred")
        }
    }
}
```

当应用程序展示一个虚拟空间—— [ImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) 时，系统为了防止视觉冲突会隐藏其他 App 的内容。当你的虚拟空间展示着的时候，其他应用程序会一直被隐藏着，直到用户退出了你的虚拟空间，其他应用程序才会恢复显示。当你没有退出当前显示的虚拟空间，而去打开另一个虚拟空间，系统就会抛出一个运行时的错误。
