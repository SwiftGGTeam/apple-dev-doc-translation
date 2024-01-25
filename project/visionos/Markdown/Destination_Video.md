# 目的地视频

利用 3D 视频和空间音频提供身临其境的体验。

## 概述

Destination Video 是一款适用于 visionOS、iOS 和 tvOS 的多平台视频播放应用。应用为用户提供了熟悉的媒体浏览体验：在图书馆中浏览内容并可以播放感兴趣的视频。该应用在所有支持的平台上提供了相近的体验，但同时也利用了 visionOS 的特有功能来创建新颖、身临其境的视听体验。

[演示视频](https://docs-assets.developer.apple.com/published/473827229dad2914cdf0f7c616f9da2b/Destination-Video-overview.mp4)

### 在内联播放器中播放视频

当您在资源库中选择视频时，“目的地视频”会显示一个有关该项目详情的视图。该视图显示播放视频的控件，并允许用户指定是否将其包含在“待播清单”列表中。在visionOS中，它还会靠左显示一张视频海报。点击视图中的“预览”按钮可在行中显示视频的预览。

[演示视频](https://docs-assets.developer.apple.com/published/1b23463c1339306b1921101dc14fd7aa/DV-inline.mp4)

当您将 [`AVPlayerViewController`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller) 的界面作为一个子视图显示时，暂停、跳过和查找等内联控件将显示。在应用中显示标准播放控件可提供可自动调整其外观以适应平台的熟悉 UI，这在大多数情况下是推荐的选择。

目的地视频使用简单的 UI 作为内联播放器视图：一个用于切换播放状态的按钮。 [`AVPlayerViewController`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller) 不提供此控件样式，但本应用仅仅使用它来显示视频内容，而不显示控件，其实现方法是将 [`showsPlaybackControls`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/1615824-showsplaybackcontrols) 属性的值设置为 `false`。然后应用会用所需的自定义播放控件覆盖在上。如想了解此功能的详情，请参阅目的地视频中的 `InlinePlayerView` 类型。

> 注意
> [`AVPlayerViewController`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller) 在内联嵌入时仅支持显示 2D 内容。

### 在全屏播放器中播放视频

visionOS 最令人兴奋的功能之一是它能够播放 3D 视频和空间音频，这为观看体验增加了更深层的沉浸感。在应用中播放 3D 内容需要在全屏模式下显示 [`AVPlayerViewController`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller)。当您以这种方式呈现播放器时，系统会自动将其停靠在理想的观看位置，并提供简化的播放控件，使用户专注于内容。

> 注意
> 在 iOS 或 Apple tvOS 中，你可以使用 [`fullScreenCover(isPresented:onDismiss:content:)`](https://developer.apple.com/documentation/SwiftUI/View/fullScreenCover(isPresented:onDismiss:content:)) 修饰符在全屏模式下演示视频。此 API 在 visionOS 亦可用；然而，推荐的全屏模式播放视频方法是将视图设置为应用窗口组的根视图。

默认情况下，目的地视频的 `ContentView` 会显示应用的资源库。它观察对播放器模型中 `presentation`（演示状态） 属性的更改，属性的变化意味着应用请求内联播放或全屏播放。当演示状态变为 `fullWindow` 时，视图将重新绘制 UI，用播放器视图来代替原本的资源库视图。

```swift
struct ContentView: View {
    
    /// The library's selection path.
    @State private var navigationPath = [Video]()
    /// A Boolean value that indicates whether the app is currently presenting an immersive space.
    @State private var isPresentingSpace = false
    /// The app's player model.
    @Environment(PlayerModel.self) private var player
    
    var body: some View {
        switch player.presentation {
        case .fullWindow:
            // Present the player full window and begin playback.
            PlayerView()
                .onAppear {
                    player.play()
                }
        default:
            // Show the app's content library by default.
            LibraryView(path: $navigationPath, isPresentingSpace: $isPresentingSpace)
        }
    }
}
```

当用户在详细信息视图上选择了“播放视频”按钮时，应用会调用播放器模型的 `loadVideo(_: presentation:)` 方法，请求`fullWindow` 演示选项。

```swift
Button {
    /// Load the media item for full-window presentation.
    player.loadVideo(video, presentation: .fullWindow)
} label: {
    Label("Play Video", systemImage: "play.fill")
}
```

当播放器模型成功加载视频内容并播放后，会将其 `presentation` 值更新为 `fullWindow` ，这会让应用将资源库替换为 `PlayerView` 。

要在 visionOS 中关闭全屏播放器，用户可以点击播放器 UI 中的“后退”按钮。为了处理此操作，应用的 `PlayerViewControllerDelegate` 类型定义了一个 [`AVPlayerViewControllerDelegate`](https://developer.apple.com/documentation/avkit/avplayerviewcontrollerdelegate) 对象，用于关闭界面。

```swift
func playerViewController(_ playerViewController: AVPlayerViewController,
                          willEndFullScreenPresentationWithAnimationCoordinator coordinator: UIViewControllerTransitionCoordinator) {
    // Reset the player model's state.
    player.reset()
}
```

当委托（delegate）收到此调用时，它会从播放器模型中清除媒体，并将演示状态重置回其默认值，这会让目的地视频应用重新显示资源库视图。

### 配置空间音频体验

媒体播放应用需要对其功能和音频会话进行通用配置。除了执行[配置应用以进行媒体播放](https://developer.apple.com/documentation/avfoundation/media_playback/configuring_your_app_for_media_playback)中列举的步骤之外，Destination Video 还采用新的 [`AVAudioSession`](https://developer.apple.com/documentation/avfaudio/avaudiosession) API 来自定义用户的空间音频体验。

应用成功加载视频进行播放后，会为当前的演示内容配置空间音频体验。对于内联播放器视图，它将体验设置为一个小型的、集中的声场，其中音源来自视图的位置。当显示全屏视频时，它会将体验设置为大型、完全沉浸式的声场。

```swift
/// Configures a person's intended Spatial Audio experience to best fit the presentation.
/// - Parameter presentation: the requested player presentation.
private func configureAudioExperience(for presentation: Presentation) {
    #if os(xrOS)
    do {
        let experience: AVAudioSessionSpatialExperience
        switch presentation {
        case .inline:
            // Set a small, focused sound stage when watching trailers.
            experience = .headTracked(soundStageSize: .small, anchoringStrategy: .automatic)
        case .fullWindow:
            // Set a large sound stage size when viewing full window.
            experience = .headTracked(soundStageSize: .large, anchoringStrategy: .automatic)
        }
        try AVAudioSession.sharedInstance().setIntendedSpatialExperience(experience)
    } catch {
        logger.error("Unable to set the intended spatial experience. \(error.localizedDescription)")
    }
    #endif
}
```

### 呈现沉浸式空间

在 visionOS 上制作视频播放应用提供了新的机会，因为开发者可以将观看体验扩展到播放器窗口之外。为了提供更强的沉浸感，该示例提供了一个在用户观看视频时在其周围显示风景的沉浸式空间。应用结构体 `DestinationVideo` 中定义了沉浸式空间。

```swift
struct DestinationVideo: App {
    
    var body: some Scene {
        // The app's primary window.
        WindowGroup {
            ContentView()
        }


        // Defines an immersive space to present a destination in which to watch the video.
        ImmersiveSpace(for: Destination.self) { $destination in
            if let destination {
                DestinationView(destination)
            }
        }
        // Set the immersion style to progressive, so the person can use the Digital Crown to dial in their experience.
        .immersionStyle(selection: .constant(.progressive), in: .progressive)
    }
}
```

沉浸式空间呈现了一个 `DestinationView` 的实例，它将纹理映射到围绕用户而显示的球体内部。应用使用 `.progressive` 风格呈现它，该风格允许用户通过转动设备上的“数码表冠”来修改沉浸度。

[演示视频](https://docs-assets.developer.apple.com/published/6bca50241441a64e7ef14eb97cb9c0d6/DV-immersive-space.mp4)

应用会在用户进入视频详情视图时自动显示沉浸式空间，并在返回资源库时将其关闭。为了监视这些事件，应用会观察其导航路径以确定导航事件发生的时机，并显示或关闭空间。

```swift
.onChange(of: navigationPath) {
    Task {
        // The selection path becomes empty when the person returns to the main library window.
        if navigationPath.isEmpty {
            if isSpaceOpen {
                // Dismiss the space and return the person to their real-world space.
                await dismissSpace()
                isSpaceOpen = false
            }
        } else {
            // The navigationPath has one video, or is empty.
            guard let video = navigationPath.first else { fatalError() }
            // Await the request to open the destination and set the state accordingly.
            switch await openSpace(value: video.destination) {
            case .opened: isSpaceOpen = true
            default: isSpaceOpen = false
            }
        }
    }
}
```

### 提供共享的观看体验

增强应用播放体验的最佳方法之一是使体验可与他人共享。您可以使用 [`AVFoundation`](https://developer.apple.com/documentation/avfoundation) 和 [Group Activities](https://developer.apple.com/documentation/GroupActivities) 框架来构建[同播共享](https://developer.apple.com/shareplay/)体验，即使用户们不在同一个地方，同播体验也能够拉近他们的距离。

目的地视频创造了一种用户可以跨设备和平台与他人一起观看视频的体验。它定义了一个名为 `VideoWatchingActivity` 且遵循 [`GroupActivity`](https://developer.apple.com/documentation/GroupActivities/GroupActivity) 协议的群组活动。当用户们激活了 FaceTime 通话并在全屏播放器中播放视频时，通话中的每个人都有权限播放该视频。

应用的 `VideoWatchingCoordinator` actor 管理目的地视频的同播共享功能。它观察新 `VideoWatchingActivity` 会话的激活，当一个会话启动时，它将 [`GroupSession`](https://developer.apple.com/documentation/GroupActivities/GroupSession) 对象设置为播放器对象的 [`AVPlaybackCoordinator`](https://developer.apple.com/documentation/avfoundation/avplaybackcoordinator)。

```swift
private var groupSession: GroupSession<VideoWatchingActivity>? {
    didSet {
        guard let groupSession else { return }
        // Set the group session on the AVPlayer object's playback coordinator
        // so it can synchronize playback with other devices.
        playbackCoordinator.coordinateWithSession(groupSession)
    }
}
```

将播放器配置使用群组会话后，每当应用加载了新视频，它们都可以在 FaceTime 通话中与他人共享。

## 另见

#### 相关示例

- [你好，世界](https://developer.apple.com/documentation/visionos/world)
使用窗口、体量和沉浸式空间让人们了解地球。
- [快乐光束](https://developer.apple.com/documentation/visionos/happybeam)
使用 ARKit 在 Full Space 中创建有趣的游戏。
- [西洋镜](https://developer.apple.com/documentation/visionos/diorama)
使用 Reality Composer Pro 为您的 visionOS 应用程序设计场景。

#### 相关文章

- [配置应用以进行媒体播放](https://developer.apple.com/documentation/avfoundation/media_playback/configuring_your_app_for_media_playback)
配置应用以启用标准媒体播放行为。
- [在visionOS中采用系统播放器界面](https://developer.apple.com/documentation/avkit/adopting_the_system_player_interface_in_visionos)
为观看 3D 视频内容提供优化的观看体验。
- [监视应用中的播放进度](https://developer.apple.com/documentation/avfoundation/media_playback/monitoring_playback_progress_in_your_app)
观察媒体资产的播放，以更新应用的用户界面状态。
- [在 visionOS 中修剪和导出媒体](https://developer.apple.com/documentation/avkit/trimming_and_exporting_media_in_visionos)
在应用中显示标准控件，以编辑当前播放媒体的时间线。

#### 相关视频

![video-1](https://developer.apple.com/videos/play/wwdc2023/10070) | ![video-2](https://developer.apple.com/videos/play/wwdc2023/10071) | ![video-3](https://developer.apple.com/videos/play/wwdc2023/10087)
----- | ----- | -----
打造出色的空间播放体验 | 提供视频内容以提供空间体验 | 构建空间同播共享体验
