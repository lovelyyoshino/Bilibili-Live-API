## 检测风纪委员判别状况

#### 调用地址

https://api.bilibili.com/x/credit/jury/juryCase

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookies|true|POST|string|发布帐号(必须和 Cookies 帐号一致)|
|jsonp|true|POST|string|jsonp|
|cid|true|POST|string当前时间|
|Host|true|POST|string|api.bilibili.com|
|Referer|true|POST|string|https://www.bilibili.com/judgement/vote/{案件编号}|
|User-Agent|true|false|string|Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.139 Safari/537.36|
|Accept-Language|false|POST|string|zh-CN,zh;q=0.9,en;q=0.8|
