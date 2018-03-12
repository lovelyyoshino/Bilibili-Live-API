## 检测实物抽奖

#### 调用地址

https://api.live.bilibili.com/lottery/v1/box/getStatus

需要 Cookies，并验证登录状态(Cookies)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|GET|string|发布帐号(必须和 Cookies 帐号一致)|
|aid|true|GET|int|房间号|