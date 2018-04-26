# bmWXPay

> 功能简介：调用微信支付

## 集成方式

**iOS集成方式**

* 打开iOS目录`工程目录/platforms/ios/WeexEros`，编辑Podfile文件，添加`ErosPluginWXPay`组件的引用，添加代码如下

	```ruby
	def common
    	...忽略其他库的引用
   		# 在这里添加引用 ErosPluginWXShare
    	pod 'ErosPluginWXPay', :git => 'https://github.com/bmfe/eros-plugin-ios-wxpay.git', :tag => '0.0.1'
	end
	target 'WeexEros' do
    	common
	end
	```

* 在终端中`cd`到此目录下执行 `pod update`，等待命令执行完毕，重新运行项目，如果没有报错则说明`ErosPluginWXPay`组件集成成功；

**Android集成方式**

* 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/app`,编辑app目录下build.gradle 文件 `dependencies` 下添加引用，代码如下：

	```ruby
	dependencies {
		....
		compile 'com.benmu.eros:plugin-wxpay:latest.integration'
	}
	```
* 添加完后，右上角 有一个 sync now。 点击 等待同步完成没有报错证明组件添加成功

## 使用

引用Module

	```js
	var bmWXPay = weex.requireModule('bmWXPay')
	```

**API**

* 判断是否安装微信app `isInstallWXApp()` 

	> 在使用微信支付功能之前应该判断是否安装了微信app，如果没有安装应该隐藏相关功能按钮

	```js
	// 同步方法
	var result = bmWXPay.isInstallWXApp()
	```

* 初始化微信SDK `initWX('appkey')` 

	```js
	bmWXPay.initWX('appkey')
	```
* 支付 `pay(info,callback)` 

	> 调用微信支付之前所需参数应用有服务器端先调用微信相关接口生成预支付订单，然后将app所需要参数返回，请参考[微信官方文档](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_1)。

	```js
	bmWXPay.pay({
		// 微信支付所需必要参数
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