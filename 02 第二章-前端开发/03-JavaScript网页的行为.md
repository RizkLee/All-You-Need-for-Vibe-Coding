# 2.3 JavaScript——网页的行为

> JavaScript 是唯一能在浏览器中运行的编程语言，它让网页"活"起来。

---

## JavaScript 的角色

HTML 和 CSS 只能定义静态内容和样式。JavaScript（JS）让页面具备**交互性和动态性**：

- 响应用户点击、输入、滚动
- 向服务器发送请求，获取数据后更新页面
- 验证表单数据
- 实现动画效果
- 控制浏览器行为（跳转、弹窗、存储）

---

## JavaScript 的基础语法（快速过）

这里不做详细教学，只是建立认知：

```javascript
// 变量声明（现代写法用 let 和 const）
const name = "张三"     // 不可重新赋值
let count = 0           // 可重新赋值

// 函数
function add(a, b) {
  return a + b
}

// 箭头函数（现代写法）
const add = (a, b) => a + b

// 条件
if (count > 0) {
  console.log("正数")
} else {
  console.log("非正数")
}

// 循环
const fruits = ["苹果", "香蕉", "橙子"]
fruits.forEach(fruit => {
  console.log(fruit)
})

// 对象
const user = {
  name: "张三",
  age: 25,
  greet: function() {
    return `你好，我是${this.name}`
  }
}

// 数组方法
const numbers = [1, 2, 3, 4, 5]
const doubled = numbers.map(n => n * 2)      // [2, 4, 6, 8, 10]
const evens = numbers.filter(n => n % 2 === 0) // [2, 4]
```

---

## DOM 操作：JavaScript 修改页面

JavaScript 通过 **DOM API** 查找和修改 HTML 元素：

```javascript
// 获取元素
const btn = document.getElementById('my-btn')
const cards = document.querySelectorAll('.card')

// 修改内容
btn.textContent = "新文字"
btn.innerHTML = "<strong>粗体文字</strong>"

// 修改样式
btn.style.backgroundColor = 'red'
btn.classList.add('active')
btn.classList.remove('disabled')

// 监听事件
btn.addEventListener('click', () => {
  alert('按钮被点击了！')
})

// 创建新元素
const div = document.createElement('div')
div.textContent = "动态内容"
document.body.appendChild(div)
```

---

## 异步编程：Async/Await

JavaScript 是单线程的，网络请求等操作是**异步**的（不阻塞其他代码执行）。

理解异步的关键：

```javascript
// 错误理解：这不会等待请求完成
const data = fetch('/api/users')  // 这返回的是 Promise，不是数据

// 正确：使用 async/await
async function getUsers() {
  const response = await fetch('/api/users')  // 等待请求完成
  const data = await response.json()           // 等待解析完成
  console.log(data)                            // 现在才有真实数据
}

getUsers()
```

**Promise** 是 JS 异步操作的基础，**async/await** 是它的语法糖，让异步代码看起来像同步代码。

---

## 向后端发起请求：Fetch API

```javascript
// GET 请求（获取数据）
async function fetchUsers() {
  const response = await fetch('https://api.example.com/users')
  const users = await response.json()
  return users
}

// POST 请求（发送数据）
async function createUser(userData) {
  const response = await fetch('https://api.example.com/users', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${token}`
    },
    body: JSON.stringify(userData)
  })
  
  if (!response.ok) {
    throw new Error('请求失败')
  }
  
  return await response.json()
}
```

在实际项目中，通常会用 **axios** 库来简化这个过程。

---

## ES6+ 现代语法

现代 JavaScript（ES6 及之后版本）引入了很多重要特性：

```javascript
// 解构赋值
const { name, age } = user
const [first, second] = array

// 展开运算符
const merged = { ...obj1, ...obj2 }
const combined = [...arr1, ...arr2]

// 模板字符串
const greeting = `你好，${name}！你今年 ${age} 岁。`

// 可选链（安全访问可能为空的属性）
const city = user?.address?.city   // 如果 address 不存在，不报错，返回 undefined

// 空值合并
const displayName = user.name ?? '匿名用户'  // 如果 name 是 null/undefined，用默认值

// 模块系统
// 导出
export const add = (a, b) => a + b
export default function App() { ... }

// 导入
import { add } from './utils'
import App from './App'
```

---

## JavaScript 在浏览器中能做什么

```
操作 DOM          → 修改页面内容和样式
事件处理          → 响应用户交互
网络请求          → 与后端通信（fetch, axios）
本地存储          → localStorage, sessionStorage, Cookie
浏览器 API        → 地理位置、摄像头、通知、Web Worker
WebSocket        → 实时通信
Canvas/WebGL     → 图形绘制（游戏、数据可视化）
```

---

## JavaScript 与 Node.js 的关系

JavaScript 最初只能在浏览器中运行。**Node.js** 的出现让 JS 也能在服务器端运行（详见第三章第2节）。

```
浏览器中的 JS    → 前端代码，操作 DOM，发起网络请求
Node.js 中的 JS  → 后端代码，操作文件系统，启动服务器
```

同一门语言，两种运行环境，这是 JavaScript 的独特优势。

---

## 为什么在 React/Vue 中很少直接操作 DOM

手动操作 DOM 有几个问题：
1. 代码复杂，难以维护
2. 性能差（频繁操作 DOM 会导致页面重渲染）
3. 数据和 UI 同步困难

React 和 Vue 解决了这些问题，你只需要声明"当数据是 X 时，界面应该是什么样"，框架自动处理 DOM 操作。

这是前端框架存在的核心原因，详见下一节。

---

> **下一节**：[2.4 TypeScript——JavaScript的超集](./04-TypeScript.md)
