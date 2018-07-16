## 自动投币实现

#### 调用地址

https://api.bilibili.com/x/web-interface/coin/add

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookies|true|POST|string|发布帐号(必须和 Cookies 帐号一致)|
|aid|true|POST|string|赠送视频id|
|multiply|true|POST|string|数目|
|cross_domain|true|POST|string|true|
|csrf_token|true|POST|string|与 cookie 中 bili_jct 值一致（fce9ff23db2a8da348172da7e8972020）|
