# Bilibili API 文档集合

> 全面覆盖 B 站直播、视频、用户、社交、搜索等接口文档。
> 最后验证日期：2026-06-03

## 概述

本项目收集和验证 Bilibili（哔哩哔哩）平台的 Web API 接口文档，涵盖直播、视频播放、用户空间、社交互动、私信、评论、搜索等模块。所有接口均通过 Node.js 脚本或 Playwright 浏览器实测验证。

### 参考资源

| 资源 | 说明 |
| --- | --- |
| [SocialSisterYi/bilibili-API-collect](https://github.com/SocialSisterYi/bilibili-API-collect) | 最全面的 B 站 API 第三方文档（本项目主要参考源） |
| [fython/BilibiliAPIDocs](https://github.com/fython/BilibiliAPIDocs) | 早期番剧 API 文档（本项目前身） |
| [bilibili-api (PyPI)](https://pypi.org/project/bilibili-api/) | Python 开发库 |

### 技术要点

- [WBI 签名说明](./API.WBI.md) — 部分接口需要 MD5 签名
- [鉴权与安全验证](./API.auth.md) — Cookie / CSRF / Token 机制
- [API 全量索引](./data/live-api-manifest.json) — 验证端点清单（JSON）

---

## 直播 API

### 核心接口

| 文档 | 说明 |
| --- | --- |
| [直播 WebSocket 消息协议](./API.live_websocket.md) | 连接方式 + 数据包格式 + 全部 CMD 类型（弹幕/礼物/SC/大航海/PK 等） |
| [直播间管理](./API.live_room.md) | 房间信息/初始化/批量状态/弹幕配置/历史弹幕/发送弹幕/开播/下播 |
| [粉丝勋章与大航海](./API.live_medal_guard.md) | 勋章列表/佩戴/卸下/大航海成员/礼物面板 |
| [送礼与互动](./API.gift_interaction.md) | 背包礼物发送/礼物列表/房间进入上报/心跳/银瓜子兑换/一起玩 |
| [弹幕 Token](./API.getDanmuInfo.md) | WebSocket 认证 Token 获取 |
| [房间初始化](./API.room_init.md) | 房间 ID 解析/状态/加密 |

### 房间信息

| 文档 | 说明 |
| --- | --- |
| [批量房间基础信息](./API.getRoomBaseInfo.md) | 批量获取直播间信息 |
| [直播间聚合信息](./API.getInfoByRoom.md) | 房间详情聚合 |
| [播放信息](./API.getRoomPlayInfo.md) | 流地址/编码/画质 |
| [直播间主播资料](./API.live.anchor_in_room.md) | 主播信息面板 |
| [贡献榜/在线榜](./API.live.contribution_rank.md) | 排行数据 |

### 直播列表与分区

| 文档 | 说明 |
| --- | --- |
| [直播分区列表](./API.getWebAreaList.md) | 全部直播分区 |
| [直播列表](./API.getRoomList.md) | 分区房间列表 |
| [首页直播推荐](./API.webMain.getList.md) | 首页推荐与排行 |
| [直播首页接口](./API.live.homepage.md) | 首页模块 |

### 直播辅助

| 文档 | 说明 |
| --- | --- |
| [历史弹幕](./API.live.danmu_history.md) | 房间历史弹幕 |
| [礼物面板](./API.live.gift_panel.md) | 礼物配置 |
| [新发现接口](./API.live.discoveries.md) | 其他发现的直播间接口 |

### 直播历史功能（Legacy）

| 文档 | 说明 |
| --- | --- |
| [旧版 WebSocket](./API.WebSocket.md) | 旧版弹幕协议文档 |
| [抽奖检测](./API.Raffle.md) | 已失效 |
| [小电视检测](./API.smalltv.md) | 已失效 |
| [小电视加入](./API.smalltv_join.md) | 已失效 |
| [风暴检测](./API.storm.md) | 已失效 |
| [风暴加入](./API.storm_join.md) | 已失效 |
| [活动抽奖](./API.YunYing.md) | 已失效 |

---

## 视频 API

| 文档 | 说明 |
| --- | --- |
| [视频播放器接口](./API.video.md) | 播放器信息/在线人数/弹幕分段(Protobuf)/字幕/心跳上报 |
| [视频互动操作](./API.video_actions.md) | 点赞/投币/收藏/分享/一键三连/关系查询 |
| [视频信息](./API.view.md) | 视频详情/分P/标签 |
| [弹幕](./API.comment.md) | 视频弹幕获取 |

---

## 用户与社交 API

| 文档 | 说明 |
| --- | --- |
| [用户空间](./API.space.md) | 详细信息/投稿列表/专栏/导航计数/追番/合集/收藏夹 |
| [用户关系](./API.relation.md) | 关注/取关/拉黑/关注列表/粉丝列表/分组 |
| [私信系统](./API.private_message.md) | 未读数/会话列表/聊天记录/发送私信 |
| [用户信息](./API.UserInfo.md) | 基础用户信息 |
| [我的信息](./API.myinfo.md) | 登录用户信息 |
| [好友/黑名单](./API.friend.md) | 好友与拉黑列表 |

---

## 评论与动态 API

| 文档 | 说明 |
| --- | --- |
| [评论系统](./API.comments.md) | 列表(分页+游标)/回复/热评/发表/点赞/点踩/删除 |
| [动态 Feed](./API.dynamic_feed.md) | 关注时间线/用户空间动态/动态详情/发布/转发/点赞/删除 |
| [动态（旧版）](./API.dynamic.md) | 旧版动态接口 |

---

## 搜索与发现 API

| 文档 | 说明 |
| --- | --- |
| [搜索](./API.search_v2.md) | 综合搜索/分类搜索/搜索建议/默认词/热搜榜 |
| [消息中心与搜索](./API.message_search.md) | IM 未读/Toast/黑名单/搜索建议/广告位 |
| [搜索（旧版）](./API.search.md) | 旧版搜索接口 |
| [搜索关键词](./API.suggest.md) | 关键词补全 |

---

## 登录与鉴权 API

| 文档 | 说明 |
| --- | --- |
| [登录](./API.login.md) | 密码/验证码登录 |
| [第三方登录](./API.login.3rd.md) | OAuth 登录 |
| [OAuth](./API.oauth.Login.md) | access_key 获取 |
| [WBI 签名](./API.WBI.md) | 接口签名机制 |
| [鉴权与安全](./API.auth.md) | Cookie/CSRF/风控 |

---

## 番剧与内容 API

| 文档 | 说明 |
| --- | --- |
| [番剧](./API.bangumi.md) | 番剧信息 |
| [每周番剧](./API.bangumi.week.md) | 时间表 |
| [收藏/关注](./API.favourite.md) | 收藏夹操作 |
| [历史记录](./API.history.md) | 观看历史 |
| [APP 历史](./API.app.History.md) | 移动端历史 |
| [排行信息](./API.index.md) | 首页排行 |
| [视频排行](./API.list.md) | 分区排行 |
| [标签](./API.tags.md) | 视频标签 |
| [通知](./API.notify.md) | 系统通知 |

---

## 直播榜单与管理

| 文档 | 说明 |
| --- | --- |
| [舰队榜](./API.topList.md) | 大航海排名 |
| [主播信息](./API.Room_master.md) | 主播详情 |
| [房管](./API.roomAdmin.md) | 房管列表 |
| [批量关注](./API.attention.md) | 批量关注主播 |
| [投币](./API.GiveCoin2toAv.md) | 自动投币 |
| [硬币兑换](./API.coin.md) | 银瓜子兑换 |
| [日常包裹](./API.receive_daily_bag.md) | 每日背包 |
| [佩戴勋章](./API.WearFansMedal.md) | 粉丝勋章佩戴 |

---

## 风纪委员

| 文档 | 说明 |
| --- | --- |
| [案件识别](./API.juryCase.md) | 风纪委案件 |
| [投票](./API.jury_vote.md) | 风纪委投票 |

---

## 项目结构

```
├── API.*.md           → API 接口文档（73 个文件）
├── README.md          → 本文件（索引）
├── API.catalog.md     → 自动生成的全量端点索引
├── API.WBI.md         → WBI 签名机制说明
├── API.auth.md        → 鉴权说明
├── data/              → 验证数据（manifest JSON）
├── scripts/           → 验证脚本（Node.js）
└── Host.md            → API 延迟检测
```

## API 覆盖统计

| 分类 | 端点数 | 状态 |
| --- | --- | --- |
| 直播核心（WebSocket/房间/弹幕） | ~40 | ✅ 已验证 |
| 直播辅助（礼物/勋章/榜单） | ~25 | ✅ 已验证 |
| 视频（播放/互动/弹幕） | ~15 | ✅ 已验证 |
| 用户（空间/关系/私信） | ~20 | ✅ 已验证 |
| 评论与动态 | ~15 | ✅ 已验证 |
| 搜索与发现 | ~10 | ✅ 已验证 |
| 登录与鉴权 | ~8 | ✅ 已验证 |
| 番剧与内容 | ~10 | 📄 文档化 |
| 历史/Legacy | ~15 | ⚠️ 已失效 |
| **合计** | **~160+** | |

## 鉴权方式说明

| 方式 | 说明 |
| --- | --- |
| 公开 GET | 无需任何认证 |
| WBI GET | 需要 MD5 签名（mixin key 来自 `/x/web-interface/nav`） |
| Cookie GET | 需要 `SESSDATA` Cookie |
| Cookie POST | 需要 `SESSDATA` + `csrf`（= `bili_jct`） |
| APP Token | 移动端 `access_key` 认证 |

## 贡献

欢迎提交 PR 补充新发现的 API 端点。请确保：
1. 提供实际验证结果（请求/响应示例）
2. 标注鉴权方式和必需参数
3. 注明验证日期和测试环境
