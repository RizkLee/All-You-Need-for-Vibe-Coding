# 2.9 Tailwind CSS——原子化 CSS 框架

> Tailwind 改变了写 CSS 的方式，不再需要自己取类名，直接在 HTML 上堆工具类。

---

## 传统 CSS vs Tailwind

### 传统方式

```html
<!-- HTML -->
<button class="submit-button">提交</button>
```

```css
/* CSS 文件 */
.submit-button {
  background-color: #6366f1;
  color: white;
  padding: 8px 16px;
  border-radius: 6px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s;
}
.submit-button:hover {
  background-color: #4f46e5;
}
```

### Tailwind 方式

```html
<!-- 样式直接写在 HTML 里 -->
<button class="bg-indigo-500 text-white px-4 py-2 rounded-md font-semibold
               cursor-pointer transition-colors hover:bg-indigo-700">
  提交
</button>
```

---

## 什么是原子化 CSS

Tailwind 是**原子化 CSS**（Atomic CSS）的代表——每个 CSS 类只做一件事：

```
bg-indigo-500  → background-color: #6366f1
text-white     → color: white
px-4           → padding-left: 1rem; padding-right: 1rem
py-2           → padding-top: 0.5rem; padding-bottom: 0.5rem
rounded-md     → border-radius: 0.375rem
font-semibold  → font-weight: 600
hover:bg-indigo-700 → :hover { background-color: #4338ca }
```

这些"原子类"可以任意组合，覆盖几乎所有的 CSS 属性。

---

## Tailwind 的设计系统

Tailwind 内置了一套设计系统：

**颜色**（例）：
```
slate, gray, zinc, neutral, stone,
red, orange, amber, yellow, lime,
green, emerald, teal, cyan, sky, blue,
indigo, violet, purple, fuchsia, pink, rose
```
每种颜色有 50~950 的色阶（50最浅，950最深）

**间距**（基于 4px 的倍数）：
```
p-1 = 4px, p-2 = 8px, p-4 = 16px, p-8 = 32px...
```

**断点（响应式）**：
```html
<!-- 默认: 全宽；md 以上: 一半；lg 以上: 三分之一 -->
<div class="w-full md:w-1/2 lg:w-1/3">...</div>
```

---

## Tailwind 的优势

**1. 无需命名**
给 CSS 类起名是个出了名的头疼问题。Tailwind 省去了这个步骤。

**2. 不会有 CSS 膨胀**
传统 CSS 随项目增大越来越臃肿。Tailwind 会分析代码，只把用到的类打包进最终 CSS，文件极小。

**3. 与框架高度契合**
在 React/Vue 组件中，样式和结构放在一起，不需要在 CSS 文件和组件文件之间来回切换。

**4. AI 生成效果好**
AI 非常擅长生成 Tailwind 代码，因为每个类名的含义都很直观，没有命名不规范的问题。

---

## Tailwind 的劣势

**1. HTML 变得很长**
类名堆积可能让 HTML 难以阅读，特别是复杂组件。

**2. 学习曲线**
需要记忆一套新的命名规则（虽然有规律）。不过现代编辑器有自动补全，AI 也能帮你生成。

**3. 难以覆写**
当你需要精确控制某些 CSS 细节时，有时需要用到 `arbitrary values`：
```html
<div class="top-[117px] bg-[#1a1a2e]">...</div>
```

---

## Tailwind vs Bootstrap

你可能听说过 **Bootstrap**，它是更早的 CSS 框架：

| | Tailwind CSS | Bootstrap |
|--|-------------|-----------|
| 理念 | 原子化，自由组合 | 预制组件 |
| 自定义程度 | 极高 | 中等 |
| 视觉风格 | 自定义（无默认风格） | Bootstrap 风格（很多网站看着一样）|
| 文件大小 | 极小（只打包用到的）| 较大 |
| 流行度趋势 | 📈 上升 | 📉 下降 |

现代项目更多选择 Tailwind。

---

## 在 React 中使用 Tailwind

```jsx
// Card 组件
function Card({ title, description, imageUrl }) {
  return (
    <div className="bg-white rounded-xl shadow-md overflow-hidden hover:shadow-lg transition-shadow">
      <img className="w-full h-48 object-cover" src={imageUrl} alt={title} />
      <div className="p-6">
        <h2 className="text-xl font-bold text-gray-900 mb-2">{title}</h2>
        <p className="text-gray-600 text-sm leading-relaxed">{description}</p>
        <button className="mt-4 w-full bg-indigo-600 text-white py-2 rounded-lg 
                           font-medium hover:bg-indigo-700 transition-colors">
          了解更多
        </button>
      </div>
    </div>
  )
}
```

---

## @apply 指令：复用样式

当相同的类名组合多次使用时，可以用 `@apply` 提取：

```css
/* styles.css */
.btn-primary {
  @apply bg-indigo-600 text-white px-4 py-2 rounded-lg font-medium
         hover:bg-indigo-700 transition-colors;
}
```

```html
<button class="btn-primary">提交</button>
```

---

## 配置文件

```javascript
// tailwind.config.js
module.exports = {
  content: ['./src/**/*.{js,jsx,ts,tsx}'],  // 扫描哪些文件
  theme: {
    extend: {
      colors: {
        brand: '#6366f1'  // 添加自定义颜色
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif']  // 自定义字体
      }
    }
  }
}
```

---

> **下一节**：[2.10 前端构建工具：Webpack 与 Vite](./10-前端构建工具.md)
