# 2.4 TypeScript——JavaScript 的超集

> TypeScript 在 JavaScript 之上增加了类型系统，让代码更可靠、更易维护。

---

## 为什么需要 TypeScript

JavaScript 是**动态类型**语言，变量的类型在运行时才确定：

```javascript
// JavaScript：没有类型检查
function greet(user) {
  return "你好，" + user.name
}

greet("张三")     // 传入了字符串，不是对象
// 运行时才报错：Cannot read property 'name' of undefined
```

**TypeScript** 在编写代码时就能发现这类错误：

```typescript
// TypeScript：有类型检查
interface User {
  name: string
  age: number
}

function greet(user: User): string {
  return `你好，${user.name}`
}

greet("张三")  // 立即报错：类型不匹配！
greet({ name: "张三", age: 25 })  // 正确
```

---

## TypeScript 与 JavaScript 的关系

```
TypeScript ⊃ JavaScript
（TypeScript 是 JavaScript 的超集）
```

- 所有合法的 JavaScript 代码都是合法的 TypeScript 代码
- TypeScript 增加了类型注解语法
- TypeScript 代码最终会**编译（转译）成 JavaScript**，浏览器和 Node.js 执行的还是 JS
- 文件扩展名：`.ts`（TypeScript），`.tsx`（TypeScript + JSX）

---

## TypeScript 的核心概念

### 基础类型

```typescript
const name: string = "张三"
const age: number = 25
const isActive: boolean = true
const tags: string[] = ["前端", "全栈"]
const score: number[] = [90, 85, 92]

// 联合类型：可以是多种类型之一
let id: string | number = "abc-123"
id = 456  // 也合法

// 可选属性用 ?
function greet(name: string, title?: string) {
  return title ? `${title} ${name}` : name
}
```

### 接口（Interface）

描述对象的结构：

```typescript
interface Product {
  id: number
  name: string
  price: number
  description?: string  // 可选
  tags: string[]
}

function displayProduct(product: Product) {
  console.log(product.name, product.price)
}
```

### 类型别名（Type）

```typescript
type Status = "pending" | "active" | "inactive"  // 枚举几种可能的值
type Point = { x: number; y: number }

let currentStatus: Status = "active"
```

### 泛型（Generics）

写可复用的代码，类型作为参数：

```typescript
// 一个可以包装任意类型数据的 API 响应格式
interface ApiResponse<T> {
  data: T
  status: number
  message: string
}

// 使用时指定具体类型
type UserResponse = ApiResponse<User>
type ProductListResponse = ApiResponse<Product[]>
```

---

## TypeScript 在实际开发中的样子

在 React + TypeScript 项目中：

```tsx
// Button.tsx

interface ButtonProps {
  label: string
  onClick: () => void
  variant?: "primary" | "secondary" | "danger"
  disabled?: boolean
}

function Button({ label, onClick, variant = "primary", disabled = false }: ButtonProps) {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {label}
    </button>
  )
}

// 使用时，IDE 会自动提示可用的 props，并在类型错误时报警
<Button label="提交" onClick={handleSubmit} variant="primary" />
```

---

## TypeScript 的实际价值

| 场景 | 没有 TypeScript | 有 TypeScript |
|------|----------------|---------------|
| 重构代码 | 不知道改了哪里会出问题 | IDE 自动标出所有受影响的地方 |
| 调用 API | 不记得返回的字段叫什么 | 自动补全 + 类型提示 |
| 团队协作 | 看不懂别人的函数要传什么 | 接口定义即文档 |
| 发现 bug | 运行时才崩溃 | 写代码时就报错 |

---

## 应该学 TypeScript 吗？

**答**：现代全栈开发，TypeScript 已经成为标准。

- React、Vue、Next.js 都原生支持 TypeScript
- 大量开源库提供 TypeScript 类型定义
- Vibe Coding 时代，AI 生成的代码通常也用 TypeScript

你不需要精通所有类型体操技巧，但你需要能读懂和写基础的类型注解。当 AI 生成了 TypeScript 代码，你应该能理解各个类型注解的含义。

---

## tsconfig.json：TypeScript 配置

```json
{
  "compilerOptions": {
    "target": "ES2020",        // 编译成哪个 JS 版本
    "strict": true,            // 开启严格模式（推荐）
    "jsx": "react-jsx",        // 支持 JSX（React 用）
    "moduleResolution": "bundler",
    "paths": {
      "@/*": ["./src/*"]       // 路径别名，@/components 代替 ../../components
    }
  }
}
```

---

> **下一节**：[2.5 前端框架的诞生与意义](./05-前端框架的诞生与意义.md) — 为什么需要 React、Vue 这样的框架？
