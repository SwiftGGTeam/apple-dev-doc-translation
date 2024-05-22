# 在 visionOS 中剪辑和导出媒体

在应用中显示标准控件，以编辑当前播放媒体的时间线。

## 概述

你可以使用 [`AVPlayerViewController`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller) 在你的 visionOS 应用中显示系统默认的视频播放器界面。除了其主要作用外，`AVPlayerViewController` 还能提供类似于 macOS 中 QuickTime Player 界面的媒体剪辑体验，如下图所示。

[媒体剪辑界面](https://docs-assets.developer.apple.com/published/68c56a39c0/renderedDark2x-1685984498.png)

启用此功能后，用户可以指定显示媒体时间线的某个片段。本文将介绍如何在应用中采用这一功能，并说明如何使用 AVFoundation 导出剪辑后的结果。

### 确定媒体是否支持剪辑

应用通常会提供一个 UI 元素，让播放器视图控制器进入剪辑模式。由于播放器不支持剪辑某些媒体，如 HTTP 实时流媒体或受保护的内容，因此应用会观察 [`canBeginTrimming`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/4131583-canbegintrimming) 属性的状态，并相应地更新用户界面的启用状态。例如，以下代码会观察 `canBeginTrimming` 属性的状态，并更新已发布属性的状态，从而在 UI 中设置相应的启用状态：

```swift
@Published private(set) var isTrimming = true
@Published private(set) var supportsTrimming = true


var controller: AVPlayerViewController? {
    didSet {
        // Reset the internal state variables to false and exit.
        guard let controller else {
            isTrimming = false
            supportsTrimming = false
            return
        }
        // Connect the AVPlayer object to the the view controller.
        controller.player = player
        /// Update the state of `supportsTrimming` based on the value of `canBeginTrimming`.
        controller.publisher(for: \.canBeginTrimming)
            .removeDuplicates()
            .assign(to: &$supportsTrimming)
    }
}
```

### 启用剪辑用户界面

确定播放器视图控制器支持编辑当前媒体的时间线后，调用播放器的 [`beginTrimming(completionHandler:)`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/4131582-begintrimming) 方法启用其剪辑界面。你需要在异步上下文中调用此方法：

```swift
/// Enables the player view controller's media trimming interface.
func startTrimming() async {
    // Exit early if the controller doesn't support trimming.
    guard let controller, controller.canBeginTrimming else { return }
    
    isTrimming = true
    if await controller.beginTrimming() {
        // A user pinched the button to complete the trim operation.
    } else {
        // A user pinched the button to cancel their changes.
    }
    isTrimming = false
}
```

此方法返回一个布尔值，表示用户是按了 "完成 "按钮还是 "取消 "按钮。按下 "完成 "按钮会导致视图控制器更新播放器项目的 [`reversePlaybackEndTime`](https://developer.apple.com/documentation/avfoundation/avplayeritem/1388438-reverseplaybackendtime) 和 [`forwardPlaybackEndTime`](https://developer.apple.com/documentation/avfoundation/avplayeritem/1385622-forwardplaybackendtime) 属性值，使其与剪辑后的选区相匹配。

### 导出剪辑后的媒体选区

使用 [`AVAssetExportSession`](https://developer.apple.com/documentation/avfoundation/avassetexportsession) 是导出剪辑后选区的便捷方法。该对象提供了一种基于预设的简单方法，可对各种格式的媒体进行转码。通过传入播放器项目的素材和导出预设，来创建一个导出会话的实例。此外，还要配置其输出 URL 和文件类型：

```swift
// Export the asset in the highest quality.
let preset = AVAssetExportPresetHighestQuality
// Check the compatibility of the preset to export the video to the output file type.
guard await AVAssetExportSession.compatibility(ofExportPreset: preset,
                                               with: playerItem.asset,
                                               outputFileType: .mp4) else {
    print("The selected preset can't export the video to the output file type.")
    return
}


guard let exportSession = AVAssetExportSession(asset: playerItem.asset,
                                               presetName: preset) else {
    print("Unable to create an export session that supports the asset and preset.")
    return
}
```

要只导出与剪辑选区匹配的素材部分，可根据当前播放器项目的反向和正向播完时间创建一个 [`CMTimeRange`](https://developer.apple.com/documentation/coremedia/cmtimerange):

```swift
// Create a time range that matches the trimmed selection.
let startTime = playerItem.reversePlaybackEndTime
let endTime = playerItem.forwardPlaybackEndTime
exportSession.timeRange = CMTimeRange(start: startTime, end: endTime)
```

最后，开始导出操作，将媒体异步转码到输出 URL：

```swift
// Export the content.
await exportSession.export()
```

---

## 另见

### visionOS 播放器

- [在 visionOS 中采用系统播放器界面](https://developer.apple.com/documentation/avkit/adopting_the_system_player_interface_in_visionos)  
为观看 3D 视频内容提供优化的观看体验。

- [`class AVPlayerViewController`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller)
视图控制器，用于显示播放器中的内容，并提供本地用户界面以控制播放。  

- [`protocol AVPlayerViewControllerDelegate`](https://developer.apple.com/documentation/avkit/avplayerviewcontrollerdelegate)  
该协议定义了响应播放器视图控制器事件的实现方法。