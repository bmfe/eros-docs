# bmPush（1.0.0）

> 功能简介：基于GeTuiSDK实现的消息推送服务；

## 集成方式

**iOS集成方式**

* 打开iOS目录`工程目录/platforms/ios/WeexEros`，编辑Podfile文件，添加`ErosPluginGeTui`组件的引用，添加代码如下

	```ruby
	def common
    	...忽略其他库的引用
    	# 在这里添加引用 ErosPluginWXShare
    	pod 'ErosPluginGeTui', :git => 'https://github.com/bmfe/eros-plugin-ios-getui.git', :tag => '1.0.0'
	end
	target 'WeexEros' do
    	common
	end
	```

* 在终端中`cd`到此目录下执行 `pod update`，等待命令执行完毕，重新运行项目，如果没有报错则说明该组件集成成功；

**Android集成方式**

* 1、打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/app/src/main`,编辑app目录下AndroidManifest.xml 文件 `application` 节点下添加引用，代码如下
```java
 <meta-data
            android:name="PUSH_APPID"
            android:value="${GETUI_APP_ID}"/>
        <meta-data
            android:name="PUSH_APPKEY"
            android:value="${GETUI_APP_KEY}"/>
        <meta-data
            android:name="PUSH_APPSECRET"
            android:value="${GETUI_APP_SECRET}"/>

        <receiver
            android:name="com.benmu.erosplugingt.GTPushReceiver"
            android:exported="false">
            <intent-filter>
                <action android:name="com.igexin.sdk.action.${GETUI_APP_ID}"/>
            </intent-filter>
        </receiver>
```
* 2、`工程目录/platforms/android/WeexFrameworkWrapper`,目录下 gradle.properties 添加 个推相关key
```java
GETUI_APPID= your app id
GETUI_APPKEY= your app key
GETTUI_APPSECRET= your app secret
```
* 3、打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/app`,编辑app目录下build.gradle 文件 `dependencies` 下添加引用，代码如下：

	```ruby
	dependencies {
		....
		 compile 'com.github.bmfe:eros-plugin-android-getui:1.0.1'
	}
	```
* 添加完后，右上角 有一个 sync now。 点击 等待同步完成没有报错证明组件添加成功

## 使用
> 在使用推送之前，首先您需要按照[个推文档](http://docs.getui.com/getui/start/ios/)去个推申请一个账号，并注册app，获取appId、appKey和appSecret，并在 `eros.native.js` 中填写相关参数

* 引用Module

	```js
	var bmPush = weex.requireModule('bmPush')
	```

**API**

* 初始化方法 `initPush(info)` 

	> 在使用推送之前，请调用此方法进行初始化。

	```js
	bmPush.initPush({
		appId: '',				// appId、appKey、appSecret 需要到个推后台获取
		appKey: '', 
		appSecret: ''
	})
	```

* 这个时候，当您用真机启动应用的时候，您的cid和deviceToken就会上传注册到个推官网。也就是说恭喜您，您现在可以接收到推送了

* Android 配置 appkey 打开Android目录`工程目录/platforms/android/WeexFrameworkWrapper/gradle.properties` 文件里
GETUI_APPID、GETUI_APPKEY、GETTUI_APPSECRET 配置key 等信息

###个推官网后台推送测试

* 进入您个推账号的开发者中心，然后选择您创建的应用点击创建推送。

* 进入后选择透传消息（具体的差别可以看个推官方文档，最大的差别就是ios只收得到透传消息）。

* 这里比较关键，因为原生端在收取站内推送的时候做了特殊处理，所以您需要传的消息格式是一个

	```	
	{"payload":<json字符串>}
	```
	
	所以，您可以直接把下面贴到个推官网的消息内容里
	
	```	
	{"payload":"{\"aps\":{\"alert\":\"test\"}}"}
	```
	也可以将您要的标准json通过下列方式贴进去
	
	addGetuiJson![](https://raw.githubusercontent.com/myliuyx/source/master/addGetuiJson.jpeg)
* 下图是各参数的解释

	getui![](https://raw.githubusercontent.com/myliuyx/source/master/getui.jpg)

### 应用内如何接受

* 在demo的config目录中，有一个push.js 文件。

```js
globalEvent.addEventListener('pushMessage', function (options) {
    modal.alert({
        message: 'hello'+JSON.stringify(options),
        duration: 0.3
    }, function (value) {
        console.log('alert callback', value)
    })
    console.log('》》》》》》》》'+JSON.stringify(options));
})

```	
您可以选择把消息用弹框弹出来，也可以直接看一下控制台。有特殊处理，就在这里直接处理就好了。
	
### 后台调试
* 个推数据返回格式

```js
{
	"payload":{  
		"aps":{
			"alert":"test",  
		}
	}
}
```
> {"payload":"{\"aps\":{\"alert\":\"tes11\"}}"}  可直接复制此字符串直接去个推后台透传推送

* 后端代码(java)

```java
AppConfig appConfig = appSelector.getAppConfig(context.getTemplate());
        Map<String, String> templateMap = context.getTemplate().getTemplateConfig().getExtConfig();
        Map<String, Object> extMap = templateMap.entrySet().stream()
                .filter(entry -> entry.getKey().startsWith("GT_"))
                .collect(Collectors.toMap(entry -> entry.getKey().substring("GT_".length()),
                        Entry::getValue));

        SingleMessage message = new SingleMessage();
        message.setOffline(true);
        message.setOfflineExpireTime(TimeUnit.DAYS.toMillis(1));
        //判断是否客户端是否wifi环境下推送，1为在WIFI环境下，0为不限制网络环境。
        message.setPushNetWorkType(0);
        TransmissionTemplate template = appConfig.getBuilder()
                .setContent(context.getTemplate().getContent())
                .setExt(extMap)
                .build();
        message.setData(template);

        //2. create target
        Target target = new Target();
        target.setAppId(appConfig.getAppId());
        target.setClientId(context.getReceiver().getCid());
        IPushResult ret = null;

        try {
            ret = appConfig.getiGtPush().pushMessageToSingle(message, target);
            logger.info("app消息推送返回结果:{}", JsonUtil.of(ret));
        } catch (RequestException e) {
            logger.error("app推送消息出现异常:{}:{}", message, ret, e);
            ret = appConfig.getiGtPush().pushMessageToSingle(message, target, e.getRequestId());
        }
        // TransmissionTemplate 相关代码
        public TransmissionTemplate build() {
            TransmissionTemplate transmissionTemplate = new TransmissionTemplate();
            transmissionTemplate.setAppId(this.appId);
            transmissionTemplate.setAppkey(this.appKey);
            transmissionTemplate.setTransmissionType(this.transmissionType);

            APNPayload payload = new APNPayload();
            payload.setBadge(1);
            payload.setContentAvailable(1);
            payload.setSound("default");
            payload.setCategory("$由客户端定义");
            payload.setAlertMsg(new APNPayload.SimpleAlertMsg(content));
            this.ext.entrySet().forEach(entry -> {
                payload.addCustomMsg(entry.getKey(), entry.getValue());
            });
            transmissionTemplate.setTransmissionContent(JsonUtil.of(payload));
            //推送模式
            if (!transmission) {
                transmissionTemplate.setAPNInfo(payload);

            }
            return transmissionTemplate;
        }

```
> 关键代码是 TransmissionTemplate 的 transmissionTemplate.setAPNInfo(payload);
message.setData(template);
把 template 当做 message 的data
