# 直播间主播资料

> 当前状态：2026-06-02 使用 `npm run verify:http` 验证为公开读接口。

## 调用地址

```text
GET https://api.live.bilibili.com/live_user/v1/UserInfo/get_anchor_in_room
```

## 鉴权

公开 GET，不需要 Cookie。当前验证不需要 WBI。

## 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `roomid` | true | GET | int | 直播房间号。 |

## 已验证示例

```text
https://api.live.bilibili.com/live_user/v1/UserInfo/get_anchor_in_room?roomid=5050
```

## 返回

返回 JSON。`code:0` 时，2026-06-02 验证到的 `data` 顶层字段包括：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `info` | object | 主播基础资料。 |
| `level` | object | 主播等级信息。 |
| `san` | object | 主播状态或评分相关信息。 |

## 备注

- 该接口适合和 `room/v1/Room/room_init`、`getRoomBaseInfo` 一起使用，补齐房间到主播资料的映射。
- 如果只需要主播 UID，可优先使用 `room_init` 或批量房间基础信息。
