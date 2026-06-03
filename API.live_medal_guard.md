# 直播用户勋章与大航海 API（2026-06-03 验证）

> 来源：`SocialSisterYi/bilibili-API-collect` + Node.js 实测验证。

## 我的粉丝勋章列表

```text
GET https://api.live.bilibili.com/xlive/app-ucenter/v1/user/GetMyMedals?page=1&page_size=20
```

Cookie GET。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `page` | false | 页码（默认 1）。 |
| `page_size` | false | 每页数量（默认 20）。 |

返回 `data.items` 为勋章数组，每项含 `medal_id`, `medal_name`, `level`, `target_name`, `roomid` 等。

## 佩戴粉丝勋章

```text
POST https://api.live.bilibili.com/xlive/web-room/v1/fansMedal/wear
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `medal_id` | true | 勋章 ID。 |
| `csrf` | true | CSRF token。 |
| `csrf_token` | true | 同上。 |

## 卸下粉丝勋章

```text
POST https://api.live.bilibili.com/xlive/web-room/v1/fansMedal/take_off
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `csrf` | true | CSRF token。 |
| `csrf_token` | true | 同上。 |

## 大航海成员列表

```text
GET https://api.live.bilibili.com/xlive/app-room/v2/guardTab/topListNew?roomid=5050&ruid=433351&page=1&page_size=20
```

Cookie GET。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `roomid` | true | 房间号。 |
| `ruid` | true | 主播 UID。 |
| `page` | false | 页码。 |
| `page_size` | false | 每页数量。 |

返回 `data.top3` 为前三名，`data.list` 为完整列表，含 `uid`, `username`, `guard_level`, `rank` 等。

## 大航海信息

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/guard/topList?roomid=5050&ruid=433351&page=1&page_size=20
```

Cookie GET。返回房间大航海排名和信息。

## 礼物星球面板

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/giftPanel/giftConfig?platform=pc&room_id=5050
```

Cookie GET。已验证返回 `code:0`。

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `list` | array | 礼物列表（含 `id`, `name`, `price`, `coin_type`）。 |
| `combo_resources` | object | 连击资源。 |
| `guard_resources` | object | 大航海资源。 |

## 备注

- `guard_level` 值：1=总督，2=提督，3=舰长。
- 粉丝勋章等级上限为 40 级（需要巨量亲密度）。
- 佩戴勋章后在该直播间发送弹幕会显示勋章。
