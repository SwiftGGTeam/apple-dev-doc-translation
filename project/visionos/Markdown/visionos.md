# visionOS

为 Apple Vision Pro 打造全新的应用程序和游戏世界。

## 概述

visionOS 是支持 Apple Vision Pro 的操作系统。将 visionOS 与熟悉的工具和技术结合使用，可为空间计算打造身临其境的应用和游戏。

![图片](https://docs-assets.developer.apple.com/published/230b02516973a1e9ed5e1ce8f8e62ccc/overview@2x.png)

为 visionOS 开发需要配备 Apple 芯片的 Mac。使用 SwiftUI 创建新的应用程序，可充分利用 visionOS 提供的各种沉浸式体验。如果您已有 iPad 或 iPhone 应用程序，可将 visionOS 目的地添加到应用程序的目标中，以访问标准系统外观，并添加特定平台的功能，从而创建极具吸引力的体验。如果想在此期间持续访问您的内容，可提供在 visionOS 中运行的兼容版本应用程序。

### 将您的应用程序扩展到身临其境的空间

<p style="width: 100%; padding:0px;" >
<img src="https://docs-assets.developer.apple.com/published/e21016c7aa5f8264b5a09e245bbe0c87/shared-spaces~dark@2x.png" align="right" width="50%" height="auto"/>

从熟悉的基于窗口的体验开始，向人们介绍您的内容。然后，添加 visionOS 特有的 [SwiftUI](https://developer.apple.com/documentation/SwiftUI) 场景类型，如体积和空间。这些场景类型可让您融入深度、3D 物体和身临其境的体验。

使用 [RealityKit](https://developer.apple.com/documentation/RealityKit) 和 Reality Composer Pro 构建应用的 3D 内容，并通过 [`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView) 显示。在沉浸式体验中，使用 [ARKit](https://developer.apple.com/documentation/arkit) 将您的内容与人的周围环境整合在一起。

<!-- ![图片](https://docs-assets.developer.apple.com/published/e21016c7aa5f8264b5a09e245bbe0c87/shared-spaces~dark@2x.png) -->
</p>

<br></br>
<br></br>
<br></br>

### 探索新型交互方式

人们可以通过注视某个元素并轻点手指来选择该元素。他们还可以使用特定的手势来捏合、拖动、缩放和旋转对象。[SwiftUI](https://developer.apple.com/documentation/SwiftUI) 提供了对这些标准手势的内置支持，因此您应用程序的大部分输入都可以依靠它们。如果您想超越标准手势，可使用 [ARKit](https://developer.apple.com/documentation/arkit) 创建自定义手势。

![Tap to select](https://docs-assets.developer.apple.com/published/fea3d09e9c20f4348ebe2008e961401a/rotate~dark@2x.png) | ![Pinch to rotate](https://docs-assets.developer.apple.com/published/fea3d09e9c20f4348ebe2008e961401a/rotate~dark@2x.png) | ![Manipulate objects](https://docs-assets.developer.apple.com/published/ec9af3664e5b5cbe0faa426b2bb24b6e/direct-manipulation~dark@2x.png) | ![Create custom gestures](https://docs-assets.developer.apple.com/published/73ef676d9b8b32d4f7bee41370c3abcc/custom~dark@2x.png)
----- | ----- | ----- | -----
轻点选择 | 捏合旋转 | 操纵对象 | 创建自定义手势

### 深入了解特色示例应用程序

通过 Hello World 探索所有 visionOS 应用程序的核心概念。通过 Happy Beam 了解如何使用 ARKit 检测自定义手势。通过 Destination Video 探索流式 2D 和立体媒体。通过 Diorama 和 Swift Splash 学习如何使用 RealityKit 和 Reality Composer Pro 构建 3D 场景。

![你好世界截图](https://docs-assets.developer.apple.com/published/770e1d0451ba3b86de3b05eb0ce728b7/Hello-World-intro@2x.png) | ![目的地视频截图](https://docs-assets.developer.apple.com/published/3f06513f0b1feb455e5cc22930c30e1e/Destination-Video-intro@2x.png)
----- | -----
你好，世界<br/><br/>使用窗口、体量和沉浸式空间让人们了解地球。<br/><br/>[阅读示例代码](https://developer.apple.com/documentation/visionos/world) | 目的地视频<br/><br/>利用 3D 视频和空间音频提供身临其境的体验。<br/><br/>[阅读示例代码](https://developer.apple.com/documentation/visionos/destination-video)
![快乐光束截图](https://docs-assets.developer.apple.com/published/52b497b6d705af322726d7371dee6ddd/Happy-Beam-intro@2x.png) | ![Diorama截图](https://docs-assets.developer.apple.com/published/80d69390976dca0e5d70a493312d475a/Diorama-intro@2x.png)
快乐光束<br/><br/>使用 ARKit 在 Full Space 中创建有趣的游戏。<br/><br/>[阅读示例代码](https://developer.apple.com/documentation/visionos/happybeam) | Diorama<br/><br/>使用 Reality Composer Pro 设计 visionOS 应用程序中的场景。<br/><br/>[阅读示例代码](https://developer.apple.com/documentation/visionos/diorama)
![Swift Splash](https://docs-assets.developer.apple.com/published/c6937bad3cf71eb4b8c0ea21e86c4269/Swift-Splash-intro@2x.png) | 
Swift Splash<br/><br/>使用 RealityKit 在 visionOS 中搭建一个交互式的水上游乐设施。<br/><br/>[阅读示例代码](https://developer.apple.com/documentation/visionos/swift-splash) | 

---

## 主题

### 应用构建

- [创建你的第一个 visionOS 应用](https://developer.apple.com/documentation/visionos/creating-your-first-visionos-app)  
使用 SwiftUI 构建新的 visionOS app，并添加平台特有的功能。
- [为应用添加 3D 内容](https://developer.apple.com/documentation/visionos/adding-3d-content-to-your-app)  
为你的 visionOS 应用添加深度和维度，并了解如何将应用的内容融入用户周围环境中。
- [在你的应用中创造完全沉浸式的体验](https://developer.apple.com/documentation/visionos/creating-fully-immersive-experiences)  
通过将 RealityKit 或 Metal 创建的内容与空间结合，构建完全沉浸式的体验。
- [在 visionOS 中绘制基于图层的清晰内容](https://developer.apple.com/documentation/visionos/drawing-sharp-layer-based-content)  
在 visionOS 中，从自定义的 Core Animation 图层中以多种分辨率提供文本和矢量图像。

### 设计

- [针对 visionOS 进行设计](https://developer.apple.com/design/Human-Interface-Guidelines/designing-for-visionos)  
当人们佩戴 Apple Vision Pro 时，他们会进入一个无限的 3D 空间，在那里他们可以与你的 app 或游戏互动，同时与周围环境保持联系。
- [采用隐私和用户偏好的最佳实践](https://developer.apple.com/documentation/visionos/adopting-best-practices-for-privacy)  
尽量减少对敏感信息的使用，并清楚地说明您使用了哪些信息以及如何使用这些信息。
- [改进 visionOS 应用程序中的辅助功能支持](https://developer.apple.com/documentation/visionos/improving-accessibility-support-in-your-app)  
更新您的代码，以确保每个人都可以在 visionOS 中访问您的应用程序内容。

### SwiftUI

- [你好，世界](https://developer.apple.com/documentation/visionos/world)  
使用窗口、体量和沉浸式空间让人们了解地球。
- [展示窗口和空间](https://developer.apple.com/documentation/visionos/presenting-windows-and-spaces)  
打开和关闭构成应用界面的场景。
- [窗口的定位和大小调整](https://developer.apple.com/documentation/visionos/positioning-and-sizing-windows)  
影响应用显示的窗口的初始几何图形。

### RealityKit and Reality Composer Pro

- [Swift Splash](https://developer.apple.com/documentation/visionos/swift-splash)  
使用 RealityKit 在 visionOS 中搭建一个交互式的水上游乐设施。
- [Diorama](https://developer.apple.com/documentation/visionos/diorama)  
使用 Reality Composer Pro 设计 visionOS 应用程序中的场景。
- [了解 RealityKit 的模块化架构](https://developer.apple.com/documentation/visionos/understanding-the-realitykit-modular-architecture)  
了解如何在 RealityKit 中将一切组合在一起。
- [使用 Reality Composer Pro 设计 RealityKit 内容](https://developer.apple.com/documentation/visionos/designing-realitykit-content-with-reality-composer-pro)  
为你的 visionOS 应用设计 RealityKit 场景。
- [从 Apple Vision Pro 截取屏幕截图和视频以进行 2D 观看](https://developer.apple.com/documentation/visionos/capturing-screenshots-and-video-from-your-apple-vision-pro-for-2d-viewing)  
创建屏幕截图并录制 visionOS 应用及其周围环境的高质量视频，以便预览应用。

### ARKit

