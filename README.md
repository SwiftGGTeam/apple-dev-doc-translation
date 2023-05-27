# SwiftUI-Tutorial-Chinese
SwiftUI-Tutorial-Chinese 仓库用于存放 [swiftgg-trans-plugin](https://github.com/SwiftGGTeam/swiftgg-trans-plugin) 插件的翻译文件。我们将 Apple 官方文档的原文，翻译和提示信息进行归拢整理，旨在为广大开发者提供一个高质量的 SwiftUI 中文教程。

翻译文件是以 yaml 文件格式进行存储的，以下是存储规则和存储格式的具体介绍：
1. 翻译内容以单独的网页为基本单位，每个网页对应一个翻译文件，如对于url: `https://developer.apple.com/tutorials/swiftui/creating-and-combining-views`来说，它的翻译文件存储路径为：`project/swiftui/creating-and-combining-views`；
2. 翻译文件的基本结构是一个一维数组，每个数组元素由三部分组成，“内容”，“提示”，“翻译”。
* “内容”：由swiftGG项目组提供，从当前教程中抓取的原文内容，也是社区同学需要帮忙进行翻译的内容；
* “提示”：当我们面对一些涉及内容的问题时，为了方便其他iOS开发者快速应对问题，可以增加非翻译的的额外信息；
* “翻译”：具体的翻译内容，也是社区小伙伴需要帮忙翻译校验的主要部分。

数组结构示例：

```

...
- 内容: Familiarize yourself with the new views. They work together to display the hike
    data loaded into your model.
  提示: ''
  翻译: ''
- 内容: Familiarize yourself with the new views. They work together to display the hike
    data loaded into your model.
  提示: ''
  翻译: 熟悉新视图。它们一起工作，显示加载到您的模型中的徒步旅行数据。
- 内容: SwiftUI includes basic animations with predefined or custom easing, as well
    as spring and fluid animations. You can adjust an animation’s speed, set a delay
    before an animation starts, or specify that an animation repeats.
  提示: ''
  翻译: SwiftUI 包括具有预定义或自定义缓动的基本动画，以及弹簧和流体动画。你可以调整动画的速度，为动画设置开始前的延迟，或指定动画重复次数。
- 内容: Create a new Swift file called Hike.swift in your project’s Model group using
    the menu item File > New > File.
  提示: ''
  翻译: ''
  
...

```

3. 社区小伙伴提交PR且由社区校对工作同学approve后，更新内容会在第二天进行发布，如果需要加急，请联系SwiftGG管理员。


## 贡献力量
如果想做出贡献的话，你可以：
- 参与翻译
- 帮忙校对，挑错别字、病句等等
- 提出修改建议
- 提出术语翻译建议

## 常见问题及解答
### 如何提交翻译？
我们非常欢迎你为本项目做出贡献！如果需要提交翻译，在项目仓库中找到对应的 yaml 文件，将中文翻译，提示信息填写在对应的字段中，然后提交 Pull Request 进行修改。在提交 Pull Request 时，请简要说明所做的修改和原因，以让我们更好地理解你的贡献。

排版格式和流程说明：
- 翻译排版格式要求参考 SwiftGG [排版指南](https://github.com/SwiftGGTeam/translation/blob/master/SwiftGG%20排版指南.md)
- Pull Request 发起方式参考 SwiftGG [Pull Request 说明](https://github.com/SwiftGGTeam/translation/blob/master/%E7%BF%BB%E8%AF%91%E6%B5%81%E7%A8%8B%E6%A6%82%E8%BF%B0%E5%8F%8APR%E8%AF%B4%E6%98%8E.md#%E5%A6%82%E4%BD%95%E5%8F%91%E8%B5%B7-pull-request)

### 如何给翻译内容纠错或修正？
可以在项目仓库中找到对应的 yaml 翻译文件，对翻译进行更正之后，提交 Pull Request 进行修改。若你不熟悉 GitHub 操作，也可以通过 **[GitHub Issues](https://github.com/SwiftGGTeam/swiftui-tutorial-chinese/issues)** 向我们反馈，我们会尽快进行处理。

## 相关链接
- [swiftgg-trans-plugin](https://github.com/SwiftGGTeam/swiftgg-trans-plugin)：本项目的浏览器插件源码仓库。

## 关于我们
SwiftGG 是一个致力于为 Swift 语言和 iOS 开发者提供高质量中文技术文章的社区。我们热衷于分享技术知识和经验，我们希望通过这个插件，以及社区的其他活动和项目，帮助更多的开发者更好地学习和了解 Swift 和 iOS 开发。
如果你对我们的项目感兴趣，或者想要了解更多关于 SwiftGG 的信息，请访问我们的官方网站：[https://swiftgg.com](https://swiftgg.com/)。
你也可以通过以下方式关注我们：
- **[GitHub](https://github.com/SwiftGGTeam)**
- **[微博](https://weibo.com/swiftguide)**

### 开发成员
- [SketchK](https://github.com/SketchK)
- [numbbbbb](https://github.com/numbbbbb)
- [zyterence](https://github.com/zyterence)
- [OneeMe](https://github.com/OneeMe)
- [IanIsMyUsername](https://github.com/IanIsMyUsername)
- [hiETsang](https://github.com/hiETsang)
- [Ryan-BetterMe](https://github.com/Ryan-BetterMe)

## 许可证
和 [苹果官方文档](https://swift.org/documentation/) 协议一致：[Creative Commons Attribution 4.0 International (CC BY 4.0) License](https://creativecommons.org/licenses/by/4.0/)。
