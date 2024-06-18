# 分析 visionOS 应用程序的性能

使用 Instruments 中的 RealityKit 跟踪模板来评估和改进 visionOS 应用程序的性能。

## 概述

为了保持 Apple Vision Pro 的沉浸感，系统会尝试以恒定的速度为设备显示屏提供最新图像，并以最小的延迟响应交互。任何视觉不流畅或响应延迟都会干扰空间体验。长时间较高的功耗或较短时间内的极端功耗会触发热减缓，这也会影响体验质量。最大限度地减少应用对系统资源的使用，确保应用在平台上的良好表现非常重要。您在开发其他 Apple 平台时使用的许多最佳实践和优化程序同样适用于为 visionOS 开发。有关在其他平台上优化应用程序的更多信息，请参阅[提高应用程序性能](https://developer.apple.com/documentation/Xcode/improving-your-app-s-performance)。

要获取有关渲染瓶颈、高系统功耗以及影响 visionOS 应用程序响应速度的其他问题的有用信息，请使用 Instruments 中的 RealityKit 跟踪模板对应用程序进行剖析。此模板可帮助您识别

- 复杂的内容或频繁更新的内容，这些内容会导致渲染服务器错过截止日期和丢帧。
- 导致系统功耗过高的内容和任务。
- 主线程上长时间运行的任务，这些任务会影响输入事件的有效处理。
- 其他线程上运行的任务来不及同步回主线程进行视图层次更新。

> 注意  
> 您可以使用真实设备或模拟器进行剖析，但要获得最准确、最可行的信息，请使用真实设备。Mac 上的模拟器与真实设备之间存在软件和硬件差异，因此无法依赖计时信息。模拟设备对于快速迭代和改进不基于时间的性能方面非常有用。

### 打开一个新的跟踪文档

要创建新的跟踪文档

1. 在 Xcode 项目窗口中选择应用程序的方案和 visionOS 运行目标。
2. 选择产品 > 配置文件。
3. 选择 RealityKit 跟踪模板
4. 选择 Choose（选择）按钮。

<img src="https://docs-assets.developer.apple.com/published/9148e528838e80f7463e3a5ada67d624/instruments-template-chooser-realitykit-trace~dark@2x.png" alt="仪器模板选择器对话框将显示多个跟踪模板，并选择 RealityKit 跟踪模板。对话框右下方显示'取消'和'选择'按钮。">

或者，启动 Instruments 并从模板选择对话框中选择一个目标应用程序。

RealityKit 跟踪模板包括以下工具：

RealityKit 帧  
    捕捉 visionOS 渲染服务器生成的帧的渲染时间和寿命。该工具可显示帧错过渲染截止日期的时间，并提供平均 CPU 和 GPU 渲染率。

RealityKit 指标  
    从包括渲染、提交、动画、物理和空间系统在内的整个渲染流水线中获取全面的时序信息。该工具可识别应用程序进程或渲染服务器中因应用程序内容而产生的潜在瓶颈，并指出需要优化的中度和高度系统功耗使用区域。

运行循环  
    捕获并显示 Runloop 执行详情。

时间剖析器  
    以固定时间间隔剖析所有进程在所有内核上运行的线程。

挂起  
    捕获并显示主线程未响应的时间段。

金属应用  
    记录 Metal 应用程序事件。

考虑为特定调查添加其他跟踪工具。例如，您可以使用 "热状态 "工具记录设备热状态，以检查热压力是否影响性能。

### 配置您的工作流程

单击窗口左上角的记录按钮，开始捕获剖面数据。在应用程序中执行要调查的操作。完成操作后，再次单击记录按钮停止记录。

要调查性能问题或分析系统功耗影响，请单独对应用程序进行剖析，以了解应用程序对系统性能的影响，并确保获得最可行的信息。对于与其他应用程序同时运行的应用程序，请在其他应用程序运行时再次对您的应用程序进行剖析，以了解用户如何体验您的应用程序与其他应用程序的结合。

### 检查帧渲染性能

为了保持流畅的视觉体验，系统会尝试以每秒 90 帧 (FPS) 的速度为 Apple Vision Pro 渲染新帧。系统会根据所显示的内容和当前环境，以其他帧频进行渲染。根据目标帧频，每帧都有一个渲染截止时间。如果不能按时完成，就会导致丢帧。这会造成整体空间体验不佳。人们往往会在 Persona 和 SharePlay 体验的视觉表现、视频播放和滚动中注意到这一点。RealityKit 帧工具会在其时间线的 "帧 "部分显示渲染每个帧所花费的时间：

<img src="https://docs-assets.developer.apple.com/published/bfa9929d11dbe27848e4a012c12e04d9/realitykit-frames-instrument~dark@2x.png" alt="RealityKit Frames 工具时间线的截图显示了三种可视化效果： 帧、CPU 平均帧时间和 GPU 平均帧时间。帧可视化表示渲染每个帧所花费的时间。其他两个可视化显示 CPU 和 GPU 处理帧所花费的平均时间。">

放大后，你可以识别出丢帧较多的区域，或帧运行接近渲染截止日期的区域。时间线使用绿色标识在截止日期前完成渲染的帧，橙色标识在截止日期前完成渲染的帧，红色标识未完成渲染而被渲染器丢弃的帧。掉帧会导致空间体验不佳，但在接近渲染截止日期时完成渲染的帧也会显示性能问题。按住 Option 键并拖动可放大一个帧或一组帧，查看其生命周期的各个阶段：

<img src="https://docs-assets.developer.apple.com/published/d66a82496b69a01480d8993de322edb0/realitykit-frames-instrument-zoomed~dark@2x.png" alt="同一 RealityKit Frames 工具时间线的截图，放大后可显示 Frames 可视化中的更多细节。框架可视化中的每个块代表一个框架。每个帧块包含三行矩形，代表在不同任务上花费的时间。最上面一行包含一个帧编号，并表示该帧所花费的总时间。中间一行的 CPU 和 GPU 块分别表示进程所花费的时间。最后一行的区块表示帧处理的更细化阶段所花费的时间。">

这可以让你深入了解需要进一步调查的渲染管道的哪个部分。该时间线还包括可视化 CPU 平均帧时间和 GPU 平均帧时间的部分，以显示计算帧的处理类型。时间轴上没有帧块的区域表示在一段时间内，人的周围环境没有发生变化，应用程序也没有更新。在这段时间内，渲染服务器会避免计算新的帧来发送给合成器，这有助于优化功耗。

### 监控系统功耗

当热水平上升到会触发系统热缓解的水平时，性能就会下降，并对应用程序的响应速度产生负面影响。优化功耗可避免这种负面影响。RealityKit Metrics 工具的时间轴包括系统功耗影响部分，用于识别应用程序中功耗较高的区域：

<img src="https://docs-assets.developer.apple.com/published/a9a59b3a216297a7fcdd87933e9b8c38/realitykit-metrics-instrument~dark@2x.png" alt="RealityKit Metrics 工具时间轴的截图显示了两个可视化内容： 瓶颈和系统功耗影响。瓶颈可视化包含橙色和红色标记，用于指示渲染瓶颈。系统功耗影响可视化包含一个高度不等的图表，上面有绿色、橙色和红色区域。">

如果时间轴显示绿色，则表示该工具认为您的应用对系统功耗的影响较小，足以维持。显示橙色或红色的区域表示系统功耗可能会导致热量水平上升并触发热缓解措施。这会降低系统资源的可用性，从而导致视觉中断和响应速度问题。

> 注意  
> 如果渲染服务器因热压力而无法保持 90 FPS 的目标帧频，它可能会将帧频降低一半。出现这种情况时，帧轨中的所有帧都会显示为错过了渲染截止日期。其他因素也会导致帧频降低，包括系统处理内容的复杂性和频率。使用 "热状态 "工具来确定是热条件导致了速率限制，还是其他因素造成的。

### 识别瓶颈

RealityKit Metrics 工具时间线的瓶颈部分包含一些标记，这些标记表示应用程序或渲染服务器的开销过高，导致丢帧和系统功耗过高。遇到这些问题时，请检查时间轴是否标出了可以解决的瓶颈。双击任何标记，即可在工具窗口底部的详细信息区域显示更多信息。如果详细区域被隐藏，选择查看 > 详细区域 > 显示详细区域即可显示。渲染服务器在 CPU 或 GPU 上遇到瓶颈。仪器会根据严重程度和类型对瓶颈进行分类。

要将详细区域中列出的瓶颈过滤到特定时间段，可在时间线内拖动选择区域。要查看按严重程度和类型分类的瓶颈概要视图，请选择摘要： 从详细区域左上角的菜单中选择 RealityKit 瓶颈。单击大纲视图中严重程度或类型右侧的箭头按钮，显示该类别的瓶颈列表。

选择特定瓶颈时，扩展详情会为您提供解决瓶颈问题的建议--如果扩展详情被隐藏，请选择查看 > 显示扩展详情来显示扩展详情。

<img src="https://docs-assets.developer.apple.com/published/bc36ed27422b4cfbdba93bebb868466b/realitykit-metrics-bottleneck-recommendation~dark@2x.png" alt="仪器窗口显示来自 RealityKit 跟踪模板的剖面数据。窗口顶部显示 RealityKit Frames、RealityKit Metrics、Runloops、Time Profiler 和 Hangs 工具的时间线。选中 RealityKit Metrics 工具的时间线，窗口底部的详细信息区域会显示 RealityKit 瓶颈的摘要，并选中实体提交瓶颈。右侧的扩展细节侧边栏会显示建议列表。">

### 探索与瓶颈有关的指标

跟踪提供了更多信息，您可以利用这些信息确定要在应用程序中做出的更改，以解决这些瓶颈问题。单击 "RealityKit 指标 "工具时间线的扩展箭头，可显示每个主要工作类别的特定图表。使用与这些图表相关的指标来确定哪个 RealityKit 功能对应用程序进程或渲染服务器中 CPU 帧时间过高的影响最大。在解释这些图表时，越低表示性能和功耗越高。这些指标代表了所有正在运行的应用程序的数值，因此在尝试优化这些指标时，只需运行你的应用程序即可。

<img src="https://docs-assets.developer.apple.com/published/1eb07427ddaf51ad899be60f6065593d/realitykit-metrics-instrument-expanded~dark@2x.png" alt="RealityKit Metrics 工具时间轴的截图，展开后可显示 3D 渲染、核心动画渲染、实体提交、RealityKit 动画、RealityKit 物理和空间系统的其他图表。">

三维渲染  
    与渲染服务器中 3D RealityKit 渲染成本相关的指标。这包括来自所有应用程序的绘制调用、三角形和顶点的数量。

核心动画渲染  
    与呈现服务器中 UI 内容呈现成本相关的指标。这包括所有应用程序的渲染遍数、屏幕外渲染遍数和半透明 UI 网格总数。

实体提交  
    与应用程序和呈现服务器中实体提交成本相关的指标。这包括所有应用程序与呈现服务器共享的 RealityKit 实体数量，以及在特定时间间隔内从所有应用程序接收的更新数量。

RealityKit 动画  
    与应用程序和呈现服务器中的 RealityKit 动画成本相关的指标。这包括所有应用程序中骨骼动画的数量。

RealityKit 物理  
    与应用程序进程和渲染服务器中 RealityKit 物理模拟、碰撞和命中测试的成本相关的指标。这包括所有应用程序中使用的刚体计数和碰撞器数量，以及 UI 和其他 3D 内容使用的物理形状类型。

空间系统  
    与渲染服务器空间算法成本相关的指标。这包括所有应用程序中自定义锚点的数量。

> 提示  
> 某些部分的图表结合了多个单独的指标。标题会通过显示图表计数来说明这一点。单击时间线标题的底部并向下拖动，可显示每个指标的单独图表。例如，"三维渲染 "时间线可能会在标题中显示 13 个图形；展开该时间线，就会显示 "三维网格绘制调用"、"三维网格三角形"、"三维网格顶点 "和其他 10 个指标的单独图形。

应用程序进程的时间轴有助于汇总工具中有关进程的信息，以及呈现服务器为进程完成的工作。

<img src="https://docs-assets.developer.apple.com/published/aaea5f2efe2e70b2f89260bfdc9126bc/realitykit-trace-world-track~dark@2x.png" alt="应用程序进程时间线的截图显示，左侧是应用程序的名称，下方有一个标有进程的弹出按钮。右侧的时间线图显示了标有 3D 渲染、音频回放、自定义现实系统、实体提交和 RealityKit 物理的 RealityKit 系统时间。">

从时间线标题的弹出式窗口中选择一个选项，即可在时间线中显示不同的图表：

运行循环  
    每个线程等待或忙碌的时间。

挂起  
    主线程未响应的时间。

时间概况  
    CPU 使用率和生命周期状态。

RealityKit 系统时间  
    归因于 RealityKit 系统的开销。

选择应用程序进程的时间线后，可通过左上角的弹出按钮选择要在详细信息区显示的仪器摘要和配置文件数据：

<p width="30%" align="center">
    <img src="https://docs-assets.developer.apple.com/published/15d6ce198ee4578c1f8c155e213be2a8/realitykit-process-detail-options~dark@2x.png" alt="您可以选择显示进程的详细信息菜单，按仪器分类： 挂起、RealityKit Metrics、Runloops 和 Time Profiler。挂起的选项包括摘要：挂起、挂起和挂起风险。RealityKit Metrics 的选项包括摘要： RealityKit 系统 CPU 时间。运行循环选项包括摘要： Runloop Intervals、Runloop Busy Intervals 和 All Runloop Intervals。Time Profiler 的选项包括 Profile、Samples 和 App Lifecycle。">
</p>

要按时间过滤详细信息区域中的信息，请在上面的时间轴中选择时间段。

### 检测主线程上的延迟

在应用程序的进程时间线中选择挂起，以确定跟踪中可能存在交互延迟的时间。使用 RealityKit Metrics 和 Time Profiler 摘要，更好地了解应用程序正在进行的工作。从详细信息区域弹出菜单中选择以下选项：

轮廓和样本  
    显示来自 Time Profiler 工具的信息，以确定应用程序在挂起期间正在做什么。

摘要  
    RealityKit 系统 CPU 时间： 显示 CPU 在各种 RealityKit 系统操作上花费的最短时间、最长时间和平均时间。

优化你发现的任何 3D 渲染更新、命中测试和碰撞工作。有关解决应用程序挂起的更多信息，请参阅[提高应用程序的响应速度](https://developer.apple.com/documentation/Xcode/improving-app-responsiveness)。

### 管理音频开销

使用进程时间线的音频回放部分来识别音频开销较大的区域。在 visionOS 上运行应用程序时，系统默认使用空间音频。它会实时处理有关您的位置、周围环境和音频源当前位置的信息，以生成身临其境的音频体验。如果包含过多并发音频源，需要系统根据音频源在大空间中的位置进行调整，对系统资源需求的增加可能会导致音频输出延迟。

要减少空间音频工作，请限制：

- 同时播放的音频源数量
- 移动音源的数量
- 声场的大小

考虑创建音频播放器池，限制应用程序使用的最大播放器数量。在适当情况下，将播放器放在固定实体上，而不是移动实体上。同时初始化多个音频播放器会造成很大的开销，影响系统的其他方面，如渲染性能。考虑到系统在这些分配过程中完成的其他任务，将它们间隔一段时间。如需了解更多信息，请参阅[创建出色的空间播放体验](https://developer.apple.com/videos/play/wwdc2023/10070)。

### 对自定义材质进行剖面优化

在为自定义材质添加更多视觉效果之前，使用金属系统跟踪模板对其进行单独剖析。检查 Metal GPU 成本，并尝试优化每帧的 GPU ALU 指令和 GPU 纹理读写。有关使用此模板的更多信息，请参阅[分析 Metal 应用程序的性能](https://developer.apple.com/documentation/Xcode/Analyzing-the-performance-of-your-Metal-app)。

为了使用 Metal 应用程序工具准确地剖析自定义材质，请设置一个固定的性能状态：

1. 单击并按住记录按钮，然后选择记录选项。
2. 选择金属应用仪器的选项。
3. 将计数器设置设为性能限制器。
4. 将性能状态设置为最小或中等。

<p width="80%" align="center">
    <img src="https://docs-assets.developer.apple.com/published/84a2869aec6cd65e4382ca3d079a7543/metal-application-recording-options~dark@2x.png" alt="仪器录音选项的图像。图像顶部会出现一个弹出按钮，上面标有'选项'和'金属应用'。弹出按钮下是标题为 GPU 的部分。该部分显示三个弹出按钮，分别标有'选中 M2 的设备'、'选中性能限制器的计数器集'和'选中中等的性能状态'，以及一个标有'已启用着色器时间线'的未选中复选框。">
</p>

人眼的位置、人与内容的距离以及应用显示的内容数量都会影响 Reality Composer Pro 自定义材质的 GPU 工作量。要比较痕迹，请尽可能保持这些因素的一致性。对于您在完全沉浸式空间中使用的自定义材质，请尝试在剖析时移除场景中的所有其他内容，以隔离材质的开销。对于用于其他类型内容的自定义材质，可考虑在测试场景中将其固定在人的头部，以便与人的距离保持固定。

> 注意  
> 当您的应用使用 [Metal](https://developer.apple.com/documentation/metal) 和[合成器服务](https://developer.apple.com/documentation/compositorservices)框架来呈现完全沉浸式的体验时，由于您的应用必须渲染的像素数量较多，因此使用无光或廉价的自定义材质就变得更加重要。

---

## 另见

### 性能

- [为您的 visionOS 应用程序创建性能计划](https://developer.apple.com/documentation/visionos/creating-a-performance-plan-for-visionos-app)  
确定应用程序的性能和功耗目标，并制定衡量和评估这些目标的计划。
- [降低用户界面在 visionOS 上的渲染成本](https://developer.apple.com/documentation/visionos/reducing-the-rendering-cost-of-your-ui-on-visionos)  
在 visionOS 上优化 2D 用户界面的渲染。
- [在 visionOS 上降低 RealityKit 内容的渲染成本](https://developer.apple.com/documentation/visionos/reducing-the-rendering-cost-of-realitykit-content-on-visionos)  
优化应用程序的 3D 增强现实内容，以便在 visionOS 上高效渲染。
- [了解 visionOS 渲染管道](https://developer.apple.com/documentation/visionos/understanding-the-visionos-render-pipeline)  
比较 visionOS 在处理事件和管理渲染循环方面与其他 Apple 平台的不同之处。