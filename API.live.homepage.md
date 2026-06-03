# 直播首页与房间页面当前接口

> 当前状态：2026-06-02。由 `npm run discover:live-page -- --target=5050 --target=/` 捕获，并通过 Node 直接 GET/WBI GET 复验为公开读接口。

## 鉴权边界

本页接口均为 GET。未标注 WBI 的接口不需要 Cookie；标注 WBI 的接口需要 `wts` 和 `w_rid`，签名方式见 `API.WBI.md`。验证脚本使用 `scripts/lib/wbi.mjs` 从 `https://api.bilibili.com/x/web-interface/nav` 获取当前 mixin key。

## 首页导航分区

```text
GET https://api.live.bilibili.com/room/v2/Index/getNavigate
```

公开 GET，不需要参数。返回 JSON，`code:0` 时 `data` 是分区/导航数组；2026-06-02 复验到顶层索引键 `0` 到 `6`。

## 分区悬浮推荐

```text
GET https://api.live.bilibili.com/room/v2/Index/getHoverInfo?area_id=1
```

| 字段 | 必选 | 说明 |
| --- | --- | --- |
| `area_id` | true | 直播分区 ID。Playwright 捕获到 `1`、`2`、`3`、`5`、`6`、`9`、`15` 等值。 |

返回 JSON，`code:0` 时 `data` 包含：

| 字段 | 说明 |
| --- | --- |
| `list` | 悬浮推荐房间或分区内容。 |
| `banner` | 悬浮区域 Banner 配置。 |

## 按 UID 查询房间

```text
GET https://api.live.bilibili.com/room/v2/Room/room_id_by_uid?uid=433351
```

| 字段 | 必选 | 说明 |
| --- | --- | --- |
| `uid` | true | 主播 UID。 |

返回 JSON，`code:0` 时 `data.room_id` 是该 UID 对应的直播间号。无效或被隐藏 UID 可能返回非 0 code。

## 首页新版榜单 Top

```text
GET https://api.live.bilibili.com/room/v2/Index/getNewRankTop?type=guard&web_location=444.7&wts=<ts>&w_rid=<sign>
```

WBI GET，不需要 Cookie。

| 字段 | 必选 | 说明 |
| --- | --- | --- |
| `type` | false | 捕获和复验使用 `guard`。 |
| `web_location` | false | 网页端位置标识，捕获为 `444.7`。 |

返回 JSON，`code:0` 时 `data` 是榜单条目数组。

## 首页新版推荐列表

```text
GET https://api.live.bilibili.com/xlive/web-interface/v1/index/getList?platform=web&web_location=444.7&wts=<ts>&w_rid=<sign>
```

WBI GET，不需要 Cookie。该接口是当前直播首页实际捕获到的推荐聚合接口，和旧的 `xlive/web-interface/v1/webMain/getList` 并存。

返回 JSON，`code:0` 时 `data` 常见字段：

| 字段 | 说明 |
| --- | --- |
| `area_entrance_v2` | 首页分区入口。 |
| `room_list` | 首页房间列表模块。 |
| `activity_card_v2` | 活动卡片。 |
| `recommend_room_list` | 推荐直播间列表。 |
| `banner` | 首页 Banner。 |
| `cover_source` | 封面来源配置。 |

## 首页热门榜

```text
GET https://api.live.bilibili.com/xlive/web-interface/v1/index/getHotRankList?web_location=444.7&wts=<ts>&w_rid=<sign>
```

WBI GET，不需要 Cookie。返回 JSON，`code:0` 时 `data` 包含：

| 字段 | 说明 |
| --- | --- |
| `list` | 热门榜条目。 |
| `count` | 条目数量或总量。 |

## 频道推荐榜入口

```text
GET https://api.live.bilibili.com/live_user/v1/Recommend/indexRank
```

公开 GET，不需要 Cookie。2026-06-02 对 `/galaxy` 和 `/lol` 频道页做 GET-only Playwright 捕获时出现该接口，Node 直接请求也返回 `code:0`。

返回 JSON，`code:0` 时 `data` 包含：

| 字段 | 说明 |
| --- | --- |
| `tabs` | 推荐榜入口标签列表；本轮直接复验返回空数组，说明该字段可能随频道/运营配置变化。 |

## 分区遮罩配置

```text
GET https://api.live.bilibili.com/xlive/web-interface/v1/index/getAreaMask?room_id=5050&area_id=236&ruid=433351
```

| 字段 | 必选 | 说明 |
| --- | --- | --- |
| `room_id` | true | 房间号。 |
| `area_id` | true | 房间子分区 ID。 |
| `ruid` | true | 主播 UID。 |

返回 JSON，`code:0` 时 `data.area_masks` 是分区遮罩/展示控制配置。

## 离线推荐列表

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/index/getOffLiveList?room_id=5050&count=5
```

| 字段 | 必选 | 说明 |
| --- | --- | --- |
| `room_id` | true | 房间号。 |
| `count` | false | 推荐数量，复验使用 `5`。 |

返回 JSON，`code:0` 时 `data` 包含：

| 字段 | 说明 |
| --- | --- |
| `tips` | 离线提示文案/配置。 |
| `recommend` | 推荐直播间。 |
| `record_list` | 录播或回放相关列表。 |

## 房间首页弹幕/活动配置

```text
GET https://api.live.bilibili.com/xlive/web-ucenter/v1/room/GetHomePageConfig?room_id=5050
```

公开 GET，不需要 Cookie。虽然路径包含 `web-ucenter`，未登录浏览器上下文和 Node 直接请求均返回 `code:0`。

返回 JSON，`code:0` 时 `data` 包含：

| 字段 | 说明 |
| --- | --- |
| `dm_tag_info` | 弹幕标签配置。 |
| `danmu_extra` | 弹幕附加配置。 |
| `dm_activity` | 弹幕/房间活动配置。 |

## 首页 MCN/运营入口展示配置

```text
GET https://api.live.bilibili.com/xlive/mcn-interface/v1/homePage/CenterEntryIsShow
```

公开 GET，不需要 Cookie。该接口由直播首页捕获，Node 直接请求也稳定返回 `code:0`。虽然路径属于 `mcn-interface`，响应是首页运营入口展示配置，不触发账号或房间状态变更。

返回 JSON，`code:0` 时 `data` 包含：

| 字段 | 说明 |
| --- | --- |
| `navbar` | 顶部导航入口展示配置。 |
| `sidebar` | 侧边栏入口展示配置。 |
| `footer` | 页脚入口展示配置。 |
| `url` | 入口跳转地址。 |

## 公会页在线 Banner 列表

```text
GET https://api.live.bilibili.com/xlive/mcn-interface/v1/homePage/GetOnlineBannerList
```

公开 GET，不需要 Cookie。2026-06-02 `/galaxy` 频道页捕获到该接口，Node 直接请求也返回 `code:0`。虽然路径属于 `mcn-interface`，当前证据显示它只是读取公会/运营页 Banner 列表。

返回 JSON，`code:0` 时 `data` 包含：

| 字段 | 说明 |
| --- | --- |
| `data` | Banner 条目数组；本轮直接复验返回空数组，内容可能随运营配置变化。 |

## 暂不提升的同批捕获接口

以下接口本轮保留为 captured-only，不进入默认验证清单：

- `room/v1/Bg/get_list`、`xlive/web-ucenter/user/get_user_info`、`xlive/web-ucenter/v1/banned/GetShieldInfo`：未登录返回失败码。
- `xlive/lottery-interface/v1/lottery/getLotteryInfoWeb`：彩票/抽奖命名空间且捕获为 `-352`。
- `xlive/open-interface/v2/tracker/conf`：遥测配置，不属于核心公开 API。
- `xlive/play-gateway/master/url`：播放网关内部接口，参数多且响应不是普通 JSON。
- `xlive/web-ucenter/v1/labs/InfoPlugs`、`xlive/web-ucenter/v1/user_title/GetTitles`：可能与用户上下文相关，需更强证据。
- `api.bilibili.com/x/player/hls`、`api.bilibili.com/x/player/wbi/v2`：来自 blackboard 活动页，属于普通视频播放器接口且参数被脱敏，不纳入直播 API manifest。
