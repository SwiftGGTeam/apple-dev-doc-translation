# 设置对 ARKit 数据的访问

检查你的应用是否可以使用 ARKit 并尊重用户隐私。

## 概述

<p style="width: 100%; padding:0px;" >
<img src="https://docs-assets.developer.apple.com/published/caa9cb3bdf25ecb38b21c5e8343b93d1/request-data@2x.png" align="right" width="50%" height="auto" alt="截图显示了手部追踪的系统权限提示。选项有 '不允许' 和 '确定'。特定应用的用法说明是跟踪你的手以检测心形手势。"/>

在 visionOS 中，ARKit 可利用手部追踪和世界感应等数据实现全新体验。系统会对此类敏感信息的访问权限进行把关。由于用户可以拒绝应用使用 ARKit 数据的请求或稍后撤销访问权限，因此你需要提供使用应用的替代方案，并处理应用失去数据访问权限的情况。

<br></br>
<br></br>
<br></br>

</p>

### 为 ARKit 数据访问添加使用说明

用户需要知道你的应用为什么要访问 ARKit 中的数据。将以下键添加到你应用的信息属性列表中，以提供面向用户的使用说明，以解释应用如何使用数据：

[`NSHandsTrackingUsageDescription`](https://developer.apple.com/documentation/bundleresources/information_property_list/nshandstrackingusagedescription)  
  如果应用使用手部追踪，请使用此键。

[`NSWorldSensingUsageDescription`](https://developer.apple.com/documentation/bundleresources/information_property_list/nsworldsensingusagedescription)  
  如果应用使用图像追踪、平面检测或场景重建，请使用此键。

> 备注  
> 世界追踪与世界感知不同，不需要授权。更多信息，请参阅[追踪世界空间中的特定点](https://developer.apple.com/documentation/visionos/tracking-points-in-world-space)。

### 选择预先授权或按需授权

你可以选择让用户看到 ARKit 数据授权请求的时间点。如果你需要精确控制请求出现的时间，可调用 [`ARKitSession`](https://developer.apple.com/documentation/arkit/arkitsession) 中的 [`requestAuthorization(for:)`](https://developer.apple.com/documentation/arkit/arkitsession/4131678-requestauthorization) 方法，在调用该方法时明确请求授权。否则，用户会在 [`run(_:)`](https://developer.apple.com/documentation/arkit/arkitsession/4131679-run) 方法被调用时看到授权请求。这是一种隐式授权，因为请求的时间完全取决于何时启动会话。

### 打开空间并运行会话

为帮助保护用户隐私，只有当你的应用显示“完整空间”且其他应用隐藏时，才能使用 ARKit 数据。在调用 [`run(_:)`](https://developer.apple.com/documentation/arkit/arkitsession/4131679-run) 方法之前，请选择一种空间样式并展示。

下面示例了一个使用 ARKit 设置空间的应用结构体：

```swift
@main
struct MyApp: App {
    @State var session = ARKitSession()
    @State var immersionState: ImmersionStyle = .mixed
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        ImmersiveSpace(id: "appSpace") {
            MixedImmersionView()
            .task {
                let planeData = PlaneDetectionProvider(alignments: [.horizontal])
                
                if PlaneDetectionProvider.isSupported {
                    do {
                        try await session.run([planeData])
                        for await update in planeData.anchorUpdates {
                            // Update app state.
                        }
                    } catch {
                        print("ARKit session error \(error)")
                    }
                }
            }
        }
        .immersionStyle(selection: $immersionState, in: .mixed)
    }
}
```

从应用的用户界面调用 [`openImmersiveSpace`](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/openImmersiveSpace) 以创建空间，运行 ARKit 会话，并启动沉浸式体验。下面的示例是一个简单的视图，其中有一个能打开空间的按钮：

```swift
struct ContentView: View {
    @Environment(\.openImmersiveSpace) private var openImmersiveSpace
    
    var body: some View {
        Button("Start ARKit experience") {
            Task {
                await openImmersiveSpace(id: "appSpace")
            }
        }
    }
}
```

### 为被拒绝和撤销的授权提供替代方案

用户可能拒绝授权应用访问 ARKit 数据，或者他们可能选择稍后在“设置”中撤销该访问权限。请优雅地对应这些情况，并删除或转换依赖 ARKit 数据的内容。例如，你可以淡出需要移除的内容，或将内容重新调整到适当的起始位置。如果你的应用使用 ARKit 数据在用户周围放置内容，请考虑让用户使用系统提供的界面放置内容。

如果你使用 ARKit 获取用户输入，提供替代方案尤为重要。使用无障碍功能、触控板、键盘或其他输入形式的用户可能需要一种在没有 ARKit 的情况下使用应用的方法。

---

## 另见

### ARKit

- [快乐光束](https://developer.apple.com/documentation/visionos/happybeam)  
使用 ARKit 在 Full Space 中创建有趣的游戏。
- [在沉浸式体验中融入真实世界的环境](https://developer.apple.com/documentation/visionos/incorporating-real-world-surroundings-in-an-immersive-experience)  
让你的应用内容与当地世界的地形相呼应，从而创造身临其境的体验。
- [在检测到的平面上放置内容](https://developer.apple.com/documentation/visionos/placing-content-on-detected-planes)  
检测水平平面（如桌子和地板）以及垂直平面（如墙壁和门）。
- [追踪世界空间中的特定点](https://developer.apple.com/documentation/visionos/tracking-points-in-world-space)  
检索应用在 ARKit 中存储的锚点的位置和方向。
- [在三维空间中追踪预登记图像](https://developer.apple.com/documentation/visionos/tracking-images-in-3d-space)  
根据已知图像的当前位置在用户周围放置内容。