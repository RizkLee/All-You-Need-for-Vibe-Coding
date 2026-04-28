# 3.8 API 设计：REST、GraphQL、gRPC

> 前后端通信的方式不只一种，REST 是主流，GraphQL 和 gRPC 各有各的适用场景。

---

## API 是什么

**API**（Application Programming Interface）是软件之间沟通的接口。在 Web 开发中，特指后端暴露给前端（或其他服务）调用的接口。

你可以把 API 想象成**餐厅的点餐窗口**：你按照规定的方式下单（请求），厨房处理后给你端上来（响应）。你不需要知道厨房里的细节。

---

## REST API

**REST**（Representational State Transfer）是目前最普遍的 Web API 设计风格。它不是技术规范，而是一套设计约定。

REST 的核心思想：**用 URL 表示资源，用 HTTP 方法表示操作**。

```
资源：用户（users）
GET    /api/users          → 获取所有用户
GET    /api/users/123      → 获取 ID 为 123 的用户
POST   /api/users          → 创建一个新用户
PUT    /api/users/123      → 完整更新用户 123
PATCH  /api/users/123      → 部分更新用户 123
DELETE /api/users/123      → 删除用户 123

资源嵌套：用户的订单
GET    /api/users/123/orders     → 获取用户 123 的所有订单
GET    /api/users/123/orders/456 → 用户 123 的订单 456
```

**REST 的优点**：
- 易于理解，URL 本身就是文档
- 充分利用 HTTP 的特性（状态码、缓存、方法）
- 任何客户端都能调用（浏览器、App、其他服务）

**REST 的局限**：
- **过度获取（Over-fetching）**：接口返回的字段比你需要的多，浪费带宽
- **获取不足（Under-fetching）**：一个页面的数据需要多次 API 调用才能拼齐
- **版本管理**：接口变化时需要维护 v1/v2 等版本

---

## GraphQL

**GraphQL** 由 Facebook 在 2015 年开源，是一种**查询语言**，让客户端自己决定需要什么数据。

与 REST 的核心区别：

| | REST | GraphQL |
|--|------|---------|
| 端点数量 | 多个（每个资源一个）| 通常只有一个 `/graphql` |
| 数据控制权 | 服务器决定返回什么 | 客户端精确指定需要什么 |
| 多资源请求 | 多次 HTTP 请求 | 一次请求获取多个资源 |
| 类型系统 | 无内置 | 强类型 Schema |
| 学习成本 | 低 | 中 |

GraphQL 查询示例（不是代码，是查询语句）：

```graphql
# 客户端发出这样的查询
query {
  user(id: "123") {
    name
    email
    orders(last: 5) {
      id
      total
      status
    }
  }
}

# 服务器精确返回这些字段，不多不少
```

这一次请求就拿到了用户信息和最近5个订单，不需要单独调用 `/users/123` 和 `/users/123/orders`。

**GraphQL 适合**：
- 数据关系复杂、前端需求多变的应用（如 GitHub、Twitter/X）
- 多端共用同一个 API（Web、iOS、Android 各取所需）
- 需要减少请求次数的场景

**GraphQL 的代价**：
- 服务端实现更复杂（需要维护 Schema，处理嵌套查询的 N+1 问题）
- 缓存相对困难（不能简单用 URL 缓存）
- 对小项目来说引入成本高于收益

---

## gRPC

**gRPC** 是 Google 开发的高性能 RPC（Remote Procedure Call，远程过程调用）框架。

它与 REST/GraphQL 的思路完全不同：

```
REST/GraphQL 的思维：
  "我要获取 /users/123 这个资源"

gRPC 的思维：
  "我要调用 GetUser(id=123) 这个函数"
```

gRPC 的技术特点：
- 使用 **Protocol Buffers**（protobuf）定义接口和数据格式，而不是 JSON。protobuf 是二进制格式，体积小、解析快。
- 基于 **HTTP/2**，支持双向流（服务端可以主动推送数据给客户端）
- 强类型：接口定义在 `.proto` 文件中，可以自动生成多种语言的客户端代码

**gRPC 适合**：
- **微服务内部通信**：多个服务之间频繁调用，追求性能
- 需要流式传输数据的场景（如实时语音识别、大文件传输）
- 多语言系统中需要统一接口定义

**gRPC 不适合**：
- 浏览器直接调用（浏览器对 HTTP/2 底层控制有限，需要 gRPC-Web 转换层）
- 面向普通用户的公开 API

---

## 三者对比总结

```
REST
  ✓ 最通用，任何客户端都能调用
  ✓ 简单易懂，学习成本低
  ✓ HTTP 缓存友好
  ✗ 多次请求、数据冗余问题
  适用：大多数 Web/App API

GraphQL
  ✓ 客户端精确控制数据
  ✓ 减少请求次数
  ✓ 自带类型系统
  ✗ 实现复杂，缓存困难
  适用：数据关系复杂、多端共用 API

gRPC
  ✓ 极高性能
  ✓ 强类型，自动生成客户端
  ✓ 支持流式传输
  ✗ 浏览器支持差
  适用：微服务内部通信
```

---

## 如何选择

对于大多数项目的建议：

- **个人项目、初创产品**：REST，简单可靠，上手快
- **数据结构复杂、移动端+Web端**：考虑 GraphQL
- **多个后端服务互相调用**：考虑 gRPC

值得注意的是，很多大型系统同时使用多种方式：对外的 API 用 REST 或 GraphQL，服务间通信用 gRPC。

---

> **下一节**：[3.9 WebSocket 与实时通信](./09-WebSocket与实时通信.md)
