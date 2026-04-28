# 7.5 域名、DNS 与 HTTPS

> 有了服务器和部署，还需要一个域名和 HTTPS，才能让用户通过正常的网址访问你的应用。

---

## 域名是怎么工作的

当你输入 `https://www.example.com`：

```
1. 浏览器问本地 DNS 缓存："example.com 的 IP 是多少？"
   → 没有缓存

2. 问操作系统的 DNS 解析器（通常是路由器）
   → 没有

3. 问 ISP（运营商）的 DNS 服务器
   → 没有，向上查询

4. 查询根域名服务器 → .com 域名服务器 → example.com 的权威 DNS 服务器
   → 返回："example.com 对应 93.184.216.34"

5. 浏览器向 93.184.216.34 发起 HTTPS 连接
```

整个过程通常在几毫秒内完成（有缓存则更快）。

---

## 域名注册

购买域名的地方（**域名注册商**）：

| 注册商 | 特点 |
|--------|------|
| **Namecheap** | 价格便宜，界面友好，国际首选 |
| **阿里云万网** | 国内注册备案方便 |
| **腾讯云 DNSPod** | 国内，与腾讯云生态集成好 |
| **Cloudflare Registrar** | 成本价卖域名（不赚差价），配套 DNS 功能强 |
| **GoDaddy** | 老牌，但价格不透明 |

域名后缀（TLD）的选择：
- `.com`：最通用，商业项目首选
- `.cn`：国内项目，需要实名注册
- `.io`：科技项目常用（贵）
- `.app`, `.dev`：开发者友好（Google 管辖）
- `.me`, `.co`：个人品牌和创业项目

**国内项目注意**：在中国大陆服务器上运行的网站，域名必须进行 **ICP 备案**（工业和信息化部），未备案网站可能被封禁。

---

## DNS 配置

注册了域名后，需要配置 DNS 记录，告诉 DNS 系统这个域名指向哪里：

| 记录类型 | 用途 | 示例 |
|----------|------|------|
| **A 记录** | 域名 → IPv4 地址 | `example.com → 93.184.216.34` |
| **AAAA 记录** | 域名 → IPv6 地址 | 同上，用于 IPv6 |
| **CNAME 记录** | 域名 → 另一个域名（别名）| `www.example.com → example.com` |
| **MX 记录** | 邮件服务器 | 收邮件时用 |
| **TXT 记录** | 文本信息 | 域名验证、SPF、DKIM |
| **NS 记录** | 哪台服务器管理这个域名的 DNS | 更换 DNS 服务商时用 |

**实际操作示例**——把域名指向 Vercel 部署的应用：

1. 在 Vercel 项目设置里添加自定义域名
2. Vercel 提供一个 CNAME 值（如 `cname.vercel-dns.com`）
3. 在你的域名注册商处添加 CNAME 记录：`www → cname.vercel-dns.com`
4. 等待 DNS 传播（几分钟到几小时）

---

## Cloudflare：强大的 DNS 托管

**Cloudflare** 除了是 CDN 服务商，还是最受欢迎的 DNS 托管服务。把域名的 DNS 解析交给 Cloudflare 的好处：
- DNS 解析速度快（全球节点）
- 免费 CDN（静态资源加速）
- DDoS 防护
- 免费 SSL/HTTPS
- 灵活的 Proxy 和安全规则

使用方式：在域名注册商那里把 NS 记录改为 Cloudflare 提供的地址，之后所有 DNS 配置在 Cloudflare 控制台操作。

---

## HTTPS 与 SSL 证书

**HTTPS** 需要 **SSL 证书**，证书由**证书颁发机构（CA）**签发，证明你确实是这个域名的所有者。

**Let's Encrypt**（免费）：现代 Web 应用的标准选择，证书有效期 90 天，可以配合 Certbot 自动续期。几乎所有 PaaS 平台（Vercel、Railway、Render）都自动配置好了 HTTPS。

**获取证书的几种方式**：

```
方式 1：平台自动处理（推荐）
  → 部署到 Vercel/Netlify 等平台，HTTPS 自动开启

方式 2：Cloudflare
  → 开启 Cloudflare Proxy，自动提供 HTTPS（SSL在边缘终止）

方式 3：自己用 Certbot
  → 适合自管服务器，Certbot 自动申请和续期 Let's Encrypt 证书

方式 4：购买商业证书
  → 适合对 OV/EV 证书有需求的企业（地址栏显示公司名）
```

---

## 子域名的用途

一个域名可以有多个子域名，承担不同职责：

```
example.com          → 主网站
www.example.com      → 同上（www 约定）
api.example.com      → 后端 API 服务
app.example.com      → Web 应用
admin.example.com    → 后台管理
docs.example.com     → 文档站
blog.example.com     → 博客
staging.example.com  → 预发布环境
```

---

> **第七章完结**。下一章：[第八章 技术选型与架构设计](../第八章-技术选型与架构/01-如何进行技术选型.md)
