# 私信系统 API（2026-06-03 验证）

> 来源：`SocialSisterYi/bilibili-API-collect` + Node.js 实测验证。
> 私信 API 域名为 `api.vc.bilibili.com`，需要 Cookie 认证。

## 未读私信数

```text
GET https://api.vc.bilibili.com/session_svr/v1/session_svr/single_unread?unread_type=0&build=0&mobi_app=web
```

Cookie GET。已验证返回 `code:0`。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `unread_type` | false | `0` 全部，`1` 已关注，`2` 未关注，`3` 被拦截。 |
| `build` | false | 构建号，默认 `0`。 |
| `mobi_app` | false | 客户端标识，`web`。 |

返回字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `unfollow_unread` | int | 未关注人未读数。 |
| `follow_unread` | int | 已关注人未读数。 |
| `dustbin_unread` | int | 垃圾箱未读数。 |
| `biz_msg_unfollow_unread` | int | 业务消息（未关注）未读数。 |
| `biz_msg_follow_unread` | int | 业务消息（已关注）未读数。 |

## 粉丝团未读数

```text
GET https://api.vc.bilibili.com/session_svr/v1/session_svr/my_group_unread?build=0&mobi_app=web
```

Cookie GET。已验证返回 `code:0`。返回 `data.unread_count`。

## 会话列表

```text
GET https://api.vc.bilibili.com/session_svr/v1/session_svr/get_sessions?session_type=1&group_fold=1&unfollow_fold=0&sort_rule=2&size=20
```

Cookie GET。已验证返回 `code:0`。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `session_type` | true | `1` 已关注，`2` 未关注，`3` 粉丝团，`4` 全部。 |
| `group_fold` | false | 是否折叠群组（`1` 折叠）。 |
| `unfollow_fold` | false | 是否折叠未关注（`0` 不折叠）。 |
| `sort_rule` | false | 排序规则（`2` 按时间倒序）。 |
| `size` | false | 每页数量（默认 20）。 |
| `begin_ts` | false | 起始时间戳（翻页用）。 |
| `end_ts` | false | 结束时间戳。 |

返回 `data.session_list` 为会话数组，每项含 `talker_id`, `session_type`, `last_msg` 等。

## 查询聊天记录

```text
GET https://api.vc.bilibili.com/svr_sync/v1/svr_sync/fetch_session_msgs?talker_id=<uid>&session_type=1&size=20
```

Cookie GET。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `talker_id` | true | 对方 UID。 |
| `session_type` | true | `1` 私聊。 |
| `size` | false | 消息条数（默认 20）。 |
| `begin_seqno` | false | 起始序列号（翻页）。 |
| `end_seqno` | false | 结束序列号。 |

返回 `data.messages` 为消息数组。

## 发送私信

```text
POST https://api.vc.bilibili.com/web_im/v1/web_im/send_msg
```

Cookie POST + WBI 签名。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `msg[sender_uid]` | true | 发送者 UID。 |
| `msg[receiver_id]` | true | 接收者 UID。 |
| `msg[receiver_type]` | true | `1` 用户私聊。 |
| `msg[msg_type]` | true | `1` 文字，`2` 图片，`5` 卡片。 |
| `msg[dev_id]` | true | UUID v4 设备标识。 |
| `msg[timestamp]` | true | 发送时间戳（秒）。 |
| `msg[content]` | true | JSON 字符串，如 `{"content":"你好"}`。 |
| `csrf_token` | true | CSRF token。 |
| `csrf` | true | CSRF token。 |

返回 `code:0` 表示发送成功。

## 备注

- 所有私信 API 使用 `api.vc.bilibili.com` 域名（非 `api.bilibili.com`）。
- `send_msg` 需要 WBI 签名参数 `w_rid` 和 `wts`。
- `msg_type` 仅支持 1（文字）、2（图片）、5（卡片），不支持视频消息。
- 查询聊天记录通过 `begin_seqno` / `end_seqno` 进行游标翻页。
