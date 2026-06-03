## 查看抽奖状态

> 安全边界：这是需要 Cookie/CSRF 的 POST 抽奖/房间动作接口；默认验证脚本不会调用，当前仓库只把它作为历史资料和风险分类样例保留。

#### 调用地址

https://api.live.bilibili.com/room/v1/Room/room_entry_action

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|POST|string|发布帐号(必须和 Cookies 帐号一致)|
|room_id|true|POST|string|活动房间|
|platform|false|POST|string|pc|
|csrf_token|true|POST|string|与 cookie 中 `bili_jct` 值一致（此处不记录真实值）|
