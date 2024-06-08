# 监控应用中的播放进度

查看媒体资源的播放情况以更新应用的用户界面状态。

## 概述

媒体播放应用通常需要监控播放进度，以驱动播放器用户界面状态或执行其他操作。监控此状态需要比键值观察更高的时间精度，因此 [`AVPlayer`](https://developer.apple.com/documentation/avfoundation/avplayer) 提供了特定的 API 用于查看播放时间。本文将介绍如何周期性或在播放跨越特定时间界限时查看该状态。

### 周期性查看当前播放时间

查看播放器当前时间的最常用的方法是周期性观察。在驱动播放器用户界面的时间显示状态时，这种观察方式非常有用。

要周期性观察播放器的当前时间，可调用 [`addPeriodicTimeObserver(forInterval:queue:using:)`](https://developer.apple.com/documentation/avfoundation/avplayer/1385829-addperiodictimeobserver) 方法。该方法需要一个 [`CMTime`](https://developer.apple.com/documentation/coremedia/cmtime) 值（代表观察时间的间隔）、一个串行调度队列和一个播放器会在指定的时间间隔调用的回调闭包。下面的示例添加了一个播放器在正常播放时每半秒会调用一次的观察器：

```swift
@Published private(set) var duration: TimeInterval = 0.0
@Published private(set) var currentTime: TimeInterval = 0.0


private let player = AVPlayer()
private var timeObserver: Any?


/// Adds an observer of the player timing.
private func addPeriodicTimeObserver() {
    // Create a 0.5 second interval time.
    let interval = CMTime(value: 1, timescale: 2)
    timeObserver = player.addPeriodicTimeObserver(forInterval: interval,
                                                  queue: .main) { [weak self] time in
        guard let self else { return }
        // Update the published currentTime and duration values.
        currentTime = time.seconds
        duration = player.currentItem?.duration.seconds ?? 0.0
    }
}


/// Removes the time observer from the player.
private func removePeriodicTimeObserver() {
    guard let timeObserver else { return }
    player.removeTimeObserver(timeObserver)
    self.timeObserver = nil
}
```

始终要记得，在调用完播放器的 [`addPeriodicTimeObserver(forInterval:queue:using:)`](https://developer.apple.com/documentation/avfoundation/avplayer/1385829-addperiodictimeobserver) 方法后，在完成对状态的监控后调用对应的 [`removeTimeObserver(_:)`](https://developer.apple.com/documentation/avfoundation/avplayer/1387552-removetimeobserver)。不遵守这一规则会导致未知的行为。

### 观察媒体演示中特定时间的播放情况

另一种观察播放器的方法是当其在播放过程中跨越特定时间界限时进行观察。你可以通过更新播放器用户界面或执行其他操作来响应这些特定时间点的到来。

要让播放器在跨越媒体时间轴上的特定点时通知应用，请调用播放器的 [`addBoundaryTimeObserver(forTimes:queue:using:)`](https://developer.apple.com/documentation/avfoundation/avplayer/1388027-addboundarytimeobserver) 方法。该方法接收一个包装了定义边界时间的 [`CMTime`](https://developer.apple.com/documentation/coremedia/cmtime) 值的 [`NSValue`](https://developer.apple.com/documentation/foundation/nsvalue) 对象数组、一个串行调度队列和一个回调闭包。下面的示例展示了如何为每四分之一的播放内容定义边界时间：

```swift
/// Adds an observer of the player traversing specific times during standard playback.
private func addBoundaryTimeObserver() async throws {
    
    // Asynchronously load the duration of the asset.
    let duration = try await asset.load(.duration)
    
    // Divide the asset's duration into quarters.
    let quarterDuration = CMTimeMultiplyByRatio(duration,
                                                multiplier: 1,
                                                divisor: 4)
    
    var currentTime = CMTime.zero
    var times = [NSValue]()
    
    // Calculate boundary times.
    while currentTime < duration {
        currentTime = currentTime + quarterDuration
        times.append(NSValue(time:currentTime))
    }
    
    timeObserver = player.addBoundaryTimeObserver(forTimes: times,
                                                  queue: .main) { [weak self] in
        // Update the percentage complete in the user interface.
    }
}
```

如果添加了周期时间或边界时间观测器，则需要在完成观测后调用 [`removeTimeObserver(_:)`](https://developer.apple.com/documentation/avfoundation/avplayer/1387552-removetimeobserver) 来移除观测。

---

## 另见

### 展示

- [将 HEVC 视频与 Alpha 结合使用](https://developer.apple.com/documentation/avfoundation/media_playback/using_hevc_video_with_alpha)  
使用 Alpha 通道播放、写入和导出 HEVC 视频，从而为视频处理添加叠加效果。
- [`class AVPlayerLayer`](https://developer.apple.com/documentation/avfoundation/avplayerlayer)  
一个用于显示播放器对象的可视内容的对象。
- [`class AVSynchronizedLayer`](https://developer.apple.com/documentation/avfoundation/avsynchronizedlayer)  
核心动画层，其定时源于播放器项目，因此可以使图层动画与媒体播放同步。