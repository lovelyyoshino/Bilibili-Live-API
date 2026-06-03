# 直播间礼物面板

> 当前状态：2026-06-02 使用 `npm run verify:http` 验证为公开读接口。

## 礼物配置

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/giftPanel/roomGiftConfig
```

### 鉴权

公开 GET，不需要 Cookie。当前验证不需要 WBI。

### 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `room_id` | true | GET | int | 直播房间号。 |
| `platform` | false | GET | string | 网页端常见 `pc`。 |
| `source` | false | GET | string | 网页端常见 `live`。 |

### 已验证示例

```text
https://api.live.bilibili.com/xlive/web-room/v1/giftPanel/roomGiftConfig?room_id=5050&platform=pc&source=live
```

### 返回

返回 JSON。`code:0` 时，2026-06-02 验证到的 `data` 顶层字段包括：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `global_gift` | object/array | 全局礼物配置。 |
| `list` | array | 礼物配置列表。 |
| `combo_resources` | object | 连击资源配置。 |
| `guard_resources` | object | 大航海/守护相关资源配置。 |
| `naming_gift` | object | 命名礼物配置。 |
| `send_disable_msg` | string/object | 禁用发送相关提示。 |
| `gift_protocols` | array/object | 礼物协议或展示配置。 |

## 礼物列表

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/giftPanel/roomGiftList
```

### 鉴权

公开 GET，不需要 Cookie。当前验证不需要 WBI。

### 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `room_id` | true | GET | int | 直播房间号。 |
| `platform` | false | GET | string | 网页端常见 `pc`。 |
| `area_parent_id` | false | GET | int | 父分区 ID；网页端会按直播间分区传入。 |
| `area_id` | false | GET | int | 子分区 ID；网页端会按直播间分区传入。 |

### 已验证示例

```text
https://api.live.bilibili.com/xlive/web-room/v1/giftPanel/roomGiftList?room_id=5050&platform=pc&area_parent_id=2&area_id=86
```

### 返回

返回 JSON。`code:0` 时，2026-06-02 验证到的 `data` 顶层字段包括：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `gift_data` | object | 礼物数据。 |
| `gift_config` | object | 礼物面板配置。 |
| `global_config` | object | 全局配置。 |

## 备注

- 这是公开读取礼物面板配置的接口；送礼动作仍属于需要账号、Cookie/CSRF 的状态改变接口，不在自动验证范围内。
- `roomGiftList` 的分区参数可能影响返回内容，建议优先使用房间信息接口返回的分区 ID。
