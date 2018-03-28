## APP登录（获取cookies和access_key）

#### 调用地址

https://passport.bilibili.com/api/v2/oauth2/login

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|appkey|true|POST|string|秘钥|
|password|true|POST|string|密码|
|username|true|POST|string|账户|
|mobi_app|true|POST|string|手机app|
|platform|false|POST|string|平台|
|sign|true|POST|string|md5加密|
