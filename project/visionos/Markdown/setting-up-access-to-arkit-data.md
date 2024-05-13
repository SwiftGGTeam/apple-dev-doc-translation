# 设置对 ARKit 数据的访问

检查你的应用是否可以使用 ARKit 并尊重用户隐私。

## 概述

在 visionOS 中，ARKit 可利用手部跟踪和世界感应等数据实现新型体验。系统会对此类敏感信息的访问权限进行把关。由于人们可以拒绝您的应用程序使用 ARKit 数据的请求或在稍后撤销访问权限，因此您需要提供使用您的应用程序的替代方法，并处理您的应用程序失去数据访问权限的情况。

https://docs-assets.developer.apple.com/published/caa9cb3bdf25ecb38b21c5e8343b93d1/request-data@2x.png
截图显示了手部追踪的系统权限提示。选项有 "不允许 "和 "确定"。特定应用程序的用法说明是跟踪你的手以检测心脏手势。

### 为 ARKit 数据访问添加使用说明

人们需要知道您的应用程序为什么要访问 ARKit 中的数据。将以下键添加到您应用程序的信息属性列表中，以提供面向用户的使用说明，解释您的应用程序如何使用数据：

[`NSHandsTrackingUsageDescription`](https://developer.apple.com/documentation/bundleresources/information_property_list/nshandstrackingusagedescription)  
  如果您的应用程序使用手部跟踪，请使用此键。

[`NSWorldSensingUsageDescription`](https://developer.apple.com/documentation/bundleresources/information_property_list/nsworldsensingusagedescription)  
  如果您的应用程序使用图像跟踪、平面检测或场景重建，请使用此密钥。

> 备注  
> 世界追踪与世界感知不同，不需要授权。更多信息，请参阅[追踪世界空间中的特定点](https://developer.apple.com/documentation/visionos/tracking-points-in-world-space)。

### 选择预先授权还是按需授权

您可以选择某人何时看到使用 ARKit 数据的授权请求。如果您需要精确控制请求出现的时间，可调用 [`ARKitSession`](https://developer.apple.com/documentation/arkit/arkitsession) 上的 [`requestAuthorization(for:)`](https://developer.apple.com/documentation/arkit/arkitsession/4131678-requestauthorization) 方法，在调用该方法时明确授权访问。否则，人们会在调用 [`run(_:)`](https://developer.apple.com/documentation/arkit/arkitsession/4131679-run) 方法时看到授权请求。这是一种隐式授权，因为请求的时间完全取决于您何时启动会话。

### 打开空间并运行会话

为帮助保护用户隐私，只有当您的应用程序显示 "完整空间"（Full Space）且其他应用程序隐藏时，才能使用 ARKit 数据。在调用 [`run(_:)`](https://developer.apple.com/documentation/arkit/arkitsession/4131679-run) 方法之前，请呈现其中一种空间样式。

下面显示了一个使用 ARKit 设置空间的应用程序结构：

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

从应用程序的用户界面调用 [`openImmersiveSpace`](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/openImmersiveSpace) 来创建一个空间，开始运行 ARKit 会话，并启动沉浸式体验。下面显示的是一个简单的视图，其中有一个打开空间的按钮：

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

有人可能不想让您的应用程序访问 ARKit 中的数据，或者他们可能选择稍后在 "设置 "中撤销该访问权限。请从容应对这些情况，并删除或过渡依赖 ARKit 数据的内容。例如，您可能会淡出需要移除的内容，或将内容重新调整到适当的起始位置。如果您的应用程序使用 ARKit 数据在用户周围放置内容，请考虑让用户使用系统提供的界面放置内容。

如果您使用 ARKit 进行用户输入，提供替代方案尤为重要。使用无障碍功能、触控板、键盘或其他输入形式的用户可能需要一种在没有 ARKit 的情况下使用您的应用程序的方法。

---

## 另见

