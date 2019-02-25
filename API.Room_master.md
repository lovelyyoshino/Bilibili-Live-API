## 主播信息查询

### 调用地址

https://api.live.bilibili.com/room/v1/Room/get_info

需要 Cookies 并验证登录状态(Cookies)；要求应用申请弹幕权限


#### 参数
| 字段       | 必选    | 类型     | 说明  |
| -------- | ----- | ------ | ------------- |
| id      | true  | int    | 直播房间id   |

#### 返回
| 返回值字段       | 字段类型   | 字段说明    |
| ----------- | ------ | ----------------- |
| uid         | int    | 主播uid      |
| room_id     | int    | 房间id  |
| online      | int    | 在线人数 |
| description | string | 简介   |
| live_status | int    | 状态 (0: 未开播 1: 直播中) |
| area_id     | int    | 字分区id     |
| parent_area_id | int    | 总分区id    |
| parent_area_name        | string | 分区名称  |
| background | int    | 直播房间背景链接       |
| user_cover | int    | 主播图像链接       |
| keyframe   | int    | 直播关键帧     |
| live_time  | int    | 直播开始时间   |
| tags       | string | tag标签      |
