## 读取视频评论

#### 调用地址

http://api.live.bilibili.com/area/liveList

需要 Cookies，并验证登录状态(Cookies)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|cookie|true|GET|string|发布帐号(必须和 Cookies 帐号一致)|
|area|true|GET|string|访问区域|
|order|true|GET|string|请求模式|
|page|true|GET|int|页码|
