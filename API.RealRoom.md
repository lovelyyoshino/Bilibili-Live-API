## 直播源获取

#### 调用地址

https://api.live.bilibili.com/room/v1/Room/playUrl?

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|access_key|true|POST|string|发布帐号(登录获得)|
appkey |true|POST|string|iVGUTjsxvpLeuDCf(固定值)
build |true|POST|string|5370000
channel |true|POST|string|bilibiil140
cid |true|POST|string|544843(直播id)
device |true|POST|string|android
https_url_req |true|POST|string|0
mobi_app |true|POST|string| android
platform |true|POST|string| android
ptype |true|POST|string| 0
quality |true|POST|string| 0
ts |true|POST|string| 1549117192(时间戳)

这是app里面直播源的获取方法

其实h5界面获取直播也很简单,少了很多的校验
api也是上面那个,就是参数不一样
    cid:id|(直播id)
    platform|h5
    otype|json
    quality|0
可以看到h5只需要提交一个id就可以了
