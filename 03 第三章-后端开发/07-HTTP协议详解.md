# 3.7 HTTP 协议详解

> HTTP 是 Web 通信的基础，理解它能让你更清楚前后端是如何对话的。

---

## HTTP 是一种约定

HTTP（HyperText Transfer Protocol）本质上是一套**双方都遵守的通信规则**：客户端按这种格式发送请求，服务器按这种格式返回响应。

它是**无状态**的——服务器不记得上一次是谁发来的请求。每次请求对服务器来说都是全新的。这就是为什么需要 Cookie 或 Token 来"记住"登录状态。

---

## 一次 HTTP 交互的完整结构

### 请求（Request）

一个 HTTP 请求由三部分组成：

```
请求行：   GET /api/users?page=2 HTTP/1.1
请求头：   Host: api.example.com
          Authorization: Bearer eyJhbGci...
          Content-Type: application/json
          Accept: application/json

请求体：   （GET 请求通常没有，POST/PUT 有）
          {"name": "张三", "email": "zhang@example.com"}
```

### 响应（Response）

```
状态行：   HTTP/1.1 200 OK
响应头：   Content-Type: application/json
          Cache-Control: max-age=3600
          Set-Cookie: session_id=abc123

响应体：   {"id": 1, "name": "张三"}
```

---

## HTTP 方法（动词）

HTTP 定义了几种方法，约定了操作的语义：

| 方法 | 语义 | 对应操作 |
|------|------|----------|
| **GET** | 获取资源 | 查询、读取 |
| **POST** | 创建资源 | 新建、提交表单 |
| **PUT** | 完整替换资源 | 全量更新 |
| **PATCH** | 部分更新资源 | 修改部分字段 |
| **DELETE** | 删除资源 | 删除 |
| **HEAD** | 获取响应头（不要响应体） | 检查资源是否存在 |
| **OPTIONS** | 获取服务器支持的方法 | CORS 预检请求 |

GET 和 POST 最常见，但设计良好的 REST API 会正确使用所有方法。

---

## HTTP 状态码

状态码是服务器告诉客户端"结果怎么样"的方式，分五大类：

```
1xx  信息性
2xx  成功
3xx  重定向
4xx  客户端错误（你的请求有问题）
5xx  服务器错误（我出错了）
```

最常见的状态码：

| 状态码 | 含义 | 使用场景 |
|--------|------|----------|
| 200 OK | 成功 | 通用成功响应 |
| 201 Created | 已创建 | POST 成功创建资源 |
| 204 No Content | 成功但无返回内容 | DELETE 成功 |
| 301 Moved Permanently | 永久重定向 | URL 已永久迁移 |
| 400 Bad Request | 请求格式错误 | 参数缺失或格式不对 |
| 401 Unauthorized | 未认证 | 需要登录 |
| 403 Forbidden | 已认证但无权限 | 没有访问权 |
| 404 Not Found | 资源不存在 | URL 不存在 |
| 409 Conflict | 冲突 | 邮箱已被注册 |
| 422 Unprocessable Entity | 数据验证失败 | 字段格式不正确 |
| 429 Too Many Requests | 请求过于频繁 | 触发限流 |
| 500 Internal Server Error | 服务器内部错误 | 后端代码崩溃 |
| 502 Bad Gateway | 网关错误 | 代理服务器无法连接后端 |
| 503 Service Unavailable | 服务不可用 | 服务器过载或维护中 |

---

## HTTP 头（Headers）

请求头和响应头携带了大量元信息：

**常见请求头**：

| Header | 用途 |
|--------|------|
| `Authorization` | 身份认证信息，如 `Bearer <token>` |
| `Content-Type` | 请求体的格式，如 `application/json` |
| `Accept` | 告诉服务器期望返回什么格式 |
| `Cookie` | 携带 Cookie |
| `User-Agent` | 客户端的标识（浏览器信息） |
| `Origin` | 请求来源，CORS 检查用 |

**常见响应头**：

| Header | 用途 |
|--------|------|
| `Content-Type` | 响应体格式 |
| `Set-Cookie` | 要求浏览器设置 Cookie |
| `Cache-Control` | 缓存策略 |
| `Access-Control-Allow-Origin` | CORS 允许的来源 |
| `Location` | 重定向目标 URL |

---

## HTTP/1.1、HTTP/2、HTTP/3 的区别

HTTP 协议在不断演进：

**HTTP/1.1**（1997年）：每次请求需要单独建立连接，或者用 Keep-Alive 复用连接，但同一连接同时只能处理一个请求。

**HTTP/2**（2015年）：核心改进是**多路复用**——一个连接上可以同时发送多个请求，不需要等待前一个完成。大幅提升了页面加载速度（一个页面需要加载几十个资源时效果明显）。

**HTTP/3**（2022年）：把底层的 TCP 换成了 QUIC（基于 UDP），进一步减少延迟，在网络不稳定时表现更好。

对你的实际影响：这些由服务器和浏览器自动协商，**你不需要手动选择**，但了解这些能帮助你理解性能优化的原理。

---

## HTTPS：加密通信

HTTPS = HTTP + TLS（传输层安全）

TLS 提供三个保证：
1. **加密**：中间人看到的是乱码
2. **认证**：通过 SSL 证书确认你连接的是真正的服务器，而不是仿冒的
3. **完整性**：数据在传输中没有被篡改

TLS 握手过程（简化）：
```
客户端："我支持这些加密算法：xxx"
服务器："好，我们用这个算法，这是我的证书"
客户端：（验证证书合法）"证书没问题，我们用这个密钥加密"
服务器："OK，加密通信开始"
```

现代网站必须用 HTTPS。获取 SSL 证书可以用 **Let's Encrypt**（免费）。

---

## Cookie 与 Session

HTTP 是无状态的，Cookie 是解决"记住用户"的传统方案：

```
用户登录成功
  → 服务器在响应头设置：Set-Cookie: session_id=abc123; HttpOnly; Secure
  → 浏览器保存这个 Cookie
  → 之后每次请求自动带上：Cookie: session_id=abc123
  → 服务器查询 session 存储，找到对应的用户信息
```

Cookie 的属性：
- **HttpOnly**：禁止 JS 读取（防止 XSS 攻击窃取 Cookie）
- **Secure**：只在 HTTPS 下传输
- **SameSite**：限制跨站请求携带 Cookie（防止 CSRF 攻击）
- **Max-Age/Expires**：过期时间

---

## 浏览器缓存

HTTP 有完整的缓存机制，减少重复请求：

```
强缓存：Cache-Control: max-age=3600
  → 1小时内直接用缓存，不发请求

协商缓存：ETag / Last-Modified
  → 发请求问服务器：资源有没有更新？
  → 没更新 → 304 Not Modified（不传数据，只传头）
  → 更新了 → 200 OK + 新数据
```

这就是为什么第一次访问网站慢，刷新后快——静态资源（JS、CSS、图片）已经缓存了。

---

> **下一节**：[3.8 API 设计：REST、GraphQL、gRPC](./08-API设计.md)
