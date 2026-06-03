# 直播列表

> 当前状态：2026-06-03 使用 `npm run verify:http` 验证通过。该接口是公开只读房间列表接口，适合获取当前直播房间、分区和排序后的列表数据。

## 调用地址

```text
GET https://api.live.bilibili.com/room/v3/area/getRoomList
```

## 鉴权

公开 GET，不需要 Cookie。

## 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `platform` | false | GET | string | 网页端常见值为 `web`。 |
| `parent_area_id` | false | GET | int | 一级分区 ID；旧用法中 `0` 表示全站。 |
| `area_id` | false | GET | int | 二级分区 ID。未传时按全站或一级分区返回。 |
| `page` | false | GET | int | 页码，常见从 `1` 开始。 |
| `page_size` | false | GET | int | 每页数量；当前验证示例使用 `5`。 |
| `sort_type` | false | GET | string | 排序方式，历史常见值为 `online`。 |

## 已验证示例

```text
https://api.live.bilibili.com/room/v3/area/getRoomList?platform=web&page=1&page_size=5
```

## 返回

返回 JSON，`code` 为 `0` 表示成功。2026-06-03 验证到的 `data` 顶层字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `count` | number | 当前查询条件下的直播间总数。 |
| `list` | array | 直播间列表。 |
| `banner` | array | 页面配置的 banner 数据，可能为空。 |
| `tags` | array | 分区/筛选标签数据，可能为空。 |
| `new_tags` | array | 新版标签数据，可能为空。 |

`list[]` 中常见字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `roomid` | number | 直播间 ID。 |
| `uid` | number | 主播 UID。 |
| `uname` | string | 主播昵称。 |
| `title` | string | 直播标题。 |
| `online` | number | 在线/热度数值。 |
| `area_id` / `area_name` | number / string | 当前分区 ID 和名称。 |
| `parent_id` / `parent_name` | number / string | 一级分区 ID 和名称。 |
| `area_v2_id` / `area_v2_name` | number / string | v2 分区 ID 和名称。 |
| `cover` / `user_cover` / `system_cover` | string | 封面图 URL。 |
| `link` | string | 房间页路径或 URL。 |
| `face` | string | 主播头像 URL。 |
| `session_id` | string | 页面推荐/会话标识；只作为页面上下文使用。 |

## 备注

- 本仓库验证脚本见 `data/live-api-manifest.json` 的 `room_list_v3`。
- 列表内容、排序和 `count` 随时间、分区、推荐策略和直播状态变化。
- 该接口只覆盖公开直播列表。舰长、礼物、抽奖、关注、投币、加入、领取、任务等登录态或状态变更相关接口不应从本接口文档推断为安全公开接口。
