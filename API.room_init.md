## 钓鱼检测

#### 调用地址

https://api.live.bilibili.com/room/v1/Room/room_init

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|false|GET|string|发布帐号(必须和 Cookies 帐号一致)|
|id|true|GET|string|房间id|

#### 结果为"is_hidden":false,"is_locked":false,"is_portrait":false,"encrypted":false则不是钓鱼房间
