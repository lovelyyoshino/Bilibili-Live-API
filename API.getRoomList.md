### 直播列表
<https://api.live.bilibili.com/room/v3/area/getRoomList>    
希望有更完整的列表...  以这个为基础的舰长监听漏的太多了

|   字段   |  传递方式 |    类型   |  说明  |
|---------|:-----------:|:--------:| ------ |
| parent_area_id |      GET       |     int       |  [0-6] （1-6对应区号，0为全站） |
|  page                |      GET      |     int       | 页数                                                 |
|  page_size   |           GET       |     int      | [1-99] 每页显示数量 |
|  sort_type    |           GET       |  string    | online  |
