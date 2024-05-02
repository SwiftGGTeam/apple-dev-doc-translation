# 在 visionOS 中绘制基于图层的清晰内容

在 visionOS 中，从自定义的 Core Animation 图层中以多种分辨率提供文本和矢量图像。

## 概述

如果你的应用直接使用了 Core Animation 图层（layer），请更新这部分代码，以便在适当的时候绘制高分辨率版本的内容。SwiftUI 和 UIKit 视图使用 Core Animation 层来高效管理界面内容。当一个 View 绘制其内容时，底层的 layer 会捕获该内容并对其进行缓存，为后续的渲染操作作出优化。

大多数 Apple 平台上的 Core Animation 使用与屏幕相同的分辨率对图层进行栅格化，但是 visionOS 有所不同，在 visionOS 上的 Core Animation 可以以不同的分辨率栅格化，以最大化提升绘制内容的清晰度和性能。系统会跟踪人眼，根据人眼所在焦点以尽可能高的分辨率立即呈现内容。在此焦点的区域之外，系统以逐渐降低的分辨率渲染内容，以减少 GPU 工作负载。由于焦点外的内容位于人的周边视觉中，因此这些较低的分辨率不会影响内容的清晰度。当人的眼睛四处移动时，系统会以不同的分辨率重新绘制内容，来匹配焦点的变化。

(2x 分辨率的图像)[https://docs-assets.developer.apple.com/published/07dbf114a9041c9a1a42ed5dc38f769c/core-animation-2x~dark@2x.png]

(8x 分辨率的图像)[https://docs-assets.developer.apple.com/published/a188c1788736fc5f0e46e1a38b5b7e04/core-animation-8x~dark@2x.png]

如果使用自定义 [CALayer](https://developer.apple.com/documentation/quartzcore/calayer) 对象提供内容，则可以配置你的自定义图层以支持以不同分辨率来进行绘制。如果不执行这一额外的配置步骤，则每个图层都会以 @2x 的比例栅格化其内容，这对于大多数内容来说已经足够了，并且与图层在 Retina 显示屏上提供的内容相匹配。但是，如果您选择以不同的分辨率进行绘制，则图层会在 visionOS 中以高达  @8x 的比例栅格化其内容，从而为文本和基于矢量的内容添加大量细节。

### 请求自定义图层的动态缩放

默认情况下，所有 Core Animation 图层的动态内容缩放都处于关闭状态，框架或应用必须显式地启用此支持。如果您的界面仅使用 SwiftUI 或 UIKit 视图，则无需执行任何操作即可支持此功能。SwiftUI 和 UIKit 会自动为那些受益于被添加了细节的视图启用它，例如带有 SF 符号的文本和图像视图，或其他基于矢量的美术资源。但是，框架不会为所有视图（包括 [UIView](https://developer.apple.com/documentation/uikit/uiview) 和 [View](https://developer.apple.com/documentation/SwiftUI/View) ）启用该功能。

如果您的 visionOS 界面包含自定义的 Core Animation 图层，对于包含基于矢量的内容的任意 [CALayer](https://developer.apple.com/documentation/quartzcore/calayer) 对象，您都可以启用它们的 [wantsDynamicContentScaling](https://developer.apple.com/documentation/quartzcore/calayer/4241437-wantsdynamiccontentscaling) 属性。将此属性设置为 true 会告知系统您支持以不同的分辨率渲染图层的内容。但是，此设置并不能保证系统将动态内容缩放应用于您的内容。如果图层使用不兼容的函数或技术进行绘制，系统可能会禁用该属性。

以下示例演示如何为 [CATextLayer](https://developer.apple.com/documentation/quartzcore/catextlayer) 对象启用此功能。配置图层后，将 [wantsDynamicContentScaling](https://developer.apple.com/documentation/quartzcore/calayer/4241437-wantsdynamiccontentscaling) 属性设置为 true ，然后将此图层添加到图层层次结构中。

```swift
let layer = CATextLayer()


layer.string = "Hello, World!"
layer.foregroundColor = UIColor.black.cgColor
layer.frame = parentLayer.bounds


// Setting this property to true enables content scaling 
// and calls setNeedsDisplay to redraw the layer's content.
layer.wantsDynamicContentScaling = true


parentLayer.addSublayer(layer)
```

当图层包含文本或基于矢量的内容时，动态内容缩放效果最佳。如果在图层中执行以下任一操作，请勿启用该功能：

- 您使用 [contents](https://developer.apple.com/documentation/quartzcore/calayer/1410773-contents) 属性设置了图层的内容。
- 您主要绘制的是基于位图的内容。
- 您可能会在短时间内多次重绘您的内容。

[CAShapeLayer](https://developer.apple.com/documentation/quartzcore/cashapelayer) 类忽略 [wantsDynamicContentScaling](https://developer.apple.com/documentation/quartzcore/calayer/4241437-wantsdynamiccontentscaling) 属性的值，并始终启用动态内容缩放。对于其他 Core Animation 图层，必须显式启用该功能才能利用它。

### 动态绘制图层的内容

动态内容缩放要求您使用某个指定的方法绘制图层的内容。如果定义了一个自定义的 [CALayer](https://developer.apple.com/documentation/quartzcore/calayer) 子类，请在 [draw(in:)](https://developer.apple.com/documentation/quartzcore/calayer/1410757-draw) 方法中绘制图层的内容。如果使用 [CALayerDelegate](https://developer.apple.com/documentation/quartzcore/calayerdelegate) 对象绘制图层的内容，请改用 delgate 中的 [draw(in:)](https://developer.apple.com/documentation/quartzcore/calayer/1410757-draw) 方法。

当您为图层启用动态内容缩放时，系统会捕获应用程序的绘图命令以供以后重绘。当人的眼睛移动时，如果视线直视图层，系统会以更高的分辨率绘制图层，否则系统会以较低的分辨率绘制图层。由于重绘操作隐式透露了用户正在查看的内容，因此系统会在应用进程之外执行这些操作。让系统处理这些操作不仅可以保护用户隐私，同时可使您的应用程序能够受益于高分辨率绘制。


一些 Core Graphics 的 API 与动态内容缩放不兼容。即使您为图层启用了动态内容缩放，如果您的图层使用以下任一选项，系统也会自动禁用该功能：
- 使用 Core Graphics 的 着色器（shader）
- 设置意图、质量或其他位图相关属性的 API。例如，不要调用 [CGContextSetInterpolationQuality](https://developer.apple.com/documentation/coregraphics/1455656-cgcontextsetinterpolationquality) 。
- 用于绘制内容的 [CGBitmapContext](https://developer.apple.com/documentation/coregraphics/cgbitmapcontext)。

如果您的应用创建基于计时器的动画，请不要使用绘制方法对图层更改进行动画处理。在短时间内重复调用图层的 [setNeedsDisplay()](https://developer.apple.com/documentation/quartzcore/calayer/1410855-setneedsdisplay) 方法会导致系统快速连续多次绘制图层。由于 visionOS 需要一些额外的时间来以高分辨率绘制图层，因此每次重绘请求都会强制它放弃当前工作。更好的选择是对基于图层的属性进行动画处理以实现相同的效果，或者在需要时使用 [CAShapeLayer](https://developer.apple.com/documentation/quartzcore/cashapelayer) 对路径进行动画处理。

### 修改图层层次结构以改善性能

图层的后端存储在较高分辨率下比在较低分辨率下消耗更多的内存。在启用动态内容缩放之前和之后测量应用的内存使用情况，以确保增加的内存成本物有所值。如果应用的内存使用量增加过多，请对某些图层是否采用动态内容缩放加以限制。您还可以通过以下方式减少每个图层使用的内存量：

- 尽可能最小化您的图层尺寸。越大的图层需要的内存也越多，尤其是在更高的分辨率下。通过消除缩进或周围额外的空间，来使图层的大小与内容的大小相匹配。
- 将复杂内容置于不同图层。相比在单个图层中绘制所有内容，不如从多个图层构建内容并分层排列它们，以达到相同的效果。仅在实际需要的图层中启用动态内容缩放。
- 尽可能使用图层属性来应用特殊效果。在绘制过程中应用某些效果可能需要增加图层的占用大小。例如，请将缩放和旋转效果应用于图层的 [transform](https://developer.apple.com/documentation/quartzcore/calayer/1410836-transform) 属性，而不是在绘制过程中这么做。
- 不要提前用不同的分辨率绘制图层的内容并缓存图像。维护多个图像需要更多内存。如果您确需缓存图像，请仅以 @2x 的比例绘制它们。
- 不要使用绘制代码绘制单个图像。如果图层的内容由图像组成，请直接将该图像分配给图层的 [contents](https://developer.apple.com/documentation/quartzcore/calayer/1410773-contents) 属性。

复杂的绘图代码也可能导致性能问题。一个具有许多描边的图层可以在较低分辨率下快速渲染，但在计算上可能过于复杂，继而无法在较大比例下渲染。如果复杂图层无法在更高分辨率下正确渲染，请关闭动态内容缩放并再次测量渲染时间。

## 另见

#### 创建应用

- [Creating your first visionOS app](https://developer.apple.com/documentation/visionos/creating-your-first-visionos-app)
使用 SwiftUI 构建新的 visionOS App，并添加平台专属的功能。
- [为应用添加 3D 内容](https://developer.apple.com/documentation/visionos/adding-3d-content-to-your-app)
为你的 visionOS 应用添加深度和维度，并了解如何将应用的内容融入用户周围环境中。
- [在应用中构建完全沉浸式的体验](https://developer.apple.com/documentation/visionos/creating-fully-immersive-experiences)
将 RealityKit 或 Metal 创建的内容与空间结合，构建完全沉浸式的体验。
