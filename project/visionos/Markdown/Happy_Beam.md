# 快乐光束

利用 Full Space 使用 ARKit 创建有趣的游戏。

## 概述

在 visionOS 中，您可以使用多种不同的框架创建有趣、动态的游戏和应用，以创建新型空间体验：RealityKit、ARKit、SwiftUI 和小组活动。此示例介绍了 Happy Beam，这是一款游戏，您和朋友可以在其中进行 FaceTime 通话并一起玩。

您将学习游戏的机制，其中脾气暴躁的云朵在空间中漂浮，人们通过用手制作心形来投射光束来玩游戏。人们将光束对准云朵以振作起来，记分计数器会跟踪每个玩家在云彩中振作的表现。

[试玩视频](https://docs-assets.developer.apple.com/published/299cd298aee541a80039720b4f488cf5/Happy-Beam-overview.mp4)

## 在 SwiftUI 中设计游戏界面

visionOS 中的大多数应用都以窗口的形式启动，该窗口会根据应用的需要打开不同的场景类型。

在这里，您可以看到 Happy Beam 如何使用多个 SwiftUI 视图向人们呈现一个有趣的界面，这些视图显示欢迎屏幕、提供说明的教练屏幕、记分牌和游戏结束屏幕。

欢迎窗口 | 指示 | 记分牌 | 结束窗口
 ----- | ----- | ----- | ----- 
![img-1](https://docs-assets.developer.apple.com/published/a4a95e6a952a19f93222788b422649e2/HB-welcome@2x.png) | ![img-2](https://docs-assets.developer.apple.com/published/61d185d073ca3656c70068c897317b92/HB-instructions@2x.png) | ![img-3](https://docs-assets.developer.apple.com/published/79c888c096819fd9309e3427e1a5d05a/HB-scoreboard@2x.png) | ![img-4](https://docs-assets.developer.apple.com/published/658efc9a3068fd8f3e815d448db59ebc/HB-ending@2x.png)

下面显示了应用中显示游戏每个阶段的主要视图：

```swift
struct HappyBeam: View {
    @Environment(\.openImmersiveSpace) private var openImmersiveSpace
    @Environment(GameModel.self) var gameModel
    
    @State private var session: GroupSession<HeartProjection>? = nil
    @State private var timer = Timer.publish(every: 1, on: .main, in: .common).autoconnect()
    @State private var subscriptions = Set<AnyCancellable>()
    
    var body: some View {
        let gameState = GameScreen.from(state: gameModel)
        VStack {
            Spacer()
            Group {
                switch gameState {
                case .start:
                    Start()
                case .soloPlay:
                    SoloPlay()
                case .lobby:
                    Lobby()
                case .soloScore:
                    SoloScore()
                case .multiPlay:
                    MultiPlay()
                case .multiScore:
                    MultiScore()
                }
            }
            .glassBackgroundEffect(
                in: RoundedRectangle(
                    cornerRadius: 32,
                    style: .continuous
                )
            )
        }
    }
}
```

当 3D 内容开始出现时，游戏会打开一个沉浸式空间，以在主窗口之外和人周围环境中呈现内容。

```swift
@main
struct HappyBeamApp: App {
    @State private var gameModel = GameModel()
    @State private var immersionState: ImmersionStyle = .mixed
    
    var body: some SwiftUI.Scene {
        WindowGroup("HappyBeam", id: "happyBeamApp") {
            HappyBeam()
                .environmentObject(gameModel)
        }
        .windowStyle(.plain)
        
        ImmersiveSpace(id: "happyBeam") {
            HappyBeamSpace(gestureModel: HeartGestureModelContainer.heartGestureModel)
                .environmentObject(gameModel)
        }
        .immersionStyle(selection: $immersionState, in: .mixed)
    }
}
```

HappyBeam 容器视图声明对以下各项 openImmersiveSpace 的依赖关系：

```swift
@Environment(\.openImmersiveSpace) private var openImmersiveSpace
```

稍后，当需要开始显示 3D 内容时，它会使用该依赖项从应用的声明中打开空间：

```swift
if gameModel.countDown == 0 {
    Task {
        await openImmersiveSpace(id: "happyBeam")
    }
}

```

## 使用 ARKit 检测心形手势

Happy Beam 应用程序使用 ARKit 在 visionOS 中对 3D 手部跟踪的支持来识别中央心形手势。使用手部追踪需要跑步会话和佩戴者的授权。它使用 NSHandsTrackingUsageDescription 用户信息密钥向玩家解释为什么应用程序请求手部跟踪权限。

[心形手势](https://docs-assets.developer.apple.com/published/1d628bd12b2b0b094fea1fba46ed9545/HB-custom-gestures@2x.png)

```swift
Task {
    do {
        try await session.run([handTrackingProvider])
    } catch {
        print("ARKitSession error:", error)
    }
}
```

当您的应用仅显示窗口或音量时，手势跟踪数据不可用。相反，当您演示沉浸式空间时，它可用，如上例所示。

您可以使用 ARKit 数据检测手势，其准确性取决于您的用例和预期体验。例如，Happy Beam 可能需要严格定位手指关节，使其与心形非常相似。然而，相反，它会提示人们做一个心形，并使用启发式方法来指示手势何时足够接近。

下面检查一个人的拇指和食指是否几乎接触：

```swift
// Get the position of all joints in world coordinates.
let originFromLeftHandThumbKnuckleTransform = matrix_multiply(
    leftHandAnchor.originFromAnchorTransform, leftHandThumbKnuckle.anchorFromJointTransform
).columns.3.xyz
let originFromLeftHandThumbTipTransform = matrix_multiply(
    leftHandAnchor.originFromAnchorTransform, leftHandThumbTipPosition.anchorFromJointTransform
).columns.3.xyz
let originFromLeftHandIndexFingerTipTransform = matrix_multiply(
    leftHandAnchor.originFromAnchorTransform, leftHandIndexFingerTip.anchorFromJointTransform
).columns.3.xyz
let originFromRightHandThumbKnuckleTransform = matrix_multiply(
    rightHandAnchor.originFromAnchorTransform, rightHandThumbKnuckle.anchorFromJointTransform
).columns.3.xyz
let originFromRightHandThumbTipTransform = matrix_multiply(
    rightHandAnchor.originFromAnchorTransform, rightHandThumbTipPosition.anchorFromJointTransform
).columns.3.xyz
let originFromRightHandIndexFingerTipTransform = matrix_multiply(
    rightHandAnchor.originFromAnchorTransform, rightHandIndexFingerTip.anchorFromJointTransform
).columns.3.xyz


let indexFingersDistance = distance(originFromLeftHandIndexFingerTipTransform, originFromRightHandIndexFingerTipTransform)
let thumbsDistance = distance(originFromLeftHandThumbTipTransform, originFromRightHandThumbTipTransform)


// Heart gesture detection is true when the distance between the index finger tips centers
// and the distance between the thumb tip centers is each less than four centimeters.
let isHeartShapeGesture = indexFingersDistance < 0.04 && thumbsDistance < 0.04
if !isHeartShapeGesture {
    return nil
}


// Compute a position in the middle of the heart gesture.
let halfway = (originFromRightHandIndexFingerTipTransform - originFromLeftHandThumbTipTransform) / 2
let heartMidpoint = originFromRightHandIndexFingerTipTransform - halfway


// Compute the vector from left thumb knuckle to right thumb knuckle and normalize (X axis).
let xAxis = normalize(originFromRightHandThumbKnuckleTransform - originFromLeftHandThumbKnuckleTransform)


// Compute the vector from right thumb tip to right index finger tip and normalize (Y axis).
let yAxis = normalize(originFromRightHandIndexFingerTipTransform - originFromRightHandThumbTipTransform)


let zAxis = normalize(cross(xAxis, yAxis))


// Create the final transform for the heart gesture from the three axes and midpoint vector.
let heartMidpointWorldTransform = simd_matrix(
    SIMD4(xAxis.x, xAxis.y, xAxis.z, 0),
    SIMD4(yAxis.x, yAxis.y, yAxis.z, 0),
    SIMD4(zAxis.x, zAxis.y, zAxis.z, 0),
    SIMD4(heartMidpoint.x, heartMidpoint.y, heartMidpoint.z, 1)
)
return heartMidpointWorldTransform
```

