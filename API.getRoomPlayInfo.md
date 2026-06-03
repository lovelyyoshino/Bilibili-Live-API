# 新版网页播放信息

> 当前状态：2026-06-02 使用 `npm run verify:http` 验证，最新证据返回 `code:0`。

## 调用地址

```text
GET https://api.live.bilibili.com/xlive/web-room/v2/index/getRoomPlayInfo
```

## 鉴权

公开 GET，通常不需要 Cookie。

## 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `room_id` | true | GET | int | 直播房间号。 |
| `protocol` | false | GET | string | 协议集合，网页端常见 `0,1`。 |
| `format` | false | GET | string | 格式集合，网页端常见 `0,1,2`。 |
| `codec` | false | GET | string | 编码集合，网页端常见 `0,1`。 |
| `qn` | false | GET | int | 清晰度，`0` 表示自动。 |
| `platform` | false | GET | string | 网页端常见 `web`。 |
| `ptype` | false | GET | int | 网页端常见 `8`。 |
| `dolby` | false | GET | int | 杜比相关参数，网页端常见 `5`。 |
| `panoramic` | false | GET | int | 全景相关参数，网页端常见 `1`。 |

## 已验证示例

```text
https://api.live.bilibili.com/xlive/web-room/v2/index/getRoomPlayInfo?room_id=5050&protocol=0,1&format=0,1,2&codec=0,1&qn=0&platform=web&ptype=8&dolby=5&panoramic=1
```

## 返回

返回 JSON。`code:0` 时，2026-06-02 验证到的 `data` 顶层字段包括：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `room_id` | int | 房间号。 |
| `short_id` | int | 短号。 |
| `uid` | int | 主播 UID。 |
| `live_status` | int | 直播状态。 |
| `playurl_info` | object/null | 播放 URL 信息，未开播或无权限时可能为 `null`。 |

## 备注

- 旧文档 `API.RealRoom.md` 记录的是 `room/v1/Room/playUrl`，本接口是当前网页端更常见的版本。
- 历史验证中部分环境曾返回 `code:-352`，但最新默认参数验证为 `code:0`。
