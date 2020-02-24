## 读取近几周新番更新

#### 调用地址

https://bangumi.bilibili.com/web_api/timeline_global

#### 参数

无

#### 返回

|返回值字段|字段类型|字段说明|
|----------|--------|--------|
|code|int|调用状态|
|message|string|是否调用成功|
|result|list|每日更新信息|

```json
// 每日更新信息 - 示例
    {
      "date": "2-18",
      "date_ts": 1581955200,
      "day_of_week": 2, // 周二
      "is_today": 0, // 不是今天
      "seasons": [
            {
            "cover": "http://i0.hdslb.com/bfs/bangumi/c345335f3cc395f7cfbe7e7e0a4913f9b1671fe2.jpg",
            "delay": 0,
            "ep_id": 312783,
            "favorites": 1452259,
            "follow": 0,
            "is_published": 1, // 是否已经更新了
            "pub_index": "第122话",
            "pub_time": "18:25",
            "pub_ts": 1582021500,
            "season_id": 6422,
            "season_status": 13,
            "square_cover": "http://i0.hdslb.com/bfs/bangumi/1d703634cd3ee35b625bf882f27289db301cae63.jpg",
            "title": "黑色四叶草",
            "url": "https://www.bilibili.com/bangumi/play/ss6422"
            } // 等等...
        ]
    }
```
