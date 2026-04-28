# 2.6 React——组件化开发

> React 是目前最流行的前端框架，由 Facebook 开发，以组件化和声明式渲染为核心。

---

## React 的本质

React 是一个**UI 库**（不是完整框架）。它只解决一件事：**把数据渲染成界面，并在数据变化时高效更新界面**。

路由、状态管理、HTTP 请求——这些都需要配合其他库使用。这种设计哲学让 React 非常灵活，但也意味着需要更多的技术选型决策。

---

## JSX：在 JS 里写 HTML

React 引入了 **JSX** 语法，让你在 JavaScript 中直接写类 HTML 的代码：

```jsx
// 这是 JSX，不是 HTML
function Greeting({ name }) {
  return (
    <div className="greeting">   {/* class 要写成 className */}
      <h1>你好，{name}！</h1>    {/* 大括号内可以写 JS 表达式 */}
      <p>{new Date().toLocaleDateString()}</p>
    </div>
  )
}
```

JSX 实际上会被编译成 `React.createElement()` 调用，最终转换成 DOM。你不需要记这个细节，只要知道 JSX 不是真正的 HTML。

JSX 与 HTML 的区别：
- `class` → `className`
- `for` → `htmlFor`
- 所有标签必须关闭（`<br />`，不是 `<br>`）
- 组件名必须大写（`<Button>`，不是 `<button>`）
- 样式用对象：`style={{ color: 'red' }}`

---

## 组件（Component）

React 应用由组件构成，每个组件是一个**返回 JSX 的函数**（现代 React 都用函数组件）：

```jsx
// 最简单的组件
function Button({ label, onClick, disabled = false }) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className="btn"
    >
      {label}
    </button>
  )
}

// 使用组件（像 HTML 标签一样）
function App() {
  return (
    <div>
      <Button label="提交" onClick={() => console.log('clicked')} />
      <Button label="取消" onClick={() => console.log('cancel')} disabled />
    </div>
  )
}
```

---

## Props：父组件向子组件传递数据

**Props**（properties）是组件的输入参数，由父组件传入，子组件只能读取，不能修改：

```jsx
// 父组件
function ProductList() {
  const products = [
    { id: 1, name: "手机", price: 3999 },
    { id: 2, name: "耳机", price: 299 }
  ]
  
  return (
    <div>
      {products.map(product => (
        <ProductCard
          key={product.id}    // key 是 React 要求的，用于 Diff 算法
          name={product.name}
          price={product.price}
        />
      ))}
    </div>
  )
}

// 子组件
function ProductCard({ name, price }) {
  return (
    <div className="card">
      <h2>{name}</h2>
      <p>¥{price}</p>
    </div>
  )
}
```

---

## State：组件的内部状态

**State** 是组件内部维护的数据。当 state 改变时，组件重新渲染。

使用 `useState` Hook：

```jsx
import { useState } from 'react'

function Counter() {
  const [count, setCount] = useState(0)  // 初始值为 0
  
  return (
    <div>
      <p>当前计数：{count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
      <button onClick={() => setCount(count - 1)}>-1</button>
      <button onClick={() => setCount(0)}>重置</button>
    </div>
  )
}
```

**重要规则**：不能直接修改 state，必须通过 setter 函数：
```jsx
// 错误：直接修改
count = count + 1

// 正确：通过 setter
setCount(count + 1)
```

---

## Hooks：React 的功能钩子

**Hooks** 是 React 提供的一系列函数，让函数组件能使用各种 React 特性：

| Hook | 用途 |
|------|------|
| `useState` | 组件内部状态 |
| `useEffect` | 副作用（数据请求、订阅、DOM操作） |
| `useContext` | 跨组件共享数据 |
| `useRef` | 访问 DOM 元素或保存不触发渲染的值 |
| `useMemo` | 缓存计算结果（性能优化） |
| `useCallback` | 缓存函数（性能优化） |

`useEffect` 是最常用的之一——在组件挂载后执行操作：

```jsx
import { useState, useEffect } from 'react'

function UserProfile({ userId }) {
  const [user, setUser] = useState(null)
  const [loading, setLoading] = useState(true)
  
  // 当 userId 变化时，重新请求数据
  useEffect(() => {
    setLoading(true)
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        setUser(data)
        setLoading(false)
      })
  }, [userId])  // 依赖数组，userId 变化时重新执行
  
  if (loading) return <p>加载中...</p>
  return <div>{user?.name}</div>
}
```

---

## 数据流向

React 的数据流是**单向的**（从父到子）：

```
App（持有数据）
  ↓ props
ProductList
  ↓ props
ProductCard（只能读取，不能修改父组件数据）
```

子组件想修改父组件的数据？通过**回调函数（callback）**：

```jsx
function Parent() {
  const [count, setCount] = useState(0)
  
  return (
    // 把 setter 函数作为 prop 传给子组件
    <Child count={count} onIncrement={() => setCount(count + 1)} />
  )
}

function Child({ count, onIncrement }) {
  return (
    <button onClick={onIncrement}>
      点击了 {count} 次
    </button>
  )
}
```

---

## React 项目的典型技术栈

```
React（UI 框架）
+ TypeScript（类型安全）
+ Vite（构建工具）
+ React Router（页面路由）
+ Zustand / Redux（状态管理）
+ React Query / SWR（服务端数据获取）
+ Tailwind CSS（样式）
+ Axios（HTTP 请求）
```

如果用 Next.js，很多配置已经预设好了，只需要专注写业务逻辑。

---

## React 适合什么项目

- 复杂的单页应用（SPA）
- 需要丰富交互的 Web App
- 配合 Next.js 做服务端渲染
- 配合 React Native 做移动端

目前 React 在职位需求量和生态规模上都是最大的，如果只学一个前端框架，学 React 是最保险的选择。

---

> **下一节**：[2.7 Vue——渐进式前端框架](./07-Vue渐进式前端框架.md)
