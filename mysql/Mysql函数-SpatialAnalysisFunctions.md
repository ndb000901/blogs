# Mysql函数-SpatialAnalysisFunctions

MySQL 8 中的 **`Spatial Analysis Functions`**（空间分析函数）是对空间数据（如点、线、多边形等）进行几何计算和拓扑分析的函数集合。这些函数用于 GIS（地理信息系统）应用，遵循 OGC（Open Geospatial Consortium）标准，并支持 **空间索引**（如 R-Tree）。

---

## 1. 函数分类概览

| 类别 | 示例函数 | 作用 |
|------|----------|------|
| 测量类 | `ST_Distance()`, `ST_Length()`, `ST_Area()` | 计算长度、面积、距离 |
| 关系判断 | `ST_Intersects()`, `ST_Contains()` | 判断两个几何体的空间关系 |
| 几何构造 | `ST_Union()`, `ST_Intersection()` | 创建新几何体（并、交、差等） |
| 属性提取 | `ST_Centroid()`, `ST_Envelope()` | 获取中心点、边界框等信息 |

---

## 2. 常用函数

### 2.1 `ST_Distance(g1, g2)`
计算两个几何对象之间的欧几里得距离。

```sql
SELECT ST_Distance(POINT(1, 1), POINT(4, 5));  -- 返回 5
```

> 要求两个几何对象使用相同的坐标系统（SRID）。

---

### 2.2 `ST_Intersects(g1, g2)`
判断两个几何对象是否相交。

```sql
SELECT ST_Intersects(ST_GeomFromText('LINESTRING(0 0, 2 2)'), ST_GeomFromText('LINESTRING(0 2, 2 0)'));
-- 返回 1（相交）
```

---

### 2.3 `ST_Contains(g1, g2)`
判断 `g1` 是否完全包含 `g2`。

```sql
SELECT ST_Contains(POLYGON((0 0, 0 5, 5 5, 5 0, 0 0)), POINT(2, 2));
-- 返回 1
```

---

### 2.4 `ST_Within(g1, g2)`
判断 `g1` 是否在 `g2` 内部。

```sql
SELECT ST_Within(POINT(2, 2), POLYGON((0 0, 0 5, 5 5, 5 0, 0 0)));
-- 返回 1
```

---

### 2.5 `ST_Length(geometry)`
计算几何对象（如线）的总长度。

```sql
SELECT ST_Length(ST_GeomFromText('LINESTRING(0 0, 0 2, 2 2)'));  -- 返回 4
```

---

### 2.6 `ST_Area(geometry)`
计算几何对象的面积（仅对面状对象有效，如 Polygon）。

```sql
SELECT ST_Area(ST_GeomFromText('POLYGON((0 0, 0 4, 3 0, 0 0))'));  -- 返回 6
```

---

### 2.7 `ST_Centroid(geometry)`
计算几何对象的中心点。

```sql
SELECT ST_AsText(ST_Centroid(ST_GeomFromText('POLYGON((0 0, 0 4, 4 4, 4 0, 0 0))')));
-- 返回 POINT(2 2)
```

---

### 2.8 `ST_Envelope(geometry)`
返回最小外接矩形（边界框）。

```sql
SELECT ST_AsText(ST_Envelope(ST_GeomFromText('LINESTRING(0 0, 4 2)')));
-- 返回 POLYGON((0 0, 0 2, 4 2, 4 0, 0 0))
```

---

### 2.9 `ST_Union(g1, g2)`
计算两个几何对象的并集。

```sql
SELECT ST_AsText(ST_Union(
  ST_GeomFromText('POLYGON((0 0, 0 2, 2 2, 2 0, 0 0))'),
  ST_GeomFromText('POLYGON((1 1, 1 3, 3 3, 3 1, 1 1))')
));
```

---

### 2.10 `ST_Intersection(g1, g2)`
返回两个几何对象的交集。

```sql
SELECT ST_AsText(ST_Intersection(
  ST_GeomFromText('POLYGON((0 0, 0 3, 3 3, 3 0, 0 0))'),
  ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))')
));
-- 返回交集区域（1x1 正方形）
```

---

## 3. SRID 与坐标系统

MySQL 默认使用 SRID 0（平面笛卡尔坐标系），但也支持地理坐标（WGS 84 = SRID 4326）。

**示例：**

```sql
SELECT ST_Distance(
  ST_SRID(POINT(116.4, 39.9), 4326),
  ST_SRID(POINT(121.5, 31.2), 4326)
);
-- 将会返回地理距离（米），而不是平面距离
```

---


