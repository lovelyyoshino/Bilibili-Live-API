# 视频播放器 API（2026-06-03 浏览器捕获验证）

> 通过 Playwright 捕获 `www.bilibili.com/video/BV1xx411c7mD` 页面网络请求。

## 播放器信息（WBI）

```text
GET https://api.bilibili.com/x/player/wbi/v2?aid=<aid>&cid=<cid>&bvid=<bvid>
```

WBI GET + Cookie。返回 `code:0` 时 `data` 顶层字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `aid` | int | 稿件 AV 号。 |
| `bvid` | string | BV 号。 |
| `cid` | int | 分P cid。 |
| `allow_bp` | bool | 是否允许承包。 |
| `no_share` | bool | 是否禁止分享。 |
| `max_limit` | int | 未登录最大观看时长限制。 |
| `page_no` | int | 当前分P序号。 |
| `has_next` | bool | 是否有下一P。 |

## 在线观看人数

```text
GET https://api.bilibili.com/x/player/online/total?aid=<aid>&cid=<cid>&bvid=<bvid>
```

公开 GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `total` | string | 总在线人数描述。 |
| `count` | int | 当前在线人数。 |
| `show_switch` | object | 展示开关。 |
| `abtest` | object | AB 测试配置。 |

## 视频与用户的关系

```text
GET https://api.bilibili.com/x/web-interface/archive/relation?aid=<aid>&bvid=<bvid>
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `attention` | int | 是否关注 UP 主（1=已关注）。 |
| `favorite` | int | 是否收藏。 |
| `season_fav` | int | 是否收藏合集。 |
| `like` | int | 是否点赞。 |
| `dislike` | int | 是否点踩。 |
| `coin` | int | 投币数。 |

## 服务器时间

```text
GET https://api.bilibili.com/x/click-interface/click/now
```

公开 GET。返回 `code:0` 时 `data.now` 为当前 Unix 时间戳。

## 视频观看心跳上报

```text
POST https://api.bilibili.com/x/click-interface/web/heartbeat
```

Cookie POST。用于上报视频观看进度。

## 视频点击上报

```text
POST https://api.bilibili.com/x/click-interface/click/web/h5
```

Cookie POST。用于上报视频点击事件。

## Cookie 刷新信息

```text
GET https://api.bilibili.com/x/passport-login/web/cookie/info
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `refresh` | bool | 是否需要刷新 Cookie。 |
| `timestamp` | int | 当前时间戳。 |

## 弹幕分段（Protobuf）

```text
GET https://api.bilibili.com/x/v2/dm/wbi/web/seg.so?oid=<cid>&type=1&segment_index=1
```

WBI GET。返回**二进制 Protobuf** 格式弹幕分段数据，非 JSON。

## 弹幕视图信息

```text
GET https://api.bilibili.com/x/v2/dm/web/view?oid=<cid>&type=1&pid=<aid>
```

返回二进制 Protobuf，包含弹幕配置、特殊弹幕标记等。

## 字幕信息

```text
GET https://api.bilibili.com/x/v2/subtitle/web/view?aid=<aid>&cid=<cid>
```

返回 Protobuf，包含 CC 字幕列表和配置。

## WebSocket 广播服务器

```text
GET https://api.bilibili.com/x/web-interface/broadcast/servers
```

公开 GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `domain` | string | WebSocket 域名。 |
| `tcp_port` | int | TCP 端口。 |
| `ws_port` | int | WS 端口。 |
| `wss_port` | int | WSS 端口。 |
| `heartbeat` | int | 心跳间隔（秒）。 |
| `nodes` | array | 节点列表。 |
| `backoff` | object | 重连退避配置。 |
| `heartbeat_max` | int | 最大心跳间隔。 |

## 笔记相关

```text
GET https://api.bilibili.com/x/note/list/archive?oid=<aid>&oid_type=0
GET https://api.bilibili.com/x/note/publish/list/archive?oid=<aid>&oid_type=0
GET https://api.bilibili.com/x/note/is_forbid
```

Cookie GET。分别返回用户笔记列表、公开笔记列表、是否禁止笔记。
