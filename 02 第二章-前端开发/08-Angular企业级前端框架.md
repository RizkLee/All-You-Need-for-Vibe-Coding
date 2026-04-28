# 2.8 Angular——企业级前端框架

> Angular 是 Google 推出的完整前端框架，内置了构建大型应用所需的所有工具。

---

## Angular 的特点

Angular 和 React、Vue 有本质区别：

| | Angular | React / Vue |
|--|---------|-------------|
| 定位 | **完整框架** | UI 库 / 渐进式框架 |
| 路由 | 内置 | 需要另外安装 |
| HTTP 请求 | 内置 (HttpClient) | 需要 axios 等 |
| 表单管理 | 内置 (Reactive Forms) | 需要 react-hook-form 等 |
| 依赖注入 | 内置 | 无 |
| 语言 | 必须用 TypeScript | 可选 |
| 架构规范 | 严格约定 | 自由 |

Angular 提供"全家桶"——你不需要做很多技术选型，但也因此更重、学习曲线更陡。

---

## Angular 的核心概念

### 模块（Module）

Angular 应用由模块组成，模块是功能的容器：

```typescript
@NgModule({
  declarations: [AppComponent, UserListComponent],
  imports: [BrowserModule, HttpClientModule, RouterModule],
  providers: [UserService],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

（Angular 17+ 开始推广 Standalone Components，逐渐弱化模块概念）

### 组件（Component）

```typescript
@Component({
  selector: 'app-user-card',
  template: `
    <div class="card">
      <h2>{{ user.name }}</h2>
      <p>{{ user.email }}</p>
      <button (click)="onSelect()">选择</button>
    </div>
  `,
  styles: [`.card { border: 1px solid #eee; }`]
})
export class UserCardComponent {
  @Input() user: User     // 接收父组件传入的数据
  @Output() selected = new EventEmitter<User>()  // 向父组件发送事件
  
  onSelect() {
    this.selected.emit(this.user)
  }
}
```

### 服务与依赖注入（Dependency Injection）

Angular 的核心特色——通过 DI 注入依赖，而不是手动创建实例：

```typescript
@Injectable({ providedIn: 'root' })
export class UserService {
  constructor(private http: HttpClient) {}
  
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users')
  }
}

// 组件中使用：Angular 自动注入 UserService
@Component({...})
export class UserListComponent {
  users: User[] = []
  
  constructor(private userService: UserService) {}
  
  ngOnInit() {
    this.userService.getUsers().subscribe(users => {
      this.users = users
    })
  }
}
```

### Observable（RxJS）

Angular 大量使用 **RxJS**（响应式编程库），这是 Angular 最陡的学习曲线之一：

```typescript
// Observable 是一个可以随时间产生多个值的流
// 类似 Promise，但更强大，可以取消、可以转换、可以组合

this.searchInput.valueChanges.pipe(
  debounceTime(300),          // 等待300ms无输入
  distinctUntilChanged(),     // 值没变就不发出
  switchMap(query => this.searchService.search(query))  // 切换到新的搜索请求
).subscribe(results => {
  this.searchResults = results
})
```

---

## Angular 的优势场景

Angular 在以下场景表现突出：

1. **大型企业应用**：严格的架构规范防止大团队代码混乱
2. **需要统一技术决策的团队**：框架内置了所有工具，减少选型争议
3. **对 TypeScript 有强要求**：Angular 强制使用 TypeScript
4. **需要内置测试支持**：Angular CLI 自动配置 Jest/Jasmine

---

## 是否需要学 Angular

**对于大多数开发者**：不需要把 Angular 作为第一框架。

Angular 的学习成本较高（TypeScript + RxJS + DI + 模块系统），主要在金融、政务、大型企业的项目中使用。

如果你想快速构建产品，React 或 Vue 是更好的起点。如果你进入了使用 Angular 的团队，那时再深入学习也不迟。

---

## 三大框架总结对比

```
React
  ✓ 生态最大，职位最多
  ✓ 灵活，不强制架构
  ✓ React Native 覆盖移动端
  ✗ 需要自行整合各种库
  ✗ 学习曲线中等

Vue
  ✓ 上手最快
  ✓ 官方提供路由和状态管理
  ✓ 中文资源丰富
  ✓ 单文件组件直观
  ✗ 全球（非中国）市场份额低于 React

Angular
  ✓ 完整框架，开箱即用
  ✓ 严格类型和架构规范
  ✓ 适合大型团队协作
  ✗ 学习曲线最陡
  ✗ 代码量较多，较重
```

---

> **下一节**：[2.9 Tailwind CSS——原子化CSS框架](./09-TailwindCSS.md)
