# 快乐光束

利用 Full Space 使用 ARKit 创建有趣的游戏。

## 概述

在 visionOS 中，您可以使用多种不同的框架创建有趣、动态的游戏和应用，以创建新型空间体验：RealityKit、ARKit、SwiftUI 和 Group Activities。此示例介绍了快乐光束(Happy Beam)，这是一款游戏，您和朋友可以在其中进行 FaceTime 通话并一起玩。

这个游戏的机制是：暴躁乌云在空间中漂浮，玩家通过用心形手势投射光束来进行游戏。玩家使用光束射击乌云并使其振作起来，同时记分器会记录每个玩家在振奋乌云时的表现。

[试玩视频](https://docs-assets.developer.apple.com/published/299cd298aee541a80039720b4f488cf5/Happy-Beam-overview.mp4)

## 使用 SwiftUI 设计游戏界面

visionOS 中的大多数应用都以窗口的形式启动，该窗口会根据应用的需要打开不同的场景类型。

在这里，您可以看到 Happy Beam 如何使用多个 SwiftUI 视图向玩家呈现一个有趣的用户界面，其中包括了欢迎界面、提供规则说明的教学界面、记分板和游戏结束界面。

欢迎窗口 | 教学 | 记分板 | 结束窗口
 ----- | ----- | ----- | ----- 
![img-1](https://docs-assets.developer.apple.com/published/a4a95e6a952a19f93222788b422649e2/HB-welcome@2x.png) | ![img-2](https://docs-assets.developer.apple.com/published/61d185d073ca3656c70068c897317b92/HB-instructions@2x.png) | ![img-3](https://docs-assets.developer.apple.com/published/79c888c096819fd9309e3427e1a5d05a/HB-scoreboard@2x.png) | ![img-4](https://docs-assets.developer.apple.com/published/658efc9a3068fd8f3e815d448db59ebc/HB-ending@2x.png)

下方代码中包含了应用中每个游戏阶段显示的主要视图：

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

当 3D 内容开始出现时，游戏会打开一个沉浸式空间，以在主窗口之外，玩家周围环境之中呈现内容。

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

`HappyBeam` 容器视图定义了一个名为 `openImmersiveSpace` 的依赖：

```swift
@Environment(\.openImmersiveSpace) private var openImmersiveSpace
```

稍后，当需要开始显示 3D 内容时，该视图会使用此依赖从应用的声明中打开沉浸式空间：

```swift
if gameModel.countDown == 0 {
    Task {
        await openImmersiveSpace(id: "happyBeam")
    }
}

```

## 使用 ARKit 检测心形手势

Happy Beam 应用程序使用 ARKit 支持的 visionOS 中 3D 手部追踪功能来识别中央*心形*手势。使用手部追踪需要一个处于前台的会话以及设备佩戴者的授权。它使用 `NSHandsTrackingUsageDescription` 用户信息键中的字符串向玩家解释为什么应用程序请求手部追踪权限。

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

您可以使用 ARKit 数据检测手势，其准确程度取决于您的用例和预期体验。例如，Happy Beam 可以更加严格地要求手指关节位置，使其必须非常接近心形。然而，实际上应用会提示人们摆出心形手势，并使用启发式的方法来提示手势已经足够接近心形。

下述代码检查玩家的拇指和食指是否近乎接触：

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

## 支持多种输入

若要支持辅助功能和一般用户首选项，请在使用手势跟踪作为输入形式的应用中包含多种类型的输入。

Happy Beam支持多种输入：

![img-1](https://docs-assets.developer.apple.com/published/f8a2fed7cbcfb2cc62c0a3996bee40a0/HB-interactive-hands@2x.png) | ![img-2](https://docs-assets.developer.apple.com/published/9fd7384ba167df2a7b8547c4814faf8f/HB-turret@2x.png)
----- | -----
来自 ARKit 的交互式手势手势，带有自定义心形手势。 | 拖动手势输入以旋转其平台上的静止光束。
![img-3](https://docs-assets.developer.apple.com/published/7b08dd9be3dd8a0a12386d29ebd5e654/HB-accessibility@2x.png) | ![img-4](https://docs-assets.developer.apple.com/published/846e5d397ba2a3e6e8550090b033e958/HB-controller@2x.png)
来自 RealityKit 的辅助功能组件，用于支持自定义操作，为云点加油。 | 游戏控制器支持，使通过切换控制对光束的控制更具交互性。

## 使用 RealityKit 显示 3D 内容

应用程序中的 3D 内容以资产的形式提供，您可以从 Reality Composer Pro 导出这些资源。将每个资产放置在代表沉浸式空间的 RealityView 资产中。

下面展示了Happy Beam在游戏开始时如何生成云彩，以及落地式光束投影仪的材质。因为游戏使用碰撞检测来保持得分——当它们碰撞时，光束会让脾气暴躁的云振作起来——所以你要为每个可能涉及的模型制作碰撞形状。

```swift
@MainActor
func placeCloud(start: Point3D, end: Point3D, speed: Double) async throws -> Entity {
    let cloud = await loadFromRealityComposerPro(
        named: BundleAssets.cloudEntity,
        fromSceneNamed: BundleAssets.cloudScene
    )!
        .clone(recursive: true)
    
    cloud.generateCollisionShapes(recursive: true)
    cloud.components[PhysicsBodyComponent.self] = PhysicsBodyComponent()
    
    var accessibilityComponent = AccessibilityComponent()
    accessibilityComponent.label = "Cloud"
    accessibilityComponent.value = "Grumpy"
    accessibilityComponent.isAccessibilityElement = true
    accessibilityComponent.traits = [.button, .playsSound]
    accessibilityComponent.systemActions = [.activate]
    cloud.components[AccessibilityComponent.self] = accessibilityComponent
    
    let animation = cloudMovementAnimations[cloudPathsIndex]
    
    cloud.playAnimation(animation, transitionDuration: 1.0, startsPaused: false)
    cloudAnimate(cloud, kind: .sadBlink, shouldRepeat: false)
    spaceOrigin.addChild(cloud)
    
    return cloud
}
```

## 添加同播共享对多人游戏体验的支持

您可以在 visionOS 中使用“群组活动”框架在 FaceTime 通话期间支持同播共享。Happy Beam 使用小组活动来同步分数、活跃玩家列表以及每个玩家投射光束的位置。

> 注意
> 使用 [Apple Vision Pro developer kit](https://developer.apple.com/visionos/work-with-apple/) 的开发者可以通过安装 [Persona Preview Profile](https://developer.apple.com/download/all/?q=persona) 在设备上测试空间同播共享体验。

使用可靠的渠道发送正确的信息，即使信息可能会因此而略有延迟。Happy Beam如何更新游戏模型的分数状态以响应分数消息，如下所示：

```swift
sessionInfo.reliableMessenger = GroupSessionMessenger(session: newSession, deliveryMode: .reliable)


Task {
    for await (message, sender) in sessionInfo!.reliableMessenger!.messages(of: ScoreMessage.self) {
        gameModel.clouds[message.cloudID].isHappy = true
        gameModel
            .players
            .filter { $0.name == sender.source.id.asPlayerName }
            .first!
            .score += 1
    }
}
```

使用不可靠的信使发送具有低延迟要求的数据。由于传递模式不可靠，因此某些消息可能无法到达。当通话中的每个参与者在 FaceTime 通话中选择“空间”选项时，Happy Beam 会使用不可靠模式向光束位置发送实时更新。

```swift
sessionInfo.messenger = GroupSessionMessenger(session: newSession, deliveryMode: .unreliable)
```

Happy Beam如何序列化每条消息的beam数据如下：

```swift
// Send each player's beam data during FaceTime calls where players have selected the Spatial option.
func sendBeamPositionUpdate(_ pose: Pose3D) {
    if let sessionInfo = sessionInfo, let session = sessionInfo.session, let messenger = sessionInfo.messenger {
        let everyoneElse = session.activeParticipants.subtracting([session.localParticipant])
        
        if isShowingBeam, gameModel.isSpatial {
            messenger.send(BeamMessage(pose: pose), to: .only(everyoneElse)) { error in
                if let error = error { print("Message failure:", error) }
            }
        }
    }
}
```

## 另见

- [将真实世界的环境融入沉浸式体验中](https://developer.apple.com/documentation/visionos/incorporating-real-world-surroundings-in-an-immersive-experience)
通过使应用的内容响应世界的本地形状来创建身临其境的体验。
- [你好，世界](https://developer.apple.com/documentation/visionos/world)
使用窗口、体量和沉浸式空间让人们了解地球。
- [目标视频](https://developer.apple.com/documentation/visionos/destination-video)
利用 3D 视频和空间音频提供身临其境的体验。
- [西洋镜](https://developer.apple.com/documentation/visionos/diorama)
使用 Reality Composer Pro 为您的 visionOS 应用程序设计场景。

#### 相关视频

![page-1](https://developer.apple.com/videos/play/wwdc2023/10082) | ![page-2](https://developer.apple.com/videos/play/wwdc2023/10096)
----- | -----
了解用于空间计算的 ARKit | 为空间计算构建出色的游戏
![page-3](https://developer.apple.com/videos/play/wwdc2023/10034) | ![page-4](https://developer.apple.com/videos/play/wwdc2023/10087)
打造无障碍空间体验 | 构建空间同播共享体验