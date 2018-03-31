## 总督抽奖

#### 调用地址

https://search.bilibili.com/api/search

需要 Access_key 并验证登录状态(Access_key)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|search_type|true|GET|string|live|
|keyword|true|GET|string|关键地址|
|access_key|true|GET|string|发布帐号(必须和 Access_key 帐号一致)|
|appkey|true|GET|int|1d8b6e7d45233436|
|build|true|GET|string|520001|
|actionKey|true|GET|int|appkey|
|mobi_app|true|GET|int|android|
|roomid|true|GET|string|房间地址|
|platform|true|GET|int|平台（android）|
|ts|true|GET|int|时间|
|type|true|GET|string|guard|
