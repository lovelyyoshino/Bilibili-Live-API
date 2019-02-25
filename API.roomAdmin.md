##### 房管

### 调用地址

https://api.live.bilibili.com/xlive/web-room/v1/roomAdmin/get_by_room

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限

#### 参数

|字段|必选|传递方式|类型|说明|
|----|----|--------|----|----|
| roomid      | true  |POST| int    | 直播房间id    |
| page_size   | true  | POST|int    | 一页多少    |
