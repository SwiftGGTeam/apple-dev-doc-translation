# 配置应用图标

添加应用图标变体，以便在设置、搜索结果和 App Store 等位置显示你的应用。

## 概述

每个应用都有一个独特的应用图标，它可以传达应用的用途，并使其在整个系统中易于识别。应用需要多种图标的变体，以便在不同的上下文中看起来更美观。Xcode 可以使用单张高分辨率图片帮助你生成这些变体，你也可以使用项目资源目录中的应用图标的图片集来配置变体。visionOS 和 tvOS 应用图标由你在项目资源目录中配置的多个图像图层堆叠而成。iOS 和 iPadOS 应用图标额外支持深色和着色样式。

有关应用图标的设计指南，请参阅[人机界面指南 > 应用图标](https://developer.apple.com/design/Human-Interface-Guidelines/app-icons)。

### 创建应用图标

从模板创建项目时，项目会自动包含一个包含 `AppIcon` 的默认资源目录 (`Assets.xcassets`)。如果没有默认资源目录或 `AppIcon` 资源，或者你想提供一个备用资源，可以手动将应用图标添加到资源目录中：

1. 在项目导航器中，选择一个资源目录。
2. 单击大纲视图底部的添加按钮 (+)。
3. 在弹出菜单中，选择 *OS variant* > *OS variant* App Icon。Xcode 会创建一个新的名为 `AppIcon`的应用图标集或图像堆栈。

### 指定应用图标变体

应用图标的变体会出现在整个系统中的主视图、设置和搜索结果等位置。在 Xcode 14 及更高版本中，对于 iOS、macOS 或 watchOS 应用，你可以使用一张 1024×1024 像素的图像生成图标的所有变体，也可以手动提供所有所需尺寸的变体。虽然使用单一尺寸是资源目录中新应用或新图标的默认行为，但你可能需要所有尺寸的图标以便在较大的变体中包含细节，并在应用尺寸减小时进行简化。如果你有一个提供多种变体的现有项目，当应用只需要单一尺寸的图标时，请考虑提供单一尺寸。

对于应用支持的每个平台，请在使用单一尺寸和在资源目录中提供所有尺寸之间做出选择：

<img alt="Xcode 中资源目录的截图。在大纲视图中，选择了一个名为 AppIcon 的应用图标集。检查器区域显示了多个图像井，其标签描述了所需的图像尺寸、分辨率和用途。" src="https://docs-assets.developer.apple.com/published/3e28002fa0526cb99895316eb610fc64/configuring-your-app-icon-1~dark@2x.png">

1. 在项目导航器中，选择资源目录。
2. 在资源目录中，选择图标。
3. 要查看和编辑属性，请从 Xcode 的 View 菜单中选择 Inspectors > Attributes。
4. 从要更改的平台的弹出菜单中选择 Single Size 或 All Sizes。

对于应用支持的每个平台，添加一张 Xcode 可以用来生成图标变体的图片，或者为资源目录中图标集的每个图标变体添加一张图片：

<img alt="Xcode 中资源目录的截图。在大纲视图中，选择了一个名称为 AppIcon 的应用图标集。细节区域显示了多个图像井，其标签描述了所需的图像尺寸、分辨率和用途。" src="https://docs-assets.developer.apple.com/published/ddfd69f9868a9ffc8d28d220d0f627a1/configuring-your-app-icon-2~dark@2x.png">

1. 在项目导航器中，选择资源目录。
2. 在资源目录中，选择图标。
3. 从 Finder 中，将应用图标的图像变体拖动到 Xcode 中资源目录细节区域的图像井中，这些图像应与其分辨率和使用情况相匹配。visionOS 和 tvOS 应用图标将多个图像图层组合在一起，营造出深度感。对于 tvOS 应用，资源目录包含一个应用图标和顶层图片文件夹，其中包含不同的应用图标和启动图片集。

### 为 iOS 和 iPadOS 添加深色和着色图标变体

iOS 和 iPadOS 支持三种不同风格的应用图标：浅色（Light）、深色（Dark）和着色（Tinted）。你可以创建自己的变体，以确保每个变体看起来都符合你的要求。

<img alt="Xcode 中资源目录的截图。在大纲视图中，选择了一个名称为 AppIcon 的应用图标集。检查器区域显示了多个图像井，并带有描述任意、深色和着色图标外观的标签。" src="https://docs-assets.developer.apple.com/published/39d725cbd298dd9dee078e5a389ff412/configuring-your-app-icon-5~dark@2x.png">

要在应用中添加这些图标变体，请执行以下操作：

1. 在项目导航器中，选择资源目录。
2. 在资源目录中，选择图标。
3. 从 Xcode 菜单中选择 View > Inspectors > Attributes。
4. 从 iOS 弹出菜单中选择 Single Size。
5. 然后从 Appearance 弹出菜单中选择“任意（Any）”、“深色（Dark）”或“着色（Tinted）”。

从弹出菜单中选择外观后，会出现两个图像井。将深色和着色应用图标拖入相应的图像井中。请为着色应用图标提供灰度图像。为深色应用图标提供透明背景，以便系统提供的背景可以显示出来。

如果你愿意，可以利用系统自动生成的处理方法，该方法适用于所有应用图标。它采用智能设计，以保持设计意图和可读性。这也有助于在整个主屏幕上保持一致的视觉体验。

有关 iOS 和 iPadOS 的设计指南，请参阅[人机界面指南 > 应用图标](https://developer.apple.com/design/human-interface-guidelines/app-icons#iOS-iPadOS)。

### 配置图像堆栈的图层

默认情况下，visionOS 和 tvOS 应用图标由三层构成。这是 visionOS 图标支持的最大图层数，但在构建 tvOS 图标时最多可使用五个图层。要添加图层，请单击添加按钮 (+)，选择 *OS variant* > *OS variant* App Icon Layer。要移除图层，请选择图层并单击移除按钮 (-)。

<img alt="Xcode 中资源目录的截图。在大纲视图中，选择了一个名称为 AppIcon 的应用图标堆栈。细节区域显示了堆栈中每个层的图像井和标签。" src="https://docs-assets.developer.apple.com/published/3794a3383c029a0190957ee9ee601ab2/configuring-your-app-icon-3~dark@2x.png">

通过将图像从 Finder 拖动到 Xcode 中资源目录详细区域的图像井中，将图像添加到各层。有关图层使用的信息，请参阅应用图标 [visionOS](https://developer.apple.com/design/Human-Interface-Guidelines/app-icons) 和 [tvOS](https://developer.apple.com/design/Human-Interface-Guidelines/app-icons)。

> 注意  
> 你可以使用 Parallax Previewer 应用或 Parallax Exporter 插件来创建和预览图层源表示（Layer Source Representation）文件（.lsr 和 .xlsr），并将其导入 Xcode 中的资源目录。如果要将 tvOS 图标导入 Xcode，请将文件保存为 LSR 文件格式；如果要将 visionOS 图标导入 Xcode，请将文件保存为 XLSR 文件格式。请从 [Apple Design Resources](https://developer.apple.com/design/resources) 网站下载这些文件。

### 指定 App Store 图标

如果通过 App Store 发布应用，则必须提供在 App Store 中使用的应用图标图像。在项目导航器中，选择一个资源目录，然后将图标图像添加到应用图标集或图像堆栈中相应的图像井中。App Store 图像井的位置因平台而异。

平台 | App Store 图标位置
----- | -----
iOS | 将图标图像拖到 iOS 1,024pt 图像井中。
iMessage | 对于 iOS 目标，将图标图像拖至 `AppIcon` 集的 iOS 1,024pt 图像井。对于 iMessage 扩展目标，将图标拖动到 `iMessage App Icon` 集中的 Messages App Store 图像井中。
Sticker Pack | 将图标图像拖动到 iOS 1,024pt 图像井和 Messages App Store 图像井。
macOS | 拖动图标图像到 App Store - 2x 图像井。
tvOS | 在 App Icon & Top Shelf Image 文件夹中，将图像拖动到 App Icon - App Store 堆栈图层的图像井。App Store 会根据图像堆栈的图层生成图标。
visionOS | 将图片拖动到 visionOS 应用图标堆栈图层的图像井中。App Store 会根据图片层生成图标。
watchOS | 对于 iOS 目标，将图标图像拖到 iOS 1,024pt 图像井中。对于 WatchKit App 目标，将图标图像拖至 watchOS 图像井。

### 更改默认应用图标集

如果不从模板创建项目，或者想更改默认的应用图标集，请在目标的构建设置中指定使用哪个图标集。

1. 在项目导航器中选择项目，然后在项目编辑器中选择目标。
2. 在 General 窗格的 App Icons 和 Launch Images 部分，从应用图标源弹出菜单中选择应用图标集。

<img alt="选择常规选项卡后的目标设置截图。应用图标和启动图像部分显示了一个名为“应用图标源”的字段，其中列出了要从资源目录中使用的应用图标集名称。" src="https://docs-assets.developer.apple.com/published/e7c5991b91509e3fca0ff3d69d20fb70/configuring-your-app-icon-4~dark@2x.png">

如果不选择“包含所有应用图标资源”选项，Xcode 在构建应用时只会包含你在应用图标源弹出菜单中指定的应用图标集。如果你想在应用的调试和发布版本中使用不同的图标，且不在发布捆绑包中包含调试图标，则可以取消选中此选项。你可以通过修改 Build Settings 选项卡中的 App Icon Set Name 构建设置，为调试和发布配置专用的应用图标。

Xcode 还会包含你在 Alternate App Icon Sets 构建设置下指定的任何其他应用图标集。包括你的应用可以通过 [`setAlternateIconName(_:completionHandler:)`](https://developer.apple.com/documentation/uikit/uiapplication/2806818-setalternateiconname) 选择或在 App Store 产品页面中使用的所有图标集。

有关在 App Store Connect 中配置使用图标测试的信息，请参阅[产品页面优化](https://developer.apple.com/app-store/product-page-optimization)。

---

## 另见

### 应用图标和启动页面

- [配置应用以使用备用应用图标](https://developer.apple.com/documentation/xcode/configuring_your_app_to_use_alternate_app_icons)  
在应用中添加备用应用图标，让用户选择显示哪个图标。
- [指定应用的启动页面](https://developer.apple.com/documentation/xcode/specifying-your-apps-launch-screen)  
通过自定义启动页面，让你的 iOS 应用启动体验更快、反应更灵敏。