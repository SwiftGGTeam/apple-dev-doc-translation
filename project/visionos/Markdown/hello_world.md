# 你好，世界 （Hello World）

使用窗口、体量和沉浸式空间让用户了解地球。


## 概述

您可以使用 visionOS 场景类型和样式，以有趣且引人入胜的方式传达信息。体量和沉浸式空间等功能可以让你将可交互的虚拟物体放入用户的环境中，或反之将用户置于虚拟环境中。

Hello World 使用了这些工具向用户介绍地球--这个我们称之为家的星球。该应用程序展示了地球的倾斜角如何形成季节，物体如何围绕地球运行，以及地球在太空中的样子。

[Hello World 主界面](https://docs-assets.developer.apple.com/published/efa8e7a0a97cfab20bf0f4c307b9b121/Hello-World-overview.mp4)

该应用程序使用 SwiftUI 来制作其用户界面，其中包含了 2D 和 3D 元素。为了创建、自定义和管理 3D 模型和效果，它还依赖于 RealityKit 框架和 Reality Composer Pro。

### 创建应用的入口

Hello World 使用了 [WindowGroup](https://developer.apple.com/documentation/SwiftUI/WindowGroup) 来构建启动时显示的场景（WorldApp 结构体中出现的第一个场景）：

```swift
WindowGroup("Hello World", id: "modules") {
    Modules()
        .environment(model)
}
.windowStyle(.plain)
```

与其他平台（如 macOS 和 iOS）一样，visionOS 将窗口组显示为用户熟悉的窗口。在 visionOS 中，用户可以在共享空间中调整窗口大小并移动窗口。即使你的应用提供了复杂的 3D 体验，窗口也是应用的一个很好的起点，因为它可以让用户由浅入深地顺利进入体验。同时窗口也是提供说明或放置控件的理想位置。

> 提示
> 此窗口组使用了 [plain](https://developer.apple.com/documentation/SwiftUI/WindowStyle/plain) 窗口样式以保持控件悬浮在毛玻璃背景上的效果。即使没有此行代码 visionOS 也会自动添加毛玻璃背景效果。

### 使用导航栈展示不同的模块

在观看键入文本 Hello World 的简短介绍动画后，定义主场景内容的 Modules 视图会显示多种探索世界的选项。此视图包含了一个导航栈 （[NavigationStack](https://developer.apple.com/documentation/SwiftUI/NavigationStack)），导航栈的根视图是一个目录：

```swift
NavigationStack(path: $model.navigationPath) {
    TableOfContents()
        .navigationDestination(for: Module.self) { module in
            ModuleDetail(module: module)
                .navigationTitle(module.eyebrow)
        }
}
```

visionOS 导航栈与其他平台的导航栈具有相同的行为。当它首次出现时，导航栈会显示其根视图。当用户点击内嵌的导航链接 （[NavigationLink](https://developer.apple.com/documentation/SwiftUI/NavigationLink)） 时，导航栈会绘制一个新视图并在工具栏中显示一个后退按钮。当用户点击后退按钮时，导航堆将恢复上一个视图。

[工具栏上的后退按钮](https://docs-assets.developer.apple.com/published/49e8a76a78f786512b2e230446048f6b/HW-navigation-stack@2x.png)

上面代码中的 [navigationDestination(for:destination:)](https://developer.apple.com/documentation/SwiftUI/View/navigationDestination(for:destination:)) 视图修饰符的尾部闭包，会根据链接初始化时传入的模块，在用户激活链接时显示相应的视图：

```swift
NavigationLink(value: module) { /* The link's label. */ }
```


可选的模块值来源于自定义的 Module 枚举：

```swift
enum Module: String, Identifiable, CaseIterable, Equatable {
    case globe, orbit, solar
    // ...
}
```

### 在新场景中显示可交互的地球仪
当地球仪（globe）模块打开时，主窗口中会显示一些有关地球的知识，旁边装饰着一个解释内容的平面图像。为了帮助用户更好地理解模块内容，该模块包含了一个名为“View Globe”的按钮，该按钮可在新窗口中打开一个三维交互式的地球仪。

[地球仪模块](https://docs-assets.developer.apple.com/published/ed0991feb181a5ed416d5625a64a7858/HW-interactive-globe@2x.png)

为了能够打开多种场景类型，Hello World 在其 [Information Property List](https://developer.apple.com/documentation/bundleresources/information_property_list) 文件中包含了 [UIApplicationSceneManifest](https://developer.apple.com/documentation/bundleresources/information_property_list/uiapplicationscenemanifest) 键。该键的值是一个字典，其中包含了 [UIApplicationSupportsMultipleScenes](https://developer.apple.com/documentation/bundleresources/information_property_list/uiapplicationscenemanifest/uiapplicationsupportsmultiplescenes) 键，且其值为：true

```xml
<key>UIApplicationSceneManifest</key>
<dict>
    <key>UIApplicationSupportsMultipleScenes</key>
    <true/>
    <key>UISceneConfigurations</key>
    <dict/>
</dict>
```

### 为地球仪定义一个三维窗口

在上述键值就位后，应用程序就可以在其 [App](https://developer.apple.com/documentation/SwiftUI/App) 声明中使用第二个 [WindowGroup](https://developer.apple.com/documentation/SwiftUI/WindowGroup) 了。这个新窗口组使用视图 Globe 作为其内容：

```swift
WindowGroup(id: Module.globe.name) {
    Globe()
        .environment(model)
}
.windowStyle(.volumetric)
.defaultSize(width: 0.6, height: 0.6, depth: 0.6, in: .meters)
```


由于 Hello World 使用了三维 （[volumetric](https://developer.apple.com/documentation/SwiftUI/WindowStyle/volumetric)） 窗口样式场景修饰符，因此该窗口组创建了一个可自定义深度的窗口--非常适合在有边界的区域中显示三维模型，其行为就像一个透明的盒子。用户可以像移动任何其他窗口一样在共享空间中移动这个盒子，而里面的内容则保持不变。[defaultSize(width:height:depth:in:)](https://developer.apple.com/documentation/SwiftUI/Scene/defaultSize(width:height:depth:in:)) 修饰符用来指定窗口的（包含深度的）大小（以米为单位）。

(地球仪的运作视频)[https://docs-assets.developer.apple.com/published/8712f59eefa7840850fe229d9eec5e2b/HW-globe-detail.mp4]

该 Globe 视图虽然包含了 3D 内容，但其本身仍然只是一个 SwiftUI 视图。它的 [ZStack](https://developer.apple.com/documentation/SwiftUI/ZStack) 中包含了两个元素：一个绘制地球模型的子视图，另一个子视图负责提供控制面板，用户可以使用它来配置地球仪的外观。

### 打开和关闭地球仪窗口

地球仪模块显示了一个 View Globe 按钮。当用户点击该按钮后，系统会根据当前状态来显示三维窗口或关闭窗口。Hello World 通过创建一个按钮样式的 [Toggle](https://developer.apple.com/documentation/SwiftUI/Toggle)，并将其嵌入到自定义 GlobeToggle 视图中来实现这一功能。

[打开和关闭地球仪的 Toggle](https://docs-assets.developer.apple.com/published/eb12be06271bc341482cef34a0d3fe21/HW-toggle-globe@2x.png)

```swift
struct GlobeToggle: View {
    @Environment(ViewModel.self) private var model
    @Environment(\.openWindow) private var openWindow
    @Environment(\.dismissWindow) private var dismissWindow


    var body: some View {
        @Bindable var model = model


        Toggle(Module.globe.callToAction, isOn: $model.isShowingGlobe)
            .onChange(of: model.isShowingGlobe) { _, isShowing in
                if isShowing {
                    openWindow(id: Module.globe.name)
                } else {
                    dismissWindow(id: Module.globe.name)
                }
            }
            .toggleStyle(.button)
    }
}
```

当用户点击切换按钮时，isShowingGlobe 状态会发生变化， 且 [onChange(of:initial:_:)](https://developer.apple.com/documentation/SwiftUI/View/onChange(of:initial:_:)-4psgg) 修饰符会分别调用 [openWindow](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/openWindow) 或 [dismissWindow](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/dismissWindow) 操作来打开或关闭窗口。视图从环境变量中获取这些操作，并使用对应的窗口标识符来运行操作。


### 显示绕地球运行的物体

在 visionOS 中使用窗口的方式与其他平台相同。但是，visionOS 中的窗口提供了可用于创建 3D 效果的少量深度--比如显示在其他元素前方的元素。Hello World 利用这种深度来在 2D 内容的行间展示小的 3D 模型。


应用的第二个模块，“轨道上的物体”（Objects in Orbit）提供了月球和人造卫星等绕地球飞行的物体的信息。为了让用户了解这些对象的外观，该模块直接在窗口内显示这些对象的 3D 模型。

[窗口内的 3D 模型](https://docs-assets.developer.apple.com/published/9d4c7932de939ee1ea2c7e902f78773b/HW-3D-models@2x.png)

Hello World 在自定义的 ItemView 中使用 [Model3D](https://developer.apple.com/documentation/RealityKit/Model3D) 结构体从资源包里加载这些模型。视图会根据可用空间对模型进行缩放和定位，并视情况调整角度：

```swift
private struct ItemView: View {
    var item: Item
    var orientation: SIMD3<Double> = .zero


    var body: some View {
        Model3D(named: item.name, bundle: worldAssetsBundle) { model in
            model.resizable()
                .scaledToFit()
                .rotation3DEffect(
                    Rotation3D(
                        eulerAngles: .init(angles: orientation, order: .xyz)
                    )
                )
                .frame(depth: modelDepth)
                .offset(z: -modelDepth / 2)
        } placeholder: {
            ProgressView()
                .offset(z: -modelDepth * 0.75)
        }
    }
}
```

该应用为每个模型都创建了上述的 ItemView，并将它们放在一个叠加层中， 且只有当前被选中的模型会显示出来。例如，下述的叠加层显示了一个在 x 轴和 z 轴上有稍微倾斜卫星模型：

```swift
.overlay {
    ItemView(item: .satellite, orientation: [0.15, 0, 0.15])
        .opacity(selection == .satellite ? 1 : 0)
}
```

包含上述模型的 [VStack](https://developer.apple.com/documentation/SwiftUI/VStack) 中还包含了一个 [Picker](https://developer.apple.com/documentation/SwiftUI/Picker)，用来选择查看的模型：

```swift
Picker("Satellite", selection: $selection) {
    ForEach(Item.allCases) { item in
        Text(item.name)
    }
}
.pickerStyle(.segmented)
```

当你向 2D 窗口添加 3D 效果时，请牢记以下几点：

- **不要过度使用**。这种类型的效果虽然增加了趣味性，但当用户从不同方向查看窗口时，这些效果有可能会无意中遮挡重要控件或信息。
- 确保元素不超出可用深度。过大的深度会导致元素被剪切。要考虑到初始放置后可能发生的位置或角度变化。
- 避免模型与玻璃背景相交。再次强调，请考虑初始放置物体后可能发生的移动。

### 在沉浸式空间中展示地球与其卫星的关系

用户可以直观地看到卫星是如何围绕地球运动的，因为应用的轨道模块将地球、月球和一个通信卫星显示在同一个系统里。用户可以将该系统拖动到环境中的任何位置，或使用标准手势调整其大小。用户自己也可以在系统移动以获得不同的视角。

[地球、月球，和卫星](https://docs-assets.developer.apple.com/published/2676d82b6eea571323fc514dfb7dbe52/HW-orbit-module@2x.png)

> 备注
> 想了解如何在 visionOS 中使用手势进行设计，请阅读《用户界面指南》（[Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines)）中的手势（[Gestures](https://developer.apple.com/design/Human-Interface-Guidelines/gestures)）章节。

为创建这一效果，应用在 [mix](https://developer.apple.com/documentation/SwiftUI/ImmersionStyle/mixed) 沉浸风格的 [ImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/ImmersiveSpace) 场景中展示了 Orbit 视图--其中包含了一个模拟整套系统的 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView)：

```swift
ImmersiveSpace(id: Module.orbit.name) {
    Orbit()
        .environment(model)
}
.immersionStyle(selection: $orbitImmersionStyle, in: .mixed)
```

与 visionOS 应用中的所有辅助场景一样，此场景依赖于 [Information Property List file](https://developer.apple.com/documentation/bundleresources/information_property_list) 文件中的 [UIApplicationSupportsMultipleScenes](https://developer.apple.com/documentation/bundleresources/information_property_list/uiapplicationscenemanifest/uiapplicationsupportsmultiplescenes) 键。同时，应用还使用了与地球仪模组中类似的开关按钮来打开和关闭空间：

```swift
struct OrbitToggle: View {
    @Environment(ViewModel.self) private var model
    @Environment(\.openImmersiveSpace) private var openImmersiveSpace
    @Environment(\.dismissImmersiveSpace) private var dismissImmersiveSpace


    var body: some View {
        @Bindable var model = model


        Toggle(Module.orbit.callToAction, isOn: $model.isShowingOrbit)
            .onChange(of: model.isShowingOrbit) { _, isShowing in
                Task {
                    if isShowing {
                        await openImmersiveSpace(id: Module.orbit.name)
                    } else {
                        await dismissImmersiveSpace()
                    }
                }
            }
            .toggleStyle(.button)
    }
}
```

这里与[打开和关闭地球仪窗口](https://developer.apple.com/documentation/visionos/world#Open-and-dismiss-the-globe-volume)一节中的版本有几个主要区别：

- OrbitToggle 使用了环境变量中的 [openImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/openImmersiveSpace) 和 [dismissImmersiveSpace](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/dismissImmersiveSpace) 沉浸式空间操作，而不是窗口操作。
- 这时关闭操作不需要标识符，因为用户一次只能打开一个沉浸式空间，即使切换应用也是如此。
- 打开和关闭空间操作是异步的，因此它们的使用出现在 [Task](https://developer.apple.com/documentation/Swift/Task) 闭包中。


### 使用完全沉浸式从太空观察太阳系

应用的最后一个模块意在帮助用户了解地球在太阳系中的位置。与其他模块一样，这个模块包括了说明、装饰图像、及一个指向另一个场景的按钮--在本模块的场景中，用户可以从太空中观察地球。

当用户点击按钮后，应用将接管整个显示屏，并在各个方位显示星星。地球出现在正前方，月球在右侧，太阳在左侧。主窗口还显示了一个小型控制面板，用户可以用它来退出完全沉浸式体验。

[完全沉浸式中的地球和控制菜单](https://docs-assets.developer.apple.com/published/95819f4dc2608e187d9d35fb4746441c/HW-solar-system@2x.png)

> 提示
> 用户可以按下设备的数码表冠来退出完全沉浸式体验，但通常情况下，在应用程序中提供一个内置机制来控制体验是非常有帮助的。

本模块使用了另一个沉浸式空间场景，但此处使用的是 [full](https://developer.apple.com/documentation/SwiftUI/ImmersionStyle/full) 沉浸风格，它会关闭设备的现实映射功能：

```swift
ImmersiveSpace(id: Module.solar.name) {
    SolarSystem()
        .environment(model)
}
.immersionStyle(selection: $solarImmersionStyle, in: .full)
```

与其他辅助场景相同，此场景也依赖于的 [UIApplicationSupportsMultipleScenes](https://developer.apple.com/documentation/bundleresources/information_property_list/uiapplicationscenemanifest/uiapplicationsupportsmultiplescenes) 键，并由与其他场景中用的开关类似的 SolarSystemToggle 激活：

```swift
struct SolarSystemToggle: View {
    @Environment(ViewModel.self) private var model
    @Environment(\.openImmersiveSpace) private var openImmersiveSpace
    @Environment(\.dismissImmersiveSpace) private var dismissImmersiveSpace


    var body: some View {
        Button {
            Task {
                if model.isShowingSolar {
                    await dismissImmersiveSpace()
                } else {
                    await openImmersiveSpace(id: Module.solar.name)
                }
            }
        } label: {
            if model.isShowingSolar {
                Label(
                    "Exit the Solar System",
                    systemImage: "arrow.down.right.and.arrow.up.left")
            } else {
                Text(Module.solar.callToAction)
            }
        }
    }
}
```

此控件显示在主窗口中，以提供进入完全沉浸式体验的入口，同时又单独显示在控制面板中，作为退出体验的一种方式。由于应用在不同的两个位置将此控件用作按钮，而不是一个位置不变的开关，因此它是由一个根据应用状态而改变行为的 [Button](https://developer.apple.com/documentation/SwiftUI/Button) 构成的，而不是使用了按钮样式的 Toggle。

为了能在太阳系模块的控件中复用主窗口，Hello World 将导航栈和控件放在了一个 [ZStack](https://developer.apple.com/documentation/SwiftUI/ZStack) 中，然后通过设置每个控件的不透明度，确保每次只显示一个视图：

```swift
ZStack {
    SolarSystemControls()
        .opacity(model.isShowingSolar ? 1 : 0)


    NavigationStack(path: $model.navigationPath) {
        // ...
    }
    .opacity(model.isShowingSolar ? 0 : 1)
}
.animation(.default, value: model.isShowingSolar)
```

## 另见

#### 其他应用示例

- [Happy Beam](https://developer.apple.com/documentation/visionos/happybeam)
利用一片空间和 ARKit 创建有趣的游戏。
- [Destination Video](https://developer.apple.com/documentation/visionos/destination-video)
利用 3D 视频和空间音频提供身临其境的体验。
- [Diorama](https://developer.apple.com/documentation/visionos/diorama)
使用 Reality Composer Pro 为您的 visionOS 应用程序设计场景。

#### 相关文章

- [Creating your first visionOS app](https://developer.apple.com/documentation/visionos/creating-your-first-visionos-app)
使用 SwiftUI 构建新的 visionOS App，并添加特定于平台的功能。
- [Adding 3D content to your app](https://developer.apple.com/documentation/visionos/adding-3d-content-to-your-app)
为您的 visionOS 应用程序添加深度和维度，并了解如何将应用程序的内容融入到用户的周围环境中。
- [Creating fully immersive experiences in your app](https://developer.apple.com/documentation/visionos/creating-fully-immersive-experiences)
通过将空间与您使用 RealityKit 或 Metal 创建的内容相结合，构建完全身临其境的体验。
- [Presenting windows and spaces](https://developer.apple.com/documentation/visionos/presenting-windows-and-spaces)
打开和关闭构成应用界面的场景。
- [Positioning and sizing windows](https://developer.apple.com/documentation/visionos/positioning-and-sizing-windows)
影响应用程序显示窗口的初始几何形状。
