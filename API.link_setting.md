## 友爱社签到

#### 调用地址

https://api.vc.bilibili.com/link_setting/v1/link_setting/sign_in

需要 Access_key 并验证登录状态(Access_key)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|device|true|GET|string|android|
|hwid|true|GET|string|SX1NL0wuHCsaKRt4BHhIfRguTXxOfj5WN1BkBTdLfhstTn9NfUouFiUV|
|access_key|true|GET|string|发布帐号(必须和 Access_key 帐号一致)|
|appkey|true|GET|int|1d8b6e7d45233436|
|build|true|GET|string|520001|
|group_id|true|GET|int|友爱社编号|
|mobi_app|true|GET|int|android|
|owner_id|true|GET|string|自己的uid|
|platform|true|GET|int|平台（android）|
|src|true|GET|int|品牌（xiaomi）|
|trace_id|true|GET|string|20171224024300024|
|ts|true|GET|int|时间|
