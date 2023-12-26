# 在你的 visionOS 应用中增强对辅助功能的支持（Improving accessibility support in your visionOS app）

更新你的代码以确保所有用户都可以在 visionOS 中访问你的应用内容。

## 概览

visionOS 是一个可以支持不同人群的沉浸式平台。虽然平台的体验融合了引人入胜的视觉内容和手眼追踪技术，但人们仍可以通过其他方式与内容互动。事实上，平台为许多不同人群都提供了支持，包括视力障碍者、行动不便者或是有肢体差异的用户。在辅助技术的帮助下，用户就能全面地与应用中的内容进行互动。

[VoiceOver 样例视频](https://docs-assets.developer.apple.com/published/7a7d5c61aa25072baede1f25ba8a665b/VoiceOver-demo.mp4)

在开发过程中，建议启用 VoiceOver 以及其他辅助特性，来测试你的应用对辅助功能的支持情况。要确保用户能够直观地操作应用界面，并确认所有必要的元素都已展示到位。为这些元素增加描述信息，传达其预期的用途。并且确保你的应用能够适配那些会动态改变的设置，例如在应用运行时 Dynamic Type 的设置发生变化。

<table>
  <tr>
    <td align="center">
      <img src="https://docs-assets.developer.apple.com/published/ab16e10f61f3303fc759040c2e2013ef/dynamic-type-off@2x.png" alt="默认字体大小"/>
      <br />默认字体大小
    </td>
    <td align="center">
      <img src="https://docs-assets.developer.apple.com/published/668b545761521bd2676f1a17753f074a/dynamic-type-on@2x.png" alt="增大字体"/>
      <br />增大字体
    </td>
  </tr>
</table>

如需了解有关支持辅助功能的内容，请参阅[辅助功能](https://developer.apple.com/documentation/Accessibility)。对于设计指导，参见[人机界面指南 > 辅助功能](https://developer.apple.com/design/human-interface-guidelines/accessibility)。

### 给 RealityKit 实体添加辅助功能特性

VoiceOver 以及其他辅助技术依赖于你应用的视图和内容所提供的辅助功能信息。虽然 SwiftUI 和 UIKit 已为标准的系统视图控件提供了默认信息，但是 RealityKit 并不会为你场景中的实体预设任何信息。

要为 RealityKit 实体赋予辅助功能信息，就需要给这个实体添加一个 [AccessibilityComponent](https://developer.apple.com/documentation/RealityKit/AccessibilityComponent) 实例。使用这个组件你可以像设定你应用中其他视图一样地去设置相同的参数值。以下的示例代码展示了如何去创建这个组件并将其添加到实体中：

```swift
var accessibilityComponent = AccessibilityComponent()
accessibilityComponent.isAccessibilityElement = true
accessibilityComponent.traits = [.button]
accessibilityComponent.label = "Sports car"
accessibilityComponent.value = "Parked"
accessibilityComponent.systemActions = [.activate]
myEntity.components[AccessibilityComponent.self] = accessibilityComponent
```

用户能够借助 VoiceOver 触发你的实体中的特定操作。如果你的实体支持改变值的大小，或者能通过非标准点击手势被激活，那么就需要为你的组件中的 [systemActions](https://developer.apple.com/documentation/RealityKit/AccessibilityComponent/systemActions) 属性设置一个值。不过，如果用户使用的是单击手势来和实体进行交互，那就无需设定该属性。

以下的代码示例利用了 [RealityView](https://developer.apple.com/documentation/RealityKit/RealityView) 的 content 属性来判断在视图上的实体何时去触发激活辅助功能的事件。在订阅了视图的这项事件后，代码会绑定一个异步任务去处理接收到的事件。当新的事件产生时，这个任务就会调用 handleCollision 方法以处理该实体的碰撞事件。

```swift
activationSubscription = content.subscribe(to: AccessibilityEvents.Activate.self, 
                            on: nil, componentType: nil) { activation in
    Task {
        try handleCollision(for: activation.entity, gameModel: gameModel)
    }
}
```

### 为直接手势操作模式提供支持

当在 visionOS 中启用了 VoiceOver 时，用户会借助手势来浏览你的应用界面和查看相应元素内容。为了避免你的程序干扰到 VoiceOver 的交互，在此期间，系统不会给你的程序传达手势输入的信号。不过，用户仍旧可以通过一个特别的 VoiceOver 手势来启用直接手势操作模式（Direct Gesture mode），这种模式允许在保持 VoiceOver 正常工作的同时恢复手势输入对程序的影响。

在你的代码中添加支持 VoiceOver 的提示，来传达重要事件的结果。VoiceOver 会随时播放这些提示，尤其是在直接手势操作模式下，这些提示会更具意义。以下的示例代码会在自定义手势与游戏棋子触发交互时产生提示内容：

```swift
AccessibilityNotification.Announcement("Game piece hit").post()
```

### 给涉及物理动作的输入方式提供额外选择

受限的移动能力会影响一个人与你的应用内容交互的能力。在设计应用的输入方式时，尽量避免依赖特定的身体动作或是姿势。举个例子来说，如果你的应用支持自定义手部动作，那么应该为每一个手势提供相应的菜单命令，从而让用户能通过键盘或者辅助设备进行操作。

有些辅助技术让用户只需用他们的双眼就能和应用进行交互。借助这些技术，他们能够在你提供的界面中选取、滚动、长按或拖拽内容。因此，即使你的应用支持其它类型的交互，也请务必确保用户能仅通过这些眼动操作就能全方位使用你的应用。

### 避免固定于头部的内容

一些辅助技术能让用户通过头部的移动来操作或浏览你的应用界面。随着用户头部的移动，这些技术会将内容直接地聚焦在用户的视线前。如果一些显示内容随着用户头部的移动而发生变化，那么这将会干扰这些设备的正常使用。

在构造你的界面时，建议将内容设定在窗口中，或是锚定在与虚拟摄像机无关的位置。如果考虑到你确实要使用固定于头部的内容，那么在用户使用相关辅助技术时，需要提供可替代的解决方案。例如，你可以将原本固定于头部的内容移至锚定位置，使之和用户的头部移动动作无关。

你可以在 SwiftUI 中检查 [accessibilityPrefersHeadAnchorAlternative](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/accessibilityPrefersHeadAnchorAlternative) 这个环境变量，或是调用 [AXPrefersHeadAnchorAlternative](https://developer.apple.com/documentation/Accessibility/AXPrefersHeadAnchorAlternative()) 方法来判断何时去改变你的应用内容的锚定方式。当正在使用的辅助设备与头部锚定内容产生冲突时，这个变量会返回 true。每当这个时候，就应当去调整你应用的内容来兼容新的锚定方式。

### 减弱应用内容的动态效果

对于任何沉浸式设备来说，过于强烈的动态效果可能会让人感到不适，即使对于那些对动效并不敏感的人也是如此。尽量减少对快速移动、弹跳货波状运动、缩放动画、多轴移动、旋转在内的动效运用。特别是当使用设备的人对动效敏感时，需要彻底避免使用这些元素。

系统设置中的减弱动态效果可以提示你什么情况下应当为你应用中的动态效果提供替代方案。你可以在 SwiftUI 中通过 [accessibilityReduceMotion](https://developer.apple.com/documentation/SwiftUI/EnvironmentValues/accessibilityReduceMotion) 环境变量，或是 UIKit 中通过 [isReduceMotionEnabled](https://developer.apple.com/documentation/uikit/uiaccessibility/1615133-isreducemotionenabled) 属性来访问这个设置。当被开启时，尝试为这些动效呈现合适的替代方案，或是完全移除它们。比如，你可以选择展示一张静态海洋的图片来代替播放视频。

欲了解更多内容，详见[人机界面指南 > 动态效果](https://developer.apple.com/design/human-interface-guidelines/motion#visionOS)

### 为音频内容添加字幕

对于患有耳聋或听障的人来说，提供高质量的字幕显得格外重要。虽然字幕对于某些人是必须的，但在特定情况下也适用任何人。比如，在喧闹的环境中观看视频时，字幕就大有用处。需要注意的是，不仅对话和文字需要字幕，音乐和音效也同样需要添加字幕。对于空间音频内容，还应在字幕中标注出声音的来源方向。

[AVKit](https://developer.apple.com/documentation/avkit) 和 [AVFoundation](https://developer.apple.com/documentation/avfoundation) 提供对于显示字幕内容的内置支持。这些框架会根据用户的辅助功能设置，自动调整字幕的字体、大小、颜色和样式。例如，它们在显示文本时会应用当前的 Dynamic Type 设定。

如果你使用的是自定义的视频引擎，你可以通过检查辅助功能设定中的 [isClosedCaptioningEnabled](https://developer.apple.com/documentation/uikit/uiaccessibility/1615112-isclosedcaptioningenabled) 来判断何时展示字幕。想要获取正确的字幕内容显示效果，你应在项目中引入 [Media Accessibility](https://developer.apple.com/documentation/MediaAccessibility)。这个框架能提供最佳的字体、颜色和透明度信息，以便你在带字幕的文本和图像上使用。