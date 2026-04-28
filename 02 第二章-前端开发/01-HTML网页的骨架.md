# 2.1 HTML——网页的骨架

> HTML 是构建网页的基础语言，它定义了页面的结构和内容。

---

## HTML 是什么

**HTML**（HyperText Markup Language，超文本标记语言）是网页的骨架。它不是编程语言，而是**标记语言**——你用标签（Tag）来标记内容，告诉浏览器这块内容是标题、这块是段落、这块是图片。

一个最简单的 HTML 文件：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>我的网页</title>
</head>
<body>
  <h1>你好，世界</h1>
  <p>这是一个段落。</p>
</body>
</html>
```

浏览器解析这个文件后，会显示一个有标题和段落的页面。

---

## HTML 的基本结构

```
<!DOCTYPE html>      → 声明这是 HTML5 文档
<html>               → 根元素，包含所有内容
  <head>             → 页面的元信息（不显示在页面上）
    <meta charset>   → 字符编码
    <title>          → 浏览器标签页显示的标题
    <link>           → 引入外部CSS
    <script>         → 引入JS（或写在body底部）
  </head>
  <body>             → 页面显示的内容
    ...              → 所有可见元素
  </body>
</html>
```

---

## 常用 HTML 标签

### 文本内容

| 标签 | 用途 |
|------|------|
| `<h1>` ~ `<h6>` | 标题（h1最大，h6最小） |
| `<p>` | 段落 |
| `<span>` | 行内文本容器（无语义） |
| `<strong>` | **加粗**（有语义：重要内容） |
| `<em>` | *斜体*（有语义：强调） |

### 链接与媒体

```html
<a href="https://google.com">点击跳转</a>
<img src="photo.jpg" alt="照片描述">
<video src="video.mp4" controls></video>
```

### 列表

```html
<!-- 无序列表 -->
<ul>
  <li>苹果</li>
  <li>香蕉</li>
</ul>

<!-- 有序列表 -->
<ol>
  <li>第一步</li>
  <li>第二步</li>
</ol>
```

### 表格

```html
<table>
  <thead>
    <tr>
      <th>姓名</th>
      <th>年龄</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>张三</td>
      <td>25</td>
    </tr>
  </tbody>
</table>
```

### 表单

```html
<form action="/submit" method="POST">
  <input type="text" name="username" placeholder="用户名">
  <input type="password" name="password" placeholder="密码">
  <button type="submit">登录</button>
</form>
```

---

## 语义化 HTML

HTML5 引入了很多**语义化标签**，让代码结构更清晰：

```html
<header>         网页头部（导航栏、Logo）
<nav>            导航菜单
<main>           主要内容区域
<article>        独立内容（博客文章）
<section>        内容分区
<aside>          侧边栏
<footer>         网页底部
```

对比：

```html
<!-- 无语义（老式写法）-->
<div class="header">...</div>
<div class="nav">...</div>

<!-- 有语义（推荐）-->
<header>...</header>
<nav>...</nav>
```

语义化的好处：
- 对搜索引擎友好（SEO）
- 对屏幕阅读器友好（无障碍访问）
- 代码可读性更高

---

## DOM：浏览器如何理解 HTML

浏览器解析 HTML 后，会在内存中构建一棵**DOM 树**（Document Object Model，文档对象模型）：

```
Document
└── html
    ├── head
    │   └── title: "我的网页"
    └── body
        ├── h1: "你好"
        └── p: "这是段落"
```

JavaScript 通过操作 DOM 来动态改变页面内容。React、Vue 等框架的本质，就是帮你更高效地操作 DOM。

---

## HTML 属性

标签可以有**属性**，提供额外信息：

```html
<a href="url" target="_blank" rel="noopener">在新标签打开</a>
<img src="photo.jpg" alt="说明文字" width="300">
<input type="email" required placeholder="请输入邮箱">
<div id="main-content" class="container active">...</div>
```

最重要的两个属性：
- **`id`**：唯一标识，一个页面上每个 id 只能出现一次
- **`class`**：CSS 类名，可以多个元素共用，也可以一个元素有多个 class

---

## HTML 与 CSS、JavaScript 的关系

```
HTML  → 结构（这里有什么）
CSS   → 样式（它长什么样）
JS    → 行为（它做什么）
```

三者配合：

```html
<!-- HTML 定义结构 -->
<button id="my-btn" class="btn-primary">点击我</button>

<!-- CSS 控制外观 -->
<style>
.btn-primary {
  background: blue;
  color: white;
  padding: 10px 20px;
}
</style>

<!-- JS 添加交互 -->
<script>
document.getElementById('my-btn').addEventListener('click', () => {
  alert('按钮被点击了！')
})
</script>
```

---

## 在 Vibe Coding 中如何对待 HTML

你很少需要手写大量 HTML。在现代前端框架（React、Vue）中，你写的是**组件**，框架自动生成 HTML。

但你需要理解 HTML 的原因：
- 框架生成的最终产物还是 HTML
- 调试时需要在浏览器开发者工具中查看 HTML 结构
- AI 生成的代码中会有 HTML，你需要能读懂它

---

> **下一节**：[2.2 CSS——网页的样式](./02-CSS网页的样式.md)
