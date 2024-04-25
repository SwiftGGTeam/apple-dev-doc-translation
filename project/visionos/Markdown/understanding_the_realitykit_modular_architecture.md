# 了解 RealityKit 的模块化架构

了解如何在 RealityKit 中将一切组合在一起。

## 概览

RealityKit 是专为构建应用程序、游戏和其他沉浸式体验而设计的 3D 框架。虽然，它是使用面向对象的编程语言和设计原则，但是 RealityKit 采用了 “实体-组件-系统” 架构范式，将应用程序中对象分为 “实体 Entity ”，“组件 Component ”，”系统 System “三种，从而避免“组合模式”的繁重设计——通过添加实例变量来持有其他对象引用从而构建对象。

> 译者注：本文中的“系统”，除非特别指明 VisionOS ，否则默认是 ECS 中的“系统 System ”，下面“创建系统以执行实体行为”小节会做详细介绍“系统”； Entity Component System ( ECS ), “实体-组件-系统”是一种软件体系结构模式，主要用于视频游戏开发中，用于表示游戏世界对象。ECS包括由数据组件组成的实体，以及在实体组件上运行的系统。ECS遵循组合优于继承的原则，这意味着每个实体不是由类型层次结构定义的，而是由与其关联的组件定义的。系统对具有所需组件的所有实体进行全局操作。

遵循 ECS 范式可以在有着各不相同的继承链的实体之间中重用同一个组件中的功能。又或者是在除了 [Entity](https://developer.apple.com/documentation/RealityKit/Entity) 之外没有共同祖先的两个对象中添加同一组件，以赋予它们相同的“行为”或“功能”。

## 从实体开始

实体在 RealityKit 中扮演核心角色。在场景中的任何一个对象都是 [Entity](https://developer.apple.com/documentation/RealityKit/Entity) 的后代，不论对象是否可见。实体可以是3D模型，几何图元（ shape primitives ），光线，又或者像是发声器（ sound emitters ）和立体触发器（ trigger volumes ）这种隐形的存在。在实体中添加组件以使组件能够存储与特定类型功能相关的附加状态。实体自身包含相对较少的状态属性：几乎所有的实体状态都存储在实体的组件中。

> shape primitives ，几何图元，在构造实体几何中，图元是简单的几何形状，例如立方体，圆柱体，球体，圆锥体，棱锥，圆环； trigger volumes ，立体触发器。是游戏开发中常用的一种机制，用于检测游戏中的物体或角色是否进入或离开某个特定区域。当物体或角色与触发器相交时，可以触发特定的事件或行为。也可以检测物体之间的碰撞，并进行相应的处理，比如计算伤害、改变生命值等。'

RealityKit 提供了一些实体类型来代表不同种类的对象。例如，[ModelEntity](https://developer.apple.com/documentation/RealityKit/ModelEntity) 代表从 .usdz 或者是 .reality 文件导入的 3D 模型。RealityKit 提供的这些实体基本上是已经有了特定类型组件的 [Entity](https://developer.apple.com/documentation/RealityKit/Entity) 。例如，在 [Entity](https://developer.apple.com/documentation/RealityKit/Entity) 的实例中添加 [ModelComponent](https://developer.apple.com/documentation/RealityKit/ModelComponent) 组件，这个“实体”就会有和 [ModelEntity](https://developer.apple.com/documentation/RealityKit/ModelEntity) 类型的实体相拥完全的功能。'

## 在实体添加组件

组件是添加到实体中的模块化构建的代码块；系统通过组件确定对哪些实体进行操作，组件同时维护系统依赖的每个实体的状态。组件只能包含验证属性值或者设置初始化状态的逻辑代码。系统是用来管理影响实体行为或者每一帧状态潜在变化的逻辑代码。为实体添加可访问性的信息，例如，向其添加 [AccessibilityComponent](https://developer.apple.com/documentation/RealityKit/AccessibilityComponent) 组件，同时根据系统需要访问的信息设置组件的相应字段，比如 VoiceOver 会读取它的 [label](https://developer.apple.com/documentation/RealityKit/AccessibilityComponent/label) 属性所包含的描述信息（“它的 label 属性”指的 AccessibilityComponent 的 label 属性）。

> 译者注：这段原文是突出介绍“组件 Component”的，但读起来有点绕，例如：“ they identify which entities a system will act on ”，“  they ”指的是组件，我把这句话的主谓宾换了位置翻译为：“系统通过组件确定对哪些实体进行操作”。这样翻译是因为通过阅读官方时例子代码理解下来：系统（ System ）是通过组件（ Component ）识别和操作实体（ Entity ）。'

请记住，一个实体一次只能持有某种特定类型组件的一个副本。例如，不能在同一个实体中添加两个可访问性组件  AccessibilityComponent 。如果实体已经添加了一个可访问性组件，新添加的会取代之前添加的。

## 创建系统以执行实体行为

系统 [System](https://developer.apple.com/documentation/RealityKit/System) 包含实现特定类型实体的行为，或者更新特殊类型实体的状态的代码，而 RealityKit 会在每帧调用“系统”的代码。系统使用组件存储实体特定状态，同时可以通过某种类型组件或者多种类型组件组合查询需要进行操作的实体。'

例如，游戏可以通过伤害系统监听和更新实体生命值，这样实体就可以被伤害或者被毁灭。系统会通常操作一个或多个组件，所以伤害系统通过生命值组件 记录每个实体受到的伤害值，以及每个实体在被毁灭之前还能承受多少伤害。系统也会和其他的组件交互。比如，实体也可以有保护作用的盔甲组件，这时伤害系统也会去使用该组件存储的状态值。

每帧刷新时，伤害系统 会查询有生命值组件的实体，并根据App当前的状态更新这些实体的组件。如果实体受到了过大的伤害，系统就会执行特殊的动画，或者将实体从场景中移除。

采用传统的面向对象程序设计模式，实体类会包含逻辑代码，这样会导致同样的计算被多次执行，因为每个可能会受到伤害的实体都要执行一次。而在系统中编写实体逻辑可以避免重复性的工作。不管有多少个实体需要计算伤害，只需要系统执行一次计算即可。

想了解更多关于系统创建的信息，请参照 [Implementing systems for entities in a scene](https://developer.apple.com/documentation/RealityKit/implementing-systems-for-entities-in-a-scene) 。
