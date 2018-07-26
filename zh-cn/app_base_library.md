* Eros工程基础库（包含）
  * 拓展的 weex 不具有的 `module`。
  * 拓展的 weex 不具有的 `component`。
  * 拓展的`简易更新逻辑`。
  * 内置了 `WeexSDK`。
  * ...持续优化中

Eros 基础库是必须依赖的，初始化中的模板已经添加了基础库的依赖，用户需要关心的是升级基础库，通过插件的方式升级也很简单，只需要修改一下版本号即可，集成方式中有介绍；

## 集成方式

**iOS 集成** <br>
* 打开iOS目录`工程目录/platforms/ios/WeexEros`，编辑Podfile文件，修改版本号（即 tag对应的版本号），库每次升级后都会有相关说明文档详情请看页面最下方的 Change Log，如需升级将 tag 修改为对应的版本即可；
	```ruby
	def common
    	...忽略其他库的引用
       #集成 Eros iOS 基础库
    	pod 'BMBaseLibrary', :git => 'https://github.com/bmfe/Benmu-iOS-Library.git', :tag => '版本号'
	end
	target 'WeexEros' do
    	common
	end
	```

* 在终端中`cd`到此目录下执行 `pod update`，就会重新拉取最新版本的文件，等待命令执行完毕即可；

**Android 集成**

* 进入Android目录`工程目录/platforms/android/WeexFrameworkWrapper/` 目录下 clone 对应的插件。

``` java
	git clone https://github.com/bmfe/WeexErosFramework.git "wxframework" 
	git clone https://github.com/bmfe/eros-nexus.git "nexus" 
```

> 具体版本为Tag,如果您需要使用指定版本的话可以切换到指定的Tag.


* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/`,编辑`settings.gradle`,添加引入。
在`settings.gradle` 中 添加如下代码。

``` java
include ':app',':sdk',':nexus', ':wxframework'   //(这里只需要添加 app 后面的 既 ：,':sdk',':nexus', ':wxframework' )

// 基础库
project(':wxframework').projectDir = new File(settingsDir,'/wxframework/eros-framework')
project(':sdk').projectDir = new File(settingsDir,'/nexus/sdk')
project(':nexus').projectDir = new File(settingsDir,'/nexus/nexus')

```

* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/app`,编辑app目录下`build.gradle` 文件 `dependencies` 下添对应 插件引用。

``` java
	dependencies {
		....
		compile project(':nexus')
        compile project(':wxframework')
	}
```

* 具体Android 插件详细集成方式 您还可以参考[Android 插件依赖](/zh-cn/android_plugin_integration)

## Change Log
**Android 1.0.8** 2018.07.26<br>
* [bugfix-Android]`tabbar` 多页面 `watchIndex` 只有一个生效问题
* [bugfix-Android]`tabbar` 生命周期触发不正常问题。
* [bugfix-Android] 解决issue[#248][#246]
* [bugfix-Android] 解决部分页面内存泄漏问题
* [bugfix-Android] 优化图片加载过多 的OOM 问题。
* [feature]新增 `web` 标签支持本地html

**Android 1.0.7** 2018.07.18<br>
* [bugfix]加载本地`html`参数丢失的问题；
* [bugfix-Android]`input`标签动态切换`type = password`不生效；
* [bugfix-Android]从后台进入app、tabbar上所有页面都会触发生命周期，应该只有当前显示的页面触发；
* [bugfix-Android]`image`标签动态设置高度图片变形；
* [bugfix-Android]加载本地`html`参数丢失的问题；
* [feature]`bmTabbar` 新增监听切换页面方法、获取当前下标方法、及动态修改tabbar配置信息。[文档请戳](https://bmfe.github.io/eros-docs/#/zh-cn/eros_sdk_module?id=bmTabbar)；
* [feature-Android]更新`jsbundle`添加弹窗提示，行为保持跟iOS一致；
* [feature]新增`bmBundleUpdate`module，开发者可以自定义更新`jsbundle`逻辑。[文档请戳](https://bmfe.github.io/eros-docs/#/zh-cn/eros_sdk_module?id=bmBundleUpdate)；
* [feature]`weex.config.eros`中添加`tabbarHeight`原生`TabBar`高度参数；
* [feature]`bmImage`module 新增 `scanImage()`方法，识别图片二维码。[文档请戳](https://bmfe.github.io/eros-docs/#/zh-cn/eros_sdk_module?id=bmimage)；感谢 [scholar-ink](https://github.com/scholar-ink)提供pr；
* [feature]`bmRouter`module 添加 `claerHomePage()` 方法清除之前设置的首页;
* [feature]新增友盟统计插件。[使用文档请戳]


**iOS 1.2.8** 2018.07.18<br>
* [bugfix]加载本地`html`参数丢失的问题；
* [bugfix]`mediator.js`中使用`bmRouter`跳转页面无效;
* [feature]`bmTabbar` 新增监听切换页面方法、获取当前下标方法、及动态修改tabbar配置信息。[文档请戳](https://bmfe.github.io/eros-docs/#/zh-cn/eros_sdk_module?id=bmTabbar)；
* [feature]新增`bmBundleUpdate`module，开发者可以自定义更新`jsbundle`逻辑。[文档请戳](https://bmfe.github.io/eros-docs/#/zh-cn/eros_sdk_module?id=bmBundleUpdate)；
* [feature]`weex.config.eros`中添加`tabbarHeight`原生`TabBar`高度参数；
* [feature]`bmImage`module 新增 `scanImage()`方法，识别图片二维码。[文档请戳](https://bmfe.github.io/eros-docs/#/zh-cn/eros_sdk_module?id=bmimage)；感谢 [scholar-ink](https://github.com/scholar-ink)提供pr；
* [feature]`bmRouter`module 添加 `claerHomePage()` 方法清除之前设置的首页;

**iOS 1.2.7** 2018.07.04<br>
* [bugfix]修复debug按钮不兼容iPad8.0系统的问题；感谢 zhongdong提的pr；
* [bugfix]修复原生tabbar设置上边栏颜色不生效的问题； 感谢 zhongdong提的pr；
* [feature]<image>标签支持 base64图片格式；
* [feature]$router.toWebview 支持 bmlocal 机制加载jsbundle中 html 文件；
* [feature] <web> 标签及 $router.toWebview新增 web 与 weex 交互机制，详情请看[教程](/zh-cn/base_extend?id=web-%E4%B8%8E-weex-%E4%BA%A4%E4%BA%92)；

**Android 1.0.6** 2018.07.04<br>
* [bugfix]修复fetch传递参数不是字符串时导致的崩溃问题。
* [bugfix]修复tabbar 切换不触发生命周期。
* [feature]`<image>`标签支持 base64图片格式；
* [feature]`$router.toWebview` 支持 `bmlocal` 机制加载`jsbundle`中 html 文件；
* [feature] `<web>` 标签及 `$router.toWebview`新增 web 也 weex 交互机制，详情请看[教程](/zh-cn/base_extend?id=web-%E4%B8%8E-weex-%E4%BA%A4%E4%BA%92)；

**iOS 1.2.6** 2018.06.21<br>
* [feature] 新增`bmTabbar`Module，支持设置tabbar数字角标及红点 [文档请戳](https://bmfe.github.io/eros-docs/#/zh-cn/eros_sdk_module?id=bmTabbar)；
* [bugfix]修复mediator.js中使用router跳转页面无效的问题；
* [update]优化请求失败返回错误信息的逻辑；

**Android 1.0.5** 2018.06.21<br>
* [feature] 新增`bmTabbar`Module，支持设置tabbar数字角标及红点 [文档请戳](https://bmfe.github.io/eros-docs/#/zh-cn/eros_sdk_module?id=bmTabbar)；
* [bugfix-Android]解决无法获取联系人问题；
* [bugfix-Android]解决Tabbar 无法连接 weex-debugger 问题；
* [bugfix-Android]解决导航栏 在4.4.4Androud 版本兼容性问题；
* [bugfix-Android]解决拍照 allowCrop 不生效问题 ；

**iOS 1.2.5** 2018.06.06<br>

* bmAxios 请求支持 application/x-www-form-urlencoded 表单格式;

**Android 1.0.4** 2018.05.30<br>

* Axios支持表单提交格式;
* 解决Tabbar 刷新崩溃问题;
* 修复v-model问题;
* 插件最新版本
```
 implementation 'com.github.bmfe.eros-nexus:nexus:1.0.3'
 implementation 'com.github.bmfe:WeexErosFramework:1.0.4'
```

**Android 1.0.3** 2018.05.30<br>

* 拓展原生tabBar;
* sdk恢复源码依赖;
* 解决APP启动黑屏问题;

**iOS 1.2.5** 2018.06.06<br>

* [feature] bmAxios 请求支持 application/x-www-form-urlencoded 表单格式；

**iOS 1.2.4** 2018.05.30<br>

* 拓展原生tabBar;
* router open 方法添加 backgroundColor 参数；
* axios请求返回 response header；
* bmToolModul 添加监听网络状态方法；
* 修复点击推送消息唤起App崩溃的问题（请更新推送插件）；

**iOS 1.2.3** 2018.05.18<br>

* 修复模拟器首次运行一定几率crash的问题;
* 修复热刷新后修改的页面MD5值校验失败的问题；

**iOS 1.2.2** 2018.05.11<br>

* 修复热更新后，新增加的页面提示找不到的问题;
* 优化webview加载时机，提升加载速度；
* 修复日历单选模式重复点击一天样式显示的问题；