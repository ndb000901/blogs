# Redis基础-地理空间


## 1. Redis Geo

Redis Geo 是 Redis 提供的一套 **地理空间索引和操作功能**，可以用来：

- 存储地理位置（经纬度）；
- 查询两点之间距离；
- 找出某个地理位置附近的点。

本质上，Redis 使用 **有序集合（Sorted Set）+ Geohash 编码** 实现地理位置的索引和检索。

---

## 2. 底层实现原理

- Redis 将经纬度通过 **Geohash 编码 + ZSet 分数映射**到 52-bit 的 score 中。
- 存储结构为：
  ```text
  ZADD key score member
  ```
    - `score` 是地理位置对应的 geohash 值
    - `member` 是用户自定义的地点名（如 user_id、poi_id）

Redis 提供了一组 **GEO 系列命令**，对这些有序集合做地理空间操作。

---

## 3. 常用命令

---

### 3.1 添加地理位置

```bash

GEOADD key longitude latitude member [longitude latitude member ...]
```

示例：

```bash

GEOADD city:shenzhen 114.0579 22.5431 huaqiangbei
GEOADD city:shenzhen 113.9235 22.5460 nanshan
```

---

### 3.2 获取某个地点的经纬度

```bash

GEOPOS key member [member ...]
```

示例：

```bash

GEOPOS city:shenzhen huaqiangbei
# 返回：[[114.0579, 22.5431]]
```

---

### 3.3 计算两个地点的直线距离（单位可选）

```bash

GEODIST key member1 member2 [m|km|mi|ft]
```

示例：

```bash

GEODIST city:shenzhen huaqiangbei nanshan km
# 返回距离，单位为公里
```

---

### 3.4 按照给定地点坐标，搜索附近地点（半径范围）

```bash

GEORADIUS key longitude latitude radius m|km|mi|ft [WITHDIST] [WITHCOORD] [COUNT N] [ASC|DESC]
```

该命令从 Redis 6 开始**被废弃**，推荐用 `GEOSEARCH` 代替。

---

### 3.5 用 GEOSEARCH 查找附近地点

```bash

GEOSEARCH key 
    FROMMEMBER member | FROMLONLAT lon lat 
    BYRADIUS radius m|km|mi|ft 
    [WITHDIST] [WITHCOORD] [COUNT N] [ASC|DESC]
```

示例：

```bash

GEOSEARCH city:shenzhen 
  FROMMEMBER huaqiangbei 
  BYRADIUS 5 km 
  WITHDIST 
  WITHCOORD 
  ASC
```

表示从 “huaqiangbei” 出发，在 5km 范围内搜索地点，返回距离和坐标，按距离升序排列。

---

### 3.6 获取地理编码的 geohash 字符串（可用于调试）

```bash

GEOHASH key member [member ...]
```

示例：

```bash

GEOHASH city:shenzhen huaqiangbei
# 返回 geohash 编码（base32 格式）
```

---



## 4. 注意事项

| 项目           | 说明 |
|----------------|------|
| 经纬度范围     | 经度：-180 ~ 180，纬度：-85.05112878 ~ 85.05112878 |
| 精度限制       | Redis 精度约 ±1 米 |
| 无法更新位置   | 需要 `GEOADD` 同名成员覆盖 |
| 位置删除       | 使用 `ZREM` 即可 |
| 本质是 ZSet    | 所以支持 ZSet 的所有命令（如 ZREM、ZCARD） |

---
