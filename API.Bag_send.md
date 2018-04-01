## 礼物赠送

#### 调用地址

http://api.live.bilibili.com/gift/v2/live/bag_send

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookies|true|POST|string|发布帐号(必须和 Cookies 帐号一致)|
|uid|true|POST|string|赠送用户uid|
|gift_id|true|POST|string|礼物编号 1为辣条|
|ruid|true|POST|string|接收用户uid|
|gift_num|true|POST|string|礼物数量|
|bag_id|true|POST|string|来源礼物包id|
|platform|false|POST|string|平台（pc）|
|biz_code|false|POST|string|live|
|biz_id|true|POST|string|直播间号|
|rnd|true|POST|string|当前时间|
|storm_beat_id|true|POST|string|连击数 默认 0 |
|metadata|false|POST|string|空|
|price|true|POST|string|默认为 0 ，免费的|
|csrf_token|true|POST|string|与 cookie 中 bili_jct 值一致（fce9ff23db2a8da348172da7e8972020）|
