## 佩戴指定活动头衔

#### 调用地址

https://api.live.bilibili.com/i/ajaxWearTitle

需要 Cookies，并验证登录状态(Cookies)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|GET|string|发布帐号(必须和 Cookies 帐号一致)|
|id|true|GET|int|头衔编号|
|cid|true|GET|int|头衔等级（1默认初始活动头衔）|
|csrf_token|true|GET|string|与 cookie 中 bili_jct 值一致（fce9ff23db2a8da348172da7e8972020）|
