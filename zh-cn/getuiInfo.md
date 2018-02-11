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