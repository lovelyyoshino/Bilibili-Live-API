# 直播间贡献榜/在线榜

> 当前状态：2026-06-02 使用 `npm run verify:http` 验证为公开读接口。

## 调用地址

```text
GET https://api.live.bilibili.com/xlive/general-interface/v1/rank/queryContributionRank
```

## 鉴权

公开 GET，不需要 Cookie。当前验证不需要 WBI。

## 参数

| 字段 | 必选 | 传递方式 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| `ruid` | true | GET | int | 主播 UID，可由 `room_init` 返回的 `uid` 获得。 |
| `room_id` | true | GET | int | 直播房间号。 |
| `page` | false | GET | int | 页码，验证使用 `1`。 |
| `page_size` | false | GET | int | 每页数量，验证使用 `10`。 |
| `type` | false | GET | string | 榜单类型，验证使用 `online_rank`。 |

## 已验证示例

```text
https://api.live.bilibili.com/xlive/general-interface/v1/rank/queryContributionRank?ruid=433351&room_id=5050&page=1&page_size=10&type=online_rank
```

## 返回

返回 JSON。`code:0` 时，2026-06-02 验证到的 `data` 顶层字段包括：

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `count` | int | 榜单总数或计数。 |
| `item` | array | 榜单条目。 |
| `own_info` | object | 当前访问者相关信息；未登录时也可能返回默认结构。 |
| `tips_text` | string | 提示文案。 |
| `count_format` | string | 格式化计数。 |
| `desc_format` | string | 格式化描述。 |
| `config` | object | 榜单展示配置。 |
| `count_text` | string | 计数文案。 |
| `non_expandable` | int/bool | 是否不可展开。 |
| `bottom_guide` | object | 底部引导配置。 |
| `list_end_prompt` | string/object | 列表结束提示。 |

## 备注

- `ruid` 是主播 UID，不是直播间号；默认验证会先从 `room_init` 更新主播 UID。
- 该接口是读取榜单数据；关注、送礼等会改变账号状态的接口不在自动验证范围内。
