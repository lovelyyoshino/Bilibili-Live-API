# 送礼与互动 API（2026-06-03 验证）

> 通过 Node.js + Playwright 双重验证。POST 类接口需要 Cookie + `csrf`（即 `bili_jct`）。

## 送背包礼物

```text
POST https://api.live.bilibili.com/xlive/revenue/v2/gift/sendBag
```

Cookie POST。需要 `csrf` / `csrf_token`。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `uid` | true | 发送者 UID。 |
| `gift_id` | true | 礼物 ID（从 bag_list 获取）。 |
| `gift_num` | true | 发送数量。 |
| `bag_id` | true | 背包物品 ID（从 bag_list 获取）。 |
| `ruid` | true | 主播 UID。 |
| `roomid` | true | 房间号。 |
| `biz_id` | false | 业务 ID，默认 `0`。 |
| `biz_code` | false | 业务代码，默认 `live`。 |
| `platform` | false | 平台，`pc`。 |
| `csrf` | true | CSRF token（= `bili_jct`）。 |
| `csrf_token` | true | 同上。 |

返回 `code:0` 表示送礼成功。`code:200015` 表示系统暂时不可用（房间未开播）。

## 背包礼物列表

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/gift/bag_list?room_id=5050
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `list` | array | 背包礼物列表，每项含 `bag_id`, `gift_id`, `gift_name`, `gift_num`, `expire_at`。 |
| `time` | int | 服务器时间。 |
| `gift_config` | object | 礼物配置。 |

## 房间礼物配置

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/giftPanel/giftConfig?platform=pc&room_id=5050
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `list` | array | 礼物列表。 |
| `combo_resources` | object | 连击资源配置。 |
| `guard_resources` | object | 大航海资源。 |
| `naming_gift` | object | 冠名礼物。 |
| `send_disable_msg` | string | 发送禁用提示。 |

## 房间进入上报

```text
POST https://api.live.bilibili.com/xlive/web-room/v1/index/roomEntryAction
```

Cookie POST。进入直播间时上报。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `room_id` | true | 房间号。 |
| `platform` | false | 平台，`pc`。 |
| `csrf` | true | CSRF token。 |
| `csrf_token` | true | 同上。 |

返回 `code:0` 表示上报成功。

## 直播心跳上报

```text
POST https://live-trace.bilibili.com/xlive/data-interface/v1/x25Kn/E
```

Cookie POST。用于直播间在线心跳上报。浏览器捕获确认返回：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `timestamp` | int | 服务器时间戳。 |
| `heartbeat_interval` | int | 下次心跳间隔（秒）。 |
| `secret_key` | string | 加密密钥。 |
| `secret_rule` | array | 加密规则。 |
| `patch_status` | int | 补丁状态。 |

注意：心跳参数格式复杂，需要 `id`（数组编码）、`device`、`ts` 等字段。

## 银瓜子/硬币兑换状态

```text
GET https://api.live.bilibili.com/pay/v1/Exchange/getStatus
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `silver` | int | 银瓜子余额。 |
| `gold` | int | 金瓜子余额。 |
| `coin` | int | 硬币余额。 |
| `coin_2_silver_left` | int | 今日硬币→银瓜子剩余次数。 |
| `silver_2_coin_left` | int | 今日银瓜子→硬币剩余次数。 |
| `status` | int | 兑换状态。 |
| `vip` | int | VIP 状态。 |

## 一起玩信息

```text
GET https://api.live.bilibili.com/xlive/app-ucenter/v2/playTogether/GetPlayTogetherUserAnchorInfoV2?room_id=5050&ruid=433351
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `user_anchor_info_base` | object | 主播一起玩基础信息。 |
| `anchor_goods_info_list` | array | 主播商品信息列表。 |

## 一起玩服务卡片

```text
GET https://api.live.bilibili.com/xlive/app-ucenter/v2/playTogether/PlayTogetherServiceCardDetail?room_id=5050&ruid=433351
```

Cookie GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `card_level` | int | 卡片等级。 |
| `title` | string | 标题。 |
| `sub_title` | string | 副标题。 |
| `game_icon` | string | 游戏图标 URL。 |
| `goods_price` | string | 商品价格。 |
| `button_text` | string | 按钮文字。 |
| `jump_url_web` | string | 网页跳转 URL。 |
| `jump_url_app` | string | APP 跳转 URL。 |

## 用户动态 Feed

```text
GET https://api.bilibili.com/x/polymer/web-dynamic/v1/feed/space?host_mid=<uid>&offset=&timezone_offset=-480
```

公开 GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `update_num` | int | 更新数量。 |
| `update_baseline` | string | 更新基线 ID。 |
| `offset` | string | 翻页偏移。 |
| `has_more` | bool | 是否有更多。 |
| `total` | int | 总数。 |
| `items` | array | 动态列表。 |

## 备注

- `sendBag` 需要房间开播时才能成功发送，未开播返回 `200015`。
- 心跳接口（`x25Kn/E`）在 `live-trace.bilibili.com` 域名，参数编码规则复杂。
- 所有 POST 接口需要 `csrf` 参数，值等于 Cookie 中的 `bili_jct`。
- `csrf` 和 `csrf_token` 通常都需要传，值相同。
