eros 项目中依赖只有2种，一种是 `JavaScript 依赖`，一种则是 `App SDK 相关依赖`。

## JavaScript 依赖
`JavaScript 依赖`可以参考 package.json：

```js
"dependencies": {
	"eros-widget": "^1.0.0",
	"weex-ui": "^0.3.13beta2"
},
"devDependencies": {
	"babel-core": "~6.25.0",
	"babel-plugin-component": "^1.0.0",
	"babel-plugin-transform-runtime": "~6.23.0",
	"babel-preset-env": "~1.5.2",
	"babel-preset-stage-0": "^6.24.1",
	"babel-register": "^6.22.0",
	"eslint": "^4.17.0",
	"eslint-plugin-html": "^4.0.2",
	"eslint-plugin-vue": "^4.2.2"
}
```

在 `dependencies` 中我们配置了 `eros-widget` 和 `weex-ui`，`weex-ui` 我们知道是非常优秀的组件库，那么 `eros-widget` 是什么？

> `eros-widget` 是对我们 eros-sdk 拓展出来的 module 的二次封装，使我们直接在 `Vue 实例`使用，也就是 this 上调用，eros-widget 不是必须的，但他可以帮我们间接的稳定 eros-sdk，所以建议还是使用 widget 拓展的方法。

具体可以参考 [eros-widget 文档部分](/zh-cn/eros_widget)。

由于 weex 和 eros 的开发差异性， weex-ui 中的 demo 是需要进行小部分修改即可运行的，基本都是路径的问题，开发时可以自己看脚手架输入日志来解决。

#### 更新 `eros-widget`：

``` 
$ cnpm i eros-widget -S
```

## App SDK 相关依赖
* Eros工程基础库（必须依赖）
  * 拓展的 weex 不具有的 `module`。
  * 拓展的 weex 不具有的 `component`。
  * 拓展的`简易更新逻辑`。
  * 内置了 `WeexSDK`。
* 其他插件库（选择依赖） 
  * 微信分享；
  * 微信授权登录；
  * 微信支付；
  * 高德地图（封装了Weex-Amap);
  * 持续扩展中； 

#### **iOS 更新方法**

> iOS 工程中将Eros基础库，及其他插件都封装成了pod库，采用版本管理，便于大家集成使用及版本更新；<br>
> （pod简介：全名[CocoaPods](https://cocoapods.org/)，是iOS端的第三方库管理工具，类似 `npm`包管理工具）

下面以 Eros 基础库 BMBaseLibrary 为例说明一下升级依赖库的步骤：

* 打开iOS目录`工程目录/platforms/ios/WeexEros`，编辑Podfile文件，修改版本号（即 tag对应的版本号），库每次升级后都会有相关说明文档，如需升级将 tag 修改为对应的版本即可；
	```ruby
	def common
    	...忽略其他库的引用
       #Eros iOS 基础库
    	pod 'ErosPluginBaseLibrary', :git => 'https://github.com/bmfe/eros-plugin-ios-baseLibrary.git', :tag => '版本号'
	end
	target 'WeexEros' do
    	common
	end
	```

* 在终端中`cd`到此目录下执行 `pod update`，就会重新拉取最新版本的文件，等待命令执行完毕即可；


#### **Android 更新方法**

* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/`，进入对应的库目录切换 `tag`,或者 直接 使用 `git pull` 更新代码即可。

