# 4.4 MySQL 简介

> MySQL 是全球使用最广的开源关系型数据库，支撑着无数网站和应用。

---

## MySQL 的历史地位

MySQL 诞生于 1995 年，是 **LAMP 栈**（Linux + Apache + MySQL + PHP）的核心组件，驱动了早期互联网的大量应用——WordPress、Drupal 等 CMS 系统都以 MySQL 为默认数据库。

Facebook、YouTube、Twitter 早期也都用 MySQL，并通过各种工程手段将其扩展到亿级数据。

2010 年，MySQL 被 Oracle 收购，部分开发者分叉出了 **MariaDB**（完全兼容 MySQL），以保持完全开源。

---

## MySQL 与 PostgreSQL 的核心差异

大多数情况下两者可以互换，主要差异在于：

**事务和锁**：  
MySQL 的 InnoDB 存储引擎也支持 ACID 事务，但在某些边缘情况下的行为与 PostgreSQL 不同（如对 NULL 的处理、GROUP BY 的严格程度）。

**复制（Replication）**：  
MySQL 的主从复制是业界最成熟的方案之一，国内大型互联网公司有丰富的运维经验。

**全文搜索和 JSON**：  
MySQL 的全文搜索和 JSON 支持功能上不如 PostgreSQL 完善，但对于基本需求已经够用。

**存储引擎**：  
MySQL 支持多种存储引擎，最常用的是 InnoDB（支持事务）。PostgreSQL 没有这个概念，只有一种引擎。

---

## 什么时候选 MySQL

- 团队有 MySQL 运维经验
- 已有项目在用 MySQL，迁移成本不值得
- 使用 WordPress、Laravel 等以 MySQL 为默认的技术栈
- 需要兼容特定的云服务或旧系统

**对于新项目**：如果没有特殊原因，PostgreSQL 通常是更好的选择。MySQL 的选择更多是历史原因或生态兼容，而非技术上的优势。

---

## SQLite：轻量级的本地数据库

顺带介绍 **SQLite**——它和 MySQL、PostgreSQL 不同，不是一个独立的服务器进程，而是一个**嵌入式数据库**，整个数据库就是一个文件。

SQLite 的使用场景：
- **移动端应用**（iOS、Android、Flutter）：本地存储用户数据
- **桌面应用**（Electron）：本地数据持久化
- **开发和测试**：不需要安装数据库服务器，快速上手
- **小型工具**：数据量不大、不需要网络访问的应用

```
SQLite 的特点：
✓ 零配置，不需要安装服务器
✓ 单文件，便于携带和备份
✓ 支持完整的 SQL
✗ 不支持多客户端并发写入
✗ 不适合 Web 服务器场景（并发量一大就成瓶颈）
```

Flutter 应用中经常用 `sqflite` 包来使用 SQLite 进行本地数据存储。

---

> **下一节**：[4.5 MongoDB——文档型数据库](./05-MongoDB.md)
