# 分布式事务-TCC


> TCC = Try + Confirm + Cancel  
> 把事务操作拆分成三个阶段，通过业务方**主动控制**每个阶段，实现**最终一致性 + 高性能 + 非阻塞**的分布式事务方案。

---

## 1. 三个阶段

### Try（预留资源阶段）

- **检查资源是否可用**
- **预留资源但不做真正的处理**
- 不可见（不对外暴露结果）

### Confirm（确认执行阶段）

- 真正执行操作（比如扣钱、扣库存）
- **必须是幂等操作**
- 一般不会失败

### Cancel（取消操作阶段）

- 回滚 Try 阶段的预留资源
- 同样 **必须幂等**

---

## 2. 举例：下订单扣库存扣钱

> 假设有两个服务：库存服务（InventoryService）和账户服务（AccountService）

### Try
- 锁定库存（不是直接扣减）
- 冻结账户余额

### Confirm（业务成功）
- 扣除库存
- 扣除余额

### Cancel（业务失败）
- 解锁库存
- 解冻余额

---


## 3. TCC 的优势

| 优势 | 说明 |
|------|------|
| 高性能 | 不需要锁住数据库行或分布式资源 |
| 最终一致 | 只要幂等、可补偿，就能恢复一致状态 |
| 非阻塞 | 没有像 2PC 那种锁资源的阻塞问题 |
| 可控 | 每个阶段逻辑完全由业务自己控制（粒度可细到函数级） |

---

## 4. TCC 的挑战/缺点

| 问题 | 说明 |
|------|------|
| 实现成本高 | 每个服务都要写 Try/Confirm/Cancel 三段逻辑 |
| 幂等性处理复杂 | 所有操作必须幂等（防止重试问题） |
| 悬挂问题 | Cancel 比 Try 先到，需通过事务日志或状态机制规避 |
| 空回滚问题 | 没执行 Try 却收到 Cancel，也需处理（如记录事务状态） |
| 资源预留难 | 资源预留和释放的设计需业务参与 |

---



