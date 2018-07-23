## 风纪委员投票

#### 调用地址jury—_vote

http://api.bilibili.com/x/credit/jury/vote

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookies|true|POST|string|发布帐号(必须和 Cookies 帐号一致)|
|jsonp|true|POST|string|jsonp|
|cid|true|POST|string|id|
|vote|false|POST|string|vote|
|content|false|POST|string|空|
|likes|false|POST|string|空|
|hates|false|POST|string|空|
|attr|false|POST|int|1|
