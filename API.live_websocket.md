# 直播 WebSocket 消息协议（2026-07-28 更新）

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
| `SEND_GIFT_V2` | 送礼物新版（2026-07 灰度）。核心字段在 `data.data.pb`（base64 protobuf），见下节。 |
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

## 关键事件 data 结构（2026-06-15 实测）

> 以下为登录态 WebSocket（op7 鉴权 + protover 2/zlib）实抓样本解析。升级/等级类事件另见 [`API.live_upgrade_events.md`](./API.live_upgrade_events.md)。

### DANMU_MSG.info[] 数组结构

`DANMU_MSG.info` 是数组（非对象），实测长度 18，关键索引：

| 索引 | 类型 | 说明 |
| --- | --- | --- |
| `info[0]` | array | 弹幕元信息。`[0][3]`=弹幕颜色(int)，`[0][4]`=时间戳(ms)，`[0][9]`=弹幕类型，`[0][11]`=渐变色 |
| `info[1]` | string | **弹幕文本内容** |
| `info[2]` | array | 发送者：`[uid, uname, 是否房管, 是否VIP, 是否年费VIP, 等级rank, 1, name_color]` |
| `info[3]` | array | 粉丝勋章（空则 `[]`）：`[等级, 勋章名, 主播名, 主播房间号, 颜色, 特别标识, ..., guard_level, ...]` |
| `info[4]` | array | 用户等级：`[UL等级, 0, 等级颜色, 等级排名(">50000"等), ...]` |
| `info[5]` | array | 头衔：`[老爷头衔, 头衔图标]` |
| `info[7]` | int | 用户身份/守护等级 |
| `info[9]` | object | `{ct, ts}` 校验与时间戳 |
| `info[15]` | object/int | 扩展信息（含 `extra` JSON 串、`user` 信息） |

> 数组位含义随版本微调，消费端应做长度与类型保护，不要硬编码深层索引。

### SEND_GIFT.data 关键字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `giftName` / `giftId` | string / int | 礼物名 / ID |
| `num` | int | 数量 |
| `price` | int | 单价（瓜子） |
| `coin_type` | string | `gold`=金瓜子（付费），`silver`=银瓜子（免费） |
| `total_coin` | int | 总价 = price × num |
| `uid` / `uname` | int / string | 送礼用户 |
| `action` | string | 动作文案，常为 `投喂` |
| `wealth_level` | int | 送礼用户荣耀（财富）等级 |
| `guard_level` | int | 大航海等级 |
| `medal_info` | object | 粉丝勋章信息（`medal_level`/`guard_level`/`anchor_uname` 等） |
| `sender_uinfo` | object | 发送者完整 uinfo（base/medal/wealth/guard） |
| `combo_send` / `batch_combo_send` | object | 连击信息 |
| `blind_gift` | object/null | 盲盒礼物信息 |

### SEND_GIFT_V2（protobuf 编码，2026-07 灰度中）

> 2026-07 起 B 站对送礼事件灰度上线 `SEND_GIFT_V2`。实测 100 个直播间中仅个别直播间完全从 `SEND_GIFT` 切到 `SEND_GIFT_V2`，两者尚未全量替换，消费端需**同时兼容** `SEND_GIFT`（JSON）与 `SEND_GIFT_V2`（protobuf）。

与旧版最大区别：核心业务字段不再是明文 JSON，而是塞进 `data.data.pb`（base64 + protobuf）。外层仍是 JSON：

```json
{
  "cmd": "SEND_GIFT_V2",
  "danmu": { "area": 0 },
  "data": {
    "dmscore": 672,
    "pb": "CIXA2QgSCeaJ...（base64 protobuf）"
  }
}
```

**解析步骤**：`base64decode(data.data.pb)` → 按下方 proto 反序列化。以下字段名依据与旧版 `SEND_GIFT` JSON 的语义对照 + 实测值逆向而来，标 `?` 的为推断字段，消费端应做类型与存在性保护。

```proto
// SEND_GIFT_V2 data.data.pb 结构（逆向，字段名为推断）
message SendGiftV2 {
  uint64 uid          = 1;  // 送礼用户 uid
  string uname        = 2;  // 送礼用户昵称
  string face         = 3;  // 送礼用户头像 url
  MedalInfo medal     = 8;  // 送礼用户佩戴的粉丝勋章
  BlindGift blind     = 9;  // 盲盒信息（非盲盒礼物时可能缺省）
  GiftData gift       = 10; // 礼物核心数据
  uint32 field11      = 11; // ? 恒为 1
  Batch  batch        = 13; // ? { field1: 连击/批次相关 }
  UserInfo sender     = 15; // 送礼用户完整 uinfo（base + medal）
}

message MedalInfo {
  uint64 anchor_uid   = 1;  // 勋章所属主播 uid
  uint32 medal_level  = 5;  // 勋章等级
  string medal_name   = 6;  // 勋章名
  uint32 color_start  = 7;  // 勋章渐变起始色（十进制）
  uint32 color        = 8;  // 勋章主色
  uint32 color_border = 9;  // 勋章边框色
  uint32 color_end    = 10; // 勋章渐变结束色
  uint32 guard_level  = 11; // ? 大航海等级 / 点亮态
}

message BlindGift {          // 盲盒（心动盲盒等）爆出信息
  uint32 bind_gift_config_id = 1;  // ? 状态码（实测 139）
  uint32 original_gift_id    = 2;  // 盲盒原始礼物 id
  string original_gift_name  = 3;  // 盲盒名（如"心动盲盒"）
  string gift_action   = 5;  // 动作文案（"爆出"）
  uint32 blind_price   = 6;  // 盲盒本身价格（瓜子），一次性送多个盲盒时仅显示该盲盒一个的原价
}

message GiftData {
  uint32 gift_id     = 1;   // 礼物 id
  string gift_name   = 2;   // 礼物名（如"爱心抱枕"）
  uint32 num         = 3;   // 数量
  uint32 gift_type   = 4;   // ? 礼物类型
  uint32 price       = 5;   // 单价（瓜子）
  uint32 discount_price = 6;  // 普通礼物实际单价，盲盒场景下为爆出礼物的单价
  uint32 total_coin  = 7;   // 普通礼物实付总价，盲盒场景下的盲盒原本价格的总价
  string coin_type   = 8;   // gold=金瓜子 silver=银瓜子
  string tid         = 9;   // 交易流水号（字符串大数）
  uint64 timestamp   = 10;  // 送礼时间戳（秒）
  uint32 super       = 11;  // ? 特效/礼物标记
  string rnd         = 12;  // 批次/去重 uuid
  uint32 field13     = 13;  // ?（实测 10）
  uint32 combo_total_coin = 14;  // ? 总价重复（实测 16000），推测为礼物连击事件额外的total_coin字段
  uint32 field15     = 15;  // ?（实测 5）
  float  gift_ratio  = 16;  // ? 礼物比率（fixed32 浮点，实测 1.0）
  uint32 field17     = 17;  // ?
  string action      = 18;  // 动作文案（"投喂"）
  uint32 field24     = 24;  // ?（实测 521790）
  Receiver receiver  = 29;  // 收礼主播 { uname, uid }
  UserInfoWrap uinfo = 33;  // 收礼方 uinfo 包装
  GiftEffect effect  = 35;  // 礼物特效资源图
  uint32 field36     = 36;  // ? 总价重复（实测 16000）
}

message Receiver { string uname = 1; uint64 uid = 2; }

message GiftEffect {          // 礼物动效资源
  string img_basic = 1;      // png 基础图
  string img_2     = 2;      // webp 图
  string img_gif   = 5;      // gif 动图
}

message UserInfo {           // 完整用户信息（base + medal）
  uint64 uid    = 1;
  UserBase base = 2;         // { uname=1, face=2, ... }
  UserMedalColored medal = 3;// 含 #FFFFFF 等十六进制颜色串
}
```

**关键落地建议**：
- `SEND_GIFT_V2` 与 `SEND_GIFT` 语义等价，映射关系：pb `uid/uname/face` ↔ 旧版 `uid/uname/face`；`gift.gift_id/gift_name/num/price/total_coin/coin_type/action` ↔ 旧版同名字段；`medal` ↔ `medal_info`；`blind` ↔ `blind_gift`；`sender` ↔ `sender_uinfo`。
- 颜色字段在 pb `MedalInfo` 中为十进制整数，在 `UserInfo.medal` 中为 `#RRGGBBAA` 十六进制串（如 `#3FB4F699`），两处表示不同，勿混用。
- `?` 字段含义未确认，切勿硬编码依赖；`total_coin`（#6）为实付金额，是计费/统计的权威字段。
- 盲盒礼物：`blind.original_gift_name`=用户抽的盲盒名，`gift.gift_name`=实际爆出的礼物，`gift.total_coin`=盲盒原本价格的总价（`blind_price * num`）。

### 其他高频事件

| CMD | data 结构（实测） |
| --- | --- |
| `WATCHED_CHANGE` | `{num, text_small, text_large}`，如 `1477人看过` |
| `ONLINE_RANK_COUNT` | `{count, count_text, online_count, online_count_text}` 高能榜人数 |
| `ONLINE_RANK_V3` | `data.pb` 为 **protobuf 编码**（base64），顶层 field1=`"online_rank"`，field3=榜单条目子结构 |
| `DM_INTERACTION` | `data.data` 为 JSON 字符串，含 `{cnt, suffix_text:"人正在点赞", display_flag}` 等聚合互动；`type` 区分子类型（106=点赞聚合） |
| `PK_INFO` | `{members[], pk_basic, pk_play, pk_match_info, timestamp}`，`members[]` 含双方主播 `golds`/`face`/`is_winner` |
| `UNIVERSAL_EVENT_GIFT_V2` | 连麦/多人互动事件，含 `interact_template`（布局 `layout_id`）、`members[]` |
| `TRADING_SCORE` | 带货/交易积分事件（电商区） |

## 备注

- 协议版本 3（brotli 压缩）是当前 Web 端默认，需解压后按包头递归解析。无 brotli 环境可在鉴权包改用 protover 2 走 zlib。
- `DANMU_MSG` 的 `info` 字段是数组格式（非对象），结构见上节。
- `SEND_GIFT` 中 `coin_type` 区分金瓜子(gold)和银瓜子(silver)礼物。
- `SEND_GIFT_V2`（2026-07 灰度）核心字段迁至 `data.data.pb`（base64 protobuf），需先解码；尚未全量替换 `SEND_GIFT`，消费端需同时兼容两者。
- `INTERACT_WORD_V2`、`ONLINE_RANK_V3`、`SEND_GIFT_V2` 等新版事件已 **protobuf 编码**，明文 JSON 解析会失败，需先 base64 解码再按 proto 反序列化。
- 同一个事件可能同时推送多种 CMD（如上舰同时推送 `GUARD_BUY` + `USER_TOAST_MSG` + `NOTICE_MSG`）。
- 匿名连接（uid=0）实测被风控拒绝，需带登录态 `uid` + `buvid3`。
