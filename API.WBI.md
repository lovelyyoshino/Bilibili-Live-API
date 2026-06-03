# Bilibili WBI 签名

> 状态：2026-06-02 验证可用。B 站风控策略会变化，本页记录当前直播 Web API 验证所需的最小流程。

## 适用场景

部分当前直播接口直接请求会返回：

```json
{"code":-352,"message":"-352","ttl":1}
```

已验证需要或建议带 WBI 的接口：

| 接口 | 说明 |
| --- | --- |
| `/xlive/web-room/v1/index/getDanmuInfo` | 获取弹幕 WebSocket `host_list` 和 `token`，当前 WS 鉴权必须依赖它。 |
| `/xlive/web-room/v1/index/getInfoByRoom` | 获取直播间主聚合信息、主播信息、模块开关、榜单入口等。 |
| `/xlive/web-room/v1/banner/getAdBanner` | 获取直播间广告 Banner 页面模块配置。 |
| `/xlive/e-commerce-interface/v1/ecommerce-user/get_shopping_cart_status` | 获取直播间电商购物车展示状态。 |

## 签名步骤

1. 请求 `https://api.bilibili.com/x/web-interface/nav`。
2. 从 `data.wbi_img.img_url` 和 `data.wbi_img.sub_url` 取文件名，不含扩展名。
3. 拼接两个 key 后按 mixin 表重排，取前 32 位。
4. 将业务参数加上当前 Unix 秒级时间戳 `wts`。
5. 按参数名升序排序并 URL encode。
6. 计算 `md5(query + mixin_key)`，作为 `w_rid`。

本仓库实现见 `scripts/lib/wbi.mjs`。

## 已验证示例

```text
GET https://api.live.bilibili.com/xlive/web-room/v1/index/getDanmuInfo
  ?id=<room_id>
  &type=0
  &web_location=444.8
  &wts=<unix_seconds>
  &w_rid=<md5>
```

返回 `code:0` 时，`data.token` 用于 WebSocket op `7` 鉴权，`data.host_list[*].host` 和 `data.host_list[*].wss_port` 用于选择连接地址。

## 注意

- `x/web-interface/nav` 未登录时也可能返回 `code:-101`，但仍可包含 `data.wbi_img`。
- `-352` 是风控/上下文问题，不等价于接口下线。
- 同一个签名 URL 在不同 IP、headers、房间、时间窗口下可能表现不同；请以 `npm run verify:http` 的当前证据为准。
