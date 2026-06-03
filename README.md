# Bilibili API 直播，番剧文档
本文clone了[fython大佬](https://github.com/fython/BilibiliAPIDocs)
番剧相关api，并新增了bilibili直播相关API参数。（并希望大家能够提供API或者思路，毕竟总结有可能不全）

###### 提供一个便捷的python开发库：[链接](https://pypi.org/project/bilibili-api/)

### 直播 API

##### Tip:  [检测B站api延迟](./Host.md)
##### Tip:  [API 全量索引](./API.catalog.md)
##### Tip:  [当前直播 API 验证状态](./LIVE_API_STATUS.md)
##### Tip:  [当前直播 API 发现与验证](./API.live.current.md)
##### Tip:  [WBI 签名说明](./API.WBI.md)
##### Tip:  [鉴权与安全验证边界](./API.auth.md)
##### Tip:  [旧版直播接口安全边界](./API.legacy.safety.md)

> 当前可直接复核的直播公开读接口以 `LIVE_API_STATUS.md`、`API.live.current.md` 和 `data/live-api-manifest.json` 为准。旧列表中需要 Cookie、`access_key`、CSRF、领取、加入、赠送、关注、投币、抽奖、登录或验证码的接口只作为历史资料保留，默认验证脚本不会调用，也不应当视为当前可用 API。

[房间初始化/锁定状态](./API.room_init.md)；
[获取cookies与access_key](./API.oauth.Login.md)；
[检查抽奖状态](./API.Room_entry_action.md)；
[礼物赠送](./API.Bag_send.md)；
[佩戴勋章](./API.WearFansMedal.md)；
[佩戴指定活动头衔](./API.WearTitle.md)；

[APP端应援团签到](./API.Link_setting.md)；
[获取应援团信息](./API.Link_group.md)；
[用户信息](./API.getUserInfo.md)；
[直接查用户信息](./API.UserInfo.md)

[总督亲密度领取(已失效)](./API.live.Search.md)；
[日常包裹](./API.receive_daily_bag.md)；
[硬币验证码获取](./API.sliver.md)；

[活动PC检测抽奖（已失效）](./API.Raffle.md)；
[活动加入PC抽奖（已失效）](./API.Raffle_join.md)；
[活动加入APP抽奖（已失效）](./API.YunYing.md)；
[小电视（舰长/pk/大楼）检测](./API.smalltv.md)；
[小电视（舰长/pk/大楼）加入](./API.smalltv_join.md)；

[热门房间](./API.liveList.md)；
[直播列表](./API.getRoomList.md);
[检测风暴](./API.storm.md)；
[加入风暴](./API.storm_join.md)；

[双倍硬币兑换](./API.coin.md)；
[APP双倍硬币兑换](./API.App_silver2coin.md)；
[获取硬币参数](./API.getStatus.md)；

[加入实物奖励](./API.box_getStatus.md)；
[批量关注主播](./API.attention.md)；
[自动投币](./API.GiveCoin2toAv.md)；

[电视礼物结果](./API.TVResult.md)；
[活动礼物结果](./API.RaffleResult.md)；

[风纪委员案件识别](./API.caseObtain.md)；
[识别案件情况](./API.juryCase.md)；
[风纪委员投票](./API.jury_vote.md)；


[七日榜](./API.RoomRank.md)；
[主播信息](./API.Room_master.md)；
[舰队榜](./API.topList.md)；
[友爱榜](./API.UnionFans.md)；
[粉丝榜](./API.webMedalRank.md)；
[房管](./API.roomAdmin.md)；


### 直播其他操作，包含 WebSocket协议

[弹幕WS协议](./API.WebSocket.md)
[当前直播 API](./API.live.current.md)
[新版批量房间基础信息](./API.getRoomBaseInfo.md)
[新版直播间聚合信息](./API.getInfoByRoom.md)
[新版网页播放信息](./API.getRoomPlayInfo.md)
[弹幕服务器和 Token](./API.getDanmuInfo.md)
[直播间历史弹幕](./API.live.danmu_history.md)
[直播间礼物面板](./API.live.gift_panel.md)
[直播间主播资料](./API.live.anchor_in_room.md)
[直播间贡献榜/在线榜](./API.live.contribution_rank.md)
[直播全屏特效配置](./API.live.effects.md)
[直播间游戏卡片信息](./API.live.room_game_card_info.md)
[直播间表情引导配置](./API.live.emoticon_guide.md)
[直播间 WBI 页面模块](./API.live.page_modules.md)
[直播资源与配置接口](./API.live.resources.md)
[直播推荐接口](./API.live.recommendations.md)
[直播首页与房间页面当前接口](./API.live.homepage.md)
[直播电竞赛事数据接口](./API.live.esports.md)
[新版首页直播推荐与排行](./API.webMain.getList.md)
[新版直播分区列表](./API.getWebAreaList.md)
[播放记录获取](./API.RealRoom.md)
[历史记录](./API.app.History.md)




### API 番剧
[作者推荐](./API.author_recommend.md)；
[番剧](./API.bangumi.md)；
[每周番剧](./API.bangumi.week.md)
[弹幕](./API.comment.md)；
[动态](./API.dynamic.md)；

[视频/专题收藏、关注](./API.favourite.md)；
[视频评论](./API.feedback.md)；
[好友/悄悄关注/黑名单](./API.friend.md)；

[历史记录](./API.history.md)；
[批量获取排行信息(首页)](./API.index.md)；
[获取视频排行信息](./API.list.md)；

[登录行为记录](./API.log.md)；
[第三方登录](./API.login.3rd.md)；
[登录](./API.login.md)；

[我的信息](./API.myinfo.md)；
[通知](./API.notify.md)；
[视频推荐](./API.recommend.md)；
[搜索](./API.search.md)；
[读取/创建专题](./API.sp.md)；

[番剧专题](./API.spview.md)；
[搜索关键词](./API.suggest.md)；
[标签](./API.tags.md)；
[用户信息](./API.UserInfo.md)；
[获取视频信息](./API.view.md).
