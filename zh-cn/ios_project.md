## 工程简介

打开 iOS 工程首先看一下主工程目录，你会发现项目非常的清爽，只包含几个必要的文件，连一个 `UIViewController`都不存在，我们将代码都封装到 Pods 库 BMBaseLibrary 里面，通过 Pods 的方式加载进来。在工程里还有三个文件：

```js
eros.native.json 		// 配置文件: App 启动的时候会从加载里面的配置信息
bundle.zip			// js bundle，iconfont 资源包：App 首次启动会将相关资源解压到沙盒目录中，之后会从该目录加载 js 资源文件；
bundle.config		// js bundle 版本信息；描述当前 js 的版本，作为版本管理文件；

这三个文件不需要我们手动管理，在前文 内置打包 一章中有介绍，打包后会自动替换这三个文件；
```
接下来看一下工程是如何引用 WeexSDK、及 BMBaseLibrary Pods 库的；

## Podfile

看一下 Podfile 就一目了然了

```js
// 引入的其他pods库这里就不列举了，请看源文件
pod 'WeexSDK', :path=>'./WeexiOSSDK/'
pod 'BMBaseLibrary', :path=>'./Benmu-iOS-Library'
```

你会发现是从本地加载的 WeexSDK 及 BMBaseLibrary，在运行命令 `$ bm eros install` 后会将 WeexiOSSDK 及 Benmu-iOS-Library下载到工程中，之后再运行 `pod install` 就会将这个两个库了加载到工程中了；

## Module 扩展

`Module`是 js 与 native 交互的机制，比如像页面跳转、网络请求等一些操作，需要 native 通过扩展 `Module` 的方式提供接口供 js 调用，自定义 module 的步骤：

> 1. 自定义的module类 必须实现 WXModuleProtocol
2. 必须添加宏`WX_EXPORT_METHOD`, 它可以被weex识别，它的参数是 JavaScript调用 module指定方法的参数
3. 添加@synthesized weexInstance，每个moudle对象被绑定到一个指定的实例上
4. Module 方法会在UI线程中被调用，所以不要做太多耗时的任务在这里，如果要在其他线程执行整个module 方法，需要实现WXModuleProtocol中- (NSThread *)targetExecuteThread的方法，这样，分发到这个module的任务会在指定的线程中运行
5. Weex 的参数可以是 String 或者Map
6. Module 支持返回值给 JavaScript中的回调，回调的类型是WXModuleCallback,回调的参数可以是String或者Map

下面以网络请求 Module `bmAxios` 为例（其他 module 的实现请直接查看源码）：

> 可能 native 端的同学会有疑问，为啥名字是 `Axios`，因为前端有个著名的网络请求库就叫'Axios'，类似于 iOS 端的 `AFNetwork`,所以为了方便前端同学识别 module 的作用故起名为 bmAxios。

```js
//  头文件里面比较简单只需要 遵循 WXModuleProtocol 协议
//  BMAxiosNetworkModule.h
//
#import <Foundation/Foundation.h>
#import "BMModuleProtocol.h"

// 遵循 WXModuleProtocol 协议
@interface BMAxiosNetworkModule : NSObject <WXModuleProtocol>

@end

//  实现文件
//  BMAxiosNetworkModule.m
//
#import "BMAxiosNetworkModule.h"
#import "BMAxiosRequestModel.h"
#import "BMCommonRequest.h"
#import "BMBaseViewController.h"
#import "BMUserInfoModel.h"

@implementation BMAxiosNetworkModule

// 绑定一个 weexInstance 实例
@synthesize weexInstance;

// 将方法暴露出去，这个一定要添加，不然 js 端调不到这个方法
WX_EXPORT_METHOD(@selector(fetch:callback:))

/**
 方法实现

 @param info：js调用方法传递的参数 
 @param callback： 通过callback 将结果数据回传给 js
 */
- (void)fetch:(NSDictionary *)info callback:(WXModuleCallback)callback
{
    /* 添加判断 */
    if (![info isKindOfClass:[NSDictionary class]]) {
        WXLogError(@"js request info Error");
        return;
    }
    
    // 解析 info
    BMAxiosRequestModel *requestModel = [BMAxiosRequestModel yy_modelWithJSON:info];
    // 创建请求
    BMCommonRequest *api = [[BMCommonRequest alloc] initWithRequestModel:requestModel];
    
    [((BMBaseViewController *)weexInstance.viewController) addRequest:api];
    // 触发请求
    [api startRequestResult:^(id data) {
       
        WXLogInfo(@"request data: %@",data);
        // 将数据结果回传给 js
        if (callback) {
            callback(data);
        }
        
    }];
}

```

**注册 module**

通过调用 WXSDKEngine 中的 `registerModule:withClass`方法来注册自己的module <br> 
> 本工程中自定义的所有Module和Component都是在 `BMConfigManager.m` 文件中注册的

```js
+ (void)registerBmModules
{
    NSDictionary *modules = @{
                              @"bmAxios": NSStringFromClass([BMAxiosNetworkModule class])
                              };
    
    for (NSString *moduleName in modules.allKeys) {
        [WXSDKEngine registerModule:moduleName withClass:NSClassFromString([modules valueForKey:moduleName])];
    }
}
```

**js 调用 module**

```js
// 引用方式
var axios = weex.requireModule('bmAxios')

// 示例
axios.fetch({
	method: 'GET' // 请求类型 GET or POST
	url: 'http://xxx/xxx', // 请求api,完整地址
	header: {} // 自定义请求头requestHeader
	data: {} // 请求参数
}, function(resData){
	// resData 数据
})
```

## Component 扩展

Weex 官方已经扩展了一些比较基础的 Component，利用这些组件可以写出一些基本的页面，但在实际开发过程中，有些复杂的页面使用这些组件写起来可能会比较麻烦，或者之前 native 端已经写好的一些炫酷的原生 view 想要在weex中使用，都可以通过 Component 的方式来实现。自定义 Component 的基本步骤：

1. 新建类继承 WXComponent
2. 实现初始化方法，解析 styles、attributes
3. 实现 `loadView`方法，初始化自定义 view
4. 可以再 `viewDidLoad`方法中设置一些 view 属性等
4. 实现 `addEvent:` 方法，绑定事件
5. 通过宏 `WX_EXPORT_METHOD`将必要的方法暴露给 js
6. 如果 `attributes` 需要动态变化需要实现方法 `updateAttributes:`，重新解析 attributes
7. 通过调用 fireEvent:params 方法触发 js 绑定的事件

下面是 `BMCalenderComponent` 的示例：

```js
新建一个 BMCalendarComponent 类继承 WXComponent
头文件
//  BMCalendarComponent.h
//

#import <WeexSDK/WeexSDK.h>

@interface BMCalendarComponent : WXComponent

@end



实现文件
（我们的日历组件是基于 FSCalendar 实现的）
//
//  BMCalendarComponent.m
//
@interface BMCalendarComponent()<FSCalendarDataSource,FSCalendarDelegate>

// 暴露给 js 的方法
WX_EXPORT_METHOD(@selector(goPrve))
WX_EXPORT_METHOD(@selector(goNext))

/**
 *  初始化方法 通过
 *  @param ref          component 标示
 *  @param type         component 类型 这里是 bmCalendar
 *  @param styles       js 定义的样式
 *  @param attributes   js 定义的属性
 *  @param events       js 绑定的事件
 *  @param weexInstance 关联的 weex 实例
 */
-(instancetype)initWithRef:(NSString *)ref type:(NSString *)type styles:(NSDictionary *)styles attributes:(NSDictionary *)attributes events:(NSArray *)events weexInstance:(WXSDKInstance *)weexInstance
{
    self = [super initWithRef:ref type:type styles:styles attributes:attributes events:events weexInstance:weexInstance];
    if (self) {

        // component 中所有的 style，attribute，events 都会被传递到 Component 的初始化方法中,在这里可以解析这些信息
        _finishEvent = NO;
        self.maximumDate = attributes[@"maximumDate"];
        self.minimumDate = attributes[@"minimumDate"];
        
        if (attributes[@"selectType"]) {
            self.selectType = [WXConvert NSString:attributes[@"selectType"]];
        }
        ...
        ...
        [self fillCSSStyles:styles];
    }
    return self;
}

-(UIView*)loadView
{
	 // 初始化自定义 view
    FSCalendar *calendar = [[FSCalendar alloc] init];
	 ...
	 ...
    return calendar;
}

-(void)addEvent:(NSString *)eventName
{
	 // 解析事件 这里标示需要触发 finish 事件
    if ([eventName isEqualToString:k_finishEvent]) {
        _finishEvent = YES;
    }
}

// 实现暴露给 js 的方法
-(void)goPrve
{
    [self previousClicked];

}
// 实现暴露给 js 的方法
-(void)goNext
{
    [self nextClicked];
}

-(void)fireMsg
{
    ...
    // 触发 event
    if (_finishEvent) {
        [self fireEvent:k_finishEvent params:[self callbackParams]];
    }
}

@end

```

**注册 Component**

与注册 Module 类似，调用 WXSDKEngine 中的 `registerComponent:withClass:` 方法

```js
+ (void)registerBmComponents
{
    NSDictionary *components = @{
                                @"bmcalendar": NSStringFromClass([BMCalendarComponent class])
                                };
    for (NSString *componentName in components) {
        [WXSDKEngine registerComponent:componentName withClass:NSClassFromString([components valueForKey:componentName])];
    }
}
```
