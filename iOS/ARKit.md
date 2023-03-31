## 资料

[官方ARKit文档](https://developer.apple.com/cn/documentation/arkit/)

[官方Realme文档](https://developer.apple.com/documentation/realitykit)

文档里带 `{}` 的目录都是实例代码

[官方3d model](https://developer.apple.com/augmented-reality/quick-look/)





教程： https://www.ralfebert.com/ios/realitykit-dice-tutorial/

原理：https://blog.csdn.net/yolon3000/category_10141425.html



### 第三方库

平面检测：https://github.com/maxxfrazer/FocusEntity

AR绘画：https://github.com/oabdelkarim/ARPaint

AR路径：https://github.com/Reality-Dev/RealityKit-Path-Maker

AR点超出画面方向提示：https://github.com/maxxfrazer/RKPointPin



### Demo

AR标记+LBS项目：https://github.com/ProjectDent/ARKit-CoreLocation

【重要】官方标签项目：https://developer.apple.com/documentation/arkit/content_anchors/creating_screen_annotations_for_objects_in_an_ar_experience

ARKit收集：https://github.com/kboy-silvergym/ARKit-Emperor

100天AR实现挑战：https://github.com/satoshi0212/AR_100Days

Reality收集：https://github.com/john-rocky/RealityKit-Sampler

图书馆导航：https://github.com/NiallLDY/ARLibrary



### App

8个AR app介绍：https://www.bilibili.com/video/BV1354y1473W

IKEA Place



## 调试

### 录制调试

AR需要真机，调试稍微麻烦。可以下载Reality Compose辅助调试。

手机端 Reality Compose：

1. App右上角+开始
2. 选取锚定，按需选择，如水平锚
3. 长按删除默认元素
4. 右上。。。更多-开发者，录制AR会话
5. 录制过程中如果显示方框代表检测到平面
6. 按需录制后结束，共享发送到电脑端保存

电脑端 XCode

1. Product - Scheme - Edit Scheme - Run - Options 勾选ARKit Replay data，选择视频
2. 正常运行XCode即可，但还是要连接真机，运行后会自动播放录制视频



### 打印AR信息

设置 ARView.debugOptions 可以查看相关信息。

```swift
self.debugOptions = [
  .showPhysics, // 绘制碰撞器（包围盒）和所有刚体。检测3D物体时有效。
  .showStatistics, // 显示性能统计信息
  .showAnchorOrigins, // 显示ARAnchor位置
  .showAnchorGeometry, // 显示ARAnchor的几何形状
  .showWorldOrigin, // 显示世界坐标系原点位置和坐标轴
  .showFeaturePoints // 显示特征点云
]
session.run(config)
```



## 概念

ARKit 用于记录AR的基础信息，位置、会话、探测信息等

RealityKit 用于渲染实体，正方体、自定义udsz对象等



### ARKit

https://developer.apple.com/documentation/arkit/

* 运动跟踪：实时跟踪用户设备在现实世界中的运动，可以实时获取到的用户设备姿态信息。
* 场景理解：平面检测。依赖于运动跟踪和图像分析等技术。
* 渲染：不直接支持，需要结合RealityKit、SceneKit、Metal等



#### ARSession

会话。管理AR应用的状态和整个生命周期，负责动作捕捉、控制摄像、图像分析。



##### WorldOrigin

世界中心。默认情况下，会以session开始时的初始方向和位置为世界中心点和方向轴。

* x 红色，横向，左到右
* y 绿色，纵向，下到上
* z 蓝色，深度，远到近

由于有水平仪的存在，y的方向几乎不会改变。x、z 方向会根据手机起始位置变化



##### ARConfiguration 

会话配置。非必要的选项慎用，会增加性能消耗。



[ARWorldTrackingConfiguration](https://developer.apple.com/documentation/arkit/arworldtrackingconfiguration) 现实世界追踪配置，最常用。

用于追踪现实世界环境位置。

* [`planeDetection`](https://developer.apple.com/documentation/arkit/arworldtrackingconfiguration/2923548-planedetection) 探测水平、垂直平面
* [`detectionImages`](https://developer.apple.com/documentation/arkit/arworldtrackingconfiguration/2941063-detectionimages) 探测二维图片
* [`detectionObjects`](https://developer.apple.com/documentation/arkit/arworldtrackingconfiguration/2968177-detectionobjects) 探测三维物体
* 通过Ray casting 光线投射查找与设备屏幕上的触摸点相对应的真实世界特征的 3D 位置。

一般探测2D图像、3D对象、人体等都会使用其他配置，其中会做进一步优化。

使用前除了摄像头等权限检测，还会对配置进行支持情况检测，看芯片是否支持。



#### ARAnchor

锚点 。将虚拟元素锚定到现实空间。

```swift
// 世界中心
init()

// 检测平面中心。当焦点未检测到平面时，不会添加，进队列，直到检测到时才延迟触发添加操作
init(plane)

```



### RealityKit 

https://developer.apple.com/documentation/realitykit

渲染框架，基于Metal。



#### ARView

AR渲染视图入口。

每个ARView都会自动创建一个对应的场景Scene对象。而锚点Anchor和渲染对象Entity都需要自行添加。

![AR结构图](https://docs-assets.developer.apple.com/published/c54620eff873984d5217d35fecb71e70/ARView-1@2x.png)



#### Scene

场景。可以添加多个锚点。



#### Entity

<img src="/Users/lcy/Documents/note/iOS/ARKit.assets/image-20220916133855300.png" alt="image-20220916133855300" style="zoom:70%;" />

实体。

实体间可以互相包含

都具有 Synchronization（？） 和 Transform（坐标旋转等）属性

特殊子类具有不同特殊属性，且每个属性有对应的协议实现 HasXXX，可以按需自定义Entity





##### AnchorEntity

锚点实体。可以添加到Scene场景里，锚点根据需要可以是探测到的Pane平面或Image图片或直接坐标等



##### ModelEntity

模型实体。用于实际渲染虚拟物品。此外还具有实体的Collision碰撞、Physics物理属性等





#### Component

组件。可以是展示也可以是行为，用于按需组成Entity。

如ModelEntity实现了 HasModel协议，则会对应提供一个 ModelComponent需要自定义，

同理 HasCollision会对应一个CollisionComponent





#### 协议

##### HasAnchoring

可以被加入到scene中



##### HasModel

可以提供meshes结构，和materials材质



##### HasPhysics

可以提供物理属性和行为



##### HasCollision

可以提供物理碰撞属性。

如果要实现点击屏幕命中实体，则除了渲染物体外还要实现该协议具有碰撞体积。





### ARCoaching

用于指引用户完成AR识别准备工作。



### ARSCNView

SceneKit是苹果的一个3D框架，ARSCNView用于把该框架混合进AR部分。

大部分时候直接使用RealityKit

### ARSKView

SpriteKit是苹果的一个2D框架，ARSKView用于把该框架混合进AR部分。

大部分时候直接使用RealityKit



### Transform

坐标系系统，transform包含位置、旋转、缩放等信息



#### 坐标系表示

苹果使用一个4x4的矩阵表示这些信息。[介绍文章](https://medium.com/macoclock/augmented-reality-911-transform-matrix-4x4-af91a9718246)



#### 坐标系基准

AR世界有个WorldOrigin原点，anchor等无parent的transform就是基于这个原点的

anchor entity上添加 child entity后，child的transform是针对parent来说的，默认位置和角度偏移量等都是0。同时也可以position / transform (relateTo: nil) 获取无parent，即获取基于世界原点的坐标信息





## 详细技术介绍

### 运动跟踪

ARKit使用VIO（Visual Interial Odometry 视觉惯性里程计）和IO（Interial Odometry 惯性里程计）进行运动跟踪。

#### IO

依赖传感器（加速度计和陀螺仪）数据分析。

精度高，是运动跟踪主力，但还是有一定偏差。

#### VIO

依赖摄像采集的图像分析。

性能消耗高，但基于画面可以对IO进行运动跟踪的补偿校准。

##### 原理

VIO原理是从摄像采集的图像中提取特征点，通过对比不同画面下特征点推断设备的运动情况。使用VIO时设备需要进行位置移动（x、y、z方向均可，纯旋转运动无法达到目的）这样才能通过画面进行空间三角计算。

随着用户对现实世界的探索，一些不稳定的特征点被剔除，一些新的特征点会加入，并逐渐形成稳定的特征点集合，这个特征点集合在ARKit中被称为世界地图（World Map），世界地图的坐标原点为ARKit初始化时的设备位置，世界地图就是现实世界在ARKit中的数字表示。

##### 问题

通过以上原理也可以发现一些限制

1. 摄像采集图像偶尔因遮挡等原因未收集到，不会影响运动跟踪。但中断过长就会有问题，

2. VIO依赖图像特征点，所以大白墙、弱光环境、模糊等情况下，无法提取特征点，影响跟踪质量。
3. IO和VIO不一致时会导致跟踪问题，如电梯内IO变化，VIO周围画面可能不变化。



射线检测

https://davidwang.blog.csdn.net/article/details/108209015



### 手势

手指是针对ARView的，但可以在手势回调中，判断点击是否命中了模型



```swift
// 直接支持 1指平移、2指缩放 
uiView.installGestures(for: appendModelEntity)

// 注意，手势的支持需要有碰撞体积存在的前提。
modelEntity.collision = CollisionComponent(shapes: [ShapeResource.generateBox(size: [0.1, 0.1, 0.1])])
```



如果是自定义的usdz模型，无法直接使用手势，可以采用外面套一个boxShape的方式实现。





### 事件



### LiDAR

需要硬件支持。可以有效弥补只依赖VIO无法采集白墙、反光物体等表面的深度信息。



### 持久化

保存和重新加载数据



保存哪些？

会保存摄像识别的整个World信息，包括各种特征点、锚点

```swift
func saveWorldMap() {
    self.session.getCurrentWorldMap { worldMap, error in
        // 保存worldMap
    }
}
```



如何加载？

初始化session时，设置配置的initialWorldMap为之前保存的世界信息，然后run需要用 .resetTracking 配置

session运行后，会识别周围真实环境，直到找到和inital匹配的世界地图，然后回调delegate里对应session方法

```swift
func loadWorldMap() {
    // get WorldMap
    configuration.initialWorldMap = worldMap
    self.session.run(configuration, options: [.resetTracking, .removeExistingAnchors])
}

func session(_ session: ARSession, didAdd anchors: [ARAnchor]) {
  // 手动add 和 重定位恢复后都会回调本方法
}
```



问题

恢复后的anchors是ARAnchor类型（属于ArKit），但



### 多人协作

https://github.com/IamMrandrew/arkit-multipeer-connectivity-swiftui





## 最佳实践



### 实体数据和AREntity的绑定

方法1.

实体数据，其中持有AREntity变量

持久化保存时只保存实体数据基础，恢复时利用信息重新生成AREntity再加入到AR世界



方法2.

自定义对象，实现Entity，然后自定义

持久化保存时如何保存？







### 数据保存和恢复

方案一：常规方式，1个锚点 + 1个模型

然后和自定义数据 anchor.name = data.name 绑定保存。

但恢复的时候，需要重新识别ARWorldMap，找到每锚点后，才能挨个重新恢复模型。这意味着视野外等未识别的锚点，无法一次性全部载入。

宜家Place这样家里摆多个家具的，一般锚点是基于地板平面，这种情况下桌子、地上环境是可能稍微变化的，且家具位置也不需要那么精确的要求，偏移了会更难受，这种情况下可能1个锚点+1个模型的方式更好。



方案二：图片单锚点 + 基于锚点设置不同偏移量位置模型

所以对于超市地图导航这种，是否可以设置一个固定锚点，然后在上面添加多个不同位置的模型，这样一次性恢复多个模型，且可以很好的保证他们的相对位置。但任何锚点都需要摄像探测到时，才会生效。所以及时基于该锚点在镜头外位置添加实体，但镜头一转过去，因为锚点无法定位了，该物体也不会显示了。所以需要用固定图片锚点+设置世界中心的方式，详细见下文。



方案三：不保存ARWorldMap，每次基于图片重置WorldOrigin时，重新添加anchors

理由是initWorldMap 需要匹配到保存之前的特征点，才能继续session，这个需要时间且可能会失败？

如果在数据中直接保存transform信息，则直接重新生成anchor和model entity



问题：transform是SIMD类型，无法直接encode保存，需要特殊处理





### 图像探测

资源文件新建专门的AR Resource Group，拖入图片，并点击图片右侧设置真实物理大小

然后设置图片探测配置

```swift
guard let referenceImages = ARReferenceImage.referenceImages(inGroupNamed: "AR Resources", bundle: nil) else {
    fatalError("Missing expected asset catalog resources.")
}

let config = ARWorldTrackingConfiguration()
config.detectionImages = referenceImages
config.maximumNumberOfTrackedImages = 1
return config
```



探测到图片后，会在ARSessionDelegate里回调

```swift
func session(_ session: ARSession, didUpdate anchors: [ARAnchor]) {
  guard let imageAnchor = anchors.first as? ARImageAnchor,
  let _ = imageAnchor.referenceImage.name
  else { return }

  // TODO: xxxx
}
```



注意，如果希望在锚定的图片上防止虚拟物体，只要放置一次即可，上面的session回调会在每次探测到图片时高频触发，注意不要重复添加。



### 图像探测作为世界锚点

图片、平面灯任何锚点都是可移动的。及时现实世界里图片or物体保持不变，但基于该锚点设置的物体也必须在镜头探测到锚点时才会展示。所以需要用固定图片锚点+设置世界中心的方式。这也是 [ARKit官方建议方案](https://developer.apple.com/documentation/arkit/content_anchors/detecting_images_in_an_ar_experience)

重置WorldOrigin后，就可以基于世界中心坐标去设置锚点，然后添加物体了。不用再添加 detect类的锚点。



问题：每次探测到图片时，根据角度不同，位置都会有误差

方法：用多张（4个）图共同定位出中心点



问题：世界锚点的y绿色纵轴，一直是垂直的，只有xz可能变化，但如果直接把图片transform作为直接锚点，y则是垂直图片的一条线。和现实世界纵轴不同。

方法：？？？



问题：重制直接锚点后，如何添加世界坐标锚点？

方法1：常规平面探测，添加的时候把平面anchor的transform转成world的

https://stackoverflow.com/questions/63652315/arkit-4-realitykit-converting-local-transform-to-world-transform



方法2：直接用当前镜头的transform信息，arView.currentFrame.matrix





问题：重新载入ARWorldMap后，会恢复 anchors: [ARAnchor]，但不会恢复 anchor entities: [RealityKit]

方法：先创建ar anchor，然后用它去创建anchor entity 添加



### 世界固定坐标点

贴在墙上地上的图片，可以作为相对稳定的锚点。但用户接入成本高，还需要打印贴上？

是否有更好的稳定的世界坐标点

https://mobidev.biz/blog/arkit-guide-augmented-reality-app-development-ios



1.拍照作为图片锚点？



2.雷达扫描3D物体作为锚点？



3.RoomPlan作为锚点？



4.首次使用图片锚点，之后保存WorldMap信息，然后每次打开重载？慢？





### 物体移动

installGesture的移动只能xz轴

如何y轴移动呢？

https://github.com/maxxfrazer/RealityUI#gestures





xz轴移动需要设置边界，防止拖动太远了



移动后保存问题：

installGesture的物体移动是相对于parent anchor的translation，所以移动结束后需要更新自己的世界坐标



### 镜头位置添加锚点

只能添加世界锚点，一般添加在镜头深度负数一点的位置

```swift
    func addModelEntity(_ model: SignModel) {
        guard let modelEntity = model.modelEntity else {
            return
        }
        
        // 使用当前摄像头的位置+角度，在z-0.5 即镜头正前方放置物体（基于世界坐标）
        let cameraAnchorEntity = AnchorEntity(world: cameraTransform.matrix)
        let positionPlaceHolerEntity = Entity()
        positionPlaceHolerEntity.position.z -= 0.5
        cameraAnchorEntity.addChild(positionPlaceHolerEntity)
        scene.addAnchor(cameraAnchorEntity)
        
        // 但方向不希望随着camera，而是和世界保持一致
        // 所以用目标物体位置重新创建一个（relativeTo: nil获取世界位置，默认获取相对parent位置）
        let anchorEntity = AnchorEntity(world: positionPlaceHolerEntity.position(relativeTo: nil))
        anchorEntity.addChild(modelEntity)
        scene.addAnchor(anchorEntity)
        
        scene.removeAnchor(cameraAnchorEntity)
    }
```



### 添加物品预览

物品随着镜头移动，确定后才加入。

如果是PlaneAnchor，则随着镜头移动时还需要平面吸附，类似于FocusEntity、自带AR尺子





### 坐标系转换



提取位置信息

```swift
var position = SIMD3(frame.camera.transform.columns.3.x,
                     frame.camera.transform.columns.3.y,
                     frame.camera.transform.columns.3.z)
```



### 列表数据更新UI

一般来说使用适配器模式，Android RecycleViewAdapter，iOS是TableView DataSource。

如果是AR中，集合数据希望对应更新多个Entity如何处理呢？



方式1.临时变化增量（actionData）

需要根据数据修改UI，且再把变量增量加入数据集合



方式2.数据携带增量信息（data.action）

需要根据数据集合里的增量信息修改UI，最后清空数据的增量信息



方式3.自定义数据实现Entity

直接数据+Entity作为一体。

需要考虑数据持久化问题，恢复时要重新创建Entity加入Scene



## 苏格拉底问

### 什么是AR？

现实世界里放置虚拟物体



### 如何把虚拟物体和现实物体关联？

利用摄像头监测到现实物体，然后在目标上设置锚点，再在锚点上放置虚拟物体

监测目标：

* Pane平面
* Image图片
* Face人脸
* Body人体
* Object三维物体

与之对应的，可以在探测的目标上设置锚点：

* ARPlaneAnchor 平面锚点
* ARMeshAnchor 物体形状锚点
* ARImageAnchor 图片锚点
* ARObjectAnchor 三维物体锚点
* ARBodyAnchor 人体锚点
* ARFaceAnchor 人脸锚点
* ARGeoAnchor 地理位置（经纬度）锚点。只支持部分城市: [支持城市列表](https://developer.apple.com/documentation/arkit/argeotrackingconfiguration)



### 检测目标的原理？

传感器：陀螺仪测方向，加速度器测位移，雷达测深度

特征点：摄像头采集画面里的特征点，在镜头移动时经过对比等各种算法推测





## 实战项目

### 白板

- [x] 桌面放置物体
- [x] 放置文字物体
- [ ] 基于平面识别，自定义设置世界中心 [平面识别绘制](https://developer.apple.com/documentation/arkit/content_anchors/tracking_and_visualizing_planes)
- [ ] 放置带文字的卡片
- [ ] 文字卡片内容可自定义、可修改
- [ ] 箭头
- [ ] 吸附操作（参考iOS AR尺子）
- [ ] 物体追踪（追踪目标，移动tag）
- [ ] 多人协作



### 家庭物品标记

- [x] 放置球体，检测平面上
- [x] 放置球体，在摄像头前的位置上
- [x] 球体可以根据手势移动
- [x] 球体的y-axis移动
- [x] 球体移动中的样式
- [x] 球体可以编辑，自定义颜色和标签
- [x] 保存和载入ModelEntity
- [x] 屏幕外物体追踪 https://github.com/maxxfrazer/RKPointPin
- [ ] 【重】RoomPlan结合
- [x] 卡片记录页







