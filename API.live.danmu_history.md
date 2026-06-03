# 直播间历史弹幕

> 当前状态：2026-06-02 使用 `npm run verify:http` 验证为公开读接口。

## 调用地址

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/dM/gethistory
```

## 鉴权

公开 GET，不需要 Cookie。当前验证不需要 WBI。

## 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `roomid` | true | GET | int | 直播房间号。 |
| `room_type` | false | GET | int | 网页端常见 `0`。 |

## 已验证示例

```text
https://api.live.bilibili.com/xlive/web-room/v1/dM/gethistory?roomid=5050&room_type=0
```

## 返回

返回 JSON。`code:0` 时，2026-06-02 验证到的 `data` 顶层字段包括：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `admin` | array | 管理员或系统侧历史消息列表。 |
| `room` | array | 直播间历史弹幕消息列表。 |

## 备注

- 该接口返回最近历史弹幕，不等同于实时 WebSocket 弹幕流。
- 实时弹幕连接和包格式见 `API.WebSocket.md`，弹幕服务器 token 获取见 `API.getDanmuInfo.md`。
