## 获取直播间用户信息

#### 调用地址

https://api.live.bilibili.com/User/getUserInfo

需要 Cookies，并验证登录状态(Cookies)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|GET|string|发布帐号(必须和 Cookies 帐号一致)|
|uname|true|GET|string|用户姓名|
|user_level|true|GET|int|用户等级|
|silver|true|GET|int|银瓜子|
|billCoin|true|GET|string|硬币|
|user_intimacy|GET|POST|int|经验|
