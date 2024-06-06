# 配置应用以进行媒体播放

配置应用以开启标准媒体播放行为。

## 概述

在为 iOS、tvOS 和 visionOS 构建媒体播放应用时，你需要额外的配置来启用预期的播放行为。配置音频体验和后台操作有助于确保应用的音频按预期运行。它还能在支持的平台上启用隔空播放和画中画播放等高级功能。

### 配置音频会话

除了基本将控制权留给应用程序的 macOS 之外，Apple 平台提供由操作系统管理的音频体验。这样，当用户在应用之间切换并接收高优先级音频请求（如电话或 FaceTime 通话）时，操作系统就能为用户提供无缝的音频体验。

应用使用 [`AVAudioSession`](https://developer.apple.com/documentation/avfaudio/avaudiosession) 从语义上配置其音频行为，例如，以重放或录音为主要目的音频会话。你将这些管理细节委托给音频会话，从而确保操作系统以最佳方式管理用户的音频体验。

你的应用会自动获得一个系统配置的，具有以下默认行为的音频会话：

- 应用中的音频播放会让其他背景音频静音
- 支持音频播放，但不支持音频录制
- 在 iOS 中将“响铃/静音”开关设置为静音模式时，应用音频静音
- iOS 中锁定设备会让应用音频静音

默认音频会话提供了一些方便的行为，但通常无法满足创建播放应用时所需的体验和功能。要添加所需的行为，请配置应用的音频会话类别。

音频会话类别定义了应用所需的常规音频行为。AVFoundation 定义了几种可以使用的音频会话类别，但与媒体播放应用最相关的是 [`playback`](https://developer.apple.com/documentation/avfaudio/avaudiosession/category/1616509-playback)（播放）类别。该类别意味着媒体播放是应用的核心功能。指定此类别时，在 iOS 系统中，当用户将“响铃/静音”开关设置为静音模式时，系统不会将应用的音频静音。选用此类别意味着，如果使用音频、隔空播放和画中画背景模式，则应用可以播放背景音频，如下一节所述。

使用 `AVAudioSession` 对象配置应用的音频会话。音频会话是一个单例对象，用于设置音频会话 [`category`](https://developer.apple.com/documentation/avfaudio/avaudiosession/1616615-category)（类别）、[`mode`](https://developer.apple.com/documentation/avfaudio/avaudiosession/1616508-mode)（模式）和其他设置。下面代码配置了音频会话以优化电影播放：

```swift
class PlayerModel: ObservableObject {
    
    func configureAudioSession() {
        do {
            let session = AVAudioSession.sharedInstance()
            // Configure the app for playback of long-form movies.
            try session.setCategory(.playback, mode: .moviePlayback)
        } catch {
            // Handle error.
        }
    }
}
```

要启用此类别，请使用 [`setActive(_:options:)`](https://developer.apple.com/documentation/avfaudio/avaudiosession/1616627-setactive) 方法激活音频会话。

> 注意  
> 设置类别后，你可以随时激活音频会话，但建议将此调用推迟到应用开始播放音频时进行。推迟调用可确保不会过早地中断任何其他正在进行的背景音频。

设置类别是与音频会话的最基础交互，但你也可以使用其他选项和功能来配置音频会话。例如，在 visionOS 中，你可以通过配置音频会话来自定义用户的空间音频体验。更多信息，请参阅 [`AVAudioSession`](https://developer.apple.com/documentation/avfaudio/avaudiosession)。

### 配置后台模式

系统要求你启用某些功能来执行某些后台操作。播放应用需要的一种常见功能是播放背景音频。启用此功能后，当用户切换到其他应用或锁定 iOS 设备时，应用的音频仍会继续播放。你的应用也需要这项功能才能在支持的平台上启用隔空播放和画中画播放等高级播放功能。

使用 Xcode 配置此功能：

1. 在 Xcode 中选择应用的目标，然后选择 “Signing & Capabilities（签名和功能）”选项卡。
2. 单击 “+ Capability（功能）”按钮，将 “Background Modes（背景模式）”功能添加到项目中。
3. 在“背景模式”界面中，选择背景模式列表下的音频、隔空播放和画中画选项。

<p align="center">
    <img src="https://docs-assets.developer.apple.com/published/fc552aabf4/renderedDark2x-1687811466.png" width="70%"/>
</p>

启用该模式并配置好音频会话后，应用就可以播放背景音频了。在 iOS 中，启用此选项后，你的应用可以通过隔空播放传输播放内容，而在 iOS 和 tvOS 中，应用可以使用画中画播放。