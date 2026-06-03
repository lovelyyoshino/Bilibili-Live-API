# 视频互动操作 API（2026-06-03 验证）

> 来源：`SocialSisterYi/bilibili-API-collect` + Node.js 实测验证。
> 所有 POST 接口需要 Cookie（`SESSDATA`）+ `csrf`（= `bili_jct`）。

## 点赞视频

```text
POST https://api.bilibili.com/x/web-interface/archive/like
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `aid` | 二选一 | 视频 AV 号。 |
| `bvid` | 二选一 | 视频 BV 号。 |
| `like` | true | `1` 点赞，`2` 取消点赞。 |
| `csrf` | true | CSRF token。 |

返回 `code:0` 表示操作成功。`code:65006` 已点赞/已取消。

## 投币

```text
POST https://api.bilibili.com/x/web-interface/coin/add
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `aid` | 二选一 | 视频 AV 号。 |
| `bvid` | 二选一 | 视频 BV 号。 |
| `multiply` | true | 投币数（1 或 2）。 |
| `select_like` | false | `0` 不同时点赞，`1` 同时点赞。 |
| `csrf` | true | CSRF token。 |

返回 `code:0` 表示成功。`code:34005` 超过投币上限。

## 查询投币状态

```text
GET https://api.bilibili.com/x/web-interface/archive/coins?aid=<aid>
```

Cookie GET。返回 `code:0` 时 `data.multiply` 为已投币数（0/1/2）。

## 收藏视频

```text
POST https://api.bilibili.com/x/v3/fav/resource/deal
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `rid` | true | 视频 AV 号。 |
| `type` | true | 固定为 `2`（视频类型）。 |
| `add_media_ids` | false | 逗号分隔的收藏夹 ID（添加到）。 |
| `del_media_ids` | false | 逗号分隔的收藏夹 ID（从中移除）。 |
| `csrf` | true | CSRF token。 |

返回 `code:0` 表示成功。

## 分享视频

```text
POST https://api.bilibili.com/x/web-interface/share/add
```

Cookie POST。已验证返回 `code:0`。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `aid` | 二选一 | 视频 AV 号。 |
| `bvid` | 二选一 | 视频 BV 号。 |
| `csrf` | true | CSRF token。 |

返回 `code:0` 时增加分享计数。

## 一键三连（点赞+投币+收藏）

```text
POST https://api.bilibili.com/x/web-interface/archive/like/triple
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `aid` | 二选一 | 视频 AV 号。 |
| `bvid` | 二选一 | 视频 BV 号。 |
| `csrf` | true | CSRF token。 |

返回 `code:0` 时 `data` 含 `like`(bool), `coin`(bool), `fav`(bool) 表示各操作是否成功。

## 查询视频与用户关系

```text
GET https://api.bilibili.com/x/web-interface/archive/relation?aid=<aid>&bvid=<bvid>
```

Cookie GET。已验证返回 `code:0`。

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `attention` | int | 是否关注 UP 主（1=已关注）。 |
| `favorite` | int | 是否收藏。 |
| `season_fav` | int | 是否收藏合集。 |
| `like` | int | 是否点赞。 |
| `dislike` | int | 是否点踩。 |
| `coin` | int | 投币数。 |

## 备注

- 所有写操作需要 `buvid3` Cookie 以避免风控 `-352`。
- `aid` 和 `bvid` 二选一，都传时以 `aid` 为准。
- 频繁操作可能触发验证码（`code:-352`），需带完整浏览器环境。
