# 新版直播间聚合信息

> 当前状态：2026-06-02 已确认是当前网页端接口，但命令行环境多次返回 `code:-352`。请把 `-352` 视为风控/上下文限制，不要直接判断为接口失效。

## 调用地址

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/index/getInfoByRoom
```

## 鉴权

公开 WBI GET，不需要 Cookie，但需要 `wts` 和 `w_rid`。签名流程见 `API.WBI.md`。

## 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `room_id` | true | GET | int | 直播房间号。 |
| `web_location` | false | GET | string | 网页端常见 `444.8`。 |
| `wts` | true | GET | int | WBI 秒级时间戳。 |
| `w_rid` | true | GET | string | WBI 签名。 |

## 已验证示例

```text
https://api.live.bilibili.com/xlive/web-room/v1/index/getInfoByRoom?room_id=5050&web_location=444.8&wts=<unix_seconds>&w_rid=<sign>
```

## 返回

返回 JSON。`code:0` 时，常见 `data` 顶层字段包括：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `room_info` | object | 房间标题、分区、封面、直播状态等聚合信息。 |
| `anchor_info` | object | 主播信息。 |

当前 CLI 验证可能返回：

```json
{"code":-352,"message":"-352","ttl":1}
```

## 备注

- `npm run verify:http` 会把本接口作为 risk-gated 记录。
- 若需要完整响应字段，优先使用 `npm run discover:live-page` 在浏览器上下文中捕获，但仍保持默认 GET-only 和证据脱敏。
