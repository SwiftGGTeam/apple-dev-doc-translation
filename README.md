# SwiftUI-Tutorial-Chinese

[简体中文](./README-CN.md)

The SwiftUI-Tutorial-Chinese repository is used to store translation files for the [swiftgg-trans-plugin](https://github.com/SwiftGGTeam/swiftgg-trans-plugin). We aggregate and sort the original text, translations, and hint information from Apple's official documents, aiming to provide developers with a high-quality Chinese and other languages tutorial.

The translation mapping files are stored in the yaml file format. The following is a specific introduction to the storage rules and storage format:

1. The translation content is based on individual web pages. Each web page corresponds to a translation file. For example, for the tutorial "Creating And Combining Views" whose url is `https://developer.apple.com/tutorials/swiftui/creating-and-combining-views`, its translation file storage path should be `project/swiftui/creating-and-combining-views`;
2. The basic structure of the translation file is a one-dimensional array, each element of which is composed of three parts: "content", "hint", and "translation".
    * "Content": the original content of the relevant documents, in English, provided by the SwiftGG project team;
    * "Hint": additional information that can be added to help developers quickly understand the current content;
    * "Translation": the main content that needs to be translated and proofread, which is the core part of our collaboration with the community.

Data structure example:

```yml
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
```

3. After community members submit PRs and they are approved by peer reviewers, the updated content will be published the next day. If you need an urgent update, please contact the SwiftGG administrators.

## Contributing

If you want to contribute, you can:

- Participate in translation
- Help proofread, spotting typos and grammatical errors, etc.
- Make modification suggestions
- Offer terminological translation advice

## FAQs

### How to submit a translation?

We warmly welcome you to contribute to this project! If you need to submit a translation, find the corresponding yaml file in the project repository, fill in the non-English translation and hint information in the corresponding fields, then submit a Pull Request for modification. When submitting a Pull Request, please briefly explain the changes made and the reasons, so we can better understand your contribution.

Formatting guidelines and process instructions:

- For formatting requirements of translations, please refer to SwiftGG [Typesetting Guidelines](https://github.com/SwiftGGTeam/translation/blob/master/SwiftGG%20排版指南.md).
- For how to initiate a Pull Request, please refer to SwiftGG [Pull Request Instructions](https://github.com/SwiftGGTeam/translation/blob/master/%E7%BF%BB%E8%AF%91%E6%B5%81%E7%A8%8B%E6%A6%82%E8%BF%B0%E5%8F%8APR%E8%AF%B4%E6%98%8E.md#%E5%A6%82%E4%BD%95%E5%8F%91%E8%B5%B7-pull-request)

### How to correct or modify the translated content?

You can find the corresponding yaml translation file in the project repository, correct the translation, and then submit a Pull Request for modification. If you are unfamiliar with GitHub operations, you can also provide feedback via **[GitHub Issues](https://github.com/SwiftGGTeam/swiftui-tutorial-chinese/issues)**, and we will handle it as soon as possible.

## Related Links

- [swiftgg-trans-plugin](https://github.com/SwiftGGTeam/swiftgg-trans-plugin)：This project's browser plugin source code repository.

## About Us

SwiftGG is a community dedicated to providing high-quality Chinese technical articles for Swift language and iOS developers. We are passionate about sharing technical knowledge and experience, and we hope that through this plugin and other community activities and projects, we can help more developers learn and understand Swift and iOS development better. 

If you are interested in our project, or want to know more about SwiftGG, please visit our official website: https://swift.gg. You can also follow us through the following ways:

You can also follow us through the following ways:

- **[GitHub](https://github.com/SwiftGGTeam)**
- **[Weibo](https://weibo.com/swiftguide)**

### Development Team

- [SketchK](https://github.com/SwiftGGTeam/swiftgg-trans-plugin/commits?author=SketchK)
- [zyterence](https://github.com/zyterence)
- [OneeMe](https://github.com/OneeMe)
- [IanIsMyUsername](https://github.com/IanIsMyUsername)
- [hiETsang](https://github.com/hiETsang)
- [Ryan-BetterMe](https://github.com/Ryan-BetterMe)
- [JacobMao](https://github.com/JacobMao)
- [RyanZhu](https://github.com/underthestars-zhy)
- [yongfrank](https://github.com/yongfrank)

## License

Consistent with [Apple's official documentation](https://swift.org/documentation/) agreement: [Creative Commons Attribution 4.0 International (CC BY 4.0) License](https://creativecommons.org/licenses/by/4.0/).
