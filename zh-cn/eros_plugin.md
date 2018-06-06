在之前的工程模板中，Eros 集成了 微信分享、微信授权登录、微信支付、高德地图、个推推送等模块(Modules)及组件(Components)，实现这些功能需要引用对应的第三方SDK，比如 Amap，WechatSDK 等，不管开发者是否使用到这些功能，最终打包的App都会集成这些三方库，导致安装包过大。为了解决这个问题，我们将工程框架进行了优化改进，将这些模块及组件从基础框架中解耦出来，并封装成了插件，可供大家根据需求自由选择集成，集成方式也非常简单，按照教程来没有原生开发基础的同学也可以轻松搞定；

## 插件开发
> 有原生开发能力的同学，可以按照以下教程来贡献你的插件

## iOS 插件教程

#### 一、开发你的功能
> 这里其实没什么好说的，主要是你根据自己的想法做你自己的开发，不过可以稍微注意以下几点。

1. 最好将你的功能统一在一个目录下，这样之后方便podspec的代码引入和方便git管理。

![](https://ws1.sinaimg.cn/large/006tKfTcgy1frtgmmp3ntj307k0bcaa9.jpg)

#### 二、自定义扩展

这里需要你参考一下[weex 官方文档扩展ios功能](http://weex.apache.org/cn/guide/extend-ios.html)

下面以扩展 Module 为例：

1. 新建一个类 `XXXModule` 继承 `NSObject`, 让该类遵循 `WXModuleProtocol` 的协议，比如下方实现的 `HMGesUnLockModule`
	
	```js
	#import <Foundation/Foundation.h>
	#import "WXModuleProtocol.h"
	@interface HMGesUnLockModule : NSObject<WXModuleProtocol>
	
	@end
	```
	
2. 实现 Module 的方法，并通过 `WX_EXPORT_METHOD` 宏将方法暴露给 js

	```js
	@synthesize weexInstance;
	// 将方法暴露出去
	WX_EXPORT_METHOD(@selector(addGesturePage:))
	
	// 实现 Module 方法
	-(void)addGesturePage:(WXModuleCallback)callback{
	    [HMUnlockView showUnlockViewWithType:YWUnlockViewCreate callBack:^(BOOL result) {
	        if(callback){
	            if(result){
	                callback(@"success");
	            }else{
	                callback(@"fail");
	            }
	        }
	    }];
	}
	```

3. 注册module。	

 >最关键的一步到了！！！！这里和官方文档不太一样。

	官方文档教我们的注册的方法是 调用 `WXSDDKEngine` 的 `registerModule:withClass:`方法
	
	```js
	[WXSDKEngine registerModule:@"hmGesUnlock" withClass:[HMGesUnLockModule class]];
	```
	
	插件化我们希望的是用户直接通过 pod 引用后不需要添加任何原生代码，js 端即可正常使用，所以我们希望 Module 或者 Component 的注册方法也可以动态完成，刚好 Weex 有对应的加载器机制，那就是`WeexPluginLoader`，可以通过 `WX_PlUGIN_EXPORT_MODULE`、`WX_PlUGIN_EXPORT_COMPONENT`两个宏来注册你扩展的 Module 或 Component，比如HMGesUnLockModule.m 文件中
	
	```js
	#import <WeexPluginLoader/WeexPluginLoader.h>
	...
	// 第一个参数为暴露给 js 端 Module 的名字，
	// 第二个参数为你 Module 的类名
	WX_PlUGIN_EXPORT_MODULE(hmGesUnlock, HMGesUnLockModule)
	```
	
	这样，你就在pod引入后自动注册，而不需要特地在你的代码中的某个地方去执行 `registerModule:withClass:` 了。
	
#### 三、上传git库
到这里，你的功能应该已经在你的demo中没有任何问题了吧？那我们可以开始插件化的第一步了。

1. 在你自己的github上面创建一个仓库。
2. 在你的项目中一连串init add commit push 。。。。（由于这里是插件教学贴，不是git使用教学贴，相关git使用方法请自行 google）
3. 好了，到这里，你已经把你的代码放到你的git上面了，已经可以找人给你刷星星了。。。。虽然人家还用不了。。。。。。


#### 四、封装 pod 库

1. 首先，先将你刚刚创建的仓库clone到你的本地
2. 创建podspec文件，在这个git目录下执行
	
	```js
	pod spec create <your podspec name>
	
	#e.g.
	pod spec create WeexHMGesUnlock
	```

3. 这个时候，你在你的目录下，就可以看到
```
WeexHMGesUnlock.podspec
```
文件了。

4. 编辑 `xxx.podspec` 文件

	```ruby
	Pod::Spec.new do |s|
	
	  s.name             = "PodTestLibrary"    #插件名称
	  s.version          = "0.0.1"             #版本号
	  s.summary          = "Just Testing."     #简短介绍，下面是详细介绍
	  s.description      = <<-DESC
	                       Testing Private Podspec.
	                       * Markdown format.
	                       * Don't worry about the indent, we strip it!
	                       DESC
	
	  s.homepage         = "github地址"  #主页,这里要填写可以访问到的地址，不然验证不通过
	  s.license          = 'MIT'              #开源协议
	  s.author           = { "你的名字" => "邮箱地址" } #作者信息
	  s.source           = { :git => "git Clone 地址", :tag => s.version.to_s }      #项目地址，这里不支持ssh的地址，验证不通过，只支持HTTP和HTTPS，最好使用HTTPS
	  s.platform     = :ios, '8.0'            #支持的平台及版本
	  s.requires_arc = true                   #是否使用ARC，如果指定具体文件，则具体的问题使用ARC
	
	  s.source_files = 'Pod/Classes/**/*'     #代码源文件地址，**/*表示Classes目录及其子目录下所有文件，如果有多个目录下则用逗号分开，如果需要在项目中分组显示，这里也要做相应的设置
	
	  s.resource_bundles = {
	    'PodTestLibrary' => ['Pod/Assets/*.png']
	  }                                       #资源文件地址
	
	  s.public_header_files = 'Pod/Classes/**/*.h'   #公开头文件地址
	  s.frameworks = 'UIKit'                  #所需的framework，多个用逗号隔开
	  s.dependency 'AFNetworking', '~> 2.3'   #依赖关系，该项目所依赖的其他库，如果有多个需要填写多个s.dependency
	
	end

	```
再详细的配置，可以自行google一下，欢迎补充啊。比如还有一些s.dependency 之类的，如果有需要可以自行配置。[可参考](https://www.jianshu.com/p/f841e248bc4f)

5. 把这个文件提交到你的github上去；
6. 在你的 master 上面打个tag吧。。。。这个动作很重要！！！这个动作很重要！！！这个动作很重要！！！<br>
**注意： tag 对应的就是库的版本号，每次修改代码，都需要更新库的版本号及重新打 tag；**

#### 五、本地引入

1. 在你需要引入的项目中把你的项目`clone`到你`podfile`所在的目录。
2. 然后在你的`Podfile`中加入

	```ruby
	 pod 'WeexHMGesUnlock', :path=>'./WeexPlugin-HMGesUnlock/'
	```

3. 执行

	```
	pod update 
	```


#### 六、插件引入


1. 在你的`Podfile`中加入

	```ruby
	# tag可以不写，默认依赖 master 分支代码
	pod 'WeexHMGesUnlock', :git => 'https://github.com/shawn-tangsc/WeexPlugin-HMGesUnlock.git', :tag => '1.0.0'
	```


2. 执行 pod update

	```
	pod update 
	```
	
#### 七、书写文档

插件开发完毕后，文档至关重要，在文档中请详细说明功能及使用方法；

## Android 插件教程

[android-eros-plugin-simple](https://github.com/bmfe/android-eros-plugin-simple)
具体 插件开发可参考此demo