# Android 微信相关使用

**微信官网申请 相关Key 和签名 如下图**

图1![](https://raw.githubusercontent.com/myliuyx/source/master/bmweixin_1.jpg)
图2![](https://raw.githubusercontent.com/myliuyx/source/master/bmweixin_2.jpg)
图3![](https://raw.githubusercontent.com/myliuyx/source/master/bmweixin_3.jpg)

> [微信官网](https://open.weixin.qq.com/) 自行参考图片 申请相关Key<br/> 您申请的应用签名请一定与您的包名一致，否则可能导致微信无法回调

**Android 相关配置**

1.打开Android目录工程目录/platforms/android/WeexFrameworkWrapper/app,编辑app目录下build.gradle 文件 dependencies 下添加引用，代码如下：

  dependencies {
    ......
    compile 'com.github.bmfe:eros-plugin-android-wxpay:1.1.0' // 具体版本参考最新
  }

2.打开Android目录工程目录/platforms/android/WeexFrameworkWrapper/app/src/main/java/com/benmu/wx/wxapi/下的WXEntryActivity.java和WXPayEntryActivity.java解开注释
  
3.更改Android目录工程目录/platforms/android/WeexFrameworkWrapper/app/src/main/AndroidManifest.xml

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="你的包名">
```


4.更改Android目录工程目录/platforms/android/WeexFrameworkWrapper/app/src/main/java/下com.benmu.wx为你的包名 右上角 有一个 sync now 点击根据提示修改报错的地方com.benmu.wx为你的包名

> 如果您有修改 APPLICATION_ID 请一定需要修改您的包名与APPLICATION_ID 一致，否则可能导致无法调起微信，或者无法回调的问题。

