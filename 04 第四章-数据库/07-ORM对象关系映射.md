# 4.7 ORM：对象关系映射

> ORM 让你用代码里的对象来操作数据库，而不是手写 SQL 语句。

---

## ORM 解决了什么问题

后端程序里的数据是**对象**（如一个 User 对象），而数据库里的数据是**表和行**。这两者之间存在一个"阻抗不匹配"问题——需要把对象转换成 SQL 语句，再把查询结果转换回对象。

没有 ORM，你每次都要手写这样的代码：

```javascript
// 手写 SQL
const user = await db.query(
  'SELECT * FROM users WHERE id = $1',
  [userId]
)
// user 是一个普通对象，不是有方法的 User 实例
```

有了 ORM：

```javascript
// 用对象操作
const user = await User.findById(userId)
// user 是 User 模型的实例，可以调用方法
await user.update({ name: '新名字' })
await user.delete()
```

ORM（Object-Relational Mapping，对象关系映射）负责在两者之间做翻译。

---

## ORM 的核心功能

**1. 模型定义（Schema）**  
用代码定义数据库表的结构，ORM 负责创建和修改表：

```typescript
// Prisma 示例
model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  posts     Post[]
  createdAt DateTime @default(now())
}
```

**2. CRUD 操作**  
增删改查都变成方法调用，ORM 生成对应的 SQL：

```typescript
// 查询
const users = await prisma.user.findMany({
  where: { email: { contains: '@gmail.com' } },
  orderBy: { createdAt: 'desc' },
  take: 10
})

// 创建
const newUser = await prisma.user.create({
  data: { name: '张三', email: 'zhang@example.com' }
})
```

**3. 关联查询**  
处理表与表之间的关联，避免手写 JOIN：

```typescript
// 查询用户及其所有文章
const userWithPosts = await prisma.user.findUnique({
  where: { id: 1 },
  include: { posts: true }
})
```

**4. 数据库迁移（Migration）**  
当你修改了 Schema，ORM 可以生成迁移文件，自动执行 ALTER TABLE 等操作，记录数据库结构的变更历史。

---

## 主流 ORM 工具

### Prisma（Node.js/TypeScript）

目前 Node.js 生态中最受欢迎的 ORM，以 TypeScript 优先、开发体验好著称：

- Schema 写在单独的 `schema.prisma` 文件中
- 完整的 TypeScript 类型推导，IDE 自动补全极好
- Prisma Migrate 管理数据库变更
- Prisma Studio：可视化数据库管理界面

```
// 一次完整的 Prisma 工作流
1. 修改 schema.prisma（定义/修改数据模型）
2. npx prisma migrate dev（生成并执行迁移）
3. npx prisma generate（更新 TypeScript 类型）
4. 在代码里用 prisma.xxx 操作数据库
```

### TypeORM（Node.js/TypeScript）

另一个流行选择，风格更接近 Java 的 JPA，使用装饰器定义模型。

### SQLAlchemy（Python）

Python 生态中的标准 ORM，Django ORM 也是 Python 世界的常见选择。

### GORM（Go）

Go 生态中最流行的 ORM。

---

## ORM 的取舍

ORM 不是银弹，有时候反而是阻碍：

**ORM 的优势**：
- 不用写 SQL，降低了数据库操作的门槛
- TypeScript 类型安全，减少运行时错误
- 跨数据库兼容（切换 MySQL → PostgreSQL 改配置即可）
- 防止 SQL 注入（参数化查询是默认行为）

**ORM 的劣势**：
- 生成的 SQL 不一定最优，复杂查询可能产生低效的 SQL
- 学习 ORM 的抽象层本身也有成本
- 调试困难：出了问题要同时看 ORM 层和 SQL 层
- 某些高级 SQL 特性（窗口函数、复杂 CTE）ORM 可能不支持

**实践中的常见做法**：80% 的操作用 ORM，对于性能敏感或复杂的查询，直接写原生 SQL（ORM 通常也支持执行原始 SQL）。

---

## 数据库迁移的重要性

**迁移（Migration）**是数据库 Schema 变更的版本控制：

```
迁移文件列表（类似 Git 提交）：
  0001_create_users_table.sql
  0002_add_email_to_users.sql
  0003_create_orders_table.sql
  0004_add_index_on_email.sql
```

每次你修改了数据库结构（增加字段、创建表），都应该创建一个迁移文件，而不是直接在数据库里手动改。这样：
- 所有环境（本地、测试、生产）的数据库结构能保持一致
- 可以回滚到之前的版本
- 新成员克隆项目后能一键重建数据库结构

---

> **第四章完结**。下一章：[第五章 全栈框架](../第五章-全栈框架/01-什么是全栈框架.md)
