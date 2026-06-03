# 弹幕服务器和 Token

> 当前状态：2026-06-02 使用 `npm run verify:http` 和 `npm run verify:ws` 验证。当前 WebSocket 鉴权依赖本接口返回的 `token`。

## 调用地址

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/index/getDanmuInfo
```

## 鉴权

公开 WBI GET，不需要 Cookie，但需要 `wts` 和 `w_rid`。签名流程见 `API.WBI.md`。

## 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `id` | true | GET | int | 直播房间号。 |
| `type` | false | GET | int | 网页端常见 `0`。 |
| `web_location` | false | GET | string | 网页端常见 `444.8`。 |
| `wts` | true | GET | int | WBI 秒级时间戳。 |
| `w_rid` | true | GET | string | WBI 签名。 |

## 已验证示例

```text
https://api.live.bilibili.com/xlive/web-room/v1/index/getDanmuInfo?id=5050&type=0&web_location=444.8&wts=<unix_seconds>&w_rid=<sign>
```

## 返回

返回 JSON。`code:0` 时，2026-06-02 验证到的 `data` 顶层字段包括：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `token` | string | WebSocket op `7` 鉴权包中的 `key`。 |
| `host_list` | array | 弹幕服务器列表，包含 `host`、`ws_port`、`wss_port`。 |
| `refresh_rate` | int | 刷新间隔相关字段。 |
| `max_delay` | int | 最大延迟相关字段。 |

## WebSocket 用法

1. 用 `room/v1/Room/room_init` 解析房间状态。
2. WBI 签名请求本接口。
3. 从 `host_list` 选择支持 `wss_port` 的主机。
4. 连接 `wss://<host>:<wss_port>/sub`。
5. op `7` 鉴权包中使用 `key: data.token`。

详细包格式见 `API.WebSocket.md`。

## 备注

- 2026-06-02 最新 `npm run verify:http` 证据中，本接口 WBI 签名后直接返回 `code:0`、`host_list` 和 `token`。
- 本仓库 `scripts/verify-live-websocket.mjs` 会优先使用本接口返回的 `host_list` 和 `token`。
