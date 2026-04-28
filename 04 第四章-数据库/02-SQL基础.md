# 4.2 SQL 基础

> SQL 是操作关系型数据库的标准语言，几乎所有关系型数据库都支持它。

---

## SQL 是什么

**SQL**（Structured Query Language，结构化查询语言）是关系型数据库的通用语言。无论是 PostgreSQL、MySQL 还是 SQLite，核心 SQL 语法几乎相同，只有少量方言差异。

SQL 包含几类语句：

| 类型 | 全称 | 用途 | 常用语句 |
|------|------|------|----------|
| **DDL** | 数据定义语言 | 管理表结构 | CREATE, ALTER, DROP |
| **DML** | 数据操纵语言 | 增删改数据 | INSERT, UPDATE, DELETE |
| **DQL** | 数据查询语言 | 查询数据 | SELECT |
| **DCL** | 数据控制语言 | 权限管理 | GRANT, REVOKE |

---

## 表结构定义（DDL）

创建表时，需要定义每列的名字、数据类型和约束：

```sql
CREATE TABLE users (
    id          SERIAL PRIMARY KEY,     -- 自增主键
    name        VARCHAR(100) NOT NULL,  -- 最长100字符，不能为空
    email       VARCHAR(255) UNIQUE NOT NULL, -- 唯一且不能为空
    age         INTEGER CHECK (age >= 0),     -- 整数，且必须 >= 0
    created_at  TIMESTAMP DEFAULT NOW()       -- 默认当前时间
);
```

**常用数据类型**：

| 类型 | 用途 |
|------|------|
| `INTEGER / INT` | 整数 |
| `BIGINT` | 大整数（适合 ID、时间戳）|
| `VARCHAR(n)` | 变长字符串，最长 n 字符 |
| `TEXT` | 不限长度的文本 |
| `BOOLEAN` | 布尔值（true/false）|
| `DECIMAL(p,s)` | 精确小数（金额必用）|
| `TIMESTAMP` | 日期时间 |
| `JSONB` | JSON 数据（PostgreSQL 特有，支持索引）|

**常用约束**：

| 约束 | 含义 |
|------|------|
| `PRIMARY KEY` | 主键，唯一标识每行 |
| `NOT NULL` | 不能为空 |
| `UNIQUE` | 值不能重复 |
| `DEFAULT value` | 插入时的默认值 |
| `FOREIGN KEY` | 外键，关联到另一张表 |
| `CHECK (条件)` | 自定义验证规则 |

---

## 数据操作（DML）

### 插入数据
```sql
INSERT INTO users (name, email, age)
VALUES ('张三', 'zhang@example.com', 25);
```

### 更新数据
```sql
UPDATE users
SET name = '张三（已更新）', age = 26
WHERE id = 1;
-- 注意：WHERE 不写的话会更新所有行！
```

### 删除数据
```sql
DELETE FROM users
WHERE id = 1;
-- 同样，WHERE 不写会删除所有行！
```

---

## 查询数据（DQL）

### 基本查询

```sql
-- 查所有列
SELECT * FROM users;

-- 查特定列
SELECT name, email FROM users;

-- 加条件
SELECT * FROM users WHERE age > 18 AND email LIKE '%@gmail.com';

-- 排序
SELECT * FROM users ORDER BY created_at DESC;

-- 分页（跳过前10条，取10条）
SELECT * FROM users LIMIT 10 OFFSET 10;
```

### 聚合查询

```sql
-- 统计用户总数
SELECT COUNT(*) FROM users;

-- 计算平均年龄
SELECT AVG(age) FROM users;

-- 按城市分组，统计各城市用户数
SELECT city, COUNT(*) as user_count
FROM users
GROUP BY city
HAVING COUNT(*) > 100;  -- HAVING 是对分组结果的过滤（不是 WHERE）
```

### JOIN：连接多张表

这是 SQL 的核心能力——把多张相关的表连接起来查询：

```sql
-- 查询所有用户的订单信息
SELECT users.name, orders.id, orders.total, orders.status
FROM orders
INNER JOIN users ON orders.user_id = users.id
WHERE orders.status = 'pending';
```

**JOIN 的类型**：

| 类型 | 含义 |
|------|------|
| `INNER JOIN` | 两表都有匹配的行才出现 |
| `LEFT JOIN` | 左表全部保留，右表没匹配则 NULL |
| `RIGHT JOIN` | 右表全部保留，左表没匹配则 NULL |
| `FULL OUTER JOIN` | 两表都完整保留，没匹配的用 NULL 填充 |

---

## 索引：让查询快起来

数据量大时，没有索引的查询需要扫描整张表（全表扫描），很慢。**索引**是一种额外的数据结构，让特定字段的查询变得极快，但会增加写入时的开销。

```sql
-- 为 email 字段创建索引（查询 WHERE email = '...' 会很快）
CREATE INDEX idx_users_email ON users(email);

-- 复合索引（同时按多个字段查询时有效）
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```

**哪些字段应该建索引**：
- 经常用于 `WHERE` 条件的字段
- 经常用于 `JOIN ON` 的字段（外键）
- 经常用于 `ORDER BY` 的字段
- 值域大（如 email）比值域小（如 status 只有几种值）更值得建索引

---

## 你需要精通 SQL 吗？

在 Vibe Coding 时代，你不需要背诵 SQL 的所有细节，但你需要：

1. **理解关系型数据库的结构**（表、行、列、关联）
2. **能读懂基本的 SELECT 查询**（WHERE、JOIN、GROUP BY）
3. **知道索引的作用**（性能调优时需要）
4. **了解 ORM 在做什么**（见 4.7 节）

在实际项目中，大量的数据库操作会通过 **ORM** 完成（不需要手写 SQL），或者交给 AI 生成。但当出现性能问题，或者需要复杂查询时，理解 SQL 本身是不可替代的。

---

> **下一节**：[4.3 PostgreSQL 详解](./03-PostgreSQL详解.md)
