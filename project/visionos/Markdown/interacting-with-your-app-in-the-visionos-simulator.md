# 在 visionOS 模拟器中与应用交互

在模拟器中使用 Mac 浏览空间并控制与 visionOS 应用的交互。

## 概述

使用模拟器可在 visionOS 中运行应用，而无需将其安装到物理设备上。在模拟器中运行应用时，你可以在沉浸式空间中看到应用窗口和三维内容的单视角视图。使用 Mac 改变空间内应用的视角，并导航应用的界面。

<p align="center">
    <image src="https://docs-assets.developer.apple.com/published/d1c3334beea7ea4ad621d805bbdc99e7/interacting-with-your-app-in-the-visionos-simulator~dark@2x.png" width="75%"/>
</p>

### 与应用交互

要使用 Mac 的指针和键盘创建手势，请从 visionOS 模拟器窗口右下方的按钮中选择 "选择与场景交互"。当你将鼠标悬停在空间中的内容上时，你的指针移动会控制你的视线。

使用以下操作来触发手势：

手势 | 如何模拟
:----- | :-----
轻点 | 单击。
双点 | 双击。
触摸并捏住 | 单击并按住。
拖动（上下左右） | 向上下左右拖动。
拖动（前后） | 按住 Shift 键并上下拖动。
双手手势 | 按住 Option 键显示触摸点。按住 Option 键的同时移动指针，可更改触摸点之间的距离。移动指针并按住 Shift 键和 Option 键可重新定位触摸点。

使用菜单项或点击模拟器窗口工具栏上的控件激活设备按钮。

### 空间导航

使用 Mac 的指针和键盘在 visionOS 模拟器窗口中调整视角：

动作 | 如何模拟
:----- | :-----
前移 | 按 W 键（或向上箭头键），或在触控板上做出两指分开的捏合手势。
后移 | 按 S 键（或向下箭头键），或在触控板上用两指相向移动做捏的手势。
左移 | 按 A 键（或左箭头键），或使用触控板或 Magic Mouse 向左滚动。
右移 | 按 D 键（或右箭头键），或使用触控板或 Magic Mouse 向右滚动。
上移 | 按 E 键，或使用触控板或 Magic Mouse 向上滚动。
下移 | 按 Q 键，或使用触控板或 Magic Mouse 向下滚动。

你还可以通过标准拖动来控制视角。为此，请从模拟器窗口右下方的按钮中选择 "拖动平移摄像机 "来左右、上下移动视角，或选择 "拖动移动摄像机 "来前后移动视角。

要改变视角，可在 visionOS 模拟器窗口内按住 Control 键拖动。你可以从模拟器窗口右下方的按钮中选择 "拖动摄像机以倾斜"，这样就可以在不使用 Control 键的情况下拖动摄像机了。

要重置 visionOS 模拟器的视角，请选择窗口右下方的 "重置摄像机 "按钮。

> 注意  
> 要将指针和键盘输入定向到模拟设备，请选择 visionOS 模拟器窗口右上方工具栏中的 "捕捉指针 "或 "捕捉键盘 "按钮。这些按钮可以绕过 Mac 主机的指针和键盘输入，防止它们控制 visionOS 模拟器窗口中的视角。要将输入重新定向到 Mac 主机，请按 Esc 键。

使用触控板或 Magic Mouse 进行移动时，模拟器会尊重 macOS 的自然滚动设置。

你还可以使用游戏控制器来控制移动。使用左摇杆向左、向右、向前或向后移动。使用 R2 和 L2 可以上下移动。使用游戏控制器上的右摇杆在空间内平移。

### 在模拟场景之间切换

模拟器提供了多个内置场景，你可以模拟在不同的环境中穿行。这些场景包括用于不同测试场景的独特房间布局和家具，每个场景都有不同的照明条件。

使用模拟场景来测试：
- 应用在不同背景和不同照明条件下的可读性。
- 不同的场景，包括有限的和杂乱的环境，以了解你的应用如何适应这些场景。
- 内容布局、定位和尺寸。
- 空间音频和音响效果。

要更改模拟场景，请单击 visionOS 模拟器窗口右上方工具栏中的 "模拟场景 "按钮，然后选择不同的场景。

---

## 另见

###  模拟器交互

- [在 iOS 和 iPadOS 模拟器中与你的应用交互](https://developer.apple.com/documentation/xcode/interacting-with-your-app-in-the-ios-or-ipados-simulator)  
在模拟器中使用 Mac 控制与 iOS 和 iPadOS 应用的交互。
- [在 tvOS 模拟器中与应用交互](https://developer.apple.com/documentation/xcode/interacting-with-your-app-in-the-tvos-simulator)  
在模拟器中使用 Mac 控制与 tvOS 应用的交互。
- [在 watchOS 模拟器中与应用交互](https://developer.apple.com/documentation/xcode/interacting-with-your-app-in-the-watchos-simulator)  
在模拟器中使用 Mac 控制与 watchOS 应用的交互。
- [根据工作环境配置模拟器](https://developer.apple.com/documentation/xcode/configuring-a-simulator-for-your-environment)  
调整模拟器的窗口或屏幕大小、亮暗外观和音频设置，并重启或重置模拟设备。
- [模拟外部显示屏或 CarPlay](https://developer.apple.com/documentation/xcode/simulating-an-external-display-or-carplay)  
通过模拟器测试应用如何处理外部显示器或 CarPlay。
- [从模拟器截取屏幕截图和视频](https://developer.apple.com/documentation/xcode/capturing-screenshots-and-videos-from-simulator)  
记录和共享测试结果，或通过模拟器截取应用的屏幕截图和视频，为在 App Store 发布做好准备。