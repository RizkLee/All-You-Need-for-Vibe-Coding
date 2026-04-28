# 2.2 CSS——网页的样式

> CSS 控制网页的外观，从颜色字体到布局动画，都由它负责。

---

## CSS 是什么

**CSS**（Cascading Style Sheets，层叠样式表）定义了 HTML 元素的视觉呈现。

"层叠"指的是：多个样式规则可能作用于同一元素，CSS 有一套**优先级规则**来决定最终应用哪个样式。

引入 CSS 的方式：

```html
<!-- 方式1：外部文件（推荐） -->
<link rel="stylesheet" href="styles.css">

<!-- 方式2：内嵌在 HTML 中 -->
<style>
  h1 { color: red; }
</style>

<!-- 方式3：行内样式（不推荐，难维护） -->
<h1 style="color: red;">标题</h1>
```

---

## CSS 的基本语法

```css
选择器 {
  属性: 值;
  属性: 值;
}
```

例子：

```css
/* 选中所有 h1 元素 */
h1 {
  color: #333333;
  font-size: 32px;
  font-weight: bold;
}

/* 选中 class="card" 的元素 */
.card {
  background: white;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}

/* 选中 id="header" 的元素 */
#header {
  position: fixed;
  top: 0;
  width: 100%;
}
```

---

## 选择器

| 选择器 | 语法 | 选中什么 |
|--------|------|----------|
| 元素选择器 | `h1` | 所有 h1 标签 |
| 类选择器 | `.card` | class 含 "card" 的元素 |
| ID 选择器 | `#header` | id 为 "header" 的元素 |
| 后代选择器 | `.card p` | .card 内部的所有 p |
| 直接子元素 | `.card > p` | .card 的直接子 p |
| 伪类 | `a:hover` | 鼠标悬停时的 a |
| 伪类 | `li:first-child` | 列表中第一个 li |

---

## 盒模型（Box Model）

每个 HTML 元素都是一个矩形的"盒子"，由四层组成：

```
┌─────────────────────────────┐
│           margin            │  ← 外边距（与其他元素的距离）
│  ┌───────────────────────┐  │
│  │        border         │  │  ← 边框
│  │  ┌─────────────────┐  │  │
│  │  │     padding     │  │  │  ← 内边距（内容与边框的距离）
│  │  │  ┌───────────┐  │  │  │
│  │  │  │  content  │  │  │  │  ← 内容区域
│  │  │  └───────────┘  │  │  │
│  │  └─────────────────┘  │  │
│  └───────────────────────┘  │
└─────────────────────────────┘
```

```css
.box {
  width: 200px;         /* 内容宽度 */
  padding: 16px;        /* 内边距 */
  border: 2px solid #ccc; /* 边框 */
  margin: 24px;         /* 外边距 */
  
  /* 推荐设置：让 width 包含 padding 和 border */
  box-sizing: border-box;
}
```

---

## 布局方式

CSS 有几种核心布局方式，这是前端开发中最重要的概念之一。

### Flexbox（弹性布局）

适合一维布局（一行或一列）：

```css
.container {
  display: flex;
  flex-direction: row;      /* 水平排列 */
  justify-content: center;  /* 主轴对齐：居中 */
  align-items: center;      /* 交叉轴对齐：居中 */
  gap: 16px;                /* 元素间距 */
}
```

常用场景：导航栏、卡片行、按钮组

### Grid（网格布局）

适合二维布局（行+列）：

```css
.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);  /* 3列等宽 */
  gap: 24px;
}
```

常用场景：图片墙、卡片列表、页面整体布局

### Position（定位）

```css
.element {
  position: fixed;     /* 固定在屏幕某个位置 */
  position: absolute;  /* 相对于父元素定位 */
  position: relative;  /* 相对于自身位置微调 */
  position: sticky;    /* 滚动到一定位置后固定 */
  top: 0;
  right: 0;
}
```

---

## 响应式设计与媒体查询

让页面在不同屏幕尺寸（手机、平板、电脑）下都能正常显示：

```css
/* 默认样式（手机优先） */
.card {
  width: 100%;
}

/* 平板（768px以上） */
@media (min-width: 768px) {
  .card {
    width: 50%;
  }
}

/* 桌面（1024px以上） */
@media (min-width: 1024px) {
  .card {
    width: 33.33%;
  }
}
```

---

## CSS 变量（自定义属性）

```css
/* 定义变量 */
:root {
  --primary-color: #6366f1;
  --text-color: #1f2937;
  --spacing-md: 16px;
}

/* 使用变量 */
.button {
  background: var(--primary-color);
  color: white;
  padding: var(--spacing-md);
}
```

CSS 变量让主题切换（深色/浅色模式）变得简单。

---

## CSS 动画

```css
/* 过渡动画 */
.button {
  background: blue;
  transition: background 0.3s ease;
}
.button:hover {
  background: darkblue;
}

/* 关键帧动画 */
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}

.card {
  animation: fadeIn 0.5s ease forwards;
}
```

---

## CSS 预处理器：Sass/SCSS

CSS 原生不支持嵌套、函数等高级功能。**Sass/SCSS** 是 CSS 的扩展语言，增加了这些能力，最终编译成普通 CSS：

```scss
// SCSS 支持嵌套
.nav {
  background: #333;
  
  &-item {           // 编译为 .nav-item
    padding: 10px;
    
    &:hover {        // 编译为 .nav-item:hover
      color: white;
    }
  }
}

// SCSS 支持变量（在 CSS 变量出现之前就有了）
$primary: #6366f1;
.btn { color: $primary; }
```

现代项目中 CSS 变量已经很强大，SCSS 的使用在减少，但你会在很多老项目中看到它。

---

## 在现代框架中如何写 CSS

不同框架有不同的 CSS 方案：

| 方案 | 描述 | 代表工具 |
|------|------|----------|
| 全局 CSS | 传统写法，一个 .css 文件 | 普通 CSS |
| CSS Modules | 样式局限于当前组件，避免冲突 | `Button.module.css` |
| CSS-in-JS | 在 JS 文件中写 CSS | styled-components |
| 原子化 CSS | 直接在 HTML 上加工具类 | Tailwind CSS（见2.9节） |

---

> **下一节**：[2.3 JavaScript——网页的行为](./03-JavaScript网页的行为.md)
