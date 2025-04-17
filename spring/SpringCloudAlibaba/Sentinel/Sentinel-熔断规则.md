# Sentinel-熔断规则

Sentinel 熔断规则是其**稳定性保护机制**的一部分，用于应对 **慢调用、异常比例或异常数过多** 的场景，及时**切断故障资源的访问请求**，避免故障扩散。

---

## 1. 熔断规则（DegradeRule）

Sentinel 提供了 **3 种熔断策略**：

| 类型值 | 策略名 | 触发条件 | 应用场景 |
|--------|--------|----------|-----------|
| `0` | 慢调用比例 | RT 超过阈值，且比例达到设定值 | 接口响应慢 |
| `1` | 异常比例 | 异常比例达到阈值 | 瞬时错误飙升 |
| `2` | 异常数 | 异常数累计超过阈值 | 错误集中爆发 |

---

### 熔断规则主要字段

| 字段 | 含义 |
|------|------|
| `resource` | 熔断资源名（同样是接口名、方法名等） |
| `grade` | 熔断策略：0（慢调用比例）、1（异常比例）、2（异常数） |
| `count` | 熔断阈值（RT毫秒、异常比例、异常数） |
| `timeWindow` | 熔断时间，单位：秒 |
| `minRequestAmount` | 熔断生效前的最小请求数 |
| `statIntervalMs` | 统计时长（默认 1s），大多数场景用默认即可 |
| `slowRatioThreshold` | 慢调用比例策略下用到，RT 超过多少视为慢调用（如 300ms） |

---

## 2. 熔断策略

### 2.1 慢调用比例（grade = 0）

- 统计窗口内有超过 `minRequestAmount` 个请求
- 平均 RT 超过 `count`（毫秒）视为“慢调用”
- 如果“慢调用比例”超出 `slowRatioThreshold`，就触发熔断

---

### 2.2 异常比例（grade = 1）

- 请求数量达到 `minRequestAmount`
- 异常比例 > `count`（如 0.2 表示 20%）
- 就触发熔断


---

### 2.3 异常数（grade = 2）

- 统计窗口内异常总数 > `count`
- 达到后立即熔断

---

## 3. 熔断流程

1. **触发熔断**：进入熔断状态后，所有请求被拒绝（抛出 `DegradeException`）
2. **熔断时间结束后**：进入半开状态，允许部分流量试探
3. **试探成功**：恢复正常（关闭熔断）
4. **试探失败**：继续熔断

---


## 4. 使用 @SentinelResource 配合 fallback

```java
@SentinelResource(value = "getOrder", fallback = "fallbackGetOrder")
public String getOrder() {
    if (new Random().nextInt(10) > 7) {
        throw new RuntimeException("模拟异常");
    }
    return "正常订单";
}

public String fallbackGetOrder(Throwable t) {
    return "降级处理返回：服务临时不可用";
}
```

---


## 5. 建议

| 场景 | 建议使用 |
|------|----------|
| RT 明显波动，接口很慢 | 慢调用比例 |
| 服务突发错误增多 | 异常比例 |
| 某依赖不可用，大量报错 | 异常数 |
| 微服务之间依赖强烈，A 不能拖垮 B | 熔断保护 |

