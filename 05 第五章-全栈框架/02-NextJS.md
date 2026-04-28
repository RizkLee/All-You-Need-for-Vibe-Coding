# 5.2 Next.js——React 全栈框架

> Next.js 是目前最流行的 React 全栈框架，几乎成为 React 生产环境的默认选择。

---

## Next.js 是什么

**Next.js** 是 Vercel 公司基于 React 构建的全栈框架。它在 React 的基础上增加了：

- 文件系统路由（不需要手动配置 React Router）
- 服务端渲染（SSR）和静态生成（SSG）
- API 路由（内置轻量后端）
- 图片优化、字体优化
- 代码分割（Code Splitting）
- 中间件

Next.js 解决了"用 React 做生产级应用需要自己配置一大堆东西"的问题，让你开箱即用地得到一个性能优良的全栈应用框架。

---

## App Router vs Pages Router

Next.js 有两种路由系统：

**Pages Router（旧，Next.js 12 及之前）**：  
在 `pages/` 目录下创建 `.tsx` 文件，文件名即为路由。

**App Router（新，Next.js 13+ 推荐）**：  
在 `app/` 目录下工作，使用文件夹和 `page.tsx` 的结构，带来了更多能力（Server Components）。

```
app/
  page.tsx               → /
  about/
    page.tsx             → /about
  blog/
    page.tsx             → /blog
    [slug]/
      page.tsx           → /blog/:slug（动态路由）
  dashboard/
    layout.tsx           → /dashboard 的共享布局
    page.tsx             → /dashboard
    settings/
      page.tsx           → /dashboard/settings
  api/
    users/
      route.ts           → /api/users（API 接口）
```

---

## Server Components：重要的新概念

Next.js App Router 引入了 **React Server Components（RSC）**，这是 React 和全栈框架的重要演进：

**传统 React 组件（Client Component）**：
- 在浏览器中运行
- 可以使用 `useState`、`useEffect` 等 Hook
- 可以响应用户交互

**Server Component**：
- 在服务器上运行，输出 HTML 发送给浏览器
- **可以直接访问数据库**，不需要通过 API
- 不发送 JS 代码到浏览器，减少客户端 bundle 体积
- **不能**使用 `useState` 等 Hook，不能有交互逻辑

```
默认是 Server Component（更快，更少 JS）：
async function ProductPage({ params }) {
  // 直接查询数据库，不需要 fetch API
  const product = await prisma.product.findUnique({ where: { id: params.id } })
  return <div>{product.name}</div>
}

需要交互时，加 'use client' 变成 Client Component：
'use client'
function AddToCartButton({ productId }) {
  const [loading, setLoading] = useState(false)
  return <button onClick={...}>加入购物车</button>
}
```

这种模式让你能**最小化客户端 JavaScript 的体积**，服务器负责数据获取和渲染，浏览器只处理需要交互的部分。

---

## Next.js 的 API 路由

在 `app/api/` 目录下创建 `route.ts` 文件，就是一个后端 API：

```
app/api/users/route.ts → GET /api/users 和 POST /api/users
app/api/users/[id]/route.ts → GET/PUT/DELETE /api/users/:id
```

这些 API 在服务器端运行，可以连接数据库、处理认证等——与独立的 Express 服务器能力相似，只是更轻量。

---

## Next.js 的完整技术栈组合

Next.js 自身只是框架，完整的 Next.js 全栈项目通常还包括：

```
Next.js（框架）
  + TypeScript（类型安全）
  + Tailwind CSS（样式）
  + Prisma（数据库 ORM）
  + PostgreSQL / Supabase（数据库）
  + NextAuth.js 或 Clerk（认证）
  + React Query 或 SWR（客户端数据获取）
  + Vercel（部署）
```

这套组合被社区称为"T3 Stack"（或其变体），是目前 Next.js 全栈开发的主流方案。

---

## Vercel 与 Next.js 的关系

**Vercel** 是 Next.js 的开发公司，也是最适合部署 Next.js 的平台：

- 与 Next.js 深度集成，所有特性都能完美支持
- 连接 GitHub 仓库后，每次 push 自动部署
- 全球 CDN，静态资源分发快
- 有免费套餐，个人项目够用

当然，Next.js 也可以部署在其他平台（AWS、阿里云、自己的服务器），只是需要更多配置。

---

> **下一节**：[5.3 Nuxt.js——Vue 全栈框架](./03-NuxtJS.md)
