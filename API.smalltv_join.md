## 小电视（摩天大楼）加入

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
