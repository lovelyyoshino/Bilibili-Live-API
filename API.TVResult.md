##电视礼物抢到结果返回

#### 调用地址

https://api.bilibili.com/gift/v3/smalltv/notice

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookies|false|POST|string|发布帐号(必须和 Cookies 帐号一致)|
|type|true|POST|string|small_tv|
|roomid|true|POST|string|房间号|
|raffleId|true|POST|string|活动礼物编号|
