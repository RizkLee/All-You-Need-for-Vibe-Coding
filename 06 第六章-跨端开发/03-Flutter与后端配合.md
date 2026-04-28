# 6.3 Flutter 与后端的配合

> Flutter 只负责客户端 UI，后端服务是独立的。本节讲清楚 Flutter App 如何与后端协作。

---

## Flutter 的职责边界

Flutter 做的事：
- 渲染 UI 界面
- 处理用户交互（点击、手势、表单）
- 管理本地状态
- 发起 HTTP 请求，展示响应数据
- 本地存储（SharedPreferences、SQLite）

Flutter 不做的事：
- 存储核心业务数据（用数据库）
- 验证用户身份（后端验证）
- 执行业务逻辑计算（应在后端）
- 直接连接数据库（安全隐患）

---

## Flutter 调用 REST API

Flutter App 与后端通信的基本流程：

```
用户点击"登录"
  ↓
Flutter 收集表单数据（邮箱 + 密码）
  ↓
发起 HTTP POST 请求到 https://api.yourapp.com/auth/login
（携带 JSON body：{"email": "...", "password": "..."}）
  ↓
后端验证，返回 JWT Token
  ↓
Flutter 把 Token 存在 SharedPreferences（本地持久化）
  ↓
之后的每次请求，在 Header 中携带 Token：
Authorization: Bearer <token>
  ↓
后端验证 Token，返回数据
  ↓
Flutter 更新 UI 展示数据
```

Flutter 中通常用 **dio** 包（比原生 http 包更强大）来发送 HTTP 请求。

---

## 你需要自己搭建后端吗？

不一定。根据项目需求，有几种选择：

### 选项 A：自建后端（全控制）

自己用 Node.js（Express/NestJS）、Python（FastAPI）等搭建 API 服务器。

**适合**：
- 有复杂的业务逻辑
- 需要对数据有完全控制权
- 团队有后端开发能力

**技术栈示例**：
```
Flutter App
  ↓ HTTP API
Node.js + Express 后端（部署在云服务器）
  ↓ 查询
PostgreSQL 数据库（RDS 或 Supabase）
  ↓ 缓存
Redis
```

### 选项 B：Firebase（Backend as a Service）

Google 的 **Firebase** 是最常见的 Flutter 后端搭档：

| Firebase 服务 | 用途 |
|--------------|------|
| **Firestore** | NoSQL 实时数据库 |
| **Authentication** | 用户认证（邮箱、Google、Apple 登录）|
| **Storage** | 文件存储（图片、视频）|
| **Cloud Functions** | 服务端逻辑（Serverless）|
| **Cloud Messaging** | 推送通知 |
| **Crashlytics** | 崩溃监控 |

Firebase 的优势：零运维，快速上手，与 Flutter 深度集成，Google 生态。  
劣势：在中国大陆网络下访问不稳定，数据在 Google 服务器，不适合需要私有化部署的项目。

### 选项 C：Supabase（开源 Firebase 替代）

**Supabase** 是开源的 Firebase 替代品，基于 PostgreSQL：

| Supabase 特性 | 说明 |
|--------------|------|
| 数据库 | PostgreSQL（带 RLS 行级安全策略）|
| 认证 | 内置用户管理，支持多种登录方式 |
| Storage | 文件存储 |
| Realtime | 基于 PostgreSQL 的实时订阅 |
| Edge Functions | Serverless 函数 |
| REST API | 自动根据表结构生成 API |

Supabase 是目前 Flutter 开发者中增长最快的后端服务，在国内访问也比 Firebase 稳定。

### 选项 D：其他 BaaS 服务

- **Appwrite**：自托管的开源 BaaS，适合需要私有部署的场景
- **PocketBase**：极轻量的开源后端，单二进制文件，适合个人项目
- **LeanCloud（国内）**：阿里系的 BaaS，国内网络友好

---

## Flutter 的本地数据存储

除了网络数据，Flutter App 还需要在本地存储数据：

| 存储方案 | 适合存什么 |
|----------|-----------|
| **SharedPreferences** | 简单的键值对（用户偏好设置、Token）|
| **SQLite（sqflite）** | 结构化数据（离线缓存、本地数据库）|
| **Hive / Isar** | 高性能的 NoSQL 本地存储 |
| **File（文件系统）** | 下载的文件、图片缓存 |
| **SecureStorage** | 敏感数据（密码、密钥，存在系统 Keychain/Keystore）|

---

## 实时数据：WebSocket vs Firebase Realtime

如果你的 App 需要实时推送（聊天、通知、实时位置）：

**用 Firebase Firestore 或 Supabase Realtime**：  
提供了"监听数据库变化"的功能，一旦数据库里的数据更新，所有监听的客户端自动收到通知。不需要自己处理 WebSocket。

**自建 WebSocket 服务器**：  
如果选择了自建后端，可以用 `web_socket_channel` 包在 Flutter 中建立 WebSocket 连接，配合 Node.js 的 Socket.io 后端。

---

## 一个完整 Flutter 项目的技术栈示例

**独立创业项目（小团队）**：
```
前端：Flutter（iOS + Android）
后端：Supabase（数据库 + 认证 + 存储）
推送：Firebase Cloud Messaging
分析：Firebase Analytics
崩溃监控：Firebase Crashlytics
```

**有自建后端的项目**：
```
前端：Flutter（iOS + Android + 桌面）
后端：FastAPI（Python）+ PostgreSQL + Redis
API：REST API（Dio 调用）
认证：JWT Token
推送：APNs（iOS）+ FCM（Android）
```

---

> **下一节**：[6.4 React Native——JS 写原生应用](./04-ReactNative.md)
