## APP端友爱社签到

#### 调用地址

https://api.vc.bilibili.com/link_setting/v1/link_setting/sign_in

需要 Access_key 并验证登录状态(Access_key)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|device|true|POST|string|android|
|hwid|true|POST|string|SX1NL0wuHCsaKRt4BHhIfRguTXxOfj5WN1BkBTdLfhstTn9NfUouFiUV|
|access_key|true|POST|string|发布帐号(必须和 Access_key 帐号一致)|
|appkey|true|POST|string|1d8b6e7d45233436|
|build|true|POST|string|520001|
|group_id|true|POST|string|友爱社编号|
|mobi_app|true|POST|string|android|
|owner_id|true|POST|string|自己的uid|
|platform|true|POST|string|平台（android）|
|src|false|POST|string|品牌（xiaomi）|
|trace_id|true|POST|string|20171224024300024|
|ts|true|POST|string|时间|
