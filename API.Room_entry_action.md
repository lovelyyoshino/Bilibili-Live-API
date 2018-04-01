## 查看抽奖状态

#### 调用地址

https://api.live.bilibili.com/room/v1/Room/room_entry_action

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|POST|string|发布帐号(必须和 Cookies 帐号一致)|
|room_id|true|POST|string|活动房间|
|platform|true|POST|string|pc|
|csrf_token|true|POST|string|eb084644ba03aa194ac75cf6cc68c65f|

