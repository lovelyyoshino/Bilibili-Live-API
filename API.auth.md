# Bilibili API 鉴权与安全验证边界

> 状态：2026-06-02。本页只描述本仓库验证脚本的安全边界，不提供绕过风控或批量操作账号状态的方法。

## 鉴权类型

| 类型 | 说明 | 默认自动验证 |
| --- | --- | --- |
| `public` | 不需要登录态的 GET 接口。 | 是 |
| `public-wbi` | 不需要登录 Cookie，但需要 `wts`/`w_rid` WBI 签名。 | 是，使用 `scripts/lib/wbi.mjs` |
| `public-or-risk-gated` | 公开读接口，但命令行环境可能返回 `-352`。 | 是，`-352` 记为风控风险 |
| `auth-or-cookie` | 文档要求 Cookie、`access_key`、CSRF、`bili_jct` 或登录态。 | 否 |
| `auth-flow` | 登录、验证码、OAuth、第三方授权等流程。 | 否 |

## 不自动调用的接口

默认脚本不会调用这些接口，即使旧文档把方法写成 GET：

- 送礼、投币、关注、佩戴勋章/头衔、房管操作。
- 发弹幕、直播间进入上报、心跳上报、领取/兑换/签到。
- 抽奖加入、活动报名、风纪委员投票、登录、验证码。
- 需要 Cookie、`access_key`、`csrf`、`csrf_token` 或 `bili_jct` 的接口。

旧版直播抽奖、活动、领取、兑换和账号动作接口的文件级边界见 `API.legacy.safety.md`。

## 当前验证脚本

- `npm run verify:http` 只验证 `data/live-api-manifest.json` 中已人工确认的当前直播读接口；`sensitive: true` 的接口默认跳过，可用 `BILI_INCLUDE_SENSITIVE=1` 或 `-- --include-sensitive` 本地显式启用。
- `npm run verify:ws` 会先尝试 WBI `getDanmuInfo`，并且拒绝向非 `*.chat.bilibili.com` 主机发送 WebSocket 鉴权包。
- `npm run discover:live-page` 默认拦截非 GET 请求，并对证据中的大部分查询参数做脱敏。它支持 `live.bilibili.com`、`www.bilibili.com`、`search.bilibili.com`、`space.bilibili.com` 等 B 站页面目标，并被动记录常见 B 站 API host 的 GET 响应元数据。可用 `-- --interact=scroll,hover,buttons` 或 `BILI_CAPTURE_INTERACTIONS=scroll,hover,buttons` 触发有限滚动、hover 和点击，以发现懒加载接口。
- `npm run discover:bilibili` 是全站页面侧公开 GET 抓取入口，会打开 `www.bilibili.com`、搜索页和直播页组合；仍默认阻断非 GET。
- `npm run verify:candidates` 只从 `API.catalog.md`/`data/api-index.json` 中选择未验证、GET、`safe-read-candidate`、已知公开、非敏感、未匹配危险路径的候选接口；默认只探测 `api.live.bilibili.com`。

`verify:candidates` 的扩展选项应谨慎使用：

- `--include-needs-review`：允许探测仍需人工复核的 GET 行。
- `--include-unknown`：允许探测鉴权状态未知的 GET 行。
- `--include-sensitive`：允许探测可能返回 IP/地理位置等隐私信息的接口。
- `--interact=scroll,hover,buttons`：启用页面交互抓取。该模式仍默认阻断非 GET，但可能产生大量曝光日志 GET，例如 `/xlive/data-interface/v1/index/log`；这些日志只保留为 captured-only。

## 需要账号的接口怎么处理

需要登录态或会改变账号状态的接口，应使用单独的测试账号、明确授权的 Cookie、独立浏览器上下文和人工审核过的请求体。不要把 Cookie、CSRF token、access key 或原始证据提交到仓库。

页面抓取脚本支持本地 Cookie 注入，但不会把 Cookie 值写入 evidence：

```bash
BILI_CAPTURE_COOKIE_FILE=.bilibili-cookies npm run discover:bilibili
BILI_CAPTURE_COOKIES='name=value; another=value' npm run discover:bilibili
BILI_CAPTURE_COOKIE_FILE=.bilibili-cookies npm run discover:bilibili -- --no-screenshots
```

`.bilibili-cookies*`、`bilibili-cookies*.txt`、`bilibili-storage-state*.json`、storage-state、HAR、trace、`.env*` 和 `evidence/` 已加入 `.gitignore`。证据文件只保存 `cookieSource` 和 `cookieCount`，不保存 Cookie 原文。使用真实账号 Cookie 时建议加 `--no-screenshots` 或 `BILI_CAPTURE_SCREENSHOTS=0`，避免页面截图记录账号态信息。
