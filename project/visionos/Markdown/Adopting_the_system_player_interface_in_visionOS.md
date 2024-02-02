# 在visionOS中采用系统播放器界面

为观看 3D 视频内容提供优化的观看体验。

## 概述

为您的 visionOS 应用程序提供视频播放界面的推荐方法是采用 [`AVPlayerViewController`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller)。使用此类可以轻松提供与系统应用（如电视和音乐）相同的播放用户界面和功能。它还提供必要的系统集成，无论您是播放标准 2D 内容还是带有空间音频的沉浸式 3D 视频，都能提供最佳的观看体验。本文介绍了在 visionOS 中展示播放器的最佳实践，并介绍了播放器提供的选项，用于自定义其用户界面以最适合你的应用。

> 注意
> 除了提供系统播放界面外，您还可以使用 AVPlayerViewController 来呈现类似于 macOS 中的 QuickTime Player 的媒体修剪体验。有关更多信息，请参阅在 [visionOS 中修剪和导出媒体](https://developer.apple.com/documentation/avkit/trimming_and_exporting_media_in_visionos)。

### 探索演示选项

用于 [`AVPlayerViewController`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller) 在 visionOS 的窗口环境中播放视频。它会自动调整其用户界面以最适合其演示文稿。例如，当您将其嵌套在另一个视图中时，它会显示一个内联用户界面：

[播放界面](https://docs-assets.developer.apple.com/published/b40dac6540/rendered2x-1686261702.png)

> 注意
> 当您以内联方式显示播放器时，它仅显示标准 2D 视频。要播放 3D 内容，请全屏显示。

通过将播放器设置为应用的独占根视图，或使用 [`fullScreenCover(item:onDismiss:content:)`](https://developer.apple.com/documentation/swiftui/view/fullscreencover(item:ondismiss:content:)) 修饰符显示播放器，以全屏模式显示播放器。在全屏模式下，播放器呈现出一种更加内容前卫的设计，默认情况下会使环境变暗，以提供更合适的观看效果。这为 2D 和 3D 内容提供了简化的查看体验。

[全屏模式](https://docs-assets.developer.apple.com/published/c23a818f4b/rendered2x-1686273918.png)

### 显示支持元数据

当当前播放器项包含标题和字幕元数据时，用户界面会在传输栏上方显示标题视图。播放实时流式处理内容时，标题视图还可能显示一个锁屏提醒，以向观看者指示内容状态。

[显示元数据](https://docs-assets.developer.apple.com/published/91be82aff6/rendered2x-1686273923.png)

标题视图显示资产 [`commonIdentifierTitle`](https://developer.apple.com/documentation/avfoundation/avmetadataidentifier/1390317-commonidentifiertitle) 和 [`iTunesMetadataTrackSubTitle`](https://developer.apple.com/documentation/avfoundation/avmetadataidentifier/1388161-itunesmetadatatracksubtitle) 元数据项的值（如果可用）。如果您的媒体不提供嵌入的元数据，则可以通过创建 的实例来添加要显示的 [`AVMetadataItem`](https://developer.apple.com/documentation/avfoundation/avmetadataitem) 补充元数据。下表列出了播放器用户界面支持的元数据值。

元数据 | 标识符 | 类型
----- | ----- | -----
标题 | [`commonIdentifierTitle`](https://developer.apple.com/documentation/avfoundation/avmetadataidentifier/1390317-commonidentifiertitle) | 字符串
字幕 | [`iTunesMetadataTrackSubTitle`](https://developer.apple.com/documentation/avfoundation/avmetadataidentifier/1388161-itunesmetadatatracksubtitle) | 字符串
艺术品 | [`commonIdentifierArtwork`](https://developer.apple.com/documentation/avfoundation/avmetadataidentifier/1386324-commonidentifierartwork) | 数据
描述 | [`commonIdentifierDescription`](https://developer.apple.com/documentation/avfoundation/avmetadataidentifier/1387531-commonidentifierdescription) | 字符串
类型 | [`commonIdentifierDescription`](https://developer.apple.com/documentation/avfoundation/avmetadataidentifier/1386754-quicktimemetadatagenre) | 字符串
内容分级 | [`iTunesMetadataContentRating`](https://developer.apple.com/documentation/avfoundation/avmetadataidentifier/1387949-itunesmetadatacontentrating) | 字符串

在定义简单结构来保存字符串和数据项的应用中，可以将其值映射到其相应的元数据标识符，并生成元数据项数组：

```swift
private func createMetadataItems(for metadata: Metadata) -> [AVMetadataItem] {
    [
        metadataItem(key: .commonIdentifierTitle, value: metadata.title),
        metadataItem(key: .iTunesMetadataTrackSubTitle, value: metadata.subtitle),
        metadataItem(key: .commonIdentifierArtwork, value: metadata.imageData),
        metadataItem(key: .commonIdentifierDescription, value: metadata.description),
        metadataItem(key: .iTunesMetadataContentRating, value: metadata.rating),
        metadataItem(key: .quickTimeMetadataGenre, value: metadata.genre)
    ]
}


private func metadataItem(key identifier: AVMetadataIdentifier,
                          value: Any) -> AVMetadataItem {
    let item = AVMutableMetadataItem()
    item.identifier = identifier
    item.value = value as? NSCopying & NSObjectProtocol
    item.extendedLanguageTag = "und"
    // Return an immutable copy of the item.
    return item.copy() as! AVMetadataItem
}
```

若要将元数据应用于当前播放器项，请将元数据项数组设置为播放器项 [`externalMetadata`](https://developer.apple.com/documentation/avfoundation/avplayeritem/1627623-externalmetadata) 属性的值：

```swift
let metadata: Metadata = // A structure that contains simple string values.
playerItem.externalMetadata = createMetadataItems(for: metadata)
```

标题视图中仅显示标题和副标题值。播放器在其“信息”选项卡中显示其他支持的元数据值，以下部分将对此进行介绍。

### 显示自定义信息视图

visionOS 播放器 UI 可以在用户界面中显示一个或多个内容选项卡，以显示支持信息或相关内容。默认情况下，当资产包含嵌入的元数据或在播放器项目上设置外部元数据时，播放器会显示“信息”选项卡，如上面的“显示支持元数据”部分所述。

您的应用还可以显示自定义选项卡以显示支持内容。您可以将选项卡内容定义为标准 SwiftUI 视图，将它们包装在 [`UIHostingController`](https://developer.apple.com/documentation/swiftui/uihostingcontroller) 中，然后将它们设置为 [`customInfoViewControllers`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/3750340-custominfoviewcontrollers) 属性。播放器 UI 使用托管控制器的 [`title`](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621364-title) 属性在界面中显示为选项卡标题，因此请先设置此值，然后再在播放器视图控制器上设置该值。

```swift
// Set custom content tabs on the player UI.
let title = String(localized: "Up Next")


let view = UpNextView(videos: upNextVideos)
let hostingController = UIHostingController(title: title, rootView: view)


hostingController.preferredContentSize = CGSize(width: 300, height: 100)
controller.customInfoViewControllers = [hostingController]
```

[自定义信息视图](https://docs-assets.developer.apple.com/published/b39e0771cf/rendered2x-1686273927.png)

### 在“信息”选项卡中显示操作

当播放器 UI 显示的资产提供嵌入或外部元数据时，它会显示一个“信息”选项卡。选项卡的视图显示元数据详细信息，并且沿其后缘最多可以显示两个 [`UIAction`](https://developer.apple.com/documentation/uikit/uiaction) 控件：

[截屏](https://docs-assets.developer.apple.com/published/ef9bb34223/rendered2x-1686273910.png)

通过设置播放器视图控制器 [`infoViewActions`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/3852972-infoviewactions) 属性的值来自定义视图显示的操作。播放非实时内容时，此属性包含一个单元素数组，该数组显示从头开始播放内容的操作。您可以替换默认值（如果存在）、添加其他操作，或将此属性值设置为空数组以不显示任何操作。下面的示例演示如何向视图添加“添加到收藏夹”操作：

```swift
let infoCircle = UIImage(systemName: "info.circle")
let showMoreInfo = UIAction(title: "More Information", image: infoCircle) { action in
    // Navigate to a screen to display more information.
}
// Append the action to the array.
playerViewController.infoViewActions.append(showMoreInfo)
```

### 根据上下文显示操作

您可以使用 visionOS 播放器 UI 在上下文中显示控件，您的应用会在内容中显示特定时间范围的控件，然后关闭这些控件。此类控件的常见用途是在电影或电视节目的标题序列中显示的跳过按钮。人们可以点击按钮绕过介绍并快速跳到主要内容。

[截图](https://docs-assets.developer.apple.com/published/f081d9a9d1/rendered2x-1686273914.png)

`AVPlayerViewController` 提供可用于 [`contextualActions`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/3750339-contextualactions) 指定要显示的一个或多个操作的属性。玩家在屏幕的底部显示它们。下面的代码示例演示一个操作的简单实现，该操作将播放器向前搜索到主要内容的时间：

```swift
// Define an action to skip the intro of a media item.
private lazy var skipActions: [UIAction] = {
    [UIAction(title: "Skip Intro") { [weak self] _ in
        guard let self else { return }
        avPlayer.seek(to: skipToTime)
    }]
}()
```

当您为 [`contextualActions`](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/3750339-contextualactions) 属性设置值时，播放器会立即显示控件。要仅在内容的相关部分显示它们，请通过添加周期或边界时间观察器来观察玩家计时。以下示例定义一个周期性时间观察器，该观察器在正常播放期间每秒触发一次。在每次调用中，它都会评估新时间，以确定它是否在表示范围内。如果是这样，则该示例将 skip 操作设置为上下文操作值;否则，它将通过将值设置为空数组来清除该值。

