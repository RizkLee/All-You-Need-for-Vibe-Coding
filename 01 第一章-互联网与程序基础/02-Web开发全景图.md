# 1.2 Web开发全景图

> 全栈开发涉及的技术很多，但它们各有分工。这一节建立整体地图。

---

## 三层架构

现代 Web 应用通常分为三层：

```
┌─────────────────────────────────────────┐
│              前端（Frontend）            │
│   用户看到的界面，运行在浏览器或App中     │
│   HTML / CSS / JavaScript / React / Vue │
└───────────────────┬─────────────────────┘
                    │  API 调用
┌───────────────────▼─────────────────────┐
│              后端（Backend）             │
│   业务逻辑、数据处理，运行在服务器上     │
│   Node.js / Python / Java / Go          │
└───────────────────┬─────────────────────┘
                    │  数据库查询
┌───────────────────▼─────────────────────┐
│              数据层（Database）          │
│   持久化存储数据                         │
│   PostgreSQL / MySQL / MongoDB / Redis  │
└─────────────────────────────────────────┘
```

这三层是独立的，可以用不同语言、不同技术实现，通过 API 或数据库驱动相互通信。

---

## 前端是什么

前端负责**用户界面**，即用户直接看到和交互的部分。

前端代码最终运行在用户的**浏览器**（或 App 的渲染引擎）中，而不是服务器上。

前端的核心技术：
- **HTML**：定义页面结构（标题、段落、按钮、图片在哪）
- **CSS**：定义样式（颜色、字体、布局、动画）
- **JavaScript**：定义交互逻辑（点击按钮后发生什么）

在这之上有各种**前端框架**，如 React、Vue、Angular，它们帮助你更高效地组织代码，处理复杂的交互状态。

---

## 后端是什么

后端负责**业务逻辑**和**数据处理**，运行在服务器上，用户看不见。

后端做的事：
- 接收前端发来的请求（如"给我用户列表"）
- 验证权限（你有没有资格看这个数据）
- 从数据库查询数据
- 处理业务逻辑（计算订单总价、发送邮件等）
- 将结果返回给前端

常见的后端语言和框架：

| 语言 | 框架 |
|------|------|
| JavaScript（Node.js） | Express、NestJS、Fastify |
| Python | FastAPI、Django、Flask |
| Java | Spring Boot |
| Go | Gin、Fiber |
| PHP | Laravel |
| Ruby | Ruby on Rails |

---

## 数据库是什么

数据库负责**持久化存储数据**。程序关闭后，数据库里的数据还在。

数据库分两大类：

**关系型数据库（SQL）**：数据以表格形式组织，有严格的结构，表之间可以关联。
- PostgreSQL、MySQL、SQLite

**非关系型数据库（NoSQL）**：数据以更灵活的方式存储（文档、键值对、图等）。
- MongoDB（文档）、Redis（键值对/缓存）

后端通过数据库驱动或 ORM（对象关系映射）与数据库通信，而不是前端直接访问数据库。

---

## API：前后端通信的桥梁

前端和后端通过 **API**（Application Programming Interface，应用程序接口）通信。

最常见的形式是 **REST API**：后端暴露一组 URL 端点，前端用 HTTP 请求调用。

```
前端请求：GET https://api.example.com/users/123
后端返回：{ "id": 123, "name": "张三", "email": "zhangsan@example.com" }
```

这就像餐厅的菜单——后端是厨房，前端是服务员，API 是菜单，规定了能点什么、怎么点。

---

## 全栈与前后端分离

**前后端分离**是目前主流的开发模式：前端和后端是两个独立的项目，通过 API 通信。

```
前端项目 (React App)  ←→  后端项目 (Node.js API)  ←→  数据库
```

**全栈框架**（如 Next.js）则把前端和部分后端整合在一个项目里，减少配置成本。

这两种方式的选择取决于项目规模和团队结构，详见第五章和第八章。

---

## 技术版图速览

```
前端技术
├── 基础：HTML + CSS + JavaScript
├── 框架：React / Vue / Angular / Svelte
├── 样式：Tailwind CSS / Bootstrap / Sass
├── 构建工具：Vite / Webpack
├── 全栈框架：Next.js / Nuxt.js
└── 跨端：Flutter / React Native / Electron / 小程序

后端技术
├── 运行时：Node.js / Python / Java / Go
├── 框架：Express / FastAPI / Spring / Gin
├── API设计：REST / GraphQL / gRPC
├── 认证：JWT / OAuth2 / Session
└── 中间件

数据库
├── 关系型：PostgreSQL / MySQL / SQLite
├── 文档型：MongoDB
├── 缓存：Redis
└── ORM：Prisma / TypeORM / SQLAlchemy

DevOps
├── 容器：Docker
├── 云平台：AWS / GCP / Azure / 阿里云 / Vercel
├── CI/CD：GitHub Actions
└── 域名 + HTTPS
```

---

## 一个真实项目涉及什么

假设你要做一个"任务管理应用"（类似 Todoist）：

| 层级 | 技术 | 负责什么 |
|------|------|----------|
| 前端 | React + Next.js | 显示任务列表、表单交互 |
| 样式 | Tailwind CSS | 界面美化 |
| 后端 | Node.js + Express | 接收增删改查请求、处理业务逻辑 |
| 数据库 | PostgreSQL | 存储任务数据、用户信息 |
| 认证 | JWT | 用户登录状态管理 |
| 部署 | Vercel + Docker | 上线运行 |

这些技术组合在一起，就是一个**技术栈**（Tech Stack）。

---

> **下一节**：[1.3 一个程序项目的组成](./03-一个程序项目的组成.md) — 看看一个真实项目的文件夹结构是什么样的。
