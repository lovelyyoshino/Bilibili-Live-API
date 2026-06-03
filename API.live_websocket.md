# 直播 WebSocket 消息协议（2026-06-03 更新）

> 来源：`SocialSisterYi/bilibili-API-collect` + 浏览器捕获验证。

## 连接方式

### 1. 获取认证信息

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/index/getDanmuInfo?id=<room_id>
```

Cookie GET。已验证返回 `code:0`。返回：
- `data.token` — 认证 key
- `data.host_list[]` — WebSocket 服务器列表（含 `host`, `port`, `ws_port`, `wss_port`）

### 2. 建立 WebSocket 连接

```text
wss://{host}:{wss_port}/sub
```

### 3. 认证包

连接后立即发送 Operation=7（Auth）包：
```json
{
  "uid": 0,
  "roomid": 5050,
  "protover": 3,
  "platform": "web",
  "type": 2,
  "key": "<token from getDanmuInfo>"
}
```

### 4. 心跳包

每 30 秒发送 Operation=2（Heartbeat）包，服务器返回 Operation=3（在线人数）。

## 数据包格式

| 偏移 | 长度 | 说明 |
| --- | --- | --- |
| 0 | 4 | 包总长度（大端） |
| 4 | 2 | 头部长度（固定 16） |
| 6 | 2 | 协议版本：`0` JSON, `1` 人气值, `2` zlib, `3` brotli |
| 8 | 4 | 操作码 |
| 12 | 4 | 序列号 |
| 16 | - | 数据体 |

### 操作码

| 值 | 说明 |
| --- | --- |
| 2 | 客户端心跳 |
| 3 | 服务端心跳回复（人气值） |
| 5 | 服务端推送消息（CMD） |
| 7 | 客户端认证 |
| 8 | 服务端认证回复 |

## CMD 消息类型

### 弹幕相关

| CMD | 说明 |
| --- | --- |
| `DANMU_MSG` | 弹幕消息（含用户信息、勋章、等级）。 |
| `DANMU_AGGREGATION` | 弹幕聚合（相同内容合并展示）。 |
| `DM_INTERACTION` | 弹幕互动（连击等）。 |

### 礼物相关

| CMD | 说明 |
| --- | --- |
| `SEND_GIFT` | 送礼物。含 `giftName`, `num`, `price`, `uid`, `uname`。 |
| `COMBO_SEND` | 连击礼物。 |
| `GIFT_STAR_PROCESS` | 礼物星球进度。 |
| `POPULARITY_RED_POCKET_NEW` | 人气红包。 |
| `POPULARITY_RED_POCKET_START` | 红包开始。 |
| `POPULARITY_RED_POCKET_WINNER_LIST` | 红包中奖名单。 |

### 大航海（Guard）

| CMD | 说明 |
| --- | --- |
| `GUARD_BUY` | 上舰（含 `guard_level`: 1=总督, 2=提督, 3=舰长）。 |
| `USER_TOAST_MSG` | 上舰 Toast 通知。 |
| `NOTICE_MSG` | 全站广播通知。 |

### SC（Super Chat）

| CMD | 说明 |
| --- | --- |
| `SUPER_CHAT_MESSAGE` | 醒目留言。含 `message`, `price`, `user_info`。 |
| `SUPER_CHAT_MESSAGE_JPN` | 日文翻译版。 |
| `SUPER_CHAT_MESSAGE_DELETE` | SC 删除。 |
| `SUPER_CHAT_ENTRANCE` | SC 入口配置。 |

### 互动事件

| CMD | 说明 |
| --- | --- |
| `INTERACT_WORD` | 用户进入直播间 / 关注 / 分享。`msg_type`: 1=进入, 2=关注, 3=分享。 |
| `ENTRY_EFFECT` | 入场特效（舰长/年费老爷等）。 |
| `WELCOME` | 欢迎老爷。 |
| `WELCOME_GUARD` | 欢迎舰长。 |
| `LIKE_INFO_V3_CLICK` | 用户点赞。 |
| `LIKE_INFO_V3_UPDATE` | 点赞数更新。 |
| `ONLINE_RANK_COUNT` | 在线排名人数更新。 |
| `ONLINE_RANK_V2` | 高能用户排名。 |
| `WATCHED_CHANGE` | 看过人数变化。 |

### 房间状态

| CMD | 说明 |
| --- | --- |
| `LIVE` | 开播。 |
| `PREPARING` | 下播。 |
| `ROOM_CHANGE` | 房间信息变更（标题/分区）。 |
| `ROOM_BLOCK_MSG` | 用户被封禁。 |
| `ROOM_SILENT_ON` | 全员禁言开启。 |
| `ROOM_SILENT_OFF` | 全员禁言关闭。 |
| `CUT_OFF` | 直播被切断。 |
| `STOP_LIVE_ROOM_LIST` | 下播房间列表。 |
| `LIVE_INTERACTIVE_GAME` | 互动游戏。 |

### 活动/抽奖

| CMD | 说明 |
| --- | --- |
| `ANCHOR_LOT_START` | 天选时刻开始。 |
| `ANCHOR_LOT_END` | 天选时刻结束。 |
| `ANCHOR_LOT_AWARD` | 天选时刻开奖。 |
| `ANCHOR_LOT_CHECKSTATUS` | 天选状态检查。 |
| `COMMON_NOTICE_DANMAKU` | 通用公告弹幕。 |
| `WIDGET_BANNER` | 小部件横幅。 |
| `WIDGET_GIFT_STAR_PROCESS` | 礼物星球组件。 |

### PK 相关

| CMD | 说明 |
| --- | --- |
| `PK_BATTLE_PRE_NEW` | PK 预备。 |
| `PK_BATTLE_START_NEW` | PK 开始。 |
| `PK_BATTLE_PROCESS_NEW` | PK 进度。 |
| `PK_BATTLE_FINAL_PROCESS` | PK 最终结算。 |
| `PK_BATTLE_END` | PK 结束。 |
| `PK_BATTLE_SETTLE_V2` | PK 结算 V2。 |

### 其他

| CMD | 说明 |
| --- | --- |
| `HOT_RANK_CHANGED` | 热门排名变化。 |
| `HOT_RANK_CHANGED_V2` | 热门排名 V2。 |
| `AREA_RANK_CHANGED` | 分区排名变化。 |
| `VOICE_JOIN_STATUS` | 语音连麦状态。 |
| `VOICE_JOIN_LIST` | 连麦列表。 |
| `GUARD_HONOR_THOUSAND` | 千舰荣耀。 |
| `RING_STATUS_CHANGE` | 响铃状态。 |
| `RING_STATUS_CHANGE_V2` | 响铃 V2。 |
| `PLAY_TOGETHER_ICON_CHANGE` | 一起玩图标。 |

## 备注

- 协议版本 3（brotli 压缩）是当前 Web 端默认，需解压后按包头递归解析。
- `DANMU_MSG` 的 `info` 字段是数组格式（非对象），结构复杂。
- `SEND_GIFT` 中 `coin_type` 区分金瓜子(gold)和银瓜子(silver)礼物。
- 同一个事件可能同时推送多种 CMD（如上舰同时推送 `GUARD_BUY` + `USER_TOAST_MSG` + `NOTICE_MSG`）。
