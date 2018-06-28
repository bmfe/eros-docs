# 图片配置

此部分教程主要说明如何修改Android中 App logo、启动图（Launchimage）、以及工程中一些用到的默认图片；

### APP 图标替换
* 项目的 `/platforms/android/WeexFrameworkWrapper/app/src/main/res` 目录下 有 mipmap 如下图![](https://raw.githubusercontent.com/myliuyx/source/master/8011AB9FE2D34C066BA1D4C29652CCD3.jpg)

请分别 替换掉 mipmap-hdpi 到mipmap-xxxhdpi 里面的 ic_launcher.png 图片为您app 图标文件。保持命名一样即可。

> mipmap-hdpi 到 mipmap-xxxhdpi 分别对应的是安卓手机的分辨率 分别从低分辨率到高分辨率不同的图片尺寸。 
  您的图标尺寸可以直接参考 目录里已有的图标尺寸

### APP启动图替换

* 项目的 `/platforms/android/WeexFrameworkWrapper/app/src/main/res/drawable-xhdpi` 目录下 `ic_splash.png` 文件 

### 导航栏默认的返回按钮图片

* 项目的 `/platforms/android/WeexFrameworkWrapper/nexus/nexus/src/main/res/drawable-xhdpi` 目录下  `icon_backnav.png`

### image 标签 站位图片,图片加载失败会显示这个站位图

* 项目的 `/platforms/android/WeexFrameworkWrapper/nexus/nexus/src/main/res/drawable-xhdpi` 目录下  `place_holder.png`

### 下拉刷新动画图片

* 项目的 `/platforms/android/WeexFrameworkWrapper/app/src/main/res/drawable-xhdpi` 目录下  `loadding0001.png` 到 `loadding0028.png` 图片全部替换。


