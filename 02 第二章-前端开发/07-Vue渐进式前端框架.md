# 2.7 Vue——渐进式前端框架

> Vue 以易学著称，设计上在灵活性和约定之间找到了平衡点。

---

## Vue 的定位

Vue 自称"渐进式框架"——你可以只在页面的某个部分引入 Vue，也可以用 Vue 构建完整的单页应用，由你决定引入多少。

对比 React 和 Angular：

| | React | Vue | Angular |
|--|-------|-----|---------|
| 定位 | UI 库 | 渐进式框架 | 完整框架 |
| 学习曲线 | 中 | 低 | 高 |
| 模板语法 | JSX（JS中写HTML） | 单文件组件 | HTML模板 |
| 官方生态 | 无路由/状态管理 | Vue Router + Pinia | 全套内置 |
| 中文社区 | 一般 | 非常好（作者是中国人） |一般 |

---

## 单文件组件（SFC）

Vue 的独特设计：把 HTML、JavaScript、CSS 写在同一个 `.vue` 文件中：

```vue
<!-- ProductCard.vue -->

<template>
  <!-- HTML 结构 -->
  <div class="card">
    <h2>{{ product.name }}</h2>
    <p>¥{{ product.price }}</p>
    <button @click="addToCart">加入购物车</button>
  </div>
</template>

<script setup>
// JavaScript 逻辑（Composition API）
import { ref } from 'vue'

const props = defineProps({
  product: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['add-to-cart'])

function addToCart() {
  emit('add-to-cart', props.product)
}
</script>

<style scoped>
/* CSS 样式（scoped: 只作用于当前组件）*/
.card {
  border: 1px solid #eee;
  padding: 16px;
  border-radius: 8px;
}
</style>
```

---

## Vue 3 的两种 API 风格

### Options API（Vue 2 风格，仍然支持）

```vue
<script>
export default {
  data() {
    return {
      count: 0,
      message: '你好'
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  computed: {
    doubleCount() {
      return this.count * 2
    }
  },
  mounted() {
    console.log('组件已挂载')
  }
}
</script>
```

### Composition API（Vue 3 推荐）

```vue
<script setup>
import { ref, computed, onMounted } from 'vue'

const count = ref(0)
const message = ref('你好')

const doubleCount = computed(() => count.value * 2)

function increment() {
  count.value++  // ref 的值通过 .value 访问（在模板中自动解包）
}

onMounted(() => {
  console.log('组件已挂载')
})
</script>
```

**`<script setup>`** 是 Vue 3 的语法糖，代码更简洁，这也是现在的主流写法。

---

## Vue 的模板语法

```vue
<template>
  <!-- 数据绑定 -->
  <p>{{ message }}</p>
  
  <!-- 条件渲染 -->
  <p v-if="isLoggedIn">欢迎回来！</p>
  <p v-else>请先登录</p>
  
  <!-- 列表渲染 -->
  <ul>
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
  
  <!-- 事件绑定（@ 是 v-on: 的简写）-->
  <button @click="handleClick">点击</button>
  
  <!-- 属性绑定（: 是 v-bind: 的简写）-->
  <img :src="imageUrl" :alt="imageAlt">
  
  <!-- 双向绑定（表单）-->
  <input v-model="username" placeholder="输入用户名">
  <p>你输入了：{{ username }}</p>
</template>
```

`v-model` 是 Vue 的特色——它在表单元素和数据之间建立双向绑定，输入框值变了数据自动更新，数据变了输入框也自动更新。React 没有这个，需要手动实现。

---

## 响应式系统

Vue 3 用 **Proxy** 实现响应式：框架精准知道哪个数据被哪个 UI 依赖，数据变化时只更新相关的 DOM，不需要全树 Diff。

```javascript
import { reactive, ref } from 'vue'

// ref: 包装基础类型
const count = ref(0)
count.value++

// reactive: 包装对象
const user = reactive({
  name: '张三',
  age: 25
})
user.name = '李四'  // 直接修改即可，Vue 会追踪到
```

---

## Vue 生态

| 工具 | 用途 | Vue 官方？ |
|------|------|----------|
| Vue Router | 页面路由管理 | ✅ 是 |
| Pinia | 状态管理 | ✅ 是（Vuex 的继任者）|
| Nuxt.js | Vue 全栈框架 | 非官方但官推 |
| Vite | 构建工具 | Vue 作者开发 |
| VueUse | 实用 Hooks 集合 | 非官方 |
| Element Plus | UI 组件库（桌面端）| 非官方 |
| Vant | UI 组件库（移动端）| 非官方 |

---

## Vue 适合什么场景

- **中国市场的项目**：Vue 在国内社区和文档资源都很丰富
- **中小型团队**：学习成本低，上手快
- **后台管理系统**：Element Plus 等组件库非常完善
- **希望统一前端技术栈**：配合 Nuxt.js 做全栈开发

---

## React vs Vue：如何选择

| 考虑因素 | 选 React | 选 Vue |
|----------|---------|--------|
| 全球就业市场 | ✅ 更多 | 一般 |
| 国内就业市场 | 好 | ✅ 也很好 |
| 学习难度 | 稍高 | ✅ 稍低 |
| 灵活性 | ✅ 更灵活 | 中等 |
| 移动端（RN） | ✅ React Native | 无原生方案 |
| 完整生态 | 需要自己选 | ✅ 官方提供 |

没有绝对好坏，两者都能完成同样的任务。

---

> **下一节**：[2.8 Angular——企业级前端框架](./08-Angular企业级前端框架.md)
