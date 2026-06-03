## 批量关注主播

> 安全边界：这是需要 Cookie/CSRF 的 POST 关注/取消关注账号动作接口，会改变账号关系；默认验证脚本不会调用，当前仓库只把它作为历史资料和风险分类样例保留。

#### 调用地址

https://api.live.bilibili.com/liveact/attention

需要 Cookies，并验证登录状态(Cookies)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|POST|string|发布帐号(必须和 Cookies 帐号一致)|
|type|true|POST|string|1为关注，0为取消|
|uid|true|POST|string|直播房间号|
|csrf_token|true|POST|string|与 cookie 中 `bili_jct` 值一致（此处不记录真实值）|
