# 控制播放器的传输行为

播放、暂停和搜索媒体演示。

## 概述

AVFoundation 为播放媒体资源提供了全面支持，包括本地和远程基于文件的媒体，以及使用 HTTP Live Streaming 的流媒体。该框架使用 [`AVAsset`](https://developer.apple.com/documentation/avfoundation/avasset) 类对媒体资源进行建模，而该类提供了一个统一的接口来加载和检查媒体，无论媒体的类型或位置是什么。使用 [`AVPlayer`](https://developer.apple.com/documentation/avfoundation/avplayer) 对象以 [`AVPlayerItem`](https://developer.apple.com/documentation/avfoundation/avplayeritem) 的形式播放媒体资源，该对象对资源的动态状态（如 [`currentTime()`](https://developer.apple.com/documentation/avfoundation/avplayeritem/1387230-currenttime)）进行建模。

了解如何有效地使用 `AVPlayer` 对任何构建自定义播放器用户界面或需要对播放进行编程控制的人来说都至关重要。

### 观察播放准备情况

当你创建一个播放器项目时，它一开始的状态是 [`AVPlayerItem.Status.unknown`](https://developer.apple.com/documentation/avfoundation/avplayeritem/status/unknown)，这意味着系统尚未尝试加载媒体进行播放。只有在将项目与 [`AVPlayer`](https://developer.apple.com/documentation/avfoundation/avplayer) 对象关联后，系统才会开始加载资源的媒体。

如需查询播放器项目何时准备好播放，可观察其 [`status`](https://developer.apple.com/documentation/avfoundation/avplayeritem/1389493-status) 属性的值。在调用播放器的 [`replaceCurrentItem(with:)`](https://developer.apple.com/documentation/avfoundation/avplayer/1390806-replacecurrentitem) 方法之前，请先进行观察，因为将播放器项目与播放器关联是通知系统加载项目媒体的提示：

```swift
func playMedia(at url: URL) {
    let asset = AVAsset(url: url)
    let playerItem = AVPlayerItem(
        asset: asset,
        automaticallyLoadedAssetKeys: [.tracks, .duration, .commonMetadata]
    )
    // Register to observe the status property before associating with player.
    playerItem.publisher(for: \.status)
        .removeDuplicates()
        .receive(on: DispatchQueue.main)
        .sink { [weak self] status in
            guard let self else { return }
            switch status {
            case .readyToPlay:
                // Ready to play. Present playback UI.
            case .failed:
                // A failure while loading media occurred.
            default:
                break
            }
        }
        .store(in: &subscriptions)
    
    // Set the item as the player's current item.
    player.replaceCurrentItem(with: playerItem)
}
```

当播放器项目达到 [`AVPlayerItem.Status.readyToPlay`](https://developer.apple.com/documentation/avfoundation/avplayeritem/status/readytoplay) 状态时，显示或启用播放用户界面。或者，如果出现故障，则在播放器中显示相应的状态。

### 控制播放速率

播放器提供 [`play()`](https://developer.apple.com/documentation/avfoundation/avplayer/1386726-play) 和 [`pause()`](https://developer.apple.com/documentation/avfoundation/avplayer/1387895-pause) 方法作为控制播放速率的主要手段。当播放器项目准备好播放时，调用播放器的 `play()` 方法请求以 [`defaultRate`](https://developer.apple.com/documentation/avfoundation/avplayer/3929373-defaultrate) 开始播放，默认速率的初始值为 `1.0`（自然速率）。默认情况下，播放器会自动等待开始播放，直到有足够的媒体数据可以减少停滞。通过观察播放器的 [`timeControlStatus`](https://developer.apple.com/documentation/avfoundation/avplayer/1643485-timecontrolstatus) 值，可以确定播放器是处于暂停、等待播放还是播放状态：

```swift
@Published var isPlaying = false


private func observePlayingState() {
    player.publisher(for: \.timeControlStatus)
        .receive(on: DispatchQueue.main)
        .map { $0 == .playing }
        .assign(to: &$isPlaying)
}
```

通过观察 [`rateDidChangeNotification`](https://developer.apple.com/documentation/avfoundation/avplayer/3746584-ratedidchangenotification) 类型的通知来检查 [`rate`](https://developer.apple.com/documentation/avfoundation/avplayer/1388846-rate) 属性的变化。观察此通知与键值观察 `rate` 属性类似，但通知提供了有关速率变化原因的附加信息。使用 [`rateDidChangeReasonKey`](https://developer.apple.com/documentation/avfoundation/avplayer/3801515-ratedidchangereasonkey) 常量从通知的 [`userInfo`](https://developer.apple.com/documentation/foundation/notification/1779652-userinfo) 字典中读取原因：


```swift
// Observe changes to the playback rate asynchronously.
private func observeRateChanges() async {
    let name = AVPlayer.rateDidChangeNotification
    for await notification in NotificationCenter.default.notifications(named: name) {
        guard let reason = notification.userInfo?[AVPlayer.rateDidChangeReasonKey] as? AVPlayer.RateDidChangeReason else {
            continue
        }
        switch reason {
        case .appBackgrounded:
            // The app transitioned to the background.
        case .audioSessionInterrupted:
            // The system interrupts the app’s audio session.
        case .setRateCalled:
            // The app set the player’s rate.
        case .setRateFailed:
            // An attempt to change the player’s rate failed.
        default:
            break
        }
    }
}
```

### 在媒体时间线上搜索

可以使用 [`AVPlayer`](https://developer.apple.com/documentation/avfoundation/avplayer) 和 [`AVPlayerItem`](https://developer.apple.com/documentation/avfoundation/avplayeritem) 的方法以多种方式搜索媒体时间线。最常见的方法是使用播放器的 [`seek(to:)`](https://developer.apple.com/documentation/avfoundation/avplayer/1385953-seek) 方法，并将目标值的 [`CMTime`](https://developer.apple.com/documentation/coremedia/cmtime) 传递给它。下面展示了在异步上下文中调用此方法：

```swift
// Handle time update request from user interface.
func seek(to timeInterval: TimeInterval) async {
    // Create a CMTime value for the passed in time interval.
    let time = CMTime(seconds: timeInterval, preferredTimescale: 600)
    await avPlayer.seek(to: time)
}
```

你可以单次调用此方法来查找位置，但也可以连续调用，例如在使用 [`Slider`](https://developer.apple.com/documentation/swiftui/slider) 视图时。

[`seek(to:)`](https://developer.apple.com/documentation/avfoundation/avplayer/1385953-seek) 方法是一种在媒体演示中快速搜索的便捷方法，它为速度做了优化调整（而非精确度）。这意味着播放器搜索到的实际时间可能与你请求的时间略有不同。如果需要实现精确的搜索，可使用 [`seek(to:toleranceBefore:toleranceAfter:)`](https://developer.apple.com/documentation/avfoundation/avplayer/1387741-seek) 方法，该方法允许你指示与目标时间（之前和之后）的可容许偏差量。例如，如果需要提供样本精确的搜索行为，可指定容许偏差值为零：

```swift
// Seek precisely to the specified time.
await avPlayer.seek(to: time, toleranceBefore: .zero, toleranceAfter: .zero)
```

---

## 另见

### 播放控制

- 类 [`AVPlayer`](https://developer.apple.com/documentation/avfoundation/avplayer)  
为控制播放器的传输行为提供接口的对象。
- 类 [`AVPlayerItem`](https://developer.apple.com/documentation/avfoundation/avplayeritem)  
在播放过程中模拟资源的定时和展示状态的对象。
类 [`AVPlayerItemTrack`](https://developer.apple.com/documentation/avfoundation/avplayeritemtrack)  
表示播放过程中资源音轨呈现状态的对象。
类 [`AVQueuePlayer`](https://developer.apple.com/documentation/avfoundation/avqueueplayer)  
用于播放播放器项目序列的对象。
类 [`AVPlayerLooper`](https://developer.apple.com/documentation/avfoundation/avplayerlooper)  
使用队列播放器循环播放媒体内容的对象。