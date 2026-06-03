## APP活动

> 当前标记：历史/已失效。README 已将“活动加入APP抽奖”标为已失效；该接口要求 `access_key` 且是 APP 活动动作，默认验证脚本不会调用。当前安全边界见 `API.legacy.safety.md`。

#### 调用地址

http://api.live.bilibili.com//YunYing/roomEvent

需要 App Key ，并验证登录状态(Access key)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|access_key|true|POST|string|发布帐号(必须和 access_key 帐号一致)|
|actionKey|true|POST|string|appkey|
|appkey|true|POST|string|秘钥|
|device|true|POST|string|设备|
|event_type|true|POST|string|活动事件（flower_rain-）|
|mobi_app|true|POST|string|手机app|
|platform|false|POST|string|平台|
|room_id|true|POST|string|房间号|
|ts|true|POST|string|时间|
