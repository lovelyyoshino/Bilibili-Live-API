# 用户关系 API（2026-06-03 验证）

> 来源：`SocialSisterYi/bilibili-API-collect` + Node.js 实测验证。

## 关注/取关/拉黑

```text
POST https://api.bilibili.com/x/relation/modify
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `fid` | true | 目标用户 UID。 |
| `act` | true | 操作：`1` 关注，`2` 取消关注，`5` 拉黑，`6` 取消拉黑。 |
| `re_src` | false | 关注来源代码。 |
| `csrf` | true | CSRF token。 |

返回 `code:0` 表示操作成功。

## 批量关注/拉黑

```text
POST https://api.bilibili.com/x/relation/batch/modify
```

Cookie POST。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `fids` | true | 逗号分隔的目标 UID 列表。 |
| `act` | true | `1` 批量关注，`5` 批量拉黑。 |
| `csrf` | true | CSRF token。 |

## 关注列表

```text
GET https://api.bilibili.com/x/relation/followings?vmid=<uid>&pn=1&ps=20
```

Cookie GET。已验证返回 `code:0`。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `vmid` | true | 目标用户 UID。 |
| `pn` | false | 页码。 |
| `ps` | false | 每页数量（最大 50）。 |
| `order` | false | 排序：`desc` 最近关注优先。 |

返回 `data.list` 为关注用户数组。

## 粉丝列表

```text
GET https://api.bilibili.com/x/relation/followers?vmid=<uid>&pn=1&ps=20
```

Cookie GET。已验证返回 `code:0`。

| 参数 | 必选 | 说明 |
| --- | --- | --- |
| `vmid` | true | 目标用户 UID。 |
| `pn` | false | 页码。 |
| `ps` | false | 每页数量（最大 50）。 |

返回 `data.list` 为粉丝用户数组。仅能查看前 5 页（250 人）。

## 关系统计

```text
GET https://api.bilibili.com/x/relation/stat?vmid=<uid>
```

公开 GET。已验证返回 `code:0`。

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `mid` | int | UID。 |
| `following` | int | 关注数。 |
| `whisper` | int | 悄悄关注数。 |
| `black` | int | 拉黑数。 |
| `follower` | int | 粉丝数。 |

## 关注分组列表

```text
GET https://api.bilibili.com/x/relation/tags
```

Cookie GET。已验证返回 `code:0`。返回 `data` 为分组数组，每项含 `tagid`, `name`, `count`。

## 批量查询关系

```text
GET https://api.bilibili.com/x/relation/relations?fids=<uid1>,<uid2>
```

Cookie GET。返回 `data` 按 UID 索引关系信息（`attribute`: 0=未关注, 2=已关注, 6=互相关注, 128=已拉黑）。

## 备注

- 关注数上限为 2000（普通用户），大会员为 5000。
- 粉丝列表只能查看前 250 人（5 页 × 50）。
- `act` 操作码：1=关注, 2=取关, 3=悄悄关注, 4=取消悄悄关注, 5=拉黑, 6=取消拉黑, 7=踢出粉丝。
