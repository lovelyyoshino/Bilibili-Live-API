# 直播间管理 API（2026-06-03 验证）

> 来源：`SocialSisterYi/bilibili-API-collect` + Node.js 实测验证。

## 房间详细信息

```text
GET https://api.live.bilibili.com/room/v1/Room/get_info?room_id=5050
```

公开 GET。已验证返回 `code:0`。

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `uid` | int | 主播 UID。 |
| `room_id` | int | 长房间号。 |
| `short_id` | int | 短房间号（0=无短号）。 |
| `title` | string | 直播间标题。 |
| `live_status` | int | `0` 未开播，`1` 直播中，`2` 轮播中。 |
| `area_id` | int | 子分区 ID。 |
| `parent_area_id` | int | 父分区 ID。 |
| `area_name` | string | 子分区名。 |
| `parent_area_name` | string | 父分区名。 |
| `online` | int | 在线人数。 |
| `tags` | string | 标签。 |
| `description` | string | 房间描述。 |

## 简洁房间信息（按 UID）

```text
GET https://api.live.bilibili.com/room/v1/Room/getRoomInfoOld?mid=<uid>
```

公开 GET。已验证返回 `code:0`。

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `roomStatus` | int | 房间是否存在。 |
| `roundStatus` | int | 是否轮播中。 |
| `live_status` | int | 直播状态。 |
| `url` | string | 直播间 URL。 |
| `title` | string | 标题。 |
| `cover` | string | 封面 URL。 |
| `online` | int | 在线人数。 |
| `roomid` | int | 房间号。 |

## 房间初始化

```text
GET https://api.live.bilibili.com/room/v1/Room/room_init?id=5050
```

公开 GET。已验证返回 `code:0`。

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `room_id` | int | 长房间号。 |
| `short_id` | int | 短房间号。 |
| `uid` | int | 主播 UID。 |
| `live_status` | int | 直播状态。 |
| `is_hidden` | bool | 是否隐藏。 |
| `is_locked` | bool | 是否封禁。 |
| `encrypted` | bool | 是否加密。 |

## 批量查询直播状态

```text
POST https://api.live.bilibili.com/room/v1/Room/get_status_info_by_uids
```

公开 POST（JSON Body）。已验证返回 `code:0`。

请求体：`{"uids": [433351, 27793897]}`

返回 `data` 按 UID 索引，每项含 `title`, `room_id`, `live_status`, `area_v2_name` 等。

## 弹幕配置

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/dM/GetDMConfigByGroup?room_id=5050
```

Cookie GET。已验证返回 `code:0`。

返回可用弹幕颜色列表和显示模式配置。

## 历史弹幕

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/dM/gethistory?roomid=5050
```

公开 GET。已验证返回 `code:0`。

返回 `data.room` 为最近弹幕数组，每项含 `text`, `uid`, `nickname`, `timeline` 等。

## 发送直播弹幕

```text
POST https://api.live.bilibili.com/msg/send
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `roomid` | true | 房间号。 |
| `msg` | true | 弹幕内容。 |
| `rnd` | true | Unix 时间戳。 |
| `fontsize` | false | 字号（默认 25）。 |
| `color` | false | 颜色（十进制，默认 16777215 白色）。 |
| `mode` | false | 模式：`1` 滚动，`4` 底部，`5` 顶部。 |
| `csrf` | true | CSRF token。 |
| `csrf_token` | true | 同上。 |

## 开播

```text
POST https://api.live.bilibili.com/room/v1/Room/startLive
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `room_id` | true | 房间号。 |
| `area_v2` | true | 直播分区 ID。 |
| `platform` | false | 平台（`pc`）。 |
| `csrf` | true | CSRF token。 |
| `csrf_token` | true | 同上。 |

返回 `code:0` 时 `data` 含 RTMP 推流地址 `rtmp.addr` 和密钥 `rtmp.code`。

## 下播

```text
POST https://api.live.bilibili.com/room/v1/Room/stopLive
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `room_id` | true | 房间号。 |
| `platform` | false | 平台（`pc`）。 |
| `csrf` | true | CSRF token。 |
| `csrf_token` | true | 同上。 |

## 回放列表

```text
GET https://api.live.bilibili.com/xlive/app-blink/v1/anchorVideo/AnchorGetReplayList?page=1&page_size=20
```

Cookie GET。已验证返回 `code:0`。返回主播直播回放列表。

## 备注

- `room_init` 是最轻量的房间信息接口，适合批量轮询。
- `get_status_info_by_uids` 支持批量查询，适合关注列表开播提醒。
- `msg/send` 的 `rnd` 必须为当前 Unix 秒级时间戳。
- `startLive` 返回的推流地址有效期有限，需定期刷新。
