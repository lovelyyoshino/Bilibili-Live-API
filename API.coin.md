## 获取双倍硬币

#### 调用地址

http://api.live.bilibili.com/pay/v1/exchange/coin2silver

需要 Cookies，并验证登录状态(Cookies)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|POST|string|发布帐号(必须和 Cookies 帐号一致)|
|coin|true|POST|string|兑换数目|