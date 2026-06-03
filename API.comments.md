# 评论系统 API（2026-06-03 验证）

> 来源：`SocialSisterYi/bilibili-API-collect` + Node.js 实测验证。
> 评论区类型 `type`：1=视频, 11=图文动态, 12=专栏, 14=音频, 17=转发动态, 22=漫画, 33=课程。
> `oid` 为对应资源的 ID（视频为 AV 号）。

## 获取评论列表（传统分页）

```text
GET https://api.bilibili.com/x/v2/reply?type=1&oid=<aid>&pn=1&ps=20&sort=2
```

公开 GET。已验证返回 `code:0`。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `type` | true | 评论区类型。 |
| `oid` | true | 目标资源 ID。 |
| `sort` | false | 排序：`0` 时间，`1` 点赞数，`2` 回复数。 |
| `nohot` | false | `0` 显示热评，`1` 隐藏热评。 |
| `ps` | false | 每页条数（1-20，默认 20）。 |
| `pn` | false | 页码（默认 1）。 |

## 获取评论列表（游标分页，WBI）

```text
GET https://api.bilibili.com/x/v2/reply/wbi/main?type=1&oid=<aid>&mode=3
```

WBI GET。需要 WBI 签名，否则返回 `-403`。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `type` | true | 评论区类型。 |
| `oid` | true | 目标资源 ID。 |
| `mode` | false | `0`/`3` 热门，`1` 热门+时间，`2` 时间。 |
| `pagination_str` | false | JSON 对象含 `offset` 字段（游标翻页）。 |

## 获取评论回复

```text
GET https://api.bilibili.com/x/v2/reply/reply?type=1&oid=<aid>&root=<rpid>&ps=20&pn=1
```

公开 GET。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `type` | true | 评论区类型。 |
| `oid` | true | 目标资源 ID。 |
| `root` | true | 根评论 rpid。 |
| `ps` | false | 每页条数（1-49，默认 20）。 |
| `pn` | false | 页码。 |

## 获取热门评论

```text
GET https://api.bilibili.com/x/v2/reply/hot?type=1&oid=<aid>&pn=1&ps=20
```

公开 GET。已验证返回 `code:0`。参数同评论列表。

## 发表评论

```text
POST https://api.bilibili.com/x/v2/reply/add
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `type` | true | 评论区类型。 |
| `oid` | true | 目标资源 ID。 |
| `message` | true | 评论内容（最大 1000 字符）。 |
| `root` | false | 根评论 rpid（回复时必填）。 |
| `parent` | false | 父评论 rpid（回复时必填）。 |
| `plat` | false | 平台（`1` Web, `2` Android）。 |
| `csrf` | true | CSRF token。 |

返回 `code:0` 时 `data` 含新评论的完整信息。

## 点赞评论

```text
POST https://api.bilibili.com/x/v2/reply/action
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `type` | true | 评论区类型。 |
| `oid` | true | 目标资源 ID。 |
| `rpid` | true | 目标评论 rpid。 |
| `action` | true | `1` 点赞，`0` 取消点赞。 |
| `csrf` | true | CSRF token。 |

## 点踩评论

```text
POST https://api.bilibili.com/x/v2/reply/hate
```

Cookie POST。参数同点赞，`action` 为 `1` 点踩 / `0` 取消。

## 删除评论

```text
POST https://api.bilibili.com/x/v2/reply/del
```

Cookie POST。仅可删除自己的评论或自己管理的评论区内的评论。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `type` | true | 评论区类型。 |
| `oid` | true | 目标资源 ID。 |
| `rpid` | true | 目标评论 rpid。 |
| `csrf` | true | CSRF token。 |

## 备注

- `/x/v2/reply` 为传统分页，适合已知页码场景。
- `/x/v2/reply/wbi/main` 为游标分页（推荐），需 WBI 签名。
- 评论内容支持表情语法如 `[doge]`、`[tv_微笑]`。
- 回复时 `root` 为楼层根评论 ID，`parent` 为直接回复的评论 ID。
