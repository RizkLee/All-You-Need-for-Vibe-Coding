# 4.3 PostgreSQL 详解

> PostgreSQL 是功能最强大的开源关系型数据库，也是现代 Web 开发的首选。

---

## 为什么选 PostgreSQL

在关系型数据库里，MySQL 和 PostgreSQL 是开源世界的两个主流选择。近年来，PostgreSQL（简称 Postgres）越来越被新项目所青睐，原因是：

- **功能更强**：支持更多 SQL 标准特性，如全文搜索、JSONB、数组、地理空间数据
- **数据完整性**：对 SQL 标准遵从度更高，数据约束更严格
- **扩展性**：支持自定义数据类型、函数、索引方法
- **并发性能**：MVCC（多版本并发控制）实现更优，读写并发冲突少
- **开源协议更宽松**：完全开源，无商业限制

---

## PostgreSQL 独特的数据类型

除了标准 SQL 类型，PostgreSQL 还提供很多独特类型：

**JSONB**：以二进制格式存储 JSON，支持索引和高效查询。这让 PostgreSQL 部分具备了文档数据库的能力。

```sql
-- JSONB 字段
ALTER TABLE users ADD COLUMN preferences JSONB;

-- 可以对 JSONB 字段的子字段建索引
CREATE INDEX ON users USING gin(preferences);

-- 查询 JSONB 中的值
SELECT * FROM users WHERE preferences->>'theme' = 'dark';
```

**数组类型**：字段可以存储一组值，避免建关联表的开销。

```sql
-- 标签字段存数组
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    title TEXT,
    tags TEXT[]  -- 字符串数组
);

-- 查询包含某个标签的文章
SELECT * FROM articles WHERE 'technology' = ANY(tags);
```

**UUID**：通用唯一标识符，比自增 ID 更适合分布式系统（不同数据库生成的 ID 不会冲突）。

**地理空间类型（PostGIS 扩展）**：存储和查询地理坐标，适合"附近的商家"这类功能。

---

## 全文搜索

PostgreSQL 内置了全文搜索能力，简单场景下不需要引入 Elasticsearch：

```sql
-- 为文章内容建全文搜索索引
CREATE INDEX articles_content_fts ON articles
USING gin(to_tsvector('chinese', content));

-- 搜索包含"数据库"的文章
SELECT title FROM articles
WHERE to_tsvector('chinese', content) @@ to_tsquery('数据库');
```

对于复杂搜索需求（模糊搜索、相关度排序、多字段搜索），通常还是需要 Elasticsearch 或 Meilisearch。

---

## 数据库设计基础

### 规范化（Normalization）

好的数据库设计应该减少数据冗余：

**差的设计**（数据冗余）：
```
订单表：
order_id | customer_name | customer_email | product_name | price
  1      |    张三       | zhang@...      | 手机          | 3999
  2      |    张三       | zhang@...      | 耳机          | 299
```
如果张三改了邮箱，要更新所有他的订单行。

**好的设计**（规范化）：
```
用户表: id | name | email
订单表: id | user_id | product_id | price
商品表: id | name | price
```

将数据分散到不同表，通过外键关联，修改用户邮箱只需改一行。

### 主键的选择

| 类型 | 例子 | 优劣 |
|------|------|------|
| 自增整数 | 1, 2, 3... | 简单、顺序、有规律；分布式下可能冲突 |
| UUID | `550e8400-...` | 全球唯一、无规律；稍大、不利于索引排序 |
| ULID | `01ARZ3NDEK...` | 时间排序 + 唯一；结合了两者优点 |

---

## 连接池

数据库连接的建立是有开销的（TCP 握手、认证等）。**连接池**（Connection Pool）预先创建一批连接，复用它们，避免每次请求都重新建连接：

```
后端应用
  → 从连接池借用一个已有连接
  → 执行 SQL 查询
  → 归还连接到连接池（不关闭）
  → 下次请求再复用
```

PostgreSQL 生态中常用 **PgBouncer** 作为连接池中间件。大多数 ORM 库（Prisma、SQLAlchemy）也内置了连接池管理。

---

## PostgreSQL vs MySQL

| 比较维度 | PostgreSQL | MySQL |
|----------|------------|-------|
| SQL 标准遵从 | 更严格 | 较宽松 |
| JSON 支持 | ✅ 原生 JSONB | 基础支持 |
| 全文搜索 | ✅ 内置 | 有限 |
| 扩展性 | 极高 | 中等 |
| 复制与集群 | 功能完整 | 成熟但配置复杂 |
| 读性能 | 高 | 高（某些场景更快）|
| 使用场景 | 复杂查询、数据完整性 | 读多写少的 Web 应用 |
| 托管服务 | AWS RDS、Supabase、Neon | AWS RDS、PlanetScale |

**新项目推荐 PostgreSQL**。MySQL 在老项目和 LAMP 栈中仍然常见。

---

## 托管 PostgreSQL 服务

不想自己运维数据库？可以用云服务：

| 服务 | 特点 |
|------|------|
| **Supabase** | 开源 Firebase 替代，PostgreSQL + 实时订阅 + 认证 |
| **Neon** | Serverless PostgreSQL，按用量计费，有免费层 |
| **AWS RDS** | 企业级，完全托管，价格较高 |
| **PlanetScale** | MySQL 的 Serverless 服务（不支持外键，但很流行）|
| **Railway** | 简单部署，开发者友好 |

---

> **下一节**：[4.4 MySQL 简介](./04-MySQL简介.md)
