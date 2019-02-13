## 历史记录

#### 调用地址

https://app.bilibili.com/x/v2/history/cursor?

需要 App Key ，并验证登录状态(Access key)

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
|access_key|true|POST|string|发布帐号(登录获得)|

|access_key|true|POST|string|key值|
|appkey|true|POST|string_app-key值|
|build	|true|POST|string|不清楚,固定值（5370000）|
|business|true|POST|string|archive	（可选的有(all-所有,archive-视频,live-直播,article-专栏)）|
|channel|true|POST|string|	bilibiil140	（固定值,应该是版本号）
|max|true|POST|string|0
|max_tp|true|POST|string|	0	
|mobi_app|true|POST|string|	android
|platform|true|POST|string|android
|ps	|true|POST|string|数量（20）|
|ts	|true|POST|string|时间戳（1549783239）|
|sign |true|POST|string|2aa79554dffd9ddd9fb3e55b43992587（sign校验）|
