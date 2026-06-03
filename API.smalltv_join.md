## 小电视（摩天大楼）加入

> 安全边界：这是需要 Cookie/CSRF 的 POST 抽奖加入动作接口，会改变账号活动状态；默认验证脚本不会调用，当前仓库只把它作为历史资料和风险分类样例保留。

#### 调用地址

https://api.live.bilibili.com/xlive/lottery-interface/v5/smalltv/join

需要 Cookies，并验证登录状态(Cookies)

#### 参数

|   字段   |  传递方式 |    类型   |  说明  |
|---------|:-----------:|:--------:| ------ |
| roomid |      POST     |     int    |  房间号 |
|     id     |       POST     |     int    |  高能id  |
| type     |       POST     |   string |  类型 (guard之类的)   |
| visit_id |       POST     |   string |  [选填]  |
|   csrf    |       POST     |   string |  cookies之一 |
|   csrf_token | POST   |   string |  与csrf相同  |
