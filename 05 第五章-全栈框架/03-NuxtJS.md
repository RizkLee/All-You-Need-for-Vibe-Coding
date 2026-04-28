# 5.3 Nuxt.js——Vue 全栈框架

> Nuxt.js 对于 Vue 的关系，就像 Next.js 对于 React——提供了 SSR、文件路由、全栈能力。

---

## Nuxt.js 概述

**Nuxt.js** 是 Vue 生态中的全栈框架，提供了与 Next.js 类似的功能：

- **文件系统路由**：`pages/` 目录下的 `.vue` 文件自动成为路由
- **SSR / SSG / CSR**：多种渲染策略可选
- **服务端 API**：在 `server/api/` 目录下写后端接口
- **自动导入**：组件、Composables、工具函数自动引入，不需要手写 import
- **Nitro 引擎**：通用的服务器引擎，可部署到任何平台

---

## 文件结构

```
nuxt-app/
  pages/                 ← 页面（自动成为路由）
    index.vue            → /
    about.vue            → /about
    products/
      index.vue          → /products
      [id].vue           → /products/:id
  
  components/            ← 自动导入的组件
    Card.vue
    Header.vue
  
  composables/           ← 自动导入的 Vue composables（相当于 React hooks）
    useUser.ts
  
  server/
    api/                 ← 服务端 API
      users.get.ts       → GET /api/users
      users.post.ts      → POST /api/users
    middleware/          ← 服务端中间件
  
  layouts/               ← 布局模板
    default.vue
    admin.vue
  
  public/                ← 静态资源
  nuxt.config.ts         ← 框架配置
```

---

## Nuxt 的自动导入

这是 Nuxt 的一大特色，也是一个设计哲学上的选择：

```vue
<!-- 在 Nuxt 中，不需要 import，直接使用 -->
<script setup>
// 不需要 import { ref, computed } from 'vue'
const count = ref(0)
const doubled = computed(() => count.value * 2)

// 不需要 import { useUser } from '@/composables/useUser'
const user = useUser()

// 不需要 import MyButton from '@/components/MyButton.vue'
// 在模板里直接用 <MyButton />
</script>
```

Vue 社区对此有不同看法：一方认为自动导入减少了样板代码；另一方认为这破坏了代码的可追溯性，不利于大型项目维护。

---

## Nuxt 的数据获取

Nuxt 提供了专用的数据获取 Composable：

```vue
<script setup>
// useFetch: 在服务器端和客户端都能工作（SSR 友好）
const { data: products, pending, error } = await useFetch('/api/products')

// useAsyncData: 更灵活，适合复杂场景
const { data: user } = await useAsyncData('user', () =>
  $fetch(`/api/users/${route.params.id}`)
)
</script>
```

这些 Composable 在 SSR 时在服务器执行，在客户端导航时在浏览器执行，数据在两端自动同步。

---

## Nuxt vs Next.js

| | Nuxt.js | Next.js |
|--|---------|---------|
| 基础框架 | Vue 3 | React |
| 路由方式 | 文件系统 | 文件系统（App Router）|
| 自动导入 | ✅ 内置 | ❌ 需要手动 import |
| API 层 | server/api/ | app/api/ |
| 中国社区 | ✅ 较好 | 一般 |
| 生态规模 | 较小 | 非常大 |
| 部署平台 | Vercel/Netlify/自部署 | Vercel 最佳 |

如果你已经选择了 Vue，Nuxt 是全栈开发的自然延伸。如果你在 Vue 和 React 之间还没决定，生态规模来说 Next.js 更大。

---

> **下一节**：[5.4 其他全栈方案一览](./04-其他全栈方案.md)
