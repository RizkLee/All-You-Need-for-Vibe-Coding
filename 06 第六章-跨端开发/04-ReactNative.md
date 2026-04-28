# 6.4 React Native——JS 写原生应用

> React Native 让 JavaScript/React 开发者能用熟悉的语言构建真正的原生 App。

---

## React Native 的核心思路

**React Native**（RN）由 Facebook 于 2015 年开源。它的思路与 Flutter 完全不同：

- **Flutter**：自己画界面（不用平台原生组件）
- **React Native**：调用平台原生组件（`<View>` 对应 iOS 的 `UIView` 和 Android 的 `View`）

结果是：React Native 的应用在每个平台上使用的是**真正的原生 UI 组件**，视觉风格更贴近系统，用户感知到的是"这看起来像 iOS App"或"这看起来像 Android App"。

---

## React Native 与 React 的关系

如果你已经会 React，学 React Native 的成本非常低：

| | React（Web）| React Native |
|--|------------|-------------|
| 语法 | JSX | JSX（相同）|
| 组件思路 | 函数组件 + Hooks | 函数组件 + Hooks（相同）|
| 状态管理 | useState, Redux, Zustand | 完全相同，复用 |
| 基础标签 | `<div>`, `<p>`, `<img>` | `<View>`, `<Text>`, `<Image>` |
| 样式 | CSS | StyleSheet（类 CSS，但用 JS 写）|
| 路由 | React Router | React Navigation |
| 平台 | 浏览器 | iOS, Android |

核心逻辑、状态管理、API 请求的代码几乎可以直接复用，只有 UI 部分（原生组件替代 HTML 标签）和样式系统有差异。

---

## Expo：React Native 的最佳起点

直接用 React Native CLI 搭建项目需要配置 iOS 和 Android 的原生开发环境（Xcode、Android Studio），步骤繁琐。

**Expo** 是一个构建在 React Native 之上的工具链和平台，极大降低了入门门槛：

- **零原生环境**：用 Expo Go App 在真机上扫码预览，不需要 Xcode 或 Android Studio
- **内置常用功能**：相机、地图、推送通知、文件系统等开箱即用
- **OTA 更新**：不需要发版就能推送 JS 层的更新
- **EAS Build**：云端构建 iOS/Android 包，不需要 Mac

对于新项目，**强烈推荐从 Expo 开始**，只有在需要深度定制原生模块时才考虑 Bare Workflow（接近原生 RN）。

---

## React Native vs Flutter：如何选择

| 考虑因素 | React Native | Flutter |
|----------|-------------|---------|
| 开发语言 | JavaScript/TypeScript | Dart |
| 已有 React 经验 | ✅ 直接上手 | 需要学 Dart |
| 与 Web 代码共享 | 高（逻辑层）| 低（不同语言）|
| UI 一致性 | 平台原生风格 | 跨平台完全一致 |
| 桌面支持 | 有限 | ✅ 更完整 |
| 动画性能 | 好 | ✅ 更好 |
| 生态规模 | 大（npm 可用）| 中（pub.dev）|
| 国内大厂支持 | Meta（Facebook）| Google |

**如果你偏向 Web 技术栈**（已经懂 React/TS），React Native 是更自然的选择。  
**如果你要做 App + 桌面一体化**，Flutter 的覆盖面更广。  
**如果团队两者都不熟悉**，Flutter 因为语言一致性（Dart）、工具链成熟度，近年来热度更高。

---

## React Native 的新架构

React Native 原来的架构有一个"桥（Bridge）"——JS 线程和原生线程通过桥异步通信，在某些场景下会出现卡顿。

**新架构（JSI + Fabric + TurboModules）** 从 RN 0.71 开始稳定，大幅改善了这个问题：
- 去掉了串行化的 Bridge，JS 可以直接调用原生代码
- 动画和交互响应更流畅
- 进一步缩小了与 Flutter 的性能差距

---

> **下一节**：[6.5 Electron——跨平台桌面应用](./05-Electron.md)
