## 读取番剧专题信息

#### 调用地址

http://api.bilibili.cn/bangumi

需要 App Key

#### 参数

|字段|必选|类型|说明|
|----|----|----|----|
|btype|false|int|番剧类型 2:二次元新番 3:三次元新番 默认:所有|
|weekday|false|int|周日:0 周一:1 周二:2 周三:3 依此类推|

#### 返回

|返回值字段|字段类型|字段说明|
|----------|--------|--------|
|results|int|返回的记录总数目|
|list|array|返回数据|

##### 返回字段 "list" 子项

|返回值字段|字段类型|字段说明|
|----------|--------|--------|
|title|string|标题|
|cover|string|封面图片地址|
|bgmcount|int|番剧当前总集数|
|weekday|int|番剧周信息|
|lastupdate|int|最后更新时间(UNIX Timestamp)|
|lastupdate_at|date|最后更新时间|
|new|boolean|是否最近有更新|

---

### 2020-2-24 更新后

### 调用地址

https://api.bilibili.com/pgc/season/index/result

示例 (**极难警告**):
> https://api.bilibili.com/pgc/season/index/result?season_version=-1&area=-1&is_finish=-1&copyright=-1&season_status=-1&season_month=-1&year=-1&style_id=-1&order=3&st=1&sort=0&page=1&season_type=1&pagesize=20&type=1

**这个API参数极多.下面只列出已知使用方法的参数:**

|字段|类型|说明|
|----|----|----|
|page|int|页数|
|pagesize|int|每页内容数量|
|season_status|int|(可能有错误)视频类型 1: 番剧 2: 剧场版 3: 纪录片 4: 国漫 5: 日剧|

> 剩下的参数我真的不知道了(๑•̀ㅂ•́)و✧ 就知道这么一点


#### 返回

|返回值字段|字段类型|字段说明|
|----------|--------|--------|
|code|int|调用状态|
|data.has_next|int|是否有下一页|
|data.list|list|所有番剧|

```json

// 返回示例
{
  "code": 0,
  "data": {
    "has_next": 1,
    "list": [
      {
        "badge": "会员专享",
        "badge_type": 0,
        "cover": "http://i0.hdslb.com/bfs/bangumi/9d9cd5a6a48428fe2e4b6ed17025707696eab47b.png",
        "index_show": "全26话",
        "is_finish": 1,
        "link": "https://www.bilibili.com/bangumi/play/ss26801",
        "media_id": 22718131,
        "order": "729.7万追番",
        "order_type": "fav_count",
        "season_id": 26801,
        "title": "鬼灭之刃",
        "title_icon": ""
      },
      {
        "badge": "会员专享",
        "badge_type": 0,
        "cover": "http://i0.hdslb.com/bfs/bangumi/f5d5f51b941c01f8b90b361b412dc75ecc2608d3.png",
        "index_show": "全14话",
        "is_finish": 1,
        "link": "https://www.bilibili.com/bangumi/play/ss24588",
        "media_id": 102392,
        "order": "654.1万追番",
        "order_type": "fav_count",
        "season_id": 24588,
        "title": "工作细胞",
        "title_icon": ""
      },//...
```
