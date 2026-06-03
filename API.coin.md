## 获取双倍硬币

> 安全边界：这是需要 Cookie 的兑换/账号动作接口，会改变账号资产或状态；默认验证脚本不会调用，当前仓库只把它作为历史资料和风险分类样例保留。

#### 调用地址

http://api.live.bilibili.com/pay/v1/exchange/coin2silver

需要 Cookies，并验证登录状态(Cookies)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|POST|string|发布帐号(必须和 Cookies 帐号一致)|
|coin|true|POST|string|兑换数目|
