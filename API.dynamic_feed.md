# 动态 Feed API（2026-06-03 验证）

> 来源：`SocialSisterYi/bilibili-API-collect` + 浏览器捕获验证。

## 关注动态时间线

```text
GET https://api.bilibili.com/x/polymer/web-dynamic/v1/feed/all?type=all&offset=&timezone_offset=-480
```

Cookie GET。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `type` | false | 筛选类型：`all`, `video`, `pgc`, `article`。 |
| `offset` | false | 翻页偏移（来自上页返回的 `offset`）。 |
| `timezone_offset` | false | 时区偏移（分钟），默认 `-480`。 |

返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `has_more` | bool | 是否有更多。 |
| `offset` | string | 翻页偏移值。 |
| `update_baseline` | string | 更新基线。 |
| `update_num` | int | 新增动态数。 |
| `items` | array | 动态列表。 |

## 用户空间动态

```text
GET https://api.bilibili.com/x/polymer/web-dynamic/v1/feed/space?host_mid=<uid>&offset=&timezone_offset=-480
```

公开 GET。已验证返回 `code:0`。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `host_mid` | true | 目标用户 UID。 |
| `offset` | false | 翻页偏移。 |
| `timezone_offset` | false | 时区偏移。 |

## 动态详情

```text
GET https://api.bilibili.com/x/polymer/web-dynamic/v1/detail?id=<dynamic_id>&timezone_offset=-480
```

公开 GET。获取单条动态完整信息。

## 发布图文动态

```text
POST https://api.bilibili.com/x/dynamic/feed/create/dyn
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `dyn_req` | true | JSON 对象，含 `content`（文字）和 `pics`（图片数组）。 |
| `csrf` | true | CSRF token。 |

## 转发动态

```text
POST https://api.bilibili.com/x/dynamic/feed/create/dyn
```

Cookie POST。转发时 `dyn_req` 中包含 `repost` 对象指定原动态 ID。

## 删除动态

```text
POST https://api.bilibili.com/x/dynamic/feed/operate/remove
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `dyn_id_str` | true | 动态 ID 字符串。 |
| `csrf` | true | CSRF token。 |

## 点赞动态

```text
POST https://api.bilibili.com/x/dynamic/feed/dyn/thumb
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `dyn_id_str` | true | 动态 ID。 |
| `up` | true | `1` 点赞，`2` 取消点赞。 |
| `csrf` | true | CSRF token。 |

## 备注

- 动态 feed 使用游标翻页（`offset`），不支持传统页码。
- `items[]` 结构复杂，含 `modules`（作者/描述/内容/统计/交互等模块）。
- 发布动态的 `dyn_req` 结构较复杂，建议参考浏览器抓包。
