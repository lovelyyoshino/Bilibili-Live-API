# 新发现直播间 API（2026-06-03 浏览器捕获验证）

> 通过 Playwright 浏览器捕获 + Node.js 直接请求双重验证。

## IP 信息

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/index/getIpInfo?room_id=5050
```

需要 Cookie。返回 `code:0` 时 `data` 字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `addr` | string | IP 地址。 |
| `country` | string | 国家。 |
| `province` | string | 省份。 |
| `city` | string | 城市。 |
| `isp` | string | 运营商。 |
| `latitude` | float | 纬度。 |
| `longitude` | float | 经度。 |

## 屏蔽/禁言信息

```text
GET https://api.live.bilibili.com/xlive/web-ucenter/v1/banned/GetShieldInfo?room_id=5050
```

需要 Cookie。返回 `code:0` 时 `data` 字段：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `shield_user_list` | array | 屏蔽用户列表。 |
| `keyword_list` | array | 屏蔽关键词列表。 |
| `shield_rules` | object | 屏蔽规则配置。 |

## 表情引导

```text
GET https://api.live.bilibili.com/xlive/web-ucenter/v1/emoticon/GetEmoticonGuide?room_id=5050&platform=pc
```

需要 Cookie + 正确 referer (`https://live.bilibili.com/`)。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `is_open_guide` | bool | 是否开启引导。 |
| `guide_duration` | int | 引导持续时间。 |
| `guide_emoticon_info` | object | 引导表情配置。 |

## 实验室插件配置

```text
GET https://api.live.bilibili.com/xlive/web-ucenter/v1/labs/InfoPlugs?room_id=5050
```

需要 Cookie。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `configs` | array | 实验室功能配置列表。 |
| `uid` | int | 当前用户 UID。 |

## 用户头衔列表

```text
GET https://api.live.bilibili.com/xlive/web-ucenter/v1/user_title/GetTitles?room_id=5050
```

需要 Cookie。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `list` | array | 可佩戴的头衔列表。 |

## 关注直播间列表（xfetter）

```text
GET https://api.live.bilibili.com/xlive/web-ucenter/v1/xfetter/GetWebList?room_id=5050
```

需要 Cookie。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `rooms` | array | 已关注的直播间列表。 |
| `list` | array | 直播间详细列表。 |
| `count` | int | 总数。 |
| `not_living_num` | int | 未开播数量。 |

## 直播间抽奖信息

```text
GET https://api.live.bilibili.com/xlive/lottery-interface/v1/lottery/getLotteryInfoWeb?roomid=5050
```

需要 Cookie + referer。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `pk` | object/null | PK 抽奖信息。 |
| `guard` | object/null | 大航海抽奖。 |
| `gift` | object/null | 礼物抽奖。 |
| `storm` | object/null | 节奏风暴。 |
| `silver` | object/null | 银瓜子抽奖。 |
| `activity_box` | object/null | 活动宝箱。 |
| `danmu` | object/null | 弹幕抽奖。 |
| `anchor` | object/null | 天选之人。 |

## 房间礼物列表

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/giftPanel/roomGiftList?room_id=5050&platform=pc
```

需要 Cookie。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `gift_data` | object | 礼物数据，含各分类礼物。 |
| `gift_config` | object | 礼物配置。 |
| `global_config` | object | 全局配置。 |

## 收入 MD5 配置

```text
GET https://api.live.bilibili.com/xlive/general-interface/v1/content/getRevenueMd5
```

公开 GET + Cookie。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `wealth` | string | 财富等级 MD5 配置。 |

## 资源查询

```text
GET https://api.live.bilibili.com/xlive/open-interface/v1/query_resource
```

公开 GET + Cookie。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `hash` | string | 资源哈希。 |
| `kv` | object | 键值资源配置。 |
| `medal_icon` | object | 勋章图标资源。 |
| `birthday` | object | 生日相关资源。 |
| `guard_resource` | object | 大航海资源。 |
| `guard_resource_new` | object | 新版大航海资源。 |
| `medal` | object | 粉丝勋章资源。 |
| `medal_level_up_animation` | object | 勋章升级动画。 |

## RTC 时间戳

```text
GET https://api.live.bilibili.com/xlive/open-interface/v1/rtc/getTimestamp
```

公开 GET。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `timestamp` | int | Unix 秒级时间戳。 |
| `microtime` | int | 微秒时间戳。 |

## 游戏卡片信息

```text
GET https://api.live.bilibili.com/xlive/game-pc/room_game_card_info?room_id=5050
```

公开 GET + Cookie。返回 `code:0` 时 `data` 可能为空对象（无游戏关联时）。

## 导航栏用户统计

```text
GET https://api.bilibili.com/x/web-interface/nav/stat
```

需要 Cookie。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `following` | int | 关注数。 |
| `follower` | int | 粉丝数。 |
| `dynamic_count` | int | 动态数。 |
| `fans_effect` | object | 粉丝效果。 |

## Gaia 安全网关

```text
GET https://api.bilibili.com/x/internal/gaia-gateway/ExGetAxe
```

需要 Cookie。返回 `code:0` 时：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `version` | int | 版本号。 |
| `public_key` | string | RSA 公钥（用于客户端加密）。 |
| `deadline` | int | 过期时间戳。 |

## 备注

- 标注"需要 Cookie + referer"的接口在 CLI 直接请求时返回 404，必须带 `origin: https://live.bilibili.com` 和正确 referer。
- 部分接口（GetWidgetBannerList、GetEffectConfListV2、content/get、fetch_client_resource）需要特定参数组合，浏览器上下文正常返回 `code:0`，CLI 返回 `-400`。
- 所有接口于 2026-06-03 通过 Playwright + Node.js 双重验证。
