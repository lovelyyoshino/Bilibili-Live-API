# 消息中心与搜索 API（2026-06-03 浏览器捕获验证）

> 通过 Playwright 捕获 `message.bilibili.com` 和 `search.bilibili.com` 页面。

## IM 消息未读数

```text
GET https://api.bilibili.com/x/im/web/msgfeed/unread
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `at` | int | @我 未读数。 |
| `coin` | int | 投币通知未读数。 |
| `danmu` | int | 弹幕通知未读数。 |
| `favorite` | int | 收藏通知未读数。 |
| `like` | int | 点赞通知未读数。 |
| `recv_like` | int | 收到的赞。 |
| `recv_reply` | int | 收到的回复。 |
| `reply` | int | 回复通知未读数。 |

## Toast 消息

```text
GET https://api.bilibili.com/x/im/web/toast_msg
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `type` | int | 消息类型。 |
| `title` | string | 标题。 |
| `content` | string | 内容。 |

## 用户卡片批量查询

```text
GET https://api.bilibili.com/x/polymer/pc-electron/v1/user/cards?uids=<uid1>,<uid2>
```

Cookie GET。返回 `code:0` 时 `data` 按 UID 索引用户卡片信息。

## 黑名单列表

```text
GET https://api.bilibili.com/x/relation/blacks?pn=1&ps=20
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `list` | array | 黑名单用户列表。 |
| `re_version` | int | 版本号。 |
| `total` | int | 总数。 |

## 批量关系查询

```text
GET https://api.bilibili.com/x/relation/relations?fids=<uid1>,<uid2>
```

Cookie GET。返回 `code:0` 时 `data` 按 UID 索引关系信息。

## 搜索建议

```text
GET https://api.bilibili.com/x/web-interface/suggest?term=<keyword>
```

公开 GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `exp_str` | string | 实验标识。 |
| `code` | int | 内部状态码。 |
| `result` | array | 搜索建议列表。 |
| `stoken` | string | 搜索 token。 |
| `total_count` | int | 结果数。 |

## 搜索默认词（WBI）

```text
GET https://api.bilibili.com/x/web-interface/wbi/search/default
```

WBI GET 或公开 GET（`/x/web-interface/search/default` 也可）。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `seid` | string | 搜索会话 ID。 |
| `id` | int | 默认词 ID。 |
| `type` | int | 类型。 |
| `show_name` | string | 展示文本。 |
| `name` | string | 实际搜索词。 |
| `goto_type` | int | 跳转类型。 |
| `goto_value` | string | 跳转值。 |
| `url` | string | 跳转 URL。 |

## 动态入口信息

```text
GET https://api.bilibili.com/x/web-interface/dynamic/entrance
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `entrance` | object | 动态入口配置。 |
| `update_info` | object | 更新信息。 |
| `dyn_basic_infos` | array | 动态基本信息列表。 |

## 广告位资源（WBI）

```text
GET https://api.bilibili.com/x/web-show/wbi/res/locs?pf=0&ids=2836,2837
```

WBI GET。返回 `code:0` 时 `data` 按广告位 ID 索引广告数据。

## VIP 广告素材

```text
GET https://api.bilibili.com/x/vip/ads/materials
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `list` | array | 广告素材列表。 |
| `list_v2` | array | V2 版素材列表。 |
| `vip_login_coupon` | object | VIP 登录优惠券配置。 |

## 历史记录续播

```text
GET https://api.bilibili.com/x/web-interface/history/continuation
```

Cookie GET。返回 `code:0` 时 `data` 为续播信息。
