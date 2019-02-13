## 客户端播放

#### 调用地址

https://api.live.bilibili.com/room/v1/Room/playUrl

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|access_key|false|GET|string|发布帐号(登录获得)|
appkey |false|GET|string|秘钥
build |true|GET|string|5370000
channel |true|GET|string|bilibiil140
cid |true|GET|string|544843(直播id)
device |true|GET|string|android
https_url_req |true|GET|int|0
mobi_app |true|GET|string| android
platform |true|GET|string| android
ptype |true|GET|int| 0
quality |true|GET|int| 0
ts |true|GET|string| 1549117192(时间戳)


PC界面获取直播也很简单,少了很多的校验
#### 参数

字段 |必选|传递方式|类型| 说明
|----|----|--------|----|----|
cookies|false|GET|string|发布帐号(必须和 Cookies 帐号一致)
cid |true|GET|string|(直播id)
platform|true|GET|string|h5
otype|true|GET|string|json
quality|true|GET|int|0
