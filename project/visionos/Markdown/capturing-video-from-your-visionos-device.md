# 从 Apple Vision Pro 捕获屏幕截图和视频用于 2D 观看

为 visionOS 应用及其周围环境创建屏幕截图并录制高质量视频，以便进行应用预览。

## 概述

使用 visionOS 应用的截图和视频短片来展示用户界面、突出功能和演示使用方法。通过录制 Apple Vision Pro 中的内容（包括你的应用及其周围环境），帮助人们了解沉浸式体验的预期效果。

系统渲染的内容具有空间效果，并针对沉浸式体验中的观看效果进行了优化。在正常操作过程中提高渲染性能的技术，如降低周边图像质量的*视点渲染*技术，并不能很好地转化为 2D 显示。要制作供用户能在 2D 显示屏上观看的高分辨率内容，系统需要在没有这些效果的情况下进行渲染，并放弃一些优化。使用 Reality Composer Pro 中的 Developer Capture 来通知系统重新配置渲染，并从 Apple Vision Pro 截取屏幕截图或长达 60 秒的高分辨率视频（包括声音）。

> 注意  
> 有关在应用的产品页面中包含截图和预览的指导，请参阅[向 Apple Vision Pro 的 App Store 提交应用](https://developer.apple.com/visionos/submit/)。

### 将 Apple Vision Pro 与 Xcode 配对

在从设备截取屏幕截图和视频之前，请将设备与安装了 Xcode 和 visionOS SDK 的 Mac 配对。有关配对设备的说明，请参阅[在模拟器或设备上运行应用](https://developer.apple.com/documentation/Xcode/running-your-app-in-simulator-or-on-a-device)。

### 准备捕获应用及其周围环境

选择一个光线充足、没有杂物的位置。避免放置可能会分散用户注意力或妨碍应用窗口和三维内容的物体。在场景中包含足够的细节，以提供背景上下文和锚点。避免使用未获取拍摄许可的素材，包括人物、屏幕、品牌产品、徽标、艺术品和其他知识产权。

使用你打算与用户分享的应用版本。使用发布配置构建和安装应用。该配置启用了代码优化以提高运行时性能，并禁止生成调试信息。调试配置通常会禁用代码优化，并可能包含你不打算分享的用户界面。请勿使用它们为你准备共享的预览录制视频。构建方案管理着 Xcode 在构建操作中使用的构建配置，更多信息请参阅[自定义项目的构建方案](https://developer.apple.com/documentation/xcode/customizing-the-build-schemes-for-a-project)。

提前规划你打算捕获的任务，并保持它们简短而集中。启动你的应用，进入你计划开始捕捉的状态。通过退出其他应用和避免后台任务，减少 Apple Vision Pro 上不必要的处理开销。

要从设备上截取屏幕截图或视频，请从 Reality Composer Pro 的截取对话框中选择设备：

1. 启动 Reality Composer Pro。从 Xcode 菜单中选择 Open Developer Tool > Reality Composer Pro。
2. 选择 File > Developer Capture 以弹出 Developer Capture 对话框。
3. 从弹出菜单中选择要捕获的设备。

<img alt="开发者捕捉对话框截图，左侧是相机按钮和视频按钮，分别用于开始和结束截图和视频捕捉会话；右侧是弹出式菜单，用于选择设备；弹出式菜单下方是状态信息，在圆圈中的复选标记旁列出已准备就绪。" src="https://docs-assets.developer.apple.com/published/77715557a7955abba12561d667301721/developer-capture-dialog~dark@2x.png">

如果看到“准备就绪，请等待设备准备就绪”的信息，你可以点击弹出菜单右侧的信息按钮以获取更多信息。

### 捕获屏幕截图

要开始捕捉 Apple Vision Pro 的屏幕截图，请单击对话框中带有静态相机图标的按钮。然后系统就会开始截屏会话：

<img alt="开发者捕捉对话框截图，其中包括左侧的停止按钮，用于结束捕捉会话；倒计时按钮，用于捕捉截图；不可用的弹出式菜单，显示所选设备。在弹出菜单下方，显示了当前捕捉会话剩余时间的倒计时信息：00:46 剩余。" src="https://docs-assets.developer.apple.com/published/bb79f329d4134f368b9f3aa8fe9adbff/developer-capture-time-remaining~dark@2x.png">

要不显示倒计时而立即截取屏幕截图，请按空格键。点击倒计时按钮可在 3 秒倒计时后截取屏幕截图。截屏时，继续保持相关内容居中并在画面内。屏幕截图的宽高比会裁剪出现在体验两侧的内容。

截屏对话框的状态区域会显示系统结束截图会话前的剩余时间。点击 Mac 上的停止按钮以手动结束截图会话。

### 捕获视频

要开始从设备捕获视频，请单击 Developer Capture 对话框中的摄像机按钮。这将开始倒计时。倒计时到 0 时，捕捉会话开始。在捕捉过程中，视频会发生变化，因为系统会重新配置渲染内容以供 2D 查看。在会话过程中，你可能会发现设备的响应速度有所下降，因为它需要更多的处理能力来呈现和捕捉视频。

在设备上录制时，执行你计划好的互动。保持相关内容居中并在画面中。你所拍摄视频的宽高比会影响体验过程中出现在两侧的内容。保持头部稳定，必要时使用缓慢、稳定的动作来转换设备的焦点。在观看 2D 捕捉的视频时，头部的细微动作会被放大，可能会让观众感到不适。

当拍摄时间超过 60 秒时，拍摄会话就会结束。你可以在 Mac 上再次点击录制按钮以提前结束会话。

> 注意  
> 要开始捕捉，设备必须与 Mac 保持稳定连接，并以低功率和低热量水平启动，以保持低于实现一致帧频所需的阈值。捕捉多个会话时，可能需要在每个会话之间等待。

### 查看捕获的视频文件

每个录制会话都会创建一个 QuickTime Movie 文件（`.mov`），并将其保存到 Mac 的桌面上。该文件包括使用 HDTV Rec. 709 色彩空间中的 10 位 HEVC 以 30 FPS 捕获的视频，以及使用 32 位浮点线性 PCM 录制的系统音频。

查看视频，确保它包含你计划的所有内容，并且不包含任何意外元素。确保转场和动画流畅，帧频一致。

使用其他视频编辑工具对视频进行修剪、编辑和后期处理（如防抖动），以创建高质量的预览。

---

## 另见

### RealityKit 和 Reality Composer Pro

- [机器人植物学家](https://developer.apple.com/documentation/visionos/bot-anist)  
构建一个多平台应用，使用窗口、体量和动画来创建机器人植物学家的温室。
- [Swift Splash](https://developer.apple.com/documentation/visionos/swift-splash)  
使用 RealityKit 在 visionOS 中创建一个交互式游乐设施。
- [西洋镜](https://developer.apple.com/documentation/visionos/diorama)  
使用 Reality Composer Pro 为你的 visionOS 应用设计场景。
- [打造沉浸式的媒体观看体验](https://developer.apple.com/documentation/visionos/building-an-immersive-media-viewing-experience)  
使用 RealityKit 和 Reality Composer Pro 为你的应用中的媒体播放添加更深层次的沉浸感。
- [在沉浸式环境中实现视频反射](https://developer.apple.com/documentation/visionos/enabling-video-reflections-in-an-immersive-environment)  
通过在自定义环境中添加视频反射，创建更加沉浸式的体验。
- [了解 RealityKit 的模块化架构](https://developer.apple.com/documentation/visionos/understanding-the-realitykit-modular-architecture)  
了解 RealityKit 中的所有功能是如何组合在一起的。
- [使用 Reality Composer Pro 设计 RealityKit 内容](https://developer.apple.com/documentation/visionos/designing-realitykit-content-with-reality-composer-pro)  
为你的 visionOS 应用设计 RealityKit 场景。