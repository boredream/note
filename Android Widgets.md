# 介绍

https://developer.android.google.cn/develop/ui/views/appwidgets/overview?hl=en

https://developer.android.google.cn/guide/topics/appwidgets?hl=en

https://developer.android.com/develop/ui/views/appwidgets



问题：

1. app数据和widget数据如何互通
2. widget更新频率，费电情况
3. app关闭后，widget的操作

4. widget权限



桌面的widget和通知栏的播放器类的组件，都是RemoteView。

RemoteView看名字就知道是其他进程的View，不再属于当前app，由系统直接管理。

当前app和remoteView无法直接通信，需要跨进程通信（使用了广播和系统封装的一系列方法）



流程：

项目中新建 AppWidgetProvider，在其中onUpdate方法里刷新RemoteView

桌面widget创建时、updatePeriodMillis定时触发时，都会回调onUpdate



---



# widget创建

AppWidgetProvider.onUpdate 用于设置桌面widget样式，属于 system -> widget，渲染整个widget





---



# app和widget通信

## app -> widget

常规App内视图像内存，断电（关闭）后清空

而Widget这类RemoteView类似磁盘，无论App是否开启，桌面的widget都停留在上次接收到update时的样子



### 怎么刷？

参考：https://developer.android.com/develop/ui/views/appwidgets/advanced#types

略过 widget Collection 情况

1. 全量刷新

```kotlin
val appWidgetManager = AppWidgetManager.getInstance(context)
val remoteViews = RemoteViews(context.getPackageName(), R.layout.widgetlayout).also {
  setTextViewText(R.id.textview_widget_layout1, "Updated text1")
  setTextViewText(R.id.textview_widget_layout2, "Updated text2")
}
appWidgetManager.updateAppWidget(appWidgetId, remoteViews)
```



2. 局部刷新

```kotlin
val appWidgetManager = AppWidgetManager.getInstance(context)
val remoteViews = RemoteViews(context.getPackageName(), R.layout.widgetlayout).also {
  setTextViewText(R.id.textview_widget_layout, "Updated text")
}
appWidgetManager.partiallyUpdateAppWidget(appWidgetId, remoteViews)
```



### 什么时候刷？

桌面widget创建时、updatePeriodMillis定时触发时，都会回调onUpdate。或者按需手动刷。

updatePeriodMillis至少30分钟，如果不需要可以设置0



### 在哪刷？

1. app的页面里直接调用，如放在某个点击事件内

2. 利用PendingIntent，关联通知或其他widget等



实践：

~~利用广播，AppWidgetProvider 本身就是广播接收者，所以除了 onUpdate接收更新组件外，其它更新操作也可以放该类的 onReceive 下，统一管理。~~

Android31里，自定义广播接受不到？且app去更新widget可以直接context后去到AppWidgetManager直接更新即可，通过广播的话还需要intent传递数据。



## widget -> app

主要是布局上的可交互组件的回调

因为是远程视图，app创建时无法直接拿到远程的组件引用进行监听设置，所以要借助于PendingIntent

PendingIntent可以设置为启动一个Service并开启定位

> PendingIntent如果设置成广播，然后remoteView.setOnClickPendingIntent 传递给widget，等待点击时触发。这种方案虽然看起来比较合理，但widget使用时app可能是关闭的，接收不到广播



