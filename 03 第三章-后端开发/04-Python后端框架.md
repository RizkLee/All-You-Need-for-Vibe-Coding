# 3.4 Python 后端：FastAPI、Django、Flask

> Python 语法简洁，在 AI/ML 领域占统治地位，也是非常受欢迎的后端语言。

---

## Python 作为后端语言的优势

- **语法简洁**，代码量比 Java 少很多
- **AI/ML 生态无敌**（TensorFlow, PyTorch, scikit-learn）
- **丰富的第三方库**
- **学习曲线平缓**

劣势：比 Go、Java、Node.js 稍慢（但对大多数 Web 应用来说影响不大）

---

## 三个主要框架

| 框架 | 定位 | 特点 |
|------|------|------|
| **Django** | 完整框架 | 全家桶，内置 ORM、Admin、认证 |
| **Flask** | 微框架 | 极简，自由组合 |
| **FastAPI** | 现代高性能框架 | 自动文档、类型提示、async 支持 |

---

## FastAPI：现代 Python 后端的首选

**FastAPI** 是目前增长最快的 Python Web 框架，融合了 Python 3.6+ 的类型提示：

```python
from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel
from typing import Optional

app = FastAPI()

# 数据模型（Pydantic）
class UserCreate(BaseModel):
    name: str
    email: str
    password: str

class UserResponse(BaseModel):
    id: int
    name: str
    email: str

# 路由定义
@app.get("/")
def read_root():
    return {"message": "Hello World"}

@app.get("/users/{user_id}", response_model=UserResponse)
async def get_user(user_id: int):
    user = await db.get_user(user_id)
    if not user:
        raise HTTPException(status_code=404, detail="用户不存在")
    return user

@app.post("/users", response_model=UserResponse, status_code=201)
async def create_user(user: UserCreate):
    # FastAPI 自动验证请求体格式
    return await db.create_user(user)
```

FastAPI 的核心优势：

**自动生成 API 文档**：访问 `/docs` 就能看到交互式文档（Swagger UI），不需要手写。

**类型验证**：基于 Pydantic，请求数据自动验证，格式错误自动返回 422。

**async/await 原生支持**：性能接近 Node.js 和 Go。

---

## Django：大而全的"电池全包"框架

**Django** 的理念是"不要重复发明轮子"——它提供了你可能需要的一切：

```python
# models.py（自动对应数据库表）
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        db_table = 'users'

# views.py（处理请求）
from django.http import JsonResponse
from .models import User

def get_users(request):
    users = User.objects.filter(is_active=True).values('id', 'name', 'email')
    return JsonResponse(list(users), safe=False)

# urls.py（路由）
urlpatterns = [
    path('api/users/', views.get_users),
]
```

Django 内置了：
- **ORM**：对象关系映射，不需要写 SQL
- **Admin 后台**：自动生成数据管理界面（几行代码配置完成）
- **认证系统**：用户、权限、会话
- **表单验证**
- **模板引擎**（如果做服务端渲染）

**Django REST Framework (DRF)**：在 Django 上扩展 REST API 能力的库，是 Django 做 API 开发的标准搭配。

---

## Flask：极简微框架

**Flask** 没有内置 ORM、认证等，一切自由选择：

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/api/users', methods=['GET'])
def get_users():
    users = [{"id": 1, "name": "张三"}]
    return jsonify(users)

@app.route('/api/users', methods=['POST'])
def create_user():
    data = request.json
    # 处理逻辑...
    return jsonify({"id": 2, "name": data['name']}), 201

if __name__ == '__main__':
    app.run(debug=True, port=5000)
```

Flask 适合：小项目、原型验证、学习、或者需要极度定制化的场景。

---

## Python 的包管理

Python 有自己的包管理生态：

```bash
pip install fastapi        # 安装包（类似 npm install）
pip install -r requirements.txt  # 从文件批量安装

# 虚拟环境（隔离项目依赖，类似 node_modules）
python -m venv venv
source venv/bin/activate   # macOS/Linux
.\venv\Scripts\activate    # Windows
```

`requirements.txt`（类似 package.json）：
```
fastapi==0.103.0
uvicorn==0.23.2
sqlalchemy==2.0.20
pydantic==2.3.0
```

---

## 如何选择 Python 框架

| 如果你... | 选择 |
|-----------|------|
| 要快速构建现代 REST API | **FastAPI** |
| 需要内置 Admin 后台、复杂权限系统 | **Django + DRF** |
| 做内容型网站（博客、CMS）| **Django** |
| 做原型、简单脚本 | **Flask** |
| 需要与 ML/AI 模型集成 | **FastAPI**（更适合异步）|

---

## Python 后端 vs Node.js 后端

| 比较维度 | Python | Node.js |
|----------|--------|---------|
| 性能 | 稍慢（FastAPI 例外）| 较快 |
| 语法简洁度 | 非常简洁 | 较简洁 |
| AI/ML 集成 | ✅ 天然优势 | 需要调用 Python 服务 |
| 前后端同语言 | ❌ | ✅（都是 JS）|
| 生态丰富度 | 非常丰富 | 非常丰富 |

如果你的应用需要深度 AI 能力，Python 是最自然的选择。否则，Node.js 让全栈 JS 成为可能，减少学习成本。

---

> **下一节**：[3.5 Java 后端与 Spring 框架](./05-Java后端与Spring.md)
