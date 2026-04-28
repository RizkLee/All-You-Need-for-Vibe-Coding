# 6.5 Electron——跨平台桌面应用

> Electron 让 Web 开发者用 HTML/CSS/JavaScript 构建可以在 Windows、macOS、Linux 上运行的桌面应用。

---

## Electron 是什么

**Electron** 由 GitHub 于 2013 年开发（最初叫 Atom Shell，为 Atom 编辑器所建），核心思路非常直接：

```
Electron = Chromium（浏览器内核）+ Node.js

你的 Web 应用（React/Vue/HTML）
     ↓ 运行在 Chromium 里
    变成桌面应用窗口
     ↓ 通过 Node.js
    可以访问本地文件系统、操作系统 API
```

换句话说：Electron 应用本质上是一个打包进桌面应用里的浏览器，运行你的 Web 代码。

---

## Electron 的代表作

Electron 被大量成熟的商业产品使用，证明了它的可行性：

| 产品 | 类型 |
|------|------|
| **VS Code** | 代码编辑器 |
| **Slack** | 团队沟通 |
| **Discord** | 游戏社区通信 |
| **Figma（桌面版）** | 设计工具 |
| **Notion（桌面版）** | 笔记工具 |
| **Postman** | API 测试工具 |
| **1Password** | 密码管理 |

---

## Electron 的进程架构

Electron 有两种进程：

**主进程（Main Process）**：
- 用 Node.js 运行
- 控制窗口的创建/销毁
- 访问操作系统 API（文件系统、托盘图标、系统菜单）
- 一个应用只有一个主进程

**渲染进程（Renderer Process）**：
- 在 Chromium 中运行，每个窗口一个
- 运行 Web 前端代码（HTML/CSS/JS/React）
- 默认不能访问 Node.js API（出于安全考虑）

两者之间通过 **IPC（Inter-Process Communication，进程间通信）** 传递消息。

---

## Electron 的优势

- **对 Web 开发者零门槛**：你已经会 React/Vue，就能做桌面应用
- **跨平台**：一套代码，打包成 Windows .exe、macOS .dmg、Linux .AppImage
- **UI 库可复用**：所有 Web UI 组件可以直接用
- **快速迭代**：不需要重新学习桌面开发 SDK

---

## Electron 的劣势

**体积大**：Electron 包含完整的 Chromium 内核，最小的 Electron 应用也有 50-100MB+。而原生桌面应用可能只有几 MB。

**内存占用高**：Chromium 本身就是内存"大户"，Electron 应用通常比等效原生应用消耗更多内存（这也是 VS Code 被调侃吃内存的原因）。

**性能**：对于复杂的图形操作（如 Figma），需要大量优化。

**安全性**：历史上有不少 Electron 应用因为不恰当地开放了 Node.js API 给渲染进程而有安全漏洞。现代 Electron 有更严格的安全默认值。

---

## Tauri：Electron 的轻量替代

**Tauri** 是近年出现的 Electron 替代品，用 Rust 开发，解决了 Electron 的主要痛点：

- **体积小**：使用系统自带的 WebView（macOS 的 WebKit、Windows 的 WebView2），不打包 Chromium，应用体积只有几 MB
- **内存占用低**：Rust 后端极其轻量
- **安全性好**：Rust 的内存安全特性
- **同样用 Web 前端**：React/Vue/Svelte 都可以用

代价：
- Tauri 的 Rust 后端学习曲线稍高（相比 Node.js）
- 依赖系统 WebView，不同系统上渲染可能有差异
- 生态比 Electron 小，但增长很快

---

## Wails（Go + Electron 思路）

如果你的后端是 Go，**Wails** 是 Go 生态的 Tauri 替代——Go 后端 + Web 前端，打包成桌面应用。

---

## 什么时候选 Electron/Tauri

```
✓ Web 开发者想做桌面应用，不想学原生桌面技术
✓ 需要快速交付跨平台桌面工具
✓ 应用逻辑已经是 Web 版本，想快速出桌面版
✓ 需要访问本地文件系统

对体积/内存极度敏感 → 考虑 Tauri
团队熟悉 Node.js → Electron
团队熟悉 Rust/Go → Tauri/Wails
需要极致原生性能 → 用各平台原生技术
```

---

> **下一节**：[6.6 微信小程序与跨端小程序框架](./06-小程序开发.md)
