# 3.2 Node.js——JavaScript 的后端运行时

> Node.js 让 JavaScript 从浏览器走向了服务器，是现代全栈开发的重要基础。

---

## Node.js 是什么

JavaScript 最初只能在浏览器中运行，受限于浏览器提供的 API（操作 DOM、发网络请求等）。

**Node.js** 是一个独立的运行时环境，让 JavaScript 能在**服务器（操作系统层面）**运行：

```
浏览器中的 JS 能做：
  - 操作 DOM
  - 发 HTTP 请求
  - 读取 Cookie/localStorage

Node.js 中的 JS 能做：
  - 读写文件系统（fs 模块）
  - 启动 HTTP 服务器
  - 连接数据库
  - 操作进程和系统资源
  - 不能操作 DOM（没有浏览器）
```

---

## Node.js 的技术基础

Node.js 核心有两部分：

1. **V8 引擎**：Google Chrome 的 JavaScript 解释器，负责执行 JS 代码
2. **libuv**：负责非阻塞 I/O、事件循环、线程池

```
JavaScript 代码
      ↓
    V8 引擎（执行 JS）
      ↓
    libuv（处理异步I/O、文件、网络）
      ↓
    操作系统
```

---

## 事件循环与非阻塞 I/O

这是理解 Node.js 性能的关键概念。

**阻塞 I/O**（传统方式）：
```
发起文件读取
  → 等待文件读完（期间什么都不做）
  → 继续下一步
```

**非阻塞 I/O**（Node.js 方式）：
```
发起文件读取
  → 继续处理其他事情
  → 文件读完了，触发回调，处理文件内容
```

Node.js 是**单线程**的，但通过**事件循环**（Event Loop）实现高并发：

```
事件循环：
  1. 检查是否有新的 I/O 完成事件
  2. 执行对应的回调函数
  3. 检查定时器（setTimeout/setInterval）
  4. 重复
```

这就是为什么 Node.js 适合 **I/O 密集型**应用（Web 服务器、API 服务），而不适合 **CPU 密集型**应用（图像处理、大量计算）。

---

## Node.js 内置模块

Node.js 自带一套标准库，无需安装即可使用：

```javascript
// 文件系统
const fs = require('fs')
fs.readFileSync('./config.json', 'utf-8')
fs.writeFileSync('./output.txt', 'Hello')

// HTTP（原生方式创建服务器）
const http = require('http')
const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' })
  res.end('Hello World')
})
server.listen(3000)

// Path（路径操作）
const path = require('path')
path.join(__dirname, 'uploads', 'photo.jpg')
// → /home/user/app/uploads/photo.jpg

// OS（操作系统信息）
const os = require('os')
os.cpus()      // CPU 信息
os.totalmem()  // 总内存
```

实际开发中不会直接用这些底层模块，Express 等框架在它们之上封装了更友好的 API。

---

## npm 生态系统

Node.js 最大的优势是庞大的 **npm 生态**：全球开发者发布的 200 万+ 个包，覆盖几乎所有需求：

```bash
npm install express    # Web 框架
npm install axios      # HTTP 请求
npm install bcrypt     # 密码哈希
npm install jsonwebtoken  # JWT 认证
npm install prisma     # 数据库 ORM
npm install nodemailer # 发送邮件
npm install sharp      # 图片处理
npm install stripe     # 支付处理
```

---

## Deno 和 Bun：Node.js 的挑战者

| | Node.js | Deno | Bun |
|--|---------|------|-----|
| 诞生年份 | 2009 | 2020 | 2022 |
| 创始人 | Ryan Dahl | Ryan Dahl（同一人）| Jarred Sumner |
| 默认语言 | JS/TS（TS需配置）| TS | JS/TS |
| 安全模型 | 无限制 | 沙箱（需要授权）| 无限制 |
| 速度 | 基准 | 比 Node.js 快 | 比 Node.js 快很多 |
| 兼容性 | 标准 | 不兼容 npm | 大部分兼容 npm |
| 成熟度 | 非常成熟 | 较成熟 | 较新 |

目前：**Node.js 依然是主流**，生态最完善。Bun 在上升，值得关注。

---

## 一个最简单的 HTTP 服务器

```javascript
// server.js（原生 Node.js，不用任何框架）
const http = require('http')

const server = http.createServer((req, res) => {
  if (req.url === '/api/hello' && req.method === 'GET') {
    res.writeHead(200, { 'Content-Type': 'application/json' })
    res.end(JSON.stringify({ message: '你好！' }))
  } else {
    res.writeHead(404)
    res.end('Not Found')
  }
})

server.listen(3000, () => {
  console.log('Server running at http://localhost:3000')
})
```

这样写太麻烦了，这就是为什么需要 Express 这样的框架——见下一节。

---

> **下一节**：[3.3 Express 与 Koa——Node.js 后端框架](./03-Express与Koa.md)
