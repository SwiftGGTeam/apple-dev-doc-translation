# 使用 Reality Composer Pro 设计 RealityKit 内容

为你的 visionOS 应用设计 RealityKit 场景。

## 概述

<p style="width: 100%">
<img width="128" alt="Reality Composer Pro 图标包含一个灰色方框，上面画有黄色线条。黄色光线从方框中发出。" align="right" src="https://docs-assets.developer.apple.com/published/a8fbec81e4ba61e054c36e4db6d3ade1/RCPro-Icon-MacOS@2x.png">

使用 Reality Composer Pro 直观地设计、编辑和预览 RealityKit 内容。在 Reality Composer Pro 中，你可以创建一个或多个场景，作为 RealityKit 内容的容器。场景包含实体的层次结构，这些实体是虚拟对象，例如三维模型。
<br>
<br>
<br>
</p>

除了帮助你创建场景之外，Reality Composer Pro 还允许你向场景中的实体添加和配置组件（甚至是你编写的自定义组件），还能让你使用基于节点的材质编辑器（称为着色器图表（Shader Graph））创建复杂的材质和特效。

### 启动 Reality Composer Pro

当你在 Xcode 中创建 visionOS 项目时，它还会在 Packages 文件夹中包含一个名为 `RealityKitContent` 的默认 Reality Composer Pro 项目，这是一个 Swift 软件包。`RealityKitContent` 包可以包含图片、3D 模型以及如音频和视频等其他资源。你添加到项目中的资源会放在 `RealityKitContent.rkassets` 包中，而你的源代码会放在 Sources 目录中。该软件包还包含一个名为 `Package.realitycomposerpro` 的文件，这是实际的 Reality Composer Pro 项目。

要启动 Reality Composer Pro，请双击“项目导航器（Project navigator）”中的 `Package.realitycomposerpro` 文件，或单击“Open in Reality Composer Pro”按钮。如果你的项目中还没有 Reality Composer Pro 项目，你可以选择 Xcode > Open Developer Tool > Reality Composer Pro 直接启动 它。

为了提高效率，请将所有的 RealityKit 资源储存在 Reality Composer Pro 项目中。当你构建应用时，Xcode 会将 Reality Composer Pro 项目编译为更高效的格式。

> 注意  
> 与加载单个资源文件相比，从 `.reality` 文件加载资源要快得多，也更节省资源。

### 在 Reality Composer Pro 中确定方位

Reality Composer Pro 窗口有几个部分。上半部分显示活动场景。如果有多个场景，窗口顶部会显示一个选项卡栏，每个打开的场景都有一个选项卡。Reality Composer Pro 中的*场景*是存储在 `.usda` 文件中的实体层次结构。

顶部窗格的左侧包含层次浏览器，显示活动场景中实体的树形表示。你可以使用左上角的工具栏按钮切换它，以显示错误和警告。中间窗格是三维视图，显示活动场景的三维呈现。右上方是检查器，根据当前的焦点，会显示三维视图、层次视图或“着色器图表”中选择项目的可配置值。

> 提示  
> 一个 Reality Composer Pro 场景可以代表整个 RealityKit 场景，你可以在 Reality Composer Pro 项目中拥有多个场景，每个场景在同一个应用中驱动不同的 [`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView)。场景还可以包含作为构建模块使用的实体集合。例如，如果你有一个飞机模型，你可以为它创建一个场景，其中包含它的三维模型、使引擎冒烟的粒子效果以及代表飞机发出的各种声音的音频实体或组件。然后，你的应用就可以加载这些组合资源，并在任何需要的位置一起使用这些资源。

Reality Composer Pro 的下半部分包含以下四个选项卡：

项目浏览器（Project Browser）  
    显示项目中的所有资源。

着色器图表（Shader Graph）  
    基于节点的进阶材质编辑器。

音频混合器（Audio Mixer）  
    一款用于组合声音资源的工具。

统计信息（Statistics）  
    有关当前打开场景的信息，如包含的实体、顶点和动画数量。

<img alt="Reality Composer Pro 项目窗口截图。窗口顶部有一个名为 “Biplane”的选项卡。窗口垂直分为两部分。顶部有三个窗格：左侧是三维场景的层次视图，中间是玩具飞机模型，右侧是检查器。底部有四个选项卡，分别标有 “项目浏览器”、“着色器图表”、“音频混合器”和“统计”。当前选择的是“项目浏览器”，它也分为三个部分，左边是层次结构，中间是图标网格，右边是检查器，显示中间所选资源的实体层次结构。" src="https://docs-assets.developer.apple.com/published/e6d31f6e17f93c7fafe3e125f120b289/RCPro-BiplaneWindow~dark@2x.png">

Reality Composer Pro 项目开始时只有一个名为 `Scene` 的空场景，该场景存储在一个名为 `Scene.usda` 的文件中。你可以根据需要，通过选择 File > New > Scene 来创建更多场景。新场景在窗口顶部以选项卡的形式打开，并以 `.usda` 文件的形式出现在项目浏览器中。

如果你关闭了某个场景的选项卡并需要重新打开它，请双击项目浏览器中该场景的 `.usda` 文件。如果不再需要某个场景，请从项目浏览器中删除其 `.usda` 文件，或从 Xcode 中项目的 `.rkassets` 包中删除。

要删除场景：

1. 选择 File > Close Tab 来关闭场景选项卡
2. 在项目浏览器中选择场景的 `.usda` 文件
3. 在项目浏览器中按住 Control 键单击场景的 `.usda` 文件。
4. 在弹出菜单中选择“Delete”。
5. 单击“Move to Trash”。

这将删除场景的 `.usda` 文件和窗口顶部的场景选项卡。

### 向项目中添加资源

在 Reality Composer Pro 中，设计场景时首先要将资源导入到项目中。然后将资源添加到场景中，并移动、旋转和缩放它们。“项目浏览器”选项卡会显示项目中的所有资源文件。你可以将新资源拖到项目浏览器中，或选择“File”>“Import”，然后选择要添加到项目中的资源。要将项目浏览器中的资源添加到当前场景中，可将其拖动到窗口中央的三维视图或窗口左上方的层次视图中。

> 注意  
> Reality Composer Pro 项目可以包含不在任何场景中使用的资源。这些资源仍会编译到你的应用中，并可在运行时加载，充分利用 `.reality` 文件的高效加载过程。

Reality Composer Pro 可以将许多资源展示为实体，但并不能是所有资源；例如：

- 当你将 USDZ 模型添加到场景中时，它们会成为一个实体或实体层次结构。
- 图像文件不会成为实体。Reality Composer Pro 只能间接使用图像资源，例如作为在着色器图表（Shader Graph）中构建材质的源纹理。如果你拖动 Reality Composer Pro 无法将其转化为实体的资源，则不会发生任何操作。

<p style="width: 100%">
<img align="right" width="385" height="auto" alt="显示 Reality Composer Pro 库窗口的截图。" src="https://docs-assets.developer.apple.com/published/254710e01fa59567a0913456bc970333/rcpro-library-cropped~dark@2x.png">

将所需的三维模型、动画、声音和图像文件添加到项目中。你可以将资源组织到子文件夹中，以便随着项目规模的扩大，让项目浏览器更易于管理。

Reality Composer Pro 有一个资源库，你可以在自己的应用中使用。单击工具栏上的添加按钮 (+) 即可访问该库。点击资源旁边圆圈内的向下箭头图标，即可将资源下载到 Reality Composer Pro。下载完成后，你可以双击或拖动资源到你的项目中。

<br>
<br>
<br>
<br>
<br>
<br>
</p>

> 重要事项  
> Reality Composer Pro 将导入的资源视为只读资源。

你对场景中的资源所做的更改只会影响该场景的资源副本。你所做的更改将存储在场景的 `.usda` 文件中，而不是原始资源中。这意味着你可以放心地工作，而不必担心无意中更改其他场景。如果你计划对导入的三维模型进行重要改动，例如用动态着色器图表材料替换其材料，请将模型作为 `.usdc` 文件而不是 `.usdz` 文件导入，然后仅单独导入你需要的资源，以避免 Xcode 将你不需要的资源编译到你的应用中。

### 用资源组成场景

场景中的所有 RealityKit 实体都以特定的位置、方向和比例存在，即便该实体没有可视化表现。当你点击选择三维视图或层次视图中的实体时，Reality Composer Pro 将显示：

- 三维视图中实体上的操纵器。
- 右侧检查器中实体组件的所有可配置值。  
你可以使用操作器移动、旋转和缩放选定的实体。
- 要在三维场景中移动所选实体，可拖动与要移动的轴线相对应的彩色小圆锥体。或者，你也可以拖动实体本身，使其相对于你的视角自由移动。
- 要旋转选中的实体，请单击操作器的旋转控件（看起来像一个圆圈），然后以圆周运动的方式拖动。
    - Reality Composer Pro 的操纵器一次只能显示一个旋转控件。
    - 要在其他轴上旋转实体，请单击与要旋转的轴相对应的圆锥体。例如，如果要在 `X` 轴上旋转实体，请点击红色圆锥体，以显示该轴的红色旋转手柄。
- 要均匀缩放所选实体，请单击旋转圆，然后向远离实体原点的方向拖动以放大实体，或向实体原点的方向拖动以缩小。由于它是均匀缩放的，所以 Reality Composer Pro 显示哪个旋转手柄并不重要。

<div align="center">
    <video width="405" src="https://docs-assets.developer.apple.com/published/a2f7bf8aeb88fefc2f4b3eeeab034fc9/RCPro-Manipulator.mp4"/>
</div>

> 注意  
> 在操作器中，红色表示 X 轴，绿色表示 Y 轴，蓝色表示 Z 轴。

<p style="width: 100%">
<img align="right" width="165" alt="截图显示了检查器中的变换组件。它包含三行，分别称为位置、旋转和缩放，每一行旁边都有三个小文本框。" src="https://docs-assets.developer.apple.com/published/08fc944092621a658ef35a3578adf14d/RCPRo-TransformInspector~dark@2x.png">

或者，你也可以通过在检查器的变换组件中键入新值来对所选实体进行更改。变换组件存储实体的位置、旋转和缩放比例。操作器只是更改该组件值的一种可视化方式。

<br>
<br>
<br>
</p>


### 激活和停用场景实体

Reality Composer Pro 场景可能会变得相当复杂，有时还包含重叠的实体，会很难处理。要简化场景，可以停用实体，将其从三维视图中移除。按住 Control 键单击实体，然后从弹出菜单中选择“Deactivate”，即可停用实体。该实体仍存在于项目中，并显示在层次视图中，但会显示为灰色且没有任何子实体。不过，它不会出现在三维视图中。Xcode 不会将停用的实体编译到应用的捆绑包中，因此在保存项目之前重新激活应用所需的实体非常重要。要重新激活实体，请在层次视图中按住 Control 键并单击实体，然后从弹出菜单中选择“Activate”。

### 为实体添加组件

RealityKit 遵循一种称为实体（Entity）-组件（Component）-系统（System）（ECS）的设计模式。在 ECS 中，你使用组件在实体上存储数据，然后通过编写使用这些组件数据的系统来实现实体行为。你可以在 Reality Composer Pro 中为实体添加和配置组件，包括像粒子发射器组件（[`ParticleEmitterComponent`](https://developer.apple.com/documentation/RealityKit/ParticleEmitterComponent)）这样的内置组件，以及你编写并放置在 Reality Composer Pro Swift 软件包的 Sources 文件夹中的自定义组件。你还可以在 Reality Composer Pro 中创建新组件，并在 Xcode 中对其进行编辑。

有关 ECS 的更多信息，请参阅[了解 RealityKit 的模块化架构](https://developer.apple.com/documentation/visionos/understanding-the-realitykit-modular-architecture)。

<p style="width: 100%">
<img align="right" width="146.5" alt="截图显示了一个弹出窗口，窗口顶部有一个搜索栏，接着是一行标有“新组件”的内容，然后是“锚定”、“角色控制器”、“输入目标”、“模型调试选项”和“粒子发射器”组件。" src="https://docs-assets.developer.apple.com/published/ad9a4f60c3fd7b45f665bd6091a1f81a/RCPro-NewComponent~dark@2x.png">

要为实体添加组件，请在层次视图或三维视图中选择该实体。在检查器窗口的右下角，单击“Add Component”。此时会出现一个可用组件列表，最上方是“New Component”。如果选择第一项，Reality Composer Pro 就会在 Sources 文件夹中创建一个新的组件类，并根据需要创建一个新的系统类。它还会将组件添加到选定的实体中。如果你选择了列表中的任何其他项目，则会将该组件添加到所选实体中（如果该实体中还没有此组件）。
</p>

### 创建或修改实体层次结构

Reality Composer Pro 场景即是 RealityKit 实体的层次结构。你可以更改层次结构浏览器中实体之间的关系，但从 `.usdz` 文件导入的部分层次结构除外，因为 Reality Composer Pro 将其视为只读文件。

要更改实体之间的关系，或在两个当前不相关的实体之间创建关系，请使用层次视图并将一个实体拖到你希望它所属的另一实体上。如果希望某个实体成为根实体，可将其拖至层次视图顶部的根节点中。

### 修改或创建新材料

当你将 USDZ 模型导入 Reality Composer Pro 时，它会为该资源包含的每个基于物理的渲染 (PBR) 材质创建一个 RealityKit 材质。Reality Composer Pro 在层次视图中显示材质，就像显示实体一样，只不过使用的是画笔图标。Reality Composer Pro 不会在三维视图中显示材质。

> 注意  
> Reality Composer Pro 中的资料库包含了现实世界中常见的几种表面材质，如金属、木材和牛仔布，你可以将它们导入到你的项目中。

<p style="width: 100%">
<img align="right" width="215.5" alt="截图显示了检查器中的 PBR 材质编辑器。" src="https://docs-assets.developer.apple.com/published/0f6cb811d88416ba949431da6c7c773c/RCPro-MaterialInspector~dark@2x.png">

在层次视图中选择了一个 PBR 材质后，就可以使用检查器对其进行编辑。你可以用其他图像、颜色或数值替换任何 PBR 属性的图像、颜色或数值。对材质所做的任何更改都会影响到与该材质绑定的所有实体。你还可以通过单击场景层次视图底部的添加按钮 (+) 并选择“Material”，以从头开始创建新材质。
</p>

### 在着色器图表（Shader Graph）中构建材质

PBR（物理基础渲染）材质能很好地再现真实世界的表面。不过，它不能表现卡通着色器等非现实材质，也不能包含逻辑。这意味着你无法为 PBR 材质制作动画，也无法让它对应用的输入做出反应。

Reality Composer Pro 提供的第二种材质称为*自定义材质*。你可以使用“着色器图表”选项卡创建和编辑自定义材质。着色器图表为材质提供了大量的控制功能，让你可以完成原本需要编写 Metal 着色器才能完成的工作。有关编写 Metal 着色器的更多信息，请参阅 [Metal](https://developer.apple.com/documentation/metal)。

> 注意  
> RealityKit 并不像你期望的那样，将 Reality Composer Pro 自定义材质表示为 [`CustomMaterial`](https://developer.apple.com/documentation/RealityKit/CustomMaterial) 的实例。相反，RealityKit 会将这些材质表示为 [`ShaderGraphMaterial`](https://developer.apple.com/documentation/RealityKit/ShaderGraphMaterial) 实例。

<img alt="显示着色器图表选项卡中复杂节点图的截图。" src="https://docs-assets.developer.apple.com/published/395ff773d8c919770662b73321760993/RCPro-ShaderGraphWindow~dark@2x.png">

在编辑器中创建的材质会影响实体的外观和形状。如果创建一个节点图并将其连接到输出节点上的“自定义表面（Custom Surface）”引脚，则该节点图将控制模型的表面外观，大致相当于在片段着色器中编写 Metal 代码。如果建立一个节点图并将其连接到“自定义几何体修改器（Custom Geometry Modifier）”输出引脚，这些节点就会控制实体的形状，这相当于在顶点着色器中运行 Metal 代码。

节点代表数值和操作，其作用与 Metal 中的变量、常量或函数相同。例如，如果需要某个值的正弦值，可将该值的输出节点连接到 `Sin` 节点的输入引脚上。双击着色器图表视图的背景或点击屏幕右侧的“New Node”按钮，即可在图形中添加新节点。

> 重要  
> 有些节点（如 `Sin`）是通用的，可用于任一输出引脚。其他节点则专用于“自定义表面（Custom Surface）”或“几何体修改器（Custom Geometry Modifier）”输出。如果节点名称以“Geometry Modifier”开头，则只能将其连接到几何图形修改器输出引脚。如果节点名称以“Surface”开头，则只能将其连接到自定义表面输出引脚。

要解锁着色器图表的真正威力，需要能够通过 Swift 代码更改材质的值。着色器图表可以通过创建*升级输入*来实现这一功能，这些参数可以从 Swift 中设置和读取，从而能够在运行时更改材质。如果你想打开或关闭某个功能，你可以创建一个布尔值输入参数，并根据其值设置条件逻辑。如果你想在两种颜色之间平滑地修改值，可以创建一个 `Float` 输入参数，并用它来控制如何在两种颜色之间插值。你可以按住 Control 键单击常量节点并选择“升级（Promote）”，将其转化为升级输入。你也可以通过按住 Control 键并选择“降级（Demote）”，将已升级的输入变回常量。

<p style="width:100%">
<img align="right" width="166" alt="显示检查器中“新建输入”按钮的截图。" src="https://docs-assets.developer.apple.com/published/b6b6136cf1b3a14fe5cf420a40f8af4a/RCPro-InputParameter~dark@2x.png">

如果没有现有的可升级常量，可以使用检查器创建新的升级输入。只有在层次视图中选择了材质，但着色器图表选项卡中没有选择节点时，“New Input”按钮才会显示在检查器中。
<br>
<br>
<br>
<br>
<br>
</p>

要在 Swift 代码中更改输入参数的值，可使用 [`setParameter(name:value:)`](https://developer.apple.com/documentation/RealityKit/ShaderGraphMaterial/setParameter(name:value:))，同时传递参数名称和新值。请注意，参数名称区分大小写，因此 `name` 字符串必须与着色器图表中的参数名称完全一致。

有关着色器图表的使用示例，请参阅[西洋镜](https://developer.apple.com/documentation/visionos/diorama)和[快乐光束](https://developer.apple.com/documentation/visionos/happybeam)。

### 使用引用以复用资源

如果你的项目有多个共享资源的场景，你可以使用引用来避免创建重复的资源。*引用*的作用类似于 Finder 中的别名--它指向原始资源，其功能就如同该资源的另一个副本。

<p style="width:100%">
<img align="right" width="240.5" alt="显示检查器引用部分的截图。" src="https://docs-assets.developer.apple.com/published/a5c418ad184c483cc61a5f6edb2bae4e/RCPro-References~dark@2x.png">

使用检查器创建引用。默认情况下，对于没有任何引用的实体和材质，引用部分是隐藏的。要为没有引用的资源或材质添加新的引用，请选择 Reality Composer Pro > Settings，然后取消选中“隐藏空引用（Hide Empty References）”。

<br>
<br>
<br>
<br>
</p>


要添加引用，请单击检查器中引用部分底部的添加按钮 (+)，选择包含资源的场景的 `.usda` 文件，然后选择要链接的资源。之后，所选实体或材质将成为你所链接的实体或材质的副本。

> 重要  
> 如果你对链接的资源进行更改，这些更改将影响每个链接的引用。

### 在设备上预览场景

如果你的 Mac 连接了 Apple Vision Pro，请选择 Preview > Play 或单击 Reality Composer Pro 工具栏中的预览按钮以在设备上查看场景。预览按钮是工具栏右侧最左端的按钮，即带有 Apple Vision Pro 图标的按钮。如果你连接了多个 Apple Vision Pro 设备，可通过单击预览按钮旁边的下拉菜单选择要使用的设备。

### 在 RealityKit 中加载 Reality Composer Pro 场景

加载 Reality Composer Pro 场景与从应用捆绑包中加载 USDZ 资源几乎完全相同，只是你必须指定 Reality Composer Pro 捆绑包。你通常会在 [`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView) 初始化器的 `make` 闭包中执行这一操作。Reality Composer Pro 软件包定义了一个指向其捆绑包的全局常量，该常量以附加了“Bundle”的项目命名。在默认的 Xcode visionOS 模板中，Reality Composer Pro 项目名为 `RealityKitContent`，因此全局捆绑包变量名为 `realityKitContentBundle`：

```swift
RealityView { content in
    if let scene = try? await Entity.load(named: "Biplane", 
                                          in: realityKitContentBundle) {
        myDataModel.add(scene) 
        content.add(scene)
    }
} update: { content in
    // ...
}
```

> 备注  
> 上面的代码保存了对根节点的引用。这并不是必需的，但与 iOS 和 macOS 上的 [`ARView`](https://developer.apple.com/documentation/RealityKit/ARView) 不同，[`RealityView`](https://developer.apple.com/documentation/RealityKit/RealityView) 无法让你随时访问场景内容，因此在应用的数据模型中维护自己对场景根实体的引用通常会很方便。

当 RealityKit 完成场景加载时，`scene` 变量将包含你指定的场景根实体。将其添加到 `content` 中，RealityKit 就会将其显示给用户。

---

## 另见

### RealityKit 和 Reality Composer Pro

- [机器人植物学家](https://developer.apple.com/documentation/visionos/bot-anist)  
构建一个多平台应用，使用窗口、体量和动画来创建机器人植物学家的温室。
- [Swift Splash](https://developer.apple.com/documentation/visionos/swift-splash)  
使用 RealityKit 在 visionOS 中创建一个交互式游乐设施。
- [西洋镜](https://developer.apple.com/documentation/visionos/diorama)  
使用 Reality Composer Pro 为你的 visionOS 应用设计场景。
- [打造身临其境的媒体观看体验](https://developer.apple.com/documentation/visionos/building-an-immersive-media-viewing-experience)  
使用 RealityKit 和 Reality Composer Pro 为你的应用中的媒体播放添加更深层次的沉浸感。
- [在沉浸式环境中实现视频反射](https://developer.apple.com/documentation/visionos/enabling-video-reflections-in-an-immersive-environment)  
通过在自定义环境中添加视频反射，创建更身临其境的体验。
- [了解 RealityKit 的模块化架构](https://developer.apple.com/documentation/visionos/understanding-the-realitykit-modular-architecture)  
了解 RealityKit 中的所有功能是如何组合在一起的。
- [从 Apple Vision Pro 截取屏幕截图和视频用于 2D 观看](https://developer.apple.com/documentation/visionos/capturing-screenshots-and-video-from-your-apple-vision-pro-for-2d-viewing)  
为你的 visionOS 应用及其周围环境创建屏幕截图并录制高质量视频，用于应用预览。