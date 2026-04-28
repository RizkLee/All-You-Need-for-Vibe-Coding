# 7.3 Docker——容器化部署

> Docker 解决了"在我电脑上能跑"的问题，让应用在任何环境下都能一致地运行。

---

## 没有 Docker 的痛苦

一个 Node.js 应用，在开发者 A 的电脑上运行正常，部署到服务器上却报错。原因可能是：
- 服务器的 Node.js 版本不同（开发者用 v20，服务器是 v16）
- 某个依赖在 Linux 上的行为与 macOS 不同
- 环境变量没有正确配置
- 系统库版本不匹配

**Docker** 的解决方案：**把应用和它所需的所有运行环境打包在一起**，形成一个"镜像"。无论在哪台机器上运行这个镜像，环境完全一致。

---

## 容器 vs 虚拟机

**虚拟机（VM）**：在一台物理机上模拟多台完整的计算机，包括完整的操作系统，体积大（几 GB），启动慢（几分钟）。

**容器（Container）**：共享宿主机的操作系统内核，只打包应用和其依赖，体积小（几 MB~几百 MB），启动极快（秒级）。

```
虚拟机：
┌────────────────────────────────────────┐
│            物理服务器                   │
│  ┌─────────────┐  ┌─────────────────┐  │
│  │    VM 1     │  │     VM 2        │  │
│  │ ┌─────────┐ │  │ ┌─────────┐    │  │
│  │ │ 完整 OS │ │  │ │ 完整 OS │    │  │
│  │ │  应用   │ │  │ │  应用   │    │  │
│  │ └─────────┘ │  │ └─────────┘    │  │
│  └─────────────┘  └─────────────────┘  │
└────────────────────────────────────────┘

容器：
┌────────────────────────────────────────┐
│            物理服务器                   │
│            宿主机 OS（共享）            │
│  ┌────────────┐  ┌────────────────┐   │
│  │  容器 1    │  │    容器 2      │   │
│  │  应用+依赖 │  │   应用+依赖    │   │
│  └────────────┘  └────────────────┘   │
└────────────────────────────────────────┘
```

---

## Docker 的核心概念

**镜像（Image）**：静态的模板，包含应用代码和运行环境，类似可执行文件或安装包。

**容器（Container）**：运行中的镜像实例，类似正在运行的进程。可以从同一个镜像启动多个容器。

**Dockerfile**：定义如何构建镜像的文本文件，类似"食谱"。

**Docker Hub / Docker Registry**：存储和分发镜像的仓库（类似 npm Registry）。

```
开发者编写 Dockerfile
  ↓ docker build
生成 Image（镜像）
  ↓ docker push
推送到 Registry（镜像仓库）
  ↓ 服务器 docker pull
拉取镜像
  ↓ docker run
运行容器
```

---

## Dockerfile 示例（了解结构即可）

```dockerfile
# 基础镜像：使用官方 Node.js 20 镜像
FROM node:20-alpine

# 设置工作目录
WORKDIR /app

# 先复制 package.json，安装依赖（利用缓存）
COPY package*.json ./
RUN npm install --production

# 再复制源代码
COPY . .

# 声明应用端口
EXPOSE 3000

# 启动命令
CMD ["node", "src/index.js"]
```

构建这个镜像后，在任何有 Docker 的机器上运行，都会得到完全相同的环境：Node.js 20、安装好依赖、运行 index.js。

---

## Docker Compose：多服务编排

实际项目往往需要多个服务同时运行：后端 + 数据库 + Redis + ...

**Docker Compose** 用一个 YAML 文件定义和启动多个容器：

```yaml
# docker-compose.yml 的结构（示意）
services:
  backend:
    build: .
    ports: 3000:3000
    depends_on: [db, redis]
    environment:
      DATABASE_URL: postgresql://...

  db:
    image: postgres:16
    volumes: postgres-data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
```

运行 `docker compose up`，三个服务同时启动，形成一个完整的本地开发环境。这解决了"新成员加入项目需要手动安装 PostgreSQL、Redis、配置环境"的问题。

---

## Kubernetes：容器编排

当容器数量多了（几十个服务，每个跑多个实例），需要一个系统来管理它们：

**Kubernetes（K8s）** 是容器编排的事实标准，能做到：
- 自动调度（把容器分配到合适的服务器上）
- 自动扩缩容（流量大时自动增加容器实例）
- 健康检查和自动重启（容器挂了自动重拉）
- 滚动更新（更新时不停服）
- 服务发现（容器间互相找到彼此）

K8s 的学习成本较高，通常是中大型公司的 DevOps 工程师的专属领域。个人开发者和小团队大多用 PaaS 平台（Railway、Fly.io）代替。

---

## 你需要了解多少 Docker

对于 Vibe Coder：

**需要知道**：
- Docker 的作用（环境一致性、便于部署）
- 能读懂 Dockerfile 的基本结构
- `docker compose up` 启动本地开发环境
- 概念：镜像、容器、Registry

**不需要深入**：
- K8s 的具体配置
- Docker 网络的底层原理
- 自建 Docker Registry

---

> **下一节**：[7.4 CI/CD——持续集成与持续部署](./04-CICD.md)
