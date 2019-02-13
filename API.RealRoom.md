## 客户端播放

#### 调用地址

https://api.live.bilibili.com/room/v1/Room/playUrl?

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|access_key|true|GET|string|发布帐号(登录获得)|
appkey |true|GET|string|iVGUTjsxvpLeuDCf(固定值)
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

这是app里面直播源的获取方法

其实h5界面获取直播也很简单,少了很多的校验
api也是上面那个,就是参数不一样

字段 |必选|传递方式|类型| 说明
|----|----|--------|----|----|
cookies|true|GET|string|发布帐号(必须和 Cookies 帐号一致)
cid:id |true|GET|string|(直播id)
platform|true|GET|string|h5
otype|true|GET|string|json
quality|true|GET|int|0
    
tip：可以看到h5只需要提交一个id就可以了
