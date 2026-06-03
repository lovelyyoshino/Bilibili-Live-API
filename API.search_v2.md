# 搜索 API（2026-06-03 验证）

> 来源：`SocialSisterYi/bilibili-API-collect` + 浏览器捕获验证。

## 综合搜索

```text
GET https://api.bilibili.com/x/web-interface/search/all/v2?keyword=<keyword>&page=1
```

Cookie GET / 公开 GET。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `keyword` | true | 搜索关键词。 |
| `page` | false | 页码（默认 1）。 |
| `order` | false | 排序（视频：`totalrank`/`click`/`pubdate`/`dm`/`stow`）。 |
| `duration` | false | 时长筛选：`1`(0-10min), `2`(10-30min), `3`(30-60min), `4`(60+min)。 |
| `tids` | false | 分区 ID 筛选。 |

返回 `data.result` 按类型分类（video, media_bangumi, live_room, user 等）。

## 分类搜索

```text
GET https://api.bilibili.com/x/web-interface/search/type?search_type=<type>&keyword=<keyword>&page=1
```

| `search_type` | 说明 |
| --- | --- |
| `video` | 视频。 |
| `media_bangumi` | 番剧。 |
| `media_ft` | 影视。 |
| `live` | 直播间。 |
| `live_user` | 主播。 |
| `article` | 专栏。 |
| `bili_user` | 用户。 |
| `photo` | 相簿。 |

## 搜索建议

```text
GET https://api.bilibili.com/x/web-interface/suggest?term=<keyword>
```

公开 GET。已验证返回 `code:0`。返回搜索补全建议列表。

## 搜索默认词（WBI）

```text
GET https://api.bilibili.com/x/web-interface/wbi/search/default
```

WBI GET。已验证返回 `code:0`。返回搜索框默认展示词。

## 热搜榜

```text
GET https://api.bilibili.com/x/web-interface/wbi/search/square?limit=10
```

WBI GET。返回热搜关键词列表。

## 备注

- 综合搜索返回所有类型的结果，适合首次搜索。
- 分类搜索用于翻页或筛选特定类型。
- 搜索结果中关键词会被 `<em class="keyword">` 标签包裹。
- WBI 签名接口需要 mixin key 参与签名计算。
