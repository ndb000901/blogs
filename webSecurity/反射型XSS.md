# 反射型XSS

反射型 XSS（Reflected Cross-Site Scripting）是 **跨站脚本攻击（XSS）** 的一种类型，它的核心是：

> **恶意脚本被立即“反射”回页面中，并在用户浏览器中执行。**

相比于 **存储型 XSS（持久存在服务端）**，反射型 XSS 是 **一次性攻击**，通常通过 URL 参数诱导用户点击来触发攻击。

---

## 1. 反射型 XSS 的原理

攻击流程如下：

1. 攻击者构造恶意链接，例如：

   ```
   https://example.com/search?q=<script>alert(1)</script>
   ```

2. 目标网站在响应中未对 `q` 参数进行有效过滤，直接将其反射到 HTML 页面中：

   ```html
   <p>您搜索的是：<b><script>alert(1)</script></b></p>
   ```

3. 用户点击链接后，脚本即在其浏览器中执行，达到攻击目的。

---

## 2. 攻击载体常见位置

- URL 查询参数（GET）：
  ```
  ?q=<script>alert(1)</script>
  ```
- 表单字段（POST）
- HTTP Header，如 Referer、User-Agent（配合后端日志页面）
- 错误提示/跳转提示页

---

## 3. 反射型 XSS 示例

### 示例页面（无防护）：
```php
<?php
    $q = $_GET['q'];
    echo "您搜索的是：" . $q;
?>
```

访问链接：
```
http://vuln-site.com/search.php?q=<script>alert('XSS')</script>
```

页面输出：
```html
您搜索的是：<script>alert('XSS')</script>
```

浏览器立即执行 `alert('XSS')`。

---

## 4. 常见的反射型 XSS 利用场景

| 场景 | 描述 |
|------|------|
| **钓鱼诱导** | 发恶意链接给目标用户，通过聊天、邮件、社交平台等 |
| **绕过验证码/CSRF** | 搭配自动表单提交或 iframe 加载钓鱼页 |
| **盗取 Cookie** | 利用 JS 读取 `document.cookie` 并上报到攻击者服务器 |
| **页面伪造** | 修改 HTML 伪造登录框，引诱输入账号密码 |

---

## 5. 反射型 XSS 检测方法

### 手动测试

- 在 URL 参数中插入 `<script>alert(1)</script>`、`"><img src=1 onerror=alert(1)>`
- 看是否被反射到页面中并执行

### 自动化工具

| 工具 | 说明 |
|------|------|
| **Burp Suite** | 配合 Repeater 和 Intruder，手动 fuzz |
| **XSStrike** | 专门的 XSS 漏洞检测工具 |
| **XSSer** | 自动化注入测试脚本 |
| **OWASP ZAP** | 提供 XSS 检测插件 |

---

## 6. 反射型 XSS 防御策略

### 6.1 **输出编码（Output Encoding）**

永远不要将用户输入直接插入到 HTML 中，除非做了 HTML 编码：

```php
echo htmlspecialchars($q, ENT_QUOTES, 'UTF-8');
```

---

### 6.2 **使用前端框架自动防御（如 Vue、React）**

它们默认对数据绑定做 HTML 编码处理。

---

### 6.3 **设置 CSP（内容安全策略）**

CSP 可限制页面中 JS 的执行来源，降低 XSS 危害：

```http
Content-Security-Policy: default-src 'self'; script-src 'self';
```

---

### 6.4 **HTTP-only Cookie**

设置 `HttpOnly` 属性，即便发生 XSS，也无法通过 JS 读取 Cookie。

---

### 6.5 **URL 参数白名单验证**

对 `GET` 参数做格式验证，比如只允许数字、字母、特定字符：

```php
if (!preg_match('/^[a-zA-Z0-9 ]+$/', $_GET['q'])) {
    exit('非法参数');
}
```

---


