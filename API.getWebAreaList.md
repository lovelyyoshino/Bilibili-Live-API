# 新版直播分区列表

> 当前状态：2026-06-02 使用 `npm run verify:http` 和 `npm run discover:live-page` 验证通过。

## 调用地址

```text
GET https://api.live.bilibili.com/xlive/web-interface/v1/index/getWebAreaList
```

## 鉴权

公开 GET，不需要 Cookie。

## 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `source_id` | false | GET | int | 网页端常见 `2`。 |

## 已验证示例

```text
https://api.live.bilibili.com/xlive/web-interface/v1/index/getWebAreaList?source_id=2
```

## 返回

返回 JSON，`code` 为 `0` 表示成功。2026-06-02 验证到的 `data` 顶层字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `data` | array | 分区树，通常包含父分区和子分区。 |
| `expid` | string | 实验或推荐链路标识。 |

## 备注

旧版 `room/v1/Area/getList?show_pinyin=1` 仍可返回分区列表。当前 `/p/eden/area-tags` 分区页还会请求 lowercase 路径：

```text
GET https://api.live.bilibili.com/room/v1/area/getList?parent_id=0&platform=web
```

该接口为公开 GET。`parent_id=0` 返回父分区及子列表；指定父分区 ID（例如 `2`、`10`）时返回该父分区下的子分区列表。2026-06-02 通过 Playwright 分区页捕获和 Node 直接请求复验，返回 `code:0`。

## 分区开播房间数量

当前 `/all` 页面还会请求分区开播房间数量：

```text
GET https://api.live.bilibili.com/room/v1/Area/getLiveRoomCountByAreaID?areaId=86
```

该接口为公开 GET，不需要 Cookie。2026-06-02 Playwright `/all` 捕获到该路径返回 `code:0`，随后直接无 Cookie GET 对 `areaId=0`、`1`、`2`、`86` 均返回 `code:0`，`data` 顶层包含 `num`。

## 全部页动态刷新

当前 `/all` 页面还会请求：

```text
GET https://api.live.bilibili.com/area/dynamic?area=all
```

该接口为公开 GET，不需要 Cookie。2026-06-02 重新捕获 `/all` 页面时保留了公开 `area=all` 参数，浏览器上下文返回 `code:0`；随后直接无 Cookie GET 复验同样返回 `code:0`，`data` 为数字值。该值看起来用于全部页动态刷新/计数状态，具体业务含义仍以页面实际使用为准。
