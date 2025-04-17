# Sentinel 授权规则

Sentinel 的 **授权规则（AuthorityRule）** 是用于控制资源访问权限的规则，**通常用于服务接口的访问白名单/黑名单控制**，防止非法访问或对特定用户/应用做访问限制。

---

## 1. 授权规则的核心作用

> Sentinel 授权规则是一种**基于调用来源（origin）** 的控制手段。




## 2. 策略说明

| 策略值 | 名称       | 说明                           |
|--------|------------|--------------------------------|
| `0`    | **白名单** | 只允许 limitApp 访问该资源       |
| `1`    | **黑名单** | 除了 limitApp 之外都可以访问     |

---

## 3. 调用方(Web)需要加请求头 `origin-app` 表明身份

```http request
GET /order/create HTTP/1.1
Host: svc-order
origin-app: svc-gateway

```
