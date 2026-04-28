# 9.4 前后端 API 联调与数据流

> 全栈框架中，前后端的边界变得模糊，但数据流动的逻辑依然清晰。我们来看看数据是如何在组件和数据库之间穿梭的。

---

## 现代 React 的数据流：Server Actions

在传统的 React 开发中，前端向后端发数据需要写 API 接口，然后用 `fetch` 发送 POST 请求。
在 Next.js (App Router) 中，我们有了更简洁的方案：**Server Actions**。

Server Action 允许你在前端组件中直接调用运行在服务器上的函数，Next.js 会自动在底层帮你处理 API 请求。

### 示例：创建一个任务

**1. 定义 Server Action (在单独的文件中，或者组件的内部)**

```typescript
// app/actions/task-actions.ts
'use server' // 声明这个文件里的函数只在服务器运行

import { createServerSupabaseClient } from '@/lib/supabase/server'
import { revalidatePath } from 'next/cache'

export async function createTask(formData: FormData) {
  const title = formData.get('title') as string
  const supabase = createServerSupabaseClient()
  
  // 1. 获取当前登录用户
  const { data: { user } } = await supabase.auth.getUser()
  if (!user) throw new Error('未登录')

  // 2. 插入数据库
  const { error } = await supabase.from('tasks').insert({
    title,
    user_id: user.id,
    status: 'todo'
  })

  if (error) throw error

  // 3. 告诉 Next.js 清除缓存，刷新页面数据
  revalidatePath('/dashboard')
}
```

**2. 在前端组件中使用 (Form 组件)**

```tsx
// components/tasks/CreateTaskForm.tsx
'use client' // 这是客户端组件，因为有表单交互

import { createTask } from '@/app/actions/task-actions'
import { useFormStatus } from 'react-dom'

// 提交按钮单独提取出来，以获取加载状态
function SubmitButton() {
  const { pending } = useFormStatus()
  return (
    <button disabled={pending}>
      {pending ? '创建中...' : '创建任务'}
    </button>
  )
}

export function CreateTaskForm() {
  return (
    // 直接把 Server Action 传给 action 属性
    <form action={createTask}>
      <input type="text" name="title" placeholder="要做什么？" required />
      <SubmitButton />
    </form>
  )
}
```

这就是 Vibe Coding 时代的效率：**不需要写 API 路由，不需要用 fetch，不需要手动管理加载状态**。

---

## 数据的获取与渲染：Server Components

获取数据同样简单。我们可以直接在服务端组件中查询数据库。

```tsx
// app/dashboard/page.tsx (这是一个 Server Component)
import { createServerSupabaseClient } from '@/lib/supabase/server'
import { TaskList } from '@/components/tasks/TaskList'

export default async function DashboardPage() {
  const supabase = createServerSupabaseClient()
  
  // 1. 直接在组件里查数据库！
  const { data: tasks, error } = await supabase
    .from('tasks')
    .select('*')
    .order('created_at', { ascending: false })

  if (error) return <div>加载失败</div>

  // 2. 把数据传给展示组件
  return (
    <div>
      <h1>我的任务</h1>
      <TaskList tasks={tasks} />
    </div>
  )
}
```

**为什么这很好？**
- 零客户端 JS：这段获取数据的逻辑完全在服务器执行。
- 极快：服务器直接连接同机房的数据库，没有公网 HTTP 延迟。
- SEO 完美：返回给浏览器的是已经包含任务数据的完整 HTML。

---

## 处理客户端的实时交互

如果用户在面板上点击了"完成"复选框，我们需要立即更新 UI，同时更新数据库。这时我们可以结合 **React Query**（或类似工具）来处理这种客户端的乐观更新（Optimistic UI）：先假装成功更新 UI，然后再发请求给服务器，失败了再回退。

不过，在使用 Supabase 时，我们还可以利用它的 **Realtime** 功能：

```typescript
// 伪代码思路：监听数据库变化
useEffect(() => {
  const channel = supabase
    .channel('tasks-changes')
    .on('postgres_changes', { event: '*', schema: 'public', table: 'tasks' }, (payload) => {
      // 只要数据库里的 tasks 表有变化（别人新增了任务、我在其他设备删了任务）
      // 这个回调就会触发，我们就在这里更新本地 UI 状态
      updateLocalTasksState(payload)
    })
    .subscribe()

  return () => { supabase.removeChannel(channel) }
}, [])
```

---

## API 联调的常见坑

1. **忘记处理加载（Loading）和错误（Error）状态**：网络请求永远可能失败。使用 Next.js 的 `loading.tsx` 和 `error.tsx` 特殊文件来优雅地处理它们。
2. **安全问题 (Row Level Security - RLS)**：千万不要因为是在前端直接调 Supabase 就忘记权限！必须在 Supabase 控制台配置 RLS："只允许用户查询和修改 `user_id` 等于自己 ID 的记录"。这样即使别人拿到你的 API Key，也无法窃取其他用户的数据。
3. **跨端 CORS 报错**：如果是前后端分离的项目，前端本地跑在 3000 端口，请求 8000 端口的后端，浏览器会拦截。全栈框架（Next.js）因为前后端同源，天然没有这个问题。

---

> **下一节**：[9.5 测试、上线与运维](./05-测试上线与运维.md)
