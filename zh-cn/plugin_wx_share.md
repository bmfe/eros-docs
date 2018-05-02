# bmWXShare（1.0.0）

> 功能简介：基于友盟ShareSDK实现，微信分享（分享到朋友圈，微信好友）及微信授权登录；


## 集成方式
**iOS集成方式**

* 打开iOS目录`工程目录/platforms/ios/WeexEros`，编辑Podfile文件，添加`ErosPluginWXShare`组件的引用，添加代码如下

	```ruby
	def common
    	...忽略其他库的引用
    	# 在这里添加引用 ErosPluginWXShare
    	pod 'ErosPluginWXShare', :git => 'https://github.com/bmfe/eros-plugin-ios-wxshare.git', :tag => '1.0.0'
	end
	target 'WeexEros' do
    	common
	end
	```

* 在终端中`cd`到此目录下执行 `pod update`，等待命令执行完毕，重新运行项目，如果没有报错则说明`ErosPluginWXShare`组件集成成功；

**Android集成方式**

* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/app`,编辑app目录下build.gradle 文件 `dependencies` 下添加引用，代码如下：

	```ruby
	dependencies {
		....
		compile 'com.benmu.eros:plugin-wxshare:1.0.0'
	}
	```
* 添加完后，右上角 有一个 sync now。 点击 等待同步完成没有报错证明组件添加成功

## 使用

**引用Module**

```js
var bmWXShare = weex.requireModule('bmWXShare')
```

**API**

* 判断是否安装微信app `isInstallWXApp()` 

	> 在使用微信分享或授权登录之前应该判断是否安装了微信app，如果没有安装应该隐藏相关功能按钮

	```js
	// 同步方法
	var result = bmWXShare.isInstallWXApp()
	```

* 初始化友盟SDK `initUM('appkey')` 

	> 在使用之前，请先调用此方法初始化友盟SDK

	```js
	bmWXShare.initUM('友盟平台申请的appkey')
	```

* 初始化微信SDK `initWX(info)`

	> 在使用分享到微信平台功能，或者微信登录功能前，需要调用此方法来初始化微信平台；

	```js
	bmWXShare.initWX({
		appKey: 'appkey',			// 微信开发平台申请的appkey
		appSecret: 'appSecret',		// appKey对应的appSecret，
		redirectURL: '回调页面' 	 // 授权回调页面
	})
	```

* 分享：`share(info,successCallback,failedCallback)`

	```js
	bmWXShare.share({
		title:'',                   		// 分享的标题
   		content:'',                     	// 分享的文字内容
   		url: ''                         	// 分享对应的URL地址，如h5、音乐链接、视频链接、小程序的链接
   		image: '',                       	// 分享的图片url
   		path: '',							// 分享小程序用到的页面路径
   		shareType: 'Webpage'				// 分享的类型
	},function(resData){
    	// 成功回调
	},function(resData){
    	// 失败回调
	})

	// 分享平台
	platform:[
    	WechatSession,          // 微信好友
    	WechatTimeLine          // 分享至朋友圈
	]

	// 分享类型
	shareType:[
		Text,        // 文字
		Image,       // 图片
		TextImage,   // 图文
		Webpage,     // 网页
		Music,       // 音乐
		Video,       // 视频
		MiniProgram  // 小程序
	]
	```

* 微信授权登录：`authLogin(callback)`

	```js
	bmWXShare.authLogin(function(resData){	
		// 授权结果，成功的话会将所有的userInfo返回
		// console.log(resData)
	});
	```