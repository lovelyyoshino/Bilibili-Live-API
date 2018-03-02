## 获取直播间用户信息

#### 调用地址

http://api.live.bilibili.com/gift/v2/live/receive_daily_bag

需要 Cookies，并验证登录状态(Cookies)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|GET|string|发布帐号(必须和 Cookies 帐号一致)|
|bag_name|true|GET|string|包裹名称|
