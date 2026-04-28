# 6.2 Flutter——一套代码，多端运行

> Flutter 是 Google 开发的跨端 UI 框架，使用 Dart 语言，以一致的 UI 体验和高性能著称。

---

## Flutter 是什么

**Flutter** 是 Google 于 2018 年正式发布的开源 UI 框架。它的目标是让开发者用一套代码库同时构建 iOS、Android、Web、Windows、macOS、Linux 应用。

Flutter 的核心选择是**自带渲染引擎**：它不使用平台的原生 UI 组件，而是用自己的引擎（Skia，新版本用 Impeller）在屏幕的画布上直接绘制每一个像素。这就像游戏引擎的方式——无论在哪个平台，界面都完全一样。

---

## Flutter 的技术特点

**Dart 语言**  
Flutter 使用 Google 自研的 **Dart** 语言。Dart 是强类型、面向对象的语言，语法上与 JavaScript、Java、Kotlin 有相似之处，上手不难。

Dart 的一个关键特性：**AOT（Ahead-of-Time）编译** + **JIT（Just-in-Time）编译**。  
- 开发时用 JIT，支持热重载（修改代码不重启 App 就能看到效果）  
- 发布时用 AOT 编译成原生代码，性能接近原生

**Widget 体系**  
Flutter 的 UI 全部由 **Widget** 构建（类似 React 的组件）。一切皆 Widget：文本、按钮、布局、动画、甚至内边距。

```
一个登录页面的 Widget 树：
Scaffold
├── AppBar（顶部导航栏）
├── Column（垂直布局）
│   ├── TextField（用户名输入框）
│   ├── TextField（密码输入框）
│   └── ElevatedButton（登录按钮）
└── BottomNavigationBar（底部导航）
```

**有状态 vs 无状态 Widget**  
与 React 的 class/function 组件概念类似：  
- `StatelessWidget`：没有内部状态，只由外部数据决定外观（类似 React 函数组件）  
- `StatefulWidget`：有自己的内部状态，状态变化触发重绘（类似带 `useState` 的 React 组件）

---

## Flutter 的渲染优势

Flutter 的自绘渲染带来了几个独特优势：

**1. 跨平台 UI 完全一致**  
由于不用平台原生组件，在 iOS 和 Android 上看到的界面像素级别完全相同。这对于想要统一品牌 UI 的产品非常有价值。相比之下，React Native 用原生组件，在两个平台上可能有细微差异。

**2. 性能稳定**  
目标是 60fps（支持 120fps 屏幕），动画流畅，因为渲染完全在自己的引擎控制下。

**3. 桌面和 Web 支持更好**  
正因为不依赖平台原生组件，Flutter 在桌面端和 Web 端的支持比 React Native 更完整。

---

## Flutter 与 Web 技术的边界（回答你的问题）

**你用了 Flutter，还需要 React、Vue、Tailwind CSS 吗？**

**对于 App 界面本身：不需要**  
Flutter 有自己完整的 UI 体系（Material Design 和 Cupertino 两套风格组件库），不使用 HTML、CSS、JavaScript。Tailwind CSS 和 React 是 Web 的工具，在 Flutter 里完全用不上。

**对于后端和 API：需要**  
Flutter App 需要和后端服务器通信。你仍然需要：
- 一个后端服务（Node.js + Express / Python + FastAPI / 等）
- API 设计（REST / GraphQL）
- 数据库
- 身份认证

**对于 Web 版本（如果你用 Flutter Web）：不确定**  
Flutter 可以编译到 Web（生成 HTML + JS），但产物是 Flutter 自己渲染的 Canvas，SEO 很差，页面加载相对慢。大多数认真做 Web 的团队不会选择 Flutter Web 作为 Web 端方案，而是单独用 Next.js/Vue 做 Web。

**典型的"Flutter + Web 并存"架构**：
```
App（iOS + Android）→ Flutter
Web 版本            → Next.js / Vue
后端 API            → 共享的 Node.js 或 Python 后端
```

---

## Flutter 的状态管理

Flutter 的状态管理方案比 React 更分散，常见选择：

| 方案 | 特点 |
|------|------|
| `setState` | 最基础，只适合简单场景 |
| **Provider** | 官方推荐，简单轻量 |
| **Riverpod** | Provider 的进化版，类型安全，现在更主流 |
| **Bloc / Cubit** | 基于流（Stream），架构清晰，适合大型项目 |
| **GetX** | 全功能，简单但争议大 |

---

## Flutter 的本地能力

访问手机硬件和系统能力通过**插件（Package）**实现，发布在 [pub.dev](https://pub.dev)：

| 功能 | 常用包 |
|------|--------|
| 网络请求 | `http`, `dio` |
| 本地数据库 | `sqflite`, `Hive`, `Isar` |
| 状态管理 | `riverpod`, `bloc` |
| 摄像头 | `camera` |
| 地图 | `google_maps_flutter` |
| 推送通知 | `firebase_messaging` |
| 支付 | `pay` |
| 文件选择 | `file_picker` |

---

## Flutter 的劣势

**包大小**：Flutter App 比等效的原生 App 体积大（Flutter 引擎本身约 15-20MB）。  
**Web 表现欠佳**：Flutter Web 的 SEO、首屏性能、无障碍访问都有缺陷。  
**平台特定功能**：如某些 iOS/Android 的特定系统 API，需要写原生插件（涉及 Swift/Kotlin）。  
**Dart 生态**：相比 JavaScript/Python，Dart 的第三方库数量少得多。

---

## 适合用 Flutter 的场景

```
✓ App（iOS + Android）是核心产品，Web 版本次要
✓ 团队只想维护一套 UI 代码
✓ 对 App 动画和 UI 一致性要求高
✓ 桌面端需求（Windows / macOS 应用）
✓ 中小型团队，希望一人同时覆盖 iOS 和 Android

✗ Web 是主要平台，SEO 很重要
✗ 需要深度集成平台特有功能
✗ 已有独立的 iOS/Android 团队，性能要求极致
```

---

> **下一节**：[6.3 Flutter 与后端的配合](./03-Flutter与后端配合.md)
