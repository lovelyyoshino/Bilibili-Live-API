# 用户空间 API（2026-06-03 浏览器捕获验证）

> 通过 Playwright 捕获 `space.bilibili.com/27793897` 页面网络请求。

## 用户详细信息（WBI）

```text
GET https://api.bilibili.com/x/space/wbi/acc/info?mid=<uid>
```

WBI GET。返回 `code:0` 时 `data` 包含：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `mid` | int | 用户 UID。 |
| `name` | string | 用户名。 |
| `sex` | string | 性别。 |
| `face` | string | 头像 URL。 |
| `face_nft` | int | NFT 头像标识。 |
| `sign` | string | 个性签名。 |
| `rank` | int | 用户等级排名。 |
| `level` | int | 等级。 |
| `official` | object | 认证信息。 |
| `vip` | object | 大会员信息。 |
| `pendant` | object | 头像挂件。 |
| `fans_medal` | object | 粉丝勋章信息。 |

## 我的信息（v2）

```text
GET https://api.bilibili.com/x/space/v2/myinfo
```

Cookie GET（仅自己的空间）。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `profile` | object | 用户完整资料。 |
| `level_exp` | object | 等级经验值。 |
| `coins` | float | 硬币数。 |
| `following` | int | 关注数。 |
| `follower` | int | 粉丝数。 |

## 用户投稿视频列表（WBI）

```text
GET https://api.bilibili.com/x/space/wbi/arc/search?mid=<uid>&pn=1&ps=25&order=pubdate
```

WBI GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `list` | object | 包含 `tlist`（分区统计）和 `vlist`（视频列表）。 |
| `page` | object | 分页信息 (`pn`, `ps`, `count`)。 |
| `episodic_button` | object | 合集按钮。 |
| `is_risk` | bool | 是否触发风控。 |

## 用户专栏文章

```text
GET https://api.bilibili.com/x/space/wbi/article?mid=<uid>&pn=1&ps=12&sort=publish_time
```

WBI GET。返回 `code:0` 时 `data` 含 `pn`, `ps` 等分页字段。

## 空间导航计数

```text
GET https://api.bilibili.com/x/space/navnum?mid=<uid>
```

公开 GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `video` | int | 视频数。 |
| `bangumi` | int | 追番数。 |
| `cinema` | int | 追剧数。 |
| `channel` | int | 频道数。 |
| `favourite` | int | 收藏夹数。 |
| `tag` | int | 订阅标签数。 |
| `article` | int | 专栏数。 |
| `playlist` | int | 播放列表数。 |

## 关系统计

```text
GET https://api.bilibili.com/x/relation/stat?vmid=<uid>
```

公开 GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `mid` | int | UID。 |
| `following` | int | 关注数。 |
| `whisper` | int | 悄悄关注数。 |
| `black` | int | 拉黑数。 |
| `follower` | int | 粉丝数。 |

## UP 主统计

```text
GET https://api.bilibili.com/x/space/upstat?mid=<uid>
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `archive` | object | 视频播放量统计 (`view`)。 |
| `article` | object | 专栏阅读量统计 (`view`)。 |
| `likes` | int | 获赞总数。 |

## 空间设置

```text
GET https://api.bilibili.com/x/space/setting?mid=<uid>
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `privacy` | object | 隐私设置（关注列表、收藏等可见性）。 |
| `show_nft_switch` | bool | NFT 展示开关。 |
| `index_order` | array | 空间模块排序。 |

## 投币视频

```text
GET https://api.bilibili.com/x/space/coin/video?vmid=<uid>
```

Cookie GET（需隐私设置允许）。返回 `code:0` 时 `data` 为视频数组。

## 点赞视频

```text
GET https://api.bilibili.com/x/space/like/video?vmid=<uid>&pn=1&ps=20
```

Cookie GET。返回 `code:0` 时 `data.list` 为点赞视频列表。

## 追番列表

```text
GET https://api.bilibili.com/x/space/bangumi/follow/list?vmid=<uid>&type=1&pn=1&ps=15
```

公开 GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `list` | array | 追番/追剧列表。 |
| `pn` | int | 当前页。 |
| `ps` | int | 每页数量。 |
| `total` | int | 总数。 |

## 最近玩过的游戏

```text
GET https://api.bilibili.com/x/space/lastplaygame/v2?mid=<uid>
```

公开 GET。返回 `code:0` 时 `data.list` 为游戏列表。

## 合集与系列

```text
GET https://api.bilibili.com/x/polymer/web-space/home/seasons_series?mid=<uid>&page_num=1&page_size=20
```

公开 GET。返回 `code:0` 时 `data.items_lists` 为合集/系列列表。

## 收藏夹列表

```text
GET https://api.bilibili.com/x/v3/fav/folder/created/list?up_mid=<uid>&ps=10&pn=1
```

公开 GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `count` | int | 收藏夹数量。 |
| `list` | array | 收藏夹列表。 |
| `has_more` | bool | 是否有更多。 |
