

## eros消息推送（个推）配置流程

### 基本配置

*  首先，你需要按照[个推文档](http://docs.getui.com/getui/start/ios/)去个推申请一个账号。最后获取引用配置中的appId,appKey和appSecret。

*  然后你需要修改你应用内的native.js配置，

	```
	'getui': {
        'enabled': 'true',
        'appId': <yourGetuiAppid>,
        'appKey': <yourGetuiAppKey>
        'appSecret': <yourGetuiAppSecret>
    },
	```

* 将配置写入你的原生端,在脚手架目录下执行

	```
	eros pack --all
	``` 
* 这个时候，当你用真机启动应用的时候，你的cid和deviceToken就会传注册到个推官网。也就是说恭喜你，你现在可以接收到推送了

###个推官网后台推送测试

* 进入你个推账号的开发者中心，然后选择你创建的应用点击创建推送。

* 进入后选择透传消息（具体的差别可以看个推官方文档，最大的差别就是ios只收得到透传消息）。

* 这里比较关键，因为原生端在收取站内推送的时候做了特殊处理，所以你需要传的消息格式是一个

	```	
	{"payload":<json字符串>}
	```
	
	所以，你可以直接把下面贴到个推官网的消息内容里
	
	```	
	{"payload":"{\"aps\":{\"alert\":\"test\"}}"}
	```
	也可以将你要的标准json通过下列方式贴进去
	
	addGetuiJson![](https://raw.githubusercontent.com/myliuyx/source/master/addGetuiJson.jpeg)
* 下图是各参数的解释

	getui![](https://raw.githubusercontent.com/myliuyx/source/master/getui.jpg)

### 应用内如何接受

* 在demo的config目录中，有一个push.js 文件。

```
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
你可以选择把消息用弹框弹出来，也可以直接看一下控制台。有特殊处理，就在这里直接处理就好了。
	
### 后台调试
* 个推数据返回格式

```
{
	"payload":{  
		"aps":{
			"alert":"test",  
		}
	}
}
```
> {"payload":"{\"aps\":{\"alert\":\"tes11\"}}"}  可直接复制此字符串直接去个推后台透传推送

* 后端代码

```
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