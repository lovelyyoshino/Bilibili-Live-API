# 新版批量房间基础信息

> 当前状态：2026-06-02 使用 `npm run verify:http` 验证通过。旧版 `room/v1/Room/get_info` 仍可用，但网页端已大量使用本接口做批量房间信息读取。

## 调用地址

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/index/getRoomBaseInfo
```

## 鉴权

公开 GET，不需要 Cookie。

## 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `room_ids` | true | GET | string | 直播房间号，多个房间通常用逗号分隔。 |
| `req_biz` | false | GET | string | 网页端常见值为 `web_room_componet`。 |

## 已验证示例

```text
https://api.live.bilibili.com/xlive/web-room/v1/index/getRoomBaseInfo?room_ids=5050&req_biz=web_room_componet
```

## 返回

返回 JSON，`code` 为 `0` 表示成功。2026-06-02 验证到的 `data` 顶层字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `by_room_ids` | object | 按房间号索引的房间信息。 |
| `by_uids` | object | 按主播 UID 索引的房间信息。 |

## 备注

- 本仓库验证脚本见 `data/live-api-manifest.json` 的 `room_base_info`。
- 如果房间不存在或被隐藏，返回结构可能存在但对应房间信息为空。
