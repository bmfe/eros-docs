# bmWXPay（1.0.0）

> 功能简介：调用微信支付

## 集成方式

**iOS集成方式**

* 打开iOS目录`工程目录/platforms/ios/WeexEros`，编辑Podfile文件，添加`ErosPluginWXPay`组件的引用，添加代码如下

	```ruby
	def common
    	...忽略其他库的引用
   		# 在这里添加引用 ErosPluginWXShare
    	pod 'ErosPluginWXPay', :git => 'https://github.com/bmfe/eros-plugin-ios-wxpay.git', :tag => '1.0.0'
	end
	target 'WeexEros' do
    	common
	end
	```

* 在终端中`cd`到此目录下执行 `pod update`，等待命令执行完毕，重新运行项目，如果没有报错则说明`ErosPluginWXPay`组件集成成功；

**Android集成方式**


* 进入Android目录`工程目录/platforms/android/WeexFrameworkWrapper/` 目录下 clone 对应的插件。

``` ruby
	git clone https://github.com/bmfe/eros-plugin-android-wxpay.git "erospluginwxpay"
```

* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/`,编辑`settings.gradle`,添加引入。
在`settings.gradle` 中 添加如下代码。

``` ruby
//这里只需要在最后添加 , ':erospluginwxpay'
include ':app',':sdk',':nexus', ':wxframework', ':erospluginwxpay'  

// 微信支付
project(':erospluginwxpay').projectDir = new File(settingsDir,'/erospluginwxpay/library-wxpay')

```

* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/app`,编辑app目录下`build.gradle` 文件 `dependencies` 下添对应 插件引用。

``` ruby
	dependencies {
		....
		//微信支付
    	compile project(':erospluginwxpay')
	}
```

* 具体Android 插件详细集成方式 您还可以参考[Android 插件依赖](/zh-cn/android_plugin_integration)


* 具体Android 相关[微信Key和签名申请参考文档](/zh-cn/android_wx_apply)

## 使用

引用Module

	```js
	var bmWXPay = weex.requireModule('bmWXPay')
	```

**API**

* 初始化微信SDK `initWX('appkey')` 

	```js
	bmWXPay.initWX('appkey')
	```
* 判断是否安装微信app `isInstallWXApp()` ，使用该方法必须先调用初始化方法

	> 在使用微信支付功能之前应该判断是否安装了微信app，如果没有安装应该隐藏相关功能按钮

	```js
	// 同步方法
	var result = bmWXPay.isInstallWXApp()
	```	

* 支付 `pay(info,callback)` 

	> 调用微信支付之前所需参数应用有服务器端先调用微信相关接口生成预支付订单，然后将app所需要参数返回，请参考[微信官方文档](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_1)。

	```js
	bmWXPay.pay({
		// 微信支付所需必要参数
    	appid:'',
	sign:'',
    	timestamp:'',
    	noncestr:'',
    	partnerid:'',
    	prepayid:'',
    	packageValue:''
	},function(resData){
		// 支付结果
		// resDate.status 说明
	 	0,   /**< 成功    */
    	-1,   /**< 普通错误类型    */
    	-2,   /**< 用户点击取消并返回    */
    	-3,   /**< 发送失败    */
    	-4,   /**< 授权失败    */
    	-5,   /**< 微信不支持    */
	})
	```
