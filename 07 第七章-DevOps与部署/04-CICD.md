# 7.4 CI/CD——持续集成与持续部署

> CI/CD 让代码从提交到上线的过程自动化，减少人为错误，加快交付速度。

---

## 没有 CI/CD 的部署流程

传统手动部署：

```
开发者写完代码
  → 本地测试（可能跳过）
  → SSH 登录服务器
  → git pull 更新代码
  → npm install 安装依赖
  → 重启服务
  → 祈祷没有出错
```

问题：
- 容易出错（忘记某个步骤）
- 部署窗口依赖人工操作
- 团队多人时，不清楚是谁部署了什么
- 没有自动回滚机制

---

## CI/CD 是什么

**CI（Continuous Integration，持续集成）**：每次有代码提交，自动运行测试，确保代码没有破坏现有功能。

**CD（Continuous Delivery/Deployment，持续交付/部署）**：通过 CI 后，自动（或半自动）部署到环境。

```
开发者 git push
  ↓
CI/CD 自动触发
  ↓
  ├── 安装依赖
  ├── 运行测试（单元测试、集成测试）
  ├── 代码格式检查（ESLint、Prettier）
  ├── 构建应用（npm run build）
  └── 测试通过？
       ├── 是 → 自动部署到服务器
       └── 否 → 发通知给开发者，不部署
```

---

## GitHub Actions

**GitHub Actions** 是目前最流行的 CI/CD 工具，直接集成在 GitHub 里：

你在代码仓库的 `.github/workflows/` 目录下写 YAML 文件，定义自动化流程：

```
.github/workflows/deploy.yml 的作用（无需记住具体语法）：

触发条件：当向 main 分支推送代码时
执行步骤：
  1. 检出代码
  2. 安装 Node.js
  3. 安装依赖（npm install）
  4. 运行测试（npm test）
  5. 构建（npm run build）
  6. 部署到 Vercel / AWS / 你的服务器
```

GitHub Actions 有大量社区共享的**Action**（可复用的步骤）：设置 Node.js 环境、推送 Docker 镜像、发送 Slack 通知……不需要从头写。

---

## Vercel 的自动部署：最简单的 CI/CD

如果你用 Vercel 部署前端/Next.js，不需要手动配置 CI/CD：

```
连接 GitHub 仓库
  ↓
Vercel 自动监听所有 Push
  ↓
每次 push 到 main 分支 → 自动部署到生产环境
每次 push 到其他分支 → 生成预览 URL（Preview Deployment）
                        （PR 里直接看到预览链接）
```

这让每个 PR 都能看到视觉预览，代码 Review 时可以直接点链接看效果。

---

## CI/CD 的核心价值

**快速反馈**：代码推上去几分钟内就知道有没有问题，而不是等到部署时才发现。

**一致性**：每次部署步骤完全一致，消除手动操作的随机错误。

**可追溯**：每次部署都有记录（谁触发的、什么时间、哪次提交），出问题容易追查。

**分支隔离**：功能分支有独立的预览环境，不同功能的开发互不干扰。

---

## 常见的 CI/CD 工具

| 工具 | 特点 |
|------|------|
| **GitHub Actions** | 免费套餐丰富，GitHub 深度集成 |
| **GitLab CI/CD** | GitLab 自带，配置在 `.gitlab-ci.yml` |
| **Vercel** | 前端自动部署，几乎零配置 |
| **Netlify** | 类似 Vercel |
| **CircleCI** | 老牌 CI/CD，功能强大 |
| **Jenkins** | 老牌，自托管，配置复杂，企业用 |
| **Drone CI** | 开源，基于 Docker |

---

## 部署策略

生产环境的部署方式也有讲究，不同策略有不同的风险和成本：

**蓝绿部署（Blue-Green Deployment）**：同时维护两套生产环境（蓝/绿），部署时把流量从旧环境切换到新环境，出问题可以立即切回。

**滚动部署（Rolling Update）**：逐渐把旧版本容器替换成新版本容器，保证始终有容器在服务，不停机。

**金丝雀发布（Canary Release）**：先把 5% 的流量引导到新版本，观察没问题再逐步增加比例，最终完全切换。

---

> **下一节**：[7.5 域名、DNS 与 HTTPS](./05-域名DNS与HTTPS.md)
