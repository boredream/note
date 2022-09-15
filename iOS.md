start: 2022-08-01

end:



[安卓和iOS对比](http://t.zoukankan.com/WoodJim-p-14719481.html)





# XCode

需要登录Apple账号，且不能过期，设置里进行登录。



## 快捷键

先找到快捷键所对应的配置文件，在`Finder`中使用`Command`+`Shift`+`G`跳转到路径`/Applications/Xcode.app/Contents/Frameworks/IDEKit.framework/Versions/A/Resources/IDETextKeyBindingSet.plist`

常用（除了IntelliJ映射过的）

* Option + LeftClick 查看光标位置内容文档
* Command + Shift + 0 查看全部文档
* Command + 0~8 切换左侧菜单，0隐藏（preference - behavior 里可设置不同行为下窗口状态）
* Command + option + 0~2 切换右侧菜单，0隐藏
* Command + Shift + y 显示隐藏命令行
* Command + R 运行



## Debug

代码左边点击添加蓝色箭头断点，正常运行到这里会触发。

可以命令行上侧点击蓝色小箭头关闭所有断点。

可以左侧导航栏切换到断点列表，也可以断点导航栏左下角新增特殊断点，比如抓取Exception通用断点等。



进入断点后命令行左侧可以直接看到所有变量。

右侧是命令行，可以按需输入命令。

* po [变量名]。print object 打印对象
* p [变量]。print 打印



## 注释

// 或 /**/

// MARK: // TODO: // FIXME: 进行标记，然后点击顶部当前文件可以查看标签

如果想导航器看到标签，可以使用 #warning





## 真机/模拟器

XCode - Window / Devices and Simulators



## 导入第三方库

File - Add packages - 粘贴github地址 

下一步到完成，最后XCode菜单目录最下方就可以看到依赖包了



## 日志

XCode - Console里直接查看。

或电脑控制台软件，选择手机，按应用进程过滤。



# 概念

SwiftUI 状态式UI编程

SF Symbols 苹果官方的设计资源软件，提供统一的图标。



# 数据保存磁盘

先 NSKeyedArchiver / Unarchiver 把数据跟 Data 互转

然后 UserDefaults.standard.set / data 将序列化后的 Data 数据保存在本地或从本地加载



数据必须要实现 NSObject, NSCoding 或 Codeable ？

