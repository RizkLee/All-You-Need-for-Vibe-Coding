# 3.3 Express 与 Koa——Node.js 后端框架

> Express 是 Node.js 最经典的 Web 框架，极简但功能完整。Koa 是它的精神继任者。

---

## Express：最流行的 Node.js 框架

**Express** 是一个轻量、灵活的 Web 框架，不强制你使用某种架构，自由度很高。

```bash
npm install express
```

一个基本的 Express 应用：

```javascript
const express = require('express')
const app = express()

// 中间件：解析 JSON 请求体
app.use(express.json())

// 路由
app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.get('/api/users', async (req, res) => {
  const users = await db.query('SELECT * FROM users')
  res.json(users)
})

app.post('/api/users', async (req, res) => {
  const { name, email } = req.body
  const user = await db.create({ name, email })
  res.status(201).json(user)
})

app.listen(3000, () => console.log('Server running on port 3000'))
```

---

## Express 中间件（Middleware）

中间件是 Express 的核心概念——请求到达最终处理函数之前，经过的一系列处理函数：

```
HTTP请求
  → 中间件1（日志记录）
  → 中间件2（解析请求体）
  → 中间件3（身份认证）
  → 路由处理函数（业务逻辑）
  → HTTP响应
```

```javascript
// 自定义中间件
const logger = (req, res, next) => {
  console.log(`${req.method} ${req.url} - ${new Date().toISOString()}`)
  next()  // 调用 next() 才会继续到下一个中间件
}

// 身份认证中间件
const authenticate = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1]
  
  if (!token) {
    return res.status(401).json({ error: '请先登录' })
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET)
    req.user = decoded  // 把用户信息挂到 req 上，后续处理函数可以用
    next()
  } catch {
    res.status(401).json({ error: 'Token 无效' })
  }
}

// 全局使用
app.use(logger)

// 只对特定路由使用
app.get('/api/profile', authenticate, (req, res) => {
  res.json({ user: req.user })
})
```

常用的第三方中间件：

| 中间件 | 用途 |
|--------|------|
| `cors` | 处理跨域请求 |
| `helmet` | 设置安全相关的 HTTP 头 |
| `morgan` | 请求日志 |
| `multer` | 文件上传处理 |
| `express-rate-limit` | API 请求限流 |

---

## Express 路由组织

当路由多了，可以用 `Router` 拆分到独立文件：

```javascript
// routes/users.js
const router = express.Router()

router.get('/', getUsers)           // GET /api/users
router.get('/:id', getUserById)     // GET /api/users/123
router.post('/', createUser)        // POST /api/users
router.put('/:id', updateUser)      // PUT /api/users/123
router.delete('/:id', deleteUser)   // DELETE /api/users/123

module.exports = router

// app.js
const usersRouter = require('./routes/users')
app.use('/api/users', usersRouter)
```

---

## 项目分层架构（MVC 变体）

实际项目通常按职责分层：

```
routes/          ← 路由：定义 URL 和方法，调用 Controller
controllers/     ← 控制器：处理请求/响应，调用 Service
services/        ← 服务层：业务逻辑，调用数据库或第三方
models/          ← 数据模型：数据库操作
```

```javascript
// controllers/userController.js
const userService = require('../services/userService')

exports.createUser = async (req, res) => {
  try {
    const user = await userService.create(req.body)
    res.status(201).json({ success: true, data: user })
  } catch (error) {
    res.status(400).json({ success: false, error: error.message })
  }
}

// services/userService.js
const User = require('../models/User')
const bcrypt = require('bcrypt')

exports.create = async ({ name, email, password }) => {
  const hashedPassword = await bcrypt.hash(password, 10)
  return User.create({ name, email, password: hashedPassword })
}
```

---

## 错误处理中间件

Express 有专门的错误处理中间件（4 个参数）：

```javascript
// 统一错误处理（放在所有路由之后）
app.use((err, req, res, next) => {
  console.error(err.stack)
  
  const statusCode = err.statusCode || 500
  res.status(statusCode).json({
    error: {
      message: err.message || '服务器内部错误',
      ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
    }
  })
})
```

---

## Koa：Express 的精神继任者

**Koa** 同样由 Express 的原作者开发，更现代化、更轻量：

```javascript
const Koa = require('koa')
const Router = require('@koa/router')

const app = new Koa()
const router = new Router()

// Koa 默认支持 async/await，错误处理更优雅
app.use(async (ctx, next) => {
  try {
    await next()
  } catch (err) {
    ctx.status = err.status || 500
    ctx.body = { error: err.message }
  }
})

router.get('/api/users', async (ctx) => {
  ctx.body = await db.getUsers()  // 直接赋值 ctx.body 即可返回响应
})

app.use(router.routes())
app.listen(3000)
```

Koa vs Express：

| | Express | Koa |
|--|---------|-----|
| 内置功能 | 路由、中间件 | 极简（只有中间件） |
| 中间件机制 | 线性 | 洋葱模型（onion model）|
| async/await | 需要额外处理 | 原生支持 |
| 使用率 | 非常高 | 较低（Koa 相关生态更小）|

---

## NestJS：更完整的 Node.js 框架

如果你熟悉 Angular 或 Spring，**NestJS** 会让你感到亲切。它提供了依赖注入、装饰器、模块系统：

```typescript
@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Get()
  findAll() {
    return this.usersService.findAll()
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.usersService.findOne(+id)
  }

  @Post()
  @UseGuards(AuthGuard)  // 路由守卫
  create(@Body() createUserDto: CreateUserDto) {
    return this.usersService.create(createUserDto)
  }
}
```

NestJS 适合需要强类型和严格架构的大型 Node.js 项目。

---

## 选择哪个框架

| 场景 | 推荐 |
|------|------|
| 快速开发、小项目、学习 | Express |
| 大型项目、团队协作 | NestJS |
| 极致性能 | Fastify |
| 追求简洁现代 | Koa + 相关库 |
| 用 Next.js 全栈 | 内置 API Routes，不需要单独后端 |

---

> **下一节**：[3.4 Python后端：FastAPI、Django、Flask](./04-Python后端框架.md)
