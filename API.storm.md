## 风暴检测

#### 调用地址

https://api.live.bilibili.com/lottery/v1/Storm

需要 Cookies，并验证登录状态(Cookies)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|GET|string|发布帐号(必须和 Cookies 帐号一致)|
|joinid|true|GET|string|风暴次数|
|color|true|GET|string|弹幕颜色|
|captcha_token|true|GET|
|captcha_phrase|true|GET|
|token|true|GET|
|csrf_token|true|GET|string|与 cookie 中 bili_jct 值一致（fce9ff23db2a8da348172da7e8972020）|
