# 新版首页直播推荐与排行

> 当前状态：2026-06-02 使用 `npm run verify:http` 验证通过。

## 调用地址

```text
GET https://api.live.bilibili.com/xlive/web-interface/v1/webMain/getList
```

## 鉴权

公开 GET，不需要 Cookie。

## 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `platform` | false | GET | string | 网页端常见 `web`。 |

## 已验证示例

```text
https://api.live.bilibili.com/xlive/web-interface/v1/webMain/getList?platform=web
```

## 返回

返回 JSON，`code` 为 `0` 表示成功。2026-06-02 验证到的 `data` 顶层字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `online_total` | int/string | 首页展示的在线总量相关字段。 |
| `recommend_room_list` | array | 推荐直播间列表。 |
| `ranking_list` | array/object | 首页排行数据。 |
| `preview_banner_list` | array | 预览 Banner。 |
| `new_preview_banner_list` | array | 新版预览 Banner。 |

## 备注

本接口适合替代旧文档中部分首页、热门、推荐类接口，但返回结构会随网页端运营配置变化。
