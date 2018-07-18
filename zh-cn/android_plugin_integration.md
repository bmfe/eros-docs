# Android 插件集成方式

* 从github 上 clone 对应的 插件：

``` bash
// 基础库 （2个都是）
git clone https://github.com/bmfe/WeexErosFramework.git "wxframework" 
git clone https://github.com/bmfe/eros-nexus.git "nexus" 

// Amap
git clone https://github.com/bmfe/eros-plugin-android-amap.git "ErosPluginAmap" 
// 个推
git clone https://github.com/bmfe/eros-plugin-android-getui.git "erosplugingt" 
// 微信支付
git clone https://github.com/bmfe/eros-plugin-android-wxpay.git "erospluginwxpay" 
// 分享
git clone https://github.com/bmfe/eros-plugin-android-wxshare.git "erospluginumeng" 

```

>  如需要什么插件可直接 复制上面链接 clone 即可。具体版本为Tag,如果您需要使用指定版本的话可以自行切换到指定的Tag

* 进入Android目录`工程目录/platforms/android/WeexFrameworkWrapper/` 目录下 clone 对应的插件。

* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/`,编辑`settings.gradle`,添加引入。

* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/app`,编辑app目录下`build.gradle` 文件 `dependencies` 下添对应 插件引用。


> 具体操 `setting.gradle` 和 app 目录下的 `build.gradle` 文件如何 添加引用 请参考下图。 后面也会有个小例子。

setting.gradle全插件![](https://raw.githubusercontent.com/myliuyx/source/master/plugin_setting_gradle.png)



build.gradle全插件![](https://raw.githubusercontent.com/myliuyx/source/master/plugin_app_gradle_1.png)

## 添加基础库插件例子

* 进入Android目录`工程目录/platforms/android/WeexFrameworkWrapper/` 目录下 clone 对应的插件。

``` java
git clone https://github.com/bmfe/WeexErosFramework.git "wxframework" 
git clone https://github.com/bmfe/eros-nexus.git "nexus" 
```
> 首先clone 基础库，这里有2个。都clone 下来。

* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/`,编辑`settings.gradle`,添加引入。
在`settings.gradle` 中 添加如下代码。

``` java
include ':app',':sdk',':nexus', ':wxframework'

// 基础库
project(':wxframework').projectDir = new File(settingsDir,'/wxframework/eros-framework')
project(':sdk').projectDir = new File(settingsDir,'/nexus/sdk')
project(':nexus').projectDir = new File(settingsDir,'/nexus/nexus')

```

基础库settings.gradle![](https://raw.githubusercontent.com/myliuyx/source/master/plugin_setting_gradle_2.png)



* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/app`,编辑app目录下`build.gradle` 文件 `dependencies` 下添对应 插件引用。

``` ruby
	dependencies {
		....
		compile project(':nexus')
        compile project(':wxframework')
	}
```
基础库build.gradle![](https://raw.githubusercontent.com/myliuyx/source/master/plugin_app_gradle_2.png)

* 添加完后，右上角 有一个 sync now。 点击 等待同步完成没有报错证明组件添加成功

> 如果 Sync Now 出现错误 可尝试 clear 下项目 然后重新 同步下


