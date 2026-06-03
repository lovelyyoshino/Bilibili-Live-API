## WebSocket

Bilibili 直播弹幕 WebSocket 协议

### Current Browser Token Flow (2026 当前浏览器 token 流程)

当前网页端弹幕连接不要再依赖“直接连 `broadcastlv` 并发送无 token 鉴权”的旧流程。已验证的公开路径是：

#### Current Connection Steps

1. 用 `room/v1/Room/room_init?id=<room_id>` 解析房间和直播状态。
2. 使用 WBI 签名请求 `xlive/web-room/v1/index/getDanmuInfo?id=<room_id>&type=0&web_location=444.8&wts=<ts>&w_rid=<sign>`。
3. 从返回的 `host_list` 选择 `host` + `wss_port` 连接 `/sub`，例如 `wss://zj-cn-live-comet.chat.bilibili.com:2245/sub`。`broadcastlv.chat.bilibili.com:2245` 仍可能作为列表内 fallback。
4. op `7` 鉴权包需要带 `key`，值为 `getDanmuInfo` 返回的 token。空 token/旧式无 token 鉴权会被关闭。
5. 默认使用 `protover: 3`，op `5` 消息可能是 brotli 压缩；仍保留 `protover: 2` zlib 兼容。

#### Current Auth Payload

当前推荐鉴权 JSON：

```json
{
  "uid": 0,
  "roomid": 545068,
  "protover": 3,
  "buvid": "<buvid3-or-random-client-id>",
  "support_ack": true,
  "queue_uuid": "<random-8-hex>",
  "scene": "",
  "platform": "web",
  "type": 2,
  "key": "<getDanmuInfo token>"
}
```

已观察到的服务端操作：

| op | 说明 |
| --- | --- |
| 3 | 心跳回应，body 为 4 字节 Big Endian 人气值 |
| 5 | 弹幕/互动/点赞/观看变化等消息 |
| 8 | 鉴权成功，body 可为 `{"code":0}` |
| 24 | 已观察到当前网页端会发送的 ack 相关操作；历史 positive evidence 只保存包头元数据。后续 sanitizer-era capture 再观察到 op `24` 时，只保存脱敏 payload 形态 |

#### Safe Verification Commands

本仓库提供可重复验证脚本：

```bash
npm run verify:ws
BILI_WS_TIMEOUT_MS=12000 npm run verify:ws -- --sample-events=8
BILI_WS_TIMEOUT_MS=8000 npm run verify:ws -- --reconnect-attempts=2 --sample-events=4
BILI_WS_TIMEOUT_MS=10000 npm run verify:ws -- --sample-events=4 --host-fallbacks=3
BILI_WS_TIMEOUT_MS=12000 npm run verify:ws -- --sample-events=4 --prepend-test-host=wss://broadcastlv.chat.bilibili.com:1/sub
BILI_WS_TIMEOUT_MS=38000 npm run verify:ws -- --sample-events=4 --heartbeat-wait-ms=32000 --min-heartbeats=2
BILI_CAPTURE_TIMEOUT_MS=45000 npm run discover:live-page -- --target=5050
npm run discover:ws-report -- evidence/live-page-capture-5050-<stamp>.json
```

脚本默认在收到 4 个事件后结束；`--sample-events=<n>` 或 `BILI_WS_SAMPLE_EVENTS=<n>` 可加深采样。`--reconnect-attempts=<n>` 或 `BILI_WS_RECONNECT_ATTEMPTS=<n>` 可执行 1 到 5 次顺序重连验证，每次都会重新获取 `getDanmuInfo` token、重新连接、重新发送 op `7` 鉴权和 op `2` 心跳。`--host-fallbacks=<n>` 或 `BILI_WS_HOST_FALLBACKS=<n>` 会在单次连接尝试内按 `host_list` 去重后的 WSS 地址最多尝试 1 到 10 个 host；默认值为 `1`，只有前一个 host `closed`、`timeout` 或 `error` 时才继续下一个。`--prepend-test-host=<wss://*.chat.bilibili.com/.../sub>` 或 `BILI_WS_PREPEND_TEST_HOST` 仅用于受控负向测试：它会把一个允许域名下的失败 `/sub` host 放在真实 `host_list` 之前，并把有效 host fallback 数提升到至少 `2`，以验证 fallback 证据记录。该参数仍会经过 `wss://*.chat.bilibili.com` 与 `/sub` 路径校验。`--heartbeat-wait-ms=<ms>` 或 `BILI_WS_HEARTBEAT_WAIT_MS=<ms>` 会让脚本在达到采样数后继续保持连接到指定时长；配合 `--min-heartbeats=<n>` 或 `BILI_WS_MIN_HEARTBEATS=<n>` 可验证 30 秒周期心跳回应。

#### Evidence and Privacy Boundaries

证据 JSON 会记录 `sampleEvents`、`timeoutMs`、`hostFallbacks`、`prependTestHost`、`heartbeatWaitMs`、`minHeartbeats`、`operationCounts`、`protocolVersionCounts`、`heartbeatReplyCount`、`messageCount`、`popularitySamples` 和去重后的 `messageCommands`。`messageTimeline` 只保存前 50 条消息的顺序号、时间、命令名、`pIsAck` 和 `msgIdHash`；`msgIdHash` 是原始消息 ID 的短 HMAC-SHA256，HMAC key 每次运行随机生成且不写入 evidence，因此只能做同一证据文件内的重复判断，不能跨证据文件稳定关联。汇总还包含 `messageIdHashSamples`、`duplicateMessageIdHashCount`、`ackMessageCount`、`maxMessagesPerSecond`、`malformedPacketCount` 和 `compressionErrorCount`，用于判断去重、ack、突发流量和解析错误。`danmuInfo.hostCandidates` 会保存从 `host_list` 提取的 host、wss 端口和连接 URL，不保存 token。`attempts[].hostFallback` 记录候选 host 数、实际尝试过的 host、最终 host、是否用到 fallback，以及候选是否耗尽；`attempts[].hostAttempts` 记录每个 host 子尝试的状态、关闭码和事件类型。重连模式还会记录 `reconnect` 汇总和 `attempts[]`，包含每次尝试的 `status`、所选 host、`danmuInfo` 摘要、关闭码、op 统计和协议版本统计。默认不保存完整消息体，`BILI_WS_CAPTURE_MESSAGES=1` 仅用于本地调试；启用后 evidence 会标记 `captureFullMessages: true` 和 `sensitiveEvidence: true`。包编码、普通 JSON、zlib protover `2`、brotli protover `3`、sanitized message metadata 和 malformed packet 解析现在由 `scripts/lib/ws-packets.mjs` 提供，并用离线单测覆盖。

Playwright 捕获脚本也会对 `/sub` WebSocket 帧做被动包头解析：`websockets[]` 记录 socket URL、所属页面序号、页面 target、打开时间和帧事件时间；`websockets[].events[].packets[]` 只保存 op、协议版本、包长度、压缩类型、嵌套包头和命令名，不保存完整弹幕/消息体。这用于被动确认网页端实际发送或接收过哪些 op，例如 op `24`，不会主动发送 ack 包。对 op `24`，如果 payload 能按 JSON 解析，默认只保存 `bodyBytes`、脱敏后的顶层字段名、字段形态、`pIsAck` 布尔值和基于每次捕获随机 HMAC key 的 `msgIdHashSamples`，不保存原始 ID、正文或 payload 值；长数字、长 hex、UUID 形态的动态字段名会替换成稳定占位符。`npm run discover:ws-report` 会汇总一个或多个 Playwright capture 中的 WebSocket op、命令名、普通消息 `pIsAck` 总计数、普通消息 HMAC id 样本数、紧随 `pIsAck:true` 消息之后的 `sent:24` 计数及命令分布、op `24` 字段名、敏感证据标记和 full-message 标记，并把 `/sub` 弹幕 WebSocket 与 tracker/player 等其他 WebSocket 分开计数。

如果默认 `getDanmuInfo` 发现失败或返回空 token，脚本会以 `token-discovery-failed` 结束并写入 evidence，不再继续打开旧式空 token fallback 连接。如果设置 `BILI_WS_URL` 或 `BILI_DANMU_TOKEN`，脚本会跳过默认的 `getDanmuInfo` token/host 发现流程；这只验证指定连接或 token 的传输行为，不证明网页端当前 token 获取流程。仅设置 `BILI_DANMU_TOKEN` 时会使用旧 `wss://broadcastlv.chat.bilibili.com:443/sub` 作为显式自定义 token 连接。自定义 `BILI_WS_URL` 默认仍必须是 `wss://*.chat.bilibili.com/...`，除非显式设置 `BILI_WS_ALLOW_UNSAFE_HOST=1` 做受控本地负向测试。

#### Current Evidence Samples

2026-06-02 使用 `--sample-events=8` 的验证结果：

| 字段 | 结果 |
| --- | --- |
| `status` | `ok` |
| `sawAuthReply` | `true` |
| `sawHeartbeat` | `true` |
| `operationCounts` | op `8`: 1，op `3`: 1，op `5`: 9 |
| `protocolVersionCounts` | version `1`: 2，version `0`: 5，version `3`: 4 |
| `messageCommands` | `LOG_IN_NOTICE`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3` |
| `messageHashScope` | `per-run-hmac` |
| `captureFullMessages` / `sensitiveEvidence` | `false` / `false` |
| `maxMessagesPerSecond` | 已记录，用于突发流量观察 |
| `malformedPacketCount` / `compressionErrorCount` | 已记录，用于解析质量检查 |

这证明当前脚本不仅能完成 op `7` 鉴权和 op `2/3` 心跳，也能解包 protover `3` brotli op `5` 消息。当前 evidence 还会记录去重、ack、突发流量和解析错误的脱敏汇总字段。

2026-06-02 使用 `--reconnect-attempts=2 --sample-events=4` 的验证结果：

| 字段 | 结果 |
| --- | --- |
| `status` | `ok` |
| `reconnectAttempts` | 2 |
| `reconnect.okAttempts` | 2 |
| `reconnect.sawAuthReplyAllAttempts` | `true` |
| `reconnect.sawHeartbeatAllAttempts` | `true` |
| `reconnect.selectedHosts` | `wss://zj-cn-live-comet.chat.bilibili.com:2245/sub` |

这证明当前环境下可以连续两次获取 token、连接、鉴权和收到心跳回应。它仍不等同于完整的断线恢复验证。

2026-06-02 使用 `--host-fallbacks=3 --sample-events=4` 的验证结果：

| 字段 | 结果 |
| --- | --- |
| `status` | `ok` |
| `hostFallbacks` | 3 |
| `attempts[0].hostFallback.candidateCount` | 3 |
| `attempts[0].hostFallback.attemptedHosts` | `wss://zj-cn-live-comet.chat.bilibili.com:2245/sub` |
| `attempts[0].hostFallback.usedFallback` | `false` |
| `operationCounts` | op `8`: 1，op `3`: 1，op `5`: 1 |

这证明脚本可以从 `host_list` 生成有界 fallback 计划并记录证据；本次第一 host 成功，因此没有触发现网 fallback。

2026-06-02 使用受控失败 host `--prepend-test-host=wss://broadcastlv.chat.bilibili.com:1/sub --sample-events=4` 的验证结果，证据为 `evidence/live-ws-1780402068748.json`：

| 字段 | 结果 |
| --- | --- |
| `status` | `ok` |
| `hostFallbacks` | 2 |
| `prependTestHost` | `wss://broadcastlv.chat.bilibili.com:1/sub` |
| `attempts[0].hostFallback.attemptedHosts` | `wss://broadcastlv.chat.bilibili.com:1/sub` -> `wss://zj-cn-live-comet.chat.bilibili.com:2245/sub` |
| `attempts[0].hostAttempts[0].status` / `closeCode` | `closed` / `1006` |
| `attempts[0].hostFallback.usedFallback` | `true` |
| `attempts[0].hostFallback.exhausted` | `false` |
| `operationCounts` | op `8`: 1，op `3`: 1，op `5`: 1 |

这证明验证脚本可以在第一个允许的 Bilibili chat host 失败后，继续尝试 `getDanmuInfo` 返回的真实 host 并完成鉴权、心跳和 op `5` 解包。它是受控失败 host 的 fallback 证据，不等同于真实生产 host 故障、服务端异常关闭恢复或中途断网重连能力证明。

2026-06-02 使用 `--heartbeat-wait-ms=32000 --min-heartbeats=2` 的验证结果：

| 字段 | 结果 |
| --- | --- |
| `status` | `ok` |
| `heartbeatWaitMs` | 32000 |
| `heartbeatReplyCount` | 2 |
| `operationCounts` | op `8`: 1，op `3`: 2，op `5`: 28 |
| `protocolVersionCounts` | version `1`: 3，version `0`: 15，version `3`: 13 |
| `messageCommands` | `LOG_IN_NOTICE`、`INTERACT_WORD_V2`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`STOP_LIVE_ROOM_LIST` |

这证明当前连接能跨过第一个 30 秒周期心跳并继续收到 op `5` 消息；它仍不等同于多小时稳定性或断网恢复验证。

2026-06-02 Playwright 被动捕获 `evidence/live-page-capture-5050-1780370248279.json`、45 秒捕获 `evidence/live-page-capture-5050-1780371350701.json` 和 60 秒 scroll/hover 捕获 `evidence/live-page-capture-5050-1780372067966.json` 均记录到浏览器 `/sub` WebSocket 帧。最新 60 秒捕获的 `npm run discover:ws-report` 汇总为：

| 方向 | op | 说明 |
| --- | --- | --- |
| sent | 7 | 浏览器发送鉴权包。 |
| received | 8 | 服务端鉴权成功回应。 |
| sent | 2 | 浏览器发送心跳；60 秒捕获中记录 3 次。 |
| received | 3 | 服务端心跳回应；60 秒捕获中记录 3 次。 |
| received | 5 | 普通和 brotli 嵌套消息；60 秒捕获展开后汇总计数为 60。 |

60 秒捕获观察到的命令名包括 `INTERACT_WORD_V2`、`LOG_IN_NOTICE`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`STOP_LIVE_ROOM_LIST`、`WATCHED_CHANGE`，并标记 `captureFullMessages: false`、`sensitiveEvidence: false`、`messageHashScope: per-run-hmac`。

这些较短被动捕获当时仍未观察到 op `24`；下方长窗口多房间样本已观察到浏览器发送 `sent:24`，但仍只证明包头方向，不证明 payload schema 或精确触发条件。

2026-06-02 90 秒 room-page Playwright 被动捕获 `evidence/live-page-capture-5050-1780375303233.json` 记录到 1 个 `/sub` WebSocket、50 个 frame events 和 176 个被阻止的非 GET 浏览器请求。对应 `npm run discover:ws-report` 写入 `evidence/live-ws-capture-report-1780375312314.json`：

| 字段 | 结果 |
| --- | --- |
| `operationCounts` | `sent:7`: 1，`sent:2`: 4，`received:8`: 1，`received:3`: 4，`received:5`: 82 |
| `messageCommands` | `INTERACT_WORD_V2`、`LOG_IN_NOTICE`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`STOP_LIVE_ROOM_LIST`、`WATCHED_CHANGE` |
| `op24Observed` | `false` |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该 90 秒样本把 op `24` 的结论加强为“更长被动网页会话仍未观察到”，但仍不是不存在证明；ack 条件可能依赖特定消息类型、播放器内部状态或更长/不同直播间场景。

2026-06-02 120 秒 room-page Playwright 被动捕获 `evidence/live-page-capture-5050-1780379929303.json` 记录到 1 个 `/sub` WebSocket、72 个 frame events 和 100 个被阻止的非 GET 浏览器请求。对应 `npm run discover:ws-report` 写入 `evidence/live-ws-capture-report-1780379941862-60014-485ec0e1.json`：

| 字段 | 结果 |
| --- | --- |
| `operationCounts` | `sent:7`: 1，`sent:2`: 5，`received:8`: 1，`received:3`: 5，`received:5`: 115 |
| `messageCommands` | `DANMU_MSG`、`ENTRY_EFFECT`、`INTERACT_WORD_V2`、`LOG_IN_NOTICE`、`NOTICE_MSG`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`STOP_LIVE_ROOM_LIST`、`WATCHED_CHANGE` |
| `op24Observed` | `false` |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该 120 秒样本观察到真实弹幕 `DANMU_MSG`、入场/特效/通知等更多命令，并跨过多次 30 秒心跳周期，但仍未观察到浏览器发送 op `24`。这进一步支持“op `24` 触发条件不是普通房间页持续观看必然出现”的结论，但仍不能证明该 op 不存在。

2026-06-02 180 秒 room-page Playwright 被动捕获 `evidence/live-page-capture-5050-1780382781588.json` 记录到 1 个 `/sub` WebSocket、93 个 frame events 和 433 个被阻止的非 GET 浏览器请求。对应 `npm run discover:ws-report` 写入 `evidence/live-ws-capture-report-1780382795574-68335-2f280d11.json`：

| 字段 | 结果 |
| --- | --- |
| `operationCounts` | `sent:7`: 1，`sent:2`: 7，`received:8`: 1，`received:3`: 7，`received:5`: 155 |
| `messageCommands` | `ENTRY_EFFECT`、`INTERACT_WORD_V2`、`LOG_IN_NOTICE`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`STOP_LIVE_ROOM_LIST`、`WATCHED_CHANGE` |
| `op24Observed` | `false` |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该 180 秒样本跨过更多心跳周期，仍未观察到 op `24`，也没有保存完整消息体。该样本使用 scroll/hover/buttons 交互，适合扩大页面行为覆盖；严格被动 op `24` 结论还需要不点击按钮的多房间样本。

2026-06-02 3 房间 Playwright 被动捕获 `evidence/live-page-capture-5050-1780383144021.json` 使用从 area 页面发现的房间 `1735947155`、`1746709913`、`1775719573`，仅执行 scroll/hover，不执行 buttons。它记录到 3 个 `/sub` WebSocket、185 个 frame events 和 689 个被阻止的非 GET 浏览器请求。对应 `npm run discover:ws-report` 写入 `evidence/live-ws-capture-report-1780383157671-68875-56333f66.json`：

| 字段 | 结果 |
| --- | --- |
| `operationCounts` | `sent:7`: 3，`sent:2`: 12，`received:8`: 3，`received:3`: 12，`received:5`: 308 |
| `messageCommands` | `DANMU_MSG`、`ENTRY_EFFECT`、`INTERACT_WORD_V2`、`LOG_IN_NOTICE`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`RANK_CHANGED_V2`、`STOP_LIVE_ROOM_LIST`、`UNIVERSAL_ASR_TEXT`、`WATCHED_CHANGE` |
| `op24Observed` | `false` |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该 3 房间样本说明普通未登录、GET-only、scroll/hover、多房间被动观看可以观察到更丰富的 op `5` 命令，但仍未触发 op `24`。这加强了“op `24` 不由普通被动观看必然触发”的结论；它仍不能覆盖登录态、显式用户操作或特定 ack-required 消息类型。

2026-06-02 新一轮 3 房间 Playwright 被动捕获 `evidence/live-page-capture-5050-1780403610019.json` 使用最新 area-tags 捕获中发现的房间 `1709466125`、`1713422756`、`1722699328`，仅执行 scroll/hover，不执行 buttons。它记录到 3 个 `/sub` 弹幕 WebSocket、2 个 tracker/other WebSocket、139 个总 frame events、89 个弹幕 frame events 和 288 个被阻止的非 GET 浏览器请求。对应 `npm run discover:ws-report` 写入 `evidence/live-ws-capture-report-1780403622948-99556-90b14e28.json`：

| 字段 | 结果 |
| --- | --- |
| `operationCounts` | `sent:7`: 3，`sent:2`: 15，`received:8`: 3，`received:3`: 15，`received:5`: 84 |
| `messageCommands` | `DANMU_MSG`、`ENTRY_EFFECT`、`INTERACT_WORD_V2`、`LOG_IN_NOTICE`、`NOTICE_MSG`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`STOP_LIVE_ROOM_LIST`、`WATCHED_CHANGE` |
| `op24Observed` | `false` |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该样本进一步说明普通未登录、GET-only、scroll/hover、多房间被动观看仍只触发 op `7/2/8/3/5`。它加强了“该捕获形态未触发 op `24`”的证据，但不能证明 op `24` 不存在；ack 行为仍可能依赖登录态、显式用户动作、特定 ack-required 消息类型或更长/不同房间场景。

2026-06-02 长窗口 3 房间 Playwright 被动捕获 `evidence/live-page-capture-5050-1780406471673.json` 使用当前公开房间列表中的 `7734200`、`5050`、`545068`，仅执行 scroll/hover，不执行 buttons。它记录到 3 个 `/sub` 弹幕 WebSocket、3 个 tracker/other WebSocket、2477 个总 frame events、1428 个弹幕 frame events 和 460 个被阻止的非 GET 浏览器请求。使用当前 report 逻辑重新运行 `npm run discover:ws-report` 写入 `evidence/live-ws-capture-report-1780456449468-51461-159c7357.json`：

| 字段 | 结果 |
| --- | --- |
| `operationCounts` | `sent:7`: 3，`sent:2`: 21，`sent:24`: 3，`received:8`: 3，`received:3`: 21，`received:5`: 4597 |
| `messageCommands` | `DANMU_MSG`、`DM_INTERACTION`、`ENTRY_EFFECT`、`HOT_ROOM_NOTIFY`、`INTERACT_WORD_V2`、`LIKE_INFO_V3_CLICK`、`LIKE_INFO_V3_UPDATE`、`NOTICE_MSG`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`SUPER_CHAT_MESSAGE`、`WATCHED_CHANGE` 等 |
| `messagePIsAckCount` / `pIsAckCommandCounts` | 3 / `SUPER_CHAT_MESSAGE`: 2，`ONLINE_RANK_COUNT`: 1 |
| `pIsAckImmediateOp24Count` / `pIsAckImmediateOp24CommandCounts` | 3 / `SUPER_CHAT_MESSAGE`: 2，`ONLINE_RANK_COUNT`: 1 |
| `op24Observed` | `true`，仅观察到浏览器发送 `sent:24` |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该样本证明在普通未登录、GET-only、scroll/hover、长窗口多房间观看形态下，浏览器可以发送 op `24`。当前证据只保存包头元数据和方向，未保存 op `24` payload，也未观察到 `received:24`；因此它不能证明具体 ack payload schema、服务端响应语义、登录态差异或显式用户动作触发条件。重新汇总后的 `pIsAck` 邻接计数显示 3 条 `pIsAck:true` 消息均被紧随其后的浏览器 `sent:24` ack，命令为 `SUPER_CHAT_MESSAGE` 与 `ONLINE_RANK_COUNT`，这是后续安全复现的主要线索。

2026-06-03 单房间 `545068` Playwright 被动复测 `evidence/live-page-capture-5050-1780449406672.json` 使用相同 GET-only、scroll/hover、安全证据边界。它记录到 1 个 `/sub` 弹幕 WebSocket、1 个 tracker WebSocket、568 个总 frame events 和 1045 个被阻止的非 GET 浏览器请求。对应 `npm run discover:ws-report` 写入 `evidence/live-ws-capture-report-1780449416700-36075-326e98fe.json`：

| 字段 | 结果 |
| --- | --- |
| `operationCounts` | `sent:7`: 1，`sent:2`: 7，`received:8`: 1，`received:3`: 7，`received:5`: 867 |
| `messageCommands` | `DANMU_MSG`、`ENTRY_EFFECT`、`INTERACT_WORD_V2`、`LIKE_INFO_V3_CLICK`、`LIKE_INFO_V3_UPDATE`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`WATCHED_CHANGE` 等 |
| `op24Observed` | `false` |
| `op24 payload metadata frames` | 0 |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该复测说明即使对曾经触发 op `24` 的 LPL 房间形态，180 秒单房间被动观看也不一定再次触发 op `24`。新解析器已经能在后续 op `24` 出现时保存脱敏 payload 形态，但本次没有实际 payload 元数据。

2026-06-03 修复动态字段名 sanitizer 后，重新对历史 op `24` positive 的 3 房间组合 `7734200`、`5050`、`545068` 做 180 秒 GET-only、scroll/hover Playwright 被动捕获，证据为 `evidence/live-page-capture-5050-1780450920189.json`。对应 `npm run discover:ws-report -- evidence/live-page-capture-5050-1780450920189.json` 写入 `evidence/live-ws-capture-report-1780450935518-38981-e83306cb.json`：

| 字段 | 结果 |
| --- | --- |
| `websocketCount` | 5：3 个 live danmu `/sub`，2 个 tracker/other |
| `danmuFrameEventCount` / `otherFrameEventCount` | 516 / 493 |
| `operationCounts` | `sent:7`: 3，`sent:2`: 19，`received:8`: 3，`received:3`: 19，`received:5`: 1130 |
| `messageCommands` | `DANMU_MSG`、`ENTRY_EFFECT`、`HOT_ROOM_NOTIFY`、`INTERACT_WORD_V2`、`LIKE_INFO_V3_CLICK`、`LIKE_INFO_V3_UPDATE`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`WATCHED_CHANGE` 等 |
| `op24Observed` | `false` |
| `op24 payload metadata frames` | 0 |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该修复后复测再次说明 op `24` 不是该普通未登录、GET-only、scroll/hover、多房间观看形态的稳定必现行为。由于本次没有 op `24`，仍没有 live payload-shape evidence；当前 payload schema 只由离线 sanitizer tests 证明安全边界，等待后续 sanitizer-era capture 复现。

2026-06-03 继续对 `545068` 做 300 秒 GET-only、scroll/hover 单房间被动捕获，证据为 `evidence/live-page-capture-5050-1780451681921.json`。使用当前 report 逻辑重新运行 `npm run discover:ws-report -- evidence/live-page-capture-5050-1780451681921.json` 写入 `evidence/live-ws-capture-report-1780452410944-42619-61afdc0e.json`：

| 字段 | 结果 |
| --- | --- |
| `websocketCount` | 2：1 个 live danmu `/sub`，1 个 tracker/other |
| `frameEventCount` | 921 |
| `operationCounts` | `sent:7`: 1，`sent:2`: 11，`received:8`: 1，`received:3`: 11，`received:5`: 1488 |
| `messageCommands` | `INTERACT_WORD_V2`、`ONLINE_RANK_COUNT`、`ENTRY_EFFECT`、`DANMU_MSG`、`WATCHED_CHANGE`、`ONLINE_RANK_V3`、`LIKE_INFO_V3_UPDATE` 等 |
| `messagePIsAckCount` / `pIsAckCommandCounts` | 0 / none |
| `op24Observed` | `false` |
| `op24 payload metadata frames` | 0 |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该 300 秒样本观察到更多普通 op `5` 消息，但仍没有 `pIsAck:true` 消息，也没有 op `24`。这支持当前结论：op `24` 更可能依赖偶发 ack-required 消息类型（历史 positive 附近曾有 `SUPER_CHAT_MESSAGE` / ack 相关元数据），而不是单纯观看时长或普通互动消息量。

2026-06-03 对历史 op `24` positive 房间组合做更长 GET-only、scroll/hover 多房间复测，证据为 `evidence/live-page-capture-5050-1780455753713.json`。该 capture 依次打开 `545068`、`7734200`、`5050`，每个目标使用 300 秒窗口，仍保持 `allowPost:false`、`captureFullMessages:false`、`sensitiveEvidence:false`。对应 `npm run discover:ws-report -- evidence/live-page-capture-5050-1780455753713.json` 写入 `evidence/live-ws-capture-report-1780456449443-51462-9cf30456.json`：

| 字段 | 结果 |
| --- | --- |
| `websocketCount` | 5：3 个 live danmu `/sub`，2 个 tracker/other |
| `danmuFrameEventCount` / `otherFrameEventCount` | 867 / 764 |
| `operationCounts` | `sent:7`: 3，`sent:2`: 33，`received:8`: 3，`received:3`: 33，`received:5`: 1931 |
| `messageCommands` | `DANMU_MSG`、`ENTRY_EFFECT`、`HOT_ROOM_NOTIFY`、`INTERACT_WORD_V2`、`LIKE_INFO_V3_CLICK`、`LIKE_INFO_V3_UPDATE`、`NOTICE_MSG`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`POPULAR_RANK_CHANGED`、`POPULARITY_CHANGE`、`ROOM_REAL_TIME_MESSAGE_UPDATE`、`STOP_LIVE_ROOM_LIST`、`WATCHED_CHANGE` 等 |
| `messagePIsAckCount` / `pIsAckCommandCounts` | 0 / none |
| `pIsAckImmediateOp24Count` / `pIsAckImmediateOp24CommandCounts` | 0 / none |
| `messageIdHashSampleCount` | 4 |
| `op24Observed` | `false` |
| `op24 payload metadata frames` | 0 |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该更长多房间复测再次没有观察到 `pIsAck:true` 或 op `24`，即使普通 op `5` 消息覆盖了 1900+ 个展开包和更多命令类型。这进一步支持“op `24` 依赖偶发 ack-required 消息，而非普通多房间观看必现”的结论。由于本次仍未触发 op `24`，sanitizer-era live payload-shape evidence 仍然待捕获。

2026-06-03 还用更新后的捕获脚本做了短窗口单房间 smoke capture `evidence/live-page-capture-5050-1780455852669.json`，对应 WS report `evidence/live-ws-capture-report-1780456449407-51460-59a44c22.json`。该文件证明后续新捕获会在 `websockets[]` 中记录 `pageIndex`、`target`、`openedAt`，并在每个 frame event 记录 `eventIndex` 和 `at`，从而减少多页面 WebSocket 归因和顺序分析的不确定性。

2026-06-02 对本地 28 个 Playwright capture 运行聚合报告 `evidence/live-ws-capture-report-1780377696744.json`：

| 字段 | 结果 |
| --- | --- |
| `captureFiles` | 28 |
| `websocketCount` | 35 |
| `danmuWebsocketCount` / `otherWebsocketCount` | 21 / 14 |
| `danmuFrameEventCount` / `otherFrameEventCount` | 384 / 693 |
| `operationCounts` | `sent:7`: 8，`sent:2`: 15，`received:8`: 8，`received:3`: 14，`received:5`: 343 |
| `messageCommands` | `DANMU_MSG`、`DM_INTERACTION`、`ENTRY_EFFECT`、`INTERACT_WORD_V2`、`LIKE_INFO_V3_CLICK`、`LIKE_INFO_V3_UPDATE`、`LOG_IN_NOTICE`、`ONLINE_RANK_COUNT`、`ONLINE_RANK_V3`、`STOP_LIVE_ROOM_LIST`、`WATCHED_CHANGE` |
| `op24Observed` | `false` |
| `sensitiveEvidenceCount` / `fullMessageCaptureCount` | 0 / 0 |

该聚合结果说明 tracker、activity broadcast 等 WebSocket 会产生大量非弹幕帧，但不按 Bilibili 直播弹幕协议解析；当前 op 统计只来自已确认的直播弹幕 host。blackboard 活动页捕获到的 `wss://broadcast.chat.bilibili.com:7826/sub?platform=h5` 虽然路径也是 `/sub`，但不属于当前直播弹幕 host 形态，归类为 `other`。

#### Current Unknowns

- 主动断网、服务端异常关闭、host 不可用后触发现网 fallback 的实证；脚本已有有界 fallback 计划和离线失败模拟覆盖。
- 旧 token、空 token 或重复 token 的拒绝细节。
- op `24` ack 的具体 payload schema、服务端响应和精确触发条件；当前只在历史长窗口多房间 Playwright 被动捕获中观察到 3 个浏览器发送的 `sent:24` 包头，后续更长复测尚未再次触发可脱敏解析的 op `24` payload。
- 多小时或多轮 30 秒周期心跳后的稳定性。
- zlib protover `2` 在当前直播间的实时覆盖。
- 更长窗口下的消息顺序、去重、背压和高频事件处理；当前脚本已有脱敏顺序、重复、ack 和每秒突发计数字段。

WBI 签名细节见 `API.WBI.md`，当前接口总览见 `API.live.current.md`。

### Legacy Protocol Notes

以下内容保留历史协议、包头、旧示例代码和旧 `broadcastlv` 地址，便于理解 Bilibili 直播弹幕协议格式。不要把本节的空 token / 直连 `broadcastlv` 示例当作当前网页端推荐流程；当前可验证流程以上方 `getDanmuInfo` token、`host_list` 和 `key` 鉴权为准。

#### Legacy TCP Flow Diagram
* tcp服务器为: `broadcastlv.chat.bilibili.com`

![1](https://user-images.githubusercontent.com/14891398/125155165-98cc5400-e190-11eb-83ff-2e537a2dd7cd.png)

历史协议常量：
```
WS_OP_HEARTBEAT: 2, //心跳
WS_OP_HEARTBEAT_REPLY: 3, //心跳回应 
WS_OP_MESSAGE: 5, //弹幕,消息等
WS_OP_USER_AUTHENTICATION: 7,//用户进入房间
WS_OP_CONNECT_SUCCESS: 8, //进房回应
WS_PACKAGE_HEADER_TOTAL_LENGTH: 16,//头部字节大小
WS_PACKAGE_OFFSET: 0,
WS_HEADER_OFFSET: 4,
WS_VERSION_OFFSET: 6,
WS_OPERATION_OFFSET: 8,
WS_SEQUENCE_OFFSET: 12,
WS_BODY_PROTOCOL_VERSION_NORMAL: 0,//普通消息
WS_BODY_PROTOCOL_VERSION_BROTLI: 3,//brotli压缩信息
WS_HEADER_DEFAULT_VERSION: 1,
WS_HEADER_DEFAULT_OPERATION: 1,
WS_HEADER_DEFAULT_SEQUENCE: 1,
WS_AUTH_OK: 0,
WS_AUTH_TOKEN_ERROR: -101
```
#### Legacy broadcastlv Addresses

这些地址是历史直连示例或显式自定义 token 测试地址。当前默认验证脚本不会在 token 发现失败时打开旧式空 token fallback。

* 普通未加密的 WebSocket 连接： `ws://broadcastlv.chat.bilibili.com:2244/sub`
* 使用 SSL 的 WebSocket 连接： `wss://broadcastlv.chat.bilibili.com/sub`

#### Packet Header Format

发送和接收的包都是这种格式。

| 偏移 | 长度 | 类型 | 字节序 | 名称 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 0 | 4 | int | Big Endian | Packet Length | 数据包长度 |
| 4 | 2 | int | Big Endian | Header Length | 数据包头部长度（固定为 `16`） |
| 6 | 2 | int | Big Endian | Protocol Version | 协议版本（见下文） |
| 8 | 4 | int | Big Endian | Operation | 操作类型（见下文） |
| 12 | 4 | int | Big Endian | Sequence Id | 数据包头部长度（固定为 `1`） |
| 16 | - | byte[] | - | Body | 数据内容 |

~~同一个 `WebSocket Frame` 可能包含多个 `Bilibili 直播数据包`，每个 `Bilibili 直播数据包` 直接首尾相连，数据包长度只表示 `Bilibili 直播数据包` 的长度，并非 `WebSocket Frame` 的长度。~~

2020.6.4 现版本弹幕协议中数据包长度就是整个`WebSocket Frame`的长度，而并非`直播数据包`长度，因此已无法通过offset来切割相邻的`直播数据包`。获得数据后也不能直接使用JSON.parse进行解析，需要将多条json数据切割。

2021.7.12 现版本协议增加brotli压缩信息

#### Protocol Versions

| 值 | Body 格式 | 说明 |
| --- | --- | --- |
| 0 | JSON | JSON纯文本，可以直接通过 `JSON.stringify` 解析 |
| 1 | Int 32 Big Endian | Body 内容为房间人气值 |
| 2 | Buffer | 压缩过的 Buffer，Body 内容需要用zlib.inflate解压出一个新的数据包，然后从数据包格式那一步重新操作一遍 |
| 3 | Buffer | 压缩信息,需要brotli解压,然后从数据包格式 那一步重新操作一遍 |

#### Operation Codes

| 值 | 发送者 | Body 格式 | 名称 | 说明 |
| --- | --- | --- | --- | --- |
| 2 | 客户端 | (空) | 心跳 | 不发送心跳包，70 秒之后会断开连接，通常每 30 秒发送 1 次 |
| 3 | 服务器 | Int 32 Big Endian | 心跳回应 | Body 内容为房间人气值 |
| 5 | 服务器 | JSON | 通知 | 弹幕、广播等全部信息 |
| 7 | 客户端 | JSON | 进房 | WebSocket 连接成功后的发送的第一个数据包，发送要进入房间 ID |
| 8 | 服务器 | (空) | 进房回应 | |

#### Legacy Auth Payload

该示例缺少当前网页端需要的 `key` 字段，仅作为历史协议示例保留。当前鉴权载荷见上方 `Current Auth Payload`。

```json
{
  "clientver": "1.6.3",
  "platform": "web",
  "protover": 2,
  "roomid": 23058,
  "uid": 0,
  "type": 2
}
```

| 字段 | 必选 | 类型 | 说明|
| --- | --- | --- | --- |
| clientver | false | string | 例如 `"1.5.10.1"` |
| platform | false | string | 例如 `"web"` |
| protover | false | number | `1` 或者 `2` |
| roomid | true | number | 房间长 ID，可以通过 `room_init` API 获取 |
| uid | false | number | uin，可以通过 `getUserInfo` API 获取 |
| type | false | number | 不知道啥，总之写 `2` |

* protover 为 `1` 时不会使用zlib压缩，为 `2` 时会发送带有zlib压缩的包，也就是数据包协议为 `2` 。
#### Historical Message Command Examples
1.弹幕类
| 字段 | 说明 |
| --- | --- |
| DANMU_MSG | 弹幕消息 |
| WELCOME_GUARD | 欢迎xxx老爷 |
| ENTRY_EFFECT | 欢迎舰长进入房间 |
| WELCOME |欢迎xxx进入房间 |
|SUPER_CHAT_MESSAGE_JPN | 
|SUPER_CHAT_MESSAGE |二个都是SC留言 |

2.礼物类
| 字段 | 说明 |
| --- | --- |
| SEND_GIFT | 投喂礼物 |
| COMBO_SEND | 连击礼物 |


3.天选之人类
| 字段 | 说明 |
| --- | --- |
| ANCHOR_LOT_START | 天选之人开始完整信息 |
| ANCHOR_LOT_END | 天选之人获奖id |
| ANCHOR_LOT_AWARD| 天选之人获奖完整信息 |


4.上船类
| 字段 | 说明 |
| --- | --- |
| GUARD_BUY   |上舰长
| USER_TOAST_MSG | 续费了舰长
| NOTICE_MSG | 在本房间续费了舰长

5.分区排行类
| 字段 | 说明 |
| --- | --- |
ACTIVITY_BANNER_UPDATE_V2 |小时榜变动

6.关注数变化类
| 字段 | 说明 |
| --- | --- |
ROOM_REAL_TIME_MESSAGE_UPDATE |粉丝关注变动

#### Heartbeat Reply

内容是一个 4 字节的 Big Endian 的 整数，表示房间人气

#### Legacy Example Code

以下示例展示旧式浏览器 JavaScript 编解码流程。当前生产/验证流程应先通过 `getDanmuInfo` 获取 token 和 host，再发送带 `key` 的 op `7` 鉴权包。

这里以浏览器 JavaScript 自带的 `WebSocket` 说明

1. 声明encode和decode方法
```javascript
const textEncoder = new TextEncoder('utf-8');
const textDecoder = new TextDecoder('utf-8');

const readInt = function(buffer,start,len){
  let result = 0
  for(let i=len - 1;i >= 0;i--){
    result += Math.pow(256,len - i - 1) * buffer[start + i]
  }
  return result
}

const writeInt = function(buffer,start,len,value){
  let i=0
  while(i<len){
    buffer[start + i] = value/Math.pow(256,len - i - 1)
    i++
  }
}

const encode = function(str,op){
  let data = textEncoder.encode(str);
  let packetLen = 16 + data.byteLength;
  let header = [0,0,0,0,0,16,0,1,0,0,0,op,0,0,0,1]
  writeInt(header,0,4,packetLen)
  return (new Uint8Array(header.concat(...data))).buffer
}
const decode = function(blob){
  return new Promise(function(resolve, reject) {
    let reader = new FileReader();
    reader.onload = function (e){
      let buffer = new Uint8Array(e.target.result)
      let result = {}
      result.packetLen = readInt(buffer,0,4)
      result.headerLen = readInt(buffer,4,2)
      result.ver = readInt(buffer,6,2)
      result.op = readInt(buffer,8,4)
      result.seq = readInt(buffer,12,4)
      if(result.op === 5){
        result.body = []
        let offset = 0;
        while(offset < buffer.length){
          let packetLen = readInt(buffer,offset + 0,4)
          let headerLen = 16// readInt(buffer,offset + 4,4)
          let data = buffer.slice(offset + headerLen, offset + packetLen);
          let body = textDecoder.decode(data);
          if(body){
            result.body.push(JSON.parse(body));
          }
          offset += packetLen;
        }
      }else if(result.op === 3){
        result.body = {
          count: readInt(buffer,16,4)
        };
      }
      resolve(result)
    }
    reader.readAsArrayBuffer(blob);
  });
}
```

2. 连接 WebSocket并发送进入房间请求

```javascript
const ws = new WebSocket('wss://broadcastlv.chat.bilibili.com:2245/sub');
ws.onopen = function () {
  ws.send(encode(JSON.stringify({
    roomid: 23058
  }), 7));
};
// 如果使用的是控制台，这两句一定要一起执行，否侧onopen不会被触发
```

这个数据包必须为连接以后的第一个数据包，5 秒内不发送进房数据包，服务器主动断开连接，任何数据格式错误将直接导致服务器主动断开连接。

3. 每隔 30 秒发送一次心跳

```javascript
setInterval(function () {
  ws.send(encode('', 2));
}, 30000);
```

4. 接收

```javascript
ws.onmessage = async function (msgEvent) {
  const packet = await decode(msgEvent.data);
  switch (packet.op) {
    case 8:
      console.log('加入房间');
      break;
    case 3:
      const count = packet.body.count
      console.log(`人气：${count}`);
      break;
    case 5:
      packet.body.forEach((body)=>{
        switch (body.cmd) {
          case 'DANMU_MSG':
            console.log(`${body.info[2][1]}: ${body.info[1]}`);
            break;
          case 'SEND_GIFT':
            console.log(`${body.data.uname} ${body.data.action} ${body.data.num} 个 ${body.data.giftName}`);
            break;
          case 'WELCOME':
            console.log(`欢迎 ${body.data.uname}`);
            break;
          // 此处省略很多其他通知类型
          default:
            console.log(body);
        }
      })
      break;
    default:
      console.log(packet);
  }
};
```

5.PS

有时候弹幕消息主体经过压缩，导致不能解析

浏览器中，decode方法改写如下：
```javascript
const decode = function(blob){
  return new Promise(function(resolve, reject) {
    let reader = new FileReader();
    reader.onload = function (e){
      let buffer = new Uint8Array(e.target.result)
      let result = {}
      result.packetLen = readInt(buffer,0,4)
      result.headerLen = readInt(buffer,4,2)
      result.ver = readInt(buffer,6,2)
      result.op = readInt(buffer,8,4)
      result.seq = readInt(buffer,12,4)
      if(result.op === 5){
        result.body = []
        let offset = 0;
        while(offset < buffer.length){
          let packetLen = readInt(buffer,offset + 0,4)
          let headerLen = 16// readInt(buffer,offset + 4,4)
          let data = buffer.slice(offset + headerLen, offset + packetLen);

          /**
           * 仅有两处更改
           * 1. 引入pako做message解压处理，具体代码链接如下
           *    https://github.com/nodeca/pako/blob/master/dist/pako.js
           * 2. message文本中截断掉不需要的部分，避免JSON.parse时出现问题
           */
          /** let body = textDecoder.decode(pako.inflate(data));
          if (body) {
              // 同一条 message 中可能存在多条信息，用正则筛出来
              const group = body.split(/[\x00-\x1f]+/);
              group.forEach(item => {
                try {
                  result.body.push(JSON.parse(item));
                }
                catch(e) {
                  // 忽略非 JSON 字符串，通常情况下为分隔符
                }
              });
          }**/
          
          let body = '';
          try {
              // pako可能无法解压
              body = textDecoder.decode(pako.inflate(data));
          }
          catch (e){
             body = textDecoder.decode(data)
          }

          if (body) {
              // 同一条 message 中可能存在多条信息，用正则筛出来
              const group = body.split(/[\x00-\x1f]+/);
              group.forEach(item => {
                try {
                  const parsedItem = JSON.parse(item);
                  if (typeof parsedItem === 'object') {
                      result.body.push(parsedItem);
                  } else {
                      // 这里item可能会解析出number
                      // 此时可以尝试重新用pako解压data（携带转换参数）
                      // const newBody = textDecoder.decode(pako.inflate(data, {to: 'String'}))
                      // 重复上面的逻辑，筛选可能存在的多条信息
                      // 初步验证，这里可以解析到INTERACT_WORD、DANMU_MSG、ONLINE_RANK_COUNT
                      // SEND_GIFT、SUPER_CHAT_MESSAGE
                  }
                }
                catch(e) {
                  // 忽略非 JSON 字符串，通常情况下为分隔符
                }
              });
          }

          offset += packetLen;
        }
      }else if(result.op === 3){
        result.body = {
          count: readInt(buffer,16,4)
        };
      }
      resolve(result)
    }
    reader.readAsArrayBuffer(blob);
  });
}
```

nodejs中，decode方法改写如下：
```javascript
const zlib = require('zlib');

const decoder = function (blob) {
  let buffer = new Uint8Array(blob)
  let result = {}
  result.packetLen = readInt(buffer, 0, 4)
  result.headerLen = readInt(buffer, 4, 2)
  result.ver = readInt(buffer, 6, 2)
  result.op = readInt(buffer, 8, 4)
  result.seq = readInt(buffer, 12, 4)
  if (result.op === 5) {
    result.body = []
    let offset = 0;
    while (offset < buffer.length) {
      let packetLen = readInt(buffer, offset + 0, 4)
      let headerLen = 16// readInt(buffer,offset + 4,4)
      if (result.ver == 2) {
        let data = buffer.slice(offset + headerLen, offset + packetLen);
        let newBuffer = zlib.inflateSync(new Uint8Array(data));
        const obj = decoder(newBuffer);
        const body = obj.body;
        result.body = result.body.concat(body);
      } else {
        let data = buffer.slice(offset + headerLen, offset + packetLen);
        let body = textDecoder.decode(data);
        if (body) {
          result.body.push(JSON.parse(body));
        }
      }
      let body = textDecoder.decode(pako.inflate(data));
          if (body) {
              result.body.push(JSON.parse(body.slice(body.indexOf("{"))));
          }
      offset += packetLen;
    }
  } else if (result.op === 3) {
    result.body = {
      count: readInt(buffer, 16, 4)
    };
  }
  return result;
}

const decode = function (blob) {
  return new Promise(function (resolve, reject) {
    const result = decoder(blob);
    resolve(result)
  });
}
```
