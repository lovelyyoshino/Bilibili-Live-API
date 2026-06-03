## PC活动检测

> 当前标记：历史/已失效。README 已将“活动PC检测抽奖”标为已失效；该接口还要求 Cookie，默认验证脚本不会调用。当前安全边界见 `API.legacy.safety.md`。

#### 调用地址

http://api.live.bilibili.com/activity/v1/Raffle/check

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|GET|string|发布帐号(必须和 Cookies 帐号一致)|
|raffleId|true|GET|int|活动轮次|
