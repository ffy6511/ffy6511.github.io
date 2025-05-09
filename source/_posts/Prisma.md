---
title: Prisma
date: 2025-04-28 14:00
tags: 数据库
categories: 前后端开发
excerpt: 在图书管理系统的后端中, 使用Prisma连接本地的sql数据库.
---

### Prisma 命令行相关

#### 数据库迁移

1. **创建并应用新的迁移**：
   
   ```bash
   npx prisma migrate dev --name add_timestamps
   ```
   - 根据 `schema.prisma` 的更改生成迁移文件，并应用到数据库。
   - `--name` 指定迁移的名称。
   
2. **重置并重新应用所有迁移**：
   
   ```bash
   npx prisma migrate reset
   ```
   - 重置数据库并重新应用所有迁移，适用于开发环境。
   
3. **生成迁移文件但不应用**：
   ```bash
   npx prisma migrate dev --create-only
   ```
   - 生成迁移文件但不应用到数据库，可以手动修改迁移文件后再应用。

4. **应用未应用的迁移**：
   ```bash
   npx prisma migrate deploy
   ```
   - 将未应用的迁移应用到生产环境。

5. **查看迁移状态**：
   ```bash
   npx prisma migrate status
   ```
   - 查看当前数据库的迁移状态。
   
6. **回滚迁移状体**;

   ```bash
   npx prisma migrate resolve --rolled-back <migration_name>
   ```

   

---

#### 数据库操作

1. **生成 Prisma 客户端**：
   ```bash
   npx prisma generate
   ```
   - 根据 `schema.prisma` 生成 Prisma 客户端代码。

2. **推送 schema 到数据库（不生成迁移）**：
   ```bash
   npx prisma db push
   ```
   - 将 `schema.prisma` 的更改直接应用到数据库，不生成迁移文件。

3. **查看数据库数据**：
   ```bash
   npx prisma studio
   ```
   - 启动 Prisma Studio，一个可视化工具，用于查看和操作数据库数据。

---

#### 数据操作

1. **执行种子脚本**：
   ```bash
   npx prisma db seed
   ```
   - 运行 `prisma/seed.ts` 或 `prisma/seed.js` 脚本，用于填充数据库初始数据。

2. **运行自定义脚本**：
   ```bash
   npx prisma execute --file ./scripts/my-script.ts
   ```
   - 执行自定义的 TypeScript 或 JavaScript 脚本。

---

#### 其他常用命令

1. **初始化 Prisma**：
   ```bash
   npx prisma init
   ```
   - 初始化 Prisma，生成 `prisma/schema.prisma` 和 `.env` 文件。

2. **格式化 `schema.prisma`**：
   ```bash
   npx prisma format
   ```
   - 格式化 `schema.prisma` 文件，使其更易读。

3. **检查 Prisma 版本**：
   ```bash
   npx prisma --version
   ```
   - 查看当前安装的 Prisma 版本。

4. **清理未使用的迁移文件**：
   ```bash
   npx prisma migrate resolve --applied "20231010123456_add_timestamps"
   ```
   - 标记迁移文件为已应用，用于修复迁移状态不一致的问题。

---



### 语言特性

Prisma 中的关系定义需要双向声明

- 在引用表中进行外键的声明

  ```sql
  model BorrowRecord {
  	bookId string 
  	book       Book       @relation(fields: [bookId], references: [id])
  }
  ```

  > 表示当前表中的 `bookId`字段是被应用表 `Book` 的属性 `id`的外键.

- 同时在被引用表中定义:

  ```sql
  model Book{
    id             String         @id
    borrowRecords  BorrowRecord[] // 添加反向关系
  }
  ```





---

### 使用TS交互客户端

Prisma Client 是一个类型安全的数据库查询工具，它根据 `schema.prisma` 文件生成 TypeScript 类型定义和数据库操作 API。

以下是其主要功能及代码示例。

---

#### 1. **初始化 Prisma Client**

在使用 Prisma Client 之前，需要初始化一个 `PrismaClient` 实例。

```typescript
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();
```

---

#### 2. **查询数据**

- **查询所有记录**

使用 `findMany` 方法查询表中的所有记录。

```typescript
async function getAllUsers() {
  const users = await prisma.user.findMany();
  console.log('All users:', users);
}

getAllUsers();
```

- **查询单条记录**

使用 `findUnique` 方法根据唯一条件查询单条记录。

```typescript
async function getUserById(id: number) {
  const user = await prisma.user.findUnique({
    where: { id },
  });
  console.log('User:', user);
}

getUserById(1);
```

- **条件查询**

使用 `where` 条件过滤查询结果。

```typescript
async function getUsersByName(name: string) {
  const users = await prisma.user.findMany({
    where: { name },
  });
  console.log('Users with name:', name, users);
}

getUsersByName('Alice');
```

---

#### 3. **创建数据**

使用 `create` 方法插入新数据:

```typescript
async function createUser(name: string, email: string) {
  const newUser = await prisma.user.create({
    data: {
      name,
      email,
    },
  });
  console.log('Created new user:', newUser);
}

createUser('Bob', 'bob@example.com');
```

---

#### 4. **更新数据**

使用 `update` 方法修改现有数据。

```typescript
async function updateUserEmail(id: number, newEmail: string) {
  const updatedUser = await prisma.user.update({
    where: { id },
    data: { email: newEmail },
  });
  console.log('Updated user:', updatedUser);
}

updateUserEmail(1, 'alice_new@example.com');
```

---

#### 5. **删除数据**

使用 `delete` 方法删除数据。

```typescript
async function deleteUser(id: number) {
  const deletedUser = await prisma.user.delete({
    where: { id },
  });
  console.log('Deleted user:', deletedUser);
}

deleteUser(1);
```

---

#### 6. **关系查询**

- **查询关联数据**

使用 `include` 查询关联的模型数据。

```typescript
async function getUserWithPosts(userId: number) {
  const userWithPosts = await prisma.user.findUnique({
    where: { id: userId },
    include: { posts: true }, // 假设 User 模型与 Post 模型有关联
  });
  console.log('User with posts:', userWithPosts);
}

getUserWithPosts(1);
```

- **嵌套查询**

支持嵌套查询关联数据。

```typescript
async function getPostWithAuthor(postId: number) {
  const postWithAuthor = await prisma.post.findUnique({
    where: { id: postId },
    include: { author: true }, // 假设 Post 模型与 User 模型有关联
  });
  console.log('Post with author:', postWithAuthor);
}

getPostWithAuthor(1);
```

---

#### 7. **分页查询**

使用 `skip` 和 `take` 实现分页查询。

```typescript
async function getUsersPaginated(page: number, pageSize: number) {
  const users = await prisma.user.findMany({
    skip: (page - 1) * pageSize,
    take: pageSize,
  });
  console.log('Paginated users:', users);
}

getUsersPaginated(1, 10); // 查询第 1 页，每页 10 条记录
```

---

#### 8. **排序查询**

使用 `orderBy` 对查询结果排序。

```typescript
async function getUsersSortedByName() {
  const users = await prisma.user.findMany({
    orderBy: { name: 'asc' }, // 按 name 字段升序排序
  });
  console.log('Sorted users:', users);
}

getUsersSortedByName();
```

---

#### 9. **聚合查询**

使用 `count`、`sum`、`avg` 等聚合函数。

- **统计记录数**

```typescript
async function countUsers() {
  const userCount = await prisma.user.count();
  console.log('Total users:', userCount);
}

countUsers();
```

- **计算字段平均值**

```typescript
async function averageUserAge() {
  const avgAge = await prisma.user.aggregate({
    _avg: { age: true }, // 假设 User 模型有 age 字段
  });
  console.log('Average user age:', avgAge._avg.age);
}

averageUserAge();
```

---

#### 10. **事务操作**

使用 `$transaction` 执行事务操作。

```typescript
async function transferBalance(fromId: number, toId: number, amount: number) {
  await prisma.$transaction([
    prisma.user.update({
      where: { id: fromId },
      data: { balance: { decrement: amount } }, // 假设 User 模型有 balance 字段
    }),
    prisma.user.update({
      where: { id: toId },
      data: { balance: { increment: amount } },
    }),
  ]);
  console.log('Balance transfer completed');
}

transferBalance(1, 2, 100);
```

---

#### 11. **关闭 Prisma Client**

在程序结束时，关闭 Prisma Client 以释放数据库连接。

```typescript
async function main() {
  // 数据库操作代码
}

main()
  .catch(e => {
    console.error(e);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

---

