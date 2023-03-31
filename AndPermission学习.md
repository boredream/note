

对于图片请求等，需要Activity中处理onActivityResult的方法来说，不好直接封装成 xx.executeXX(callback)的模式使用。



而对于权限请求的AndPermission，缺成功封装了，如何实现的呢？

https://github.com/yanzhenjie/AndPermission



怎样通用的处理ActivityResult？

原理是借助一个没有页面的BridgeActivity



如何接收ActivityResult里的结果？

发送权限时，启动一个



