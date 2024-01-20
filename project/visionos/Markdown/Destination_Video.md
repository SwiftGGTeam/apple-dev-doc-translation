# 目的地视频

利用 3D 视频和空间音频提供身临其境的体验。

## 概述

Destination Video 是一款适用于 visionOS、iOS 和 tvOS 的多平台视频播放应用。应用为用户提供了熟悉的媒体浏览体验：在图书馆中浏览内容并可以播放感兴趣的视频。该应用在所有支持的平台上提供了相近的体验，但同时也利用了 visionOS 的特有功能来创建新颖、身临其境的视听体验。

[演示视频](https://docs-assets.developer.apple.com/published/473827229dad2914cdf0f7c616f9da2b/Destination-Video-overview.mp4)

### 在内嵌播放器中播放视频

当您在资源库中选择视频时，“目标视频”会显示一个视图，其中显示有关该项目的其他详细信息。该视图显示用于播放视频的控件，并指定是否将其包含在“待播清单”列表中。在visionOS中，它还会沿其前缘显示视频海报。点击视图的“预览”按钮可显示视频的内联预览。

[演示视频](https://docs-assets.developer.apple.com/published/1b23463c1339306b1921101dc14fd7aa/DV-inline.mp4)

当您将对象 [AVPlayerViewController](https://developer.apple.com/documentation/avkit/avplayerviewcontroller) 的界面显示为另一个视图的子项时，内联控件将显示暂停、跳过和查找等。在应用中显示标准播放控件可提供熟悉的 UI，该 UI 可自动调整其外观以适应每个平台，并且在大多数情况下是推荐的选择。

目标视频使用简单的 UI 作为内联播放器视图：一个用于切换播放状态的按钮。 [AVPlayerViewController](https://developer.apple.com/documentation/avkit/avplayerviewcontroller) 不提供此控件样式，但应用使用它来显示视频内容，而无需控件，方法是将其 [showsPlaybackControls](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/1615824-showsplaybackcontrols) 属性的值设置为 `false`。然后，它会覆盖所需的自定义播放控件。有关如何实现此功能的详细信息，请参阅目标视频 InlinePlayerView 的类型。

> 注意
> [AVPlayerViewController](https://developer.apple.com/documentation/avkit/avplayerviewcontroller) 仅支持在内联嵌入时显示 2D 内容。

### 在全屏播放器中播放视频

visionOS 最令人兴奋的功能之一是它能够播放 3D 视频和空间音频，这为观看体验增加了更深层次的沉浸感。在应用中播放 3D 内容需要 [AVPlayerViewController](https://developer.apple.com/documentation/avkit/avplayerviewcontroller) 显示全屏。当您以这种方式呈现播放器时，系统会自动将其停靠在理想的观看位置，并提供简化的播放控件，使用户专注于内容。

> 注意
> 在 iOS 或 Apple tvOS 中，通常使用 [fullScreenCover(isPresented:onDismiss:content:)](https://developer.apple.com/documentation/SwiftUI/View/fullScreenCover(isPresented:onDismiss:content:)) 修饰符在全屏演示文稿中演示视频。此 API 在 visionOS 中可用;但是，为全屏播放显示播放器的建议方法是将其设置为应用窗口组的根视图。

默认情况下，目标视频会 `ContentView` 显示应用的库。它观察对播放器模型属性的更改，该 `presentation` 属性指示应用是请求内联播放还是全屏播放。当演示状态更改为 `fullWindow` 时，视图将重新绘制 UI，以显示播放器视图来代替库。

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

当有人在详细信息视图上选择“播放视频”按钮时，应用会调用播放器模型的方法，请求 `loadVideo(_: presentation:)` `fullWindow` 演示选项。

```swift
Button {
    /// Load the media item for full-window presentation.
    player.loadVideo(video, presentation: .fullWindow)
} label: {
    Label("Play Video", systemImage: "play.fill")
}
```

播放器模型成功加载视频内容进行播放后，会将其 `presentation` 值更新为 `fullWindow` ，这会导致应用将库替换为 `PlayerView` 。

要在 visionOS 中关闭全屏播放器，用户点击播放器 UI 中的“后退”按钮。为了处理此操作，应用的 [`PlayerViewControllerDelegate`](https://developer.apple.com/documentation/avkit/avplayerviewcontrollerdelegate) 类型定义了一个 `AVPlayerViewControllerDelegate` 处理消除的对象。

```swift
func playerViewController(_ playerViewController: AVPlayerViewController,
                          willEndFullScreenPresentationWithAnimationCoordinator coordinator: UIViewControllerTransitionCoordinator) {
    // Reset the player model's state.
    player.reset()
}
```

当委托收到此调用时，它会从播放器模型中清除媒体，并将演示状态重置回其默认值，这会导致目标视频应用重新显示库视图。

### 配置空间音频体验

媒体播放应用需要对其功能和音频会话进行通用配置。除了执行[配置应用以进行媒体播放](https://developer.apple.com/documentation/avfoundation/media_playback/configuring_your_app_for_media_playback)中概述的步骤外，Destination Video 还采用新的 [`AVAudioSession`](https://developer.apple.com/documentation/avfaudio/avaudiosession) API 来自定义用户的空间音频体验。

应用成功加载视频进行播放后，会为当前演示文稿配置空间音频体验。对于内联播放器视图，它将体验设置为一个小型的、集中的声场，其中音频源自视图的位置。当显示视频全窗口时，它会将体验设置为大型、完全沉浸式的声场。

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

### 呈现身临其境的空间

为 visionOS 构建视频播放应用程序提供了新的机会，可以增强播放器窗口范围之外的观看体验。为了增加更高的沉浸感，该示例提供了一个沉浸式空间，该空间在观看视频时显示人周围的场景。它在应用结构中 `DestinationVideo` 定义沉浸式空间。

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

沉浸式空间呈现了 的实例 `DestinationView` ，它将纹理映射到围绕人显示的球体内部。该应用程序使用沉浸式风格呈现它，它允许某人通过转动设备上的数码表冠来更改他们的 `.progressive` 沉浸感。

[演示视频](https://docs-assets.developer.apple.com/published/6bca50241441a64e7ef14eb97cb9c0d6/DV-immersive-space.mp4)

当用户导航到视频的详细信息视图时，“目标视频”应用会自动显示沉浸式空间，并在返回库时将其关闭。为了监视这些事件，应用会观察其导航路径以确定导航事件发生的时间，以便可以显示或关闭空间。

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

增强应用播放体验的最佳方法之一是使该体验可与他人共享。您可以使用 [`AVFoundation`](https://developer.apple.com/documentation/avfoundation) 和 [Group Activities](https://developer.apple.com/documentation/GroupActivities) 框架来构建[同播共享](https://developer.apple.com/shareplay/)体验，即使人们不能在同一位置，也能将他们聚集在一起。

Destination Video 应用创造了一种体验，人们可以跨设备和平台与他人一起观看视频。它定义了一个名为采用该协议 [`GroupActivity`](https://developer.apple.com/documentation/GroupActivities/GroupActivity) 的组活动 `VideoWatchingActivity` 。当人们激活了 FaceTime 通话并在全屏播放器中播放视频时，通话中的每个人都有资格播放该视频。

该应用程序的 VideoWatchingCoordinator 参与者管理 Destination Video 的同播共享功能。它观察新会话的激活，当一个 `VideoWatchingActivity` 会话启动时，它会 [`GroupSession`](https://developer.apple.com/documentation/GroupActivities/GroupSession) 在播放器对象的 [`AVPlaybackCoordinator`](https://developer.apple.com/documentation/avfoundation/avplaybackcoordinator)。

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

将播放器配置为使用群组会话后，当 App 加载新视频时，他们就有资格在 FaceTime 通话中与他人共享。

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
