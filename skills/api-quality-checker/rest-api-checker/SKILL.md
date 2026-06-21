---
name: rest-api-checker
description: 检查 Controller 接口是否符合 RESTful 规范，包括 URI 命名、HTTP 方法、状态码、HATEOAS 链接、混合风格（RPC）等，提供修复建议。
---

# REST 接口规范检查（REST API Checker）

**description**: 检查 Controller 接口是否符合 RESTful 规范，包括 URI 命名、HTTP 方法、状态码、HATEOAS 链接、混合风格（RPC）等，提供修复建议。

## 快速使用

```
检查 REST 规范：UserController
检查 REST 规范：src/main/java/com/example/
```

## 目标

检查 Controller 中的接口是否符合 REST 规范，发现不符合项并给出修复建议。

## 规范检查规则

### 1. URI 设计规则

| 规则 | 正确示例 | 错误示例 | 检查逻辑 |
|------|---------|---------|----------|
| 动宾结构 | `GET /customers` | `GET /getAllCustomers` | 路径不能以 get/add/update/delete 等动词开头 |
| 宾语为名词 | `GET /products` | `GET /getProduct` | 资源名用名词，不能是动词 |
| 名词复数 | `GET /products/2` | `GET /product/2` | 资源名使用复数形式 |
| 避免多级 URL | `/customers/2/orders` | `/customers/2/orders/3/items` | 层级不超过 `collection/item/collection` |
| 版本控制 | `/api/v1/users` | `/api/users` | 路径中应包含版本号 `v+数字` |
| **全小写字母** | `GET /user-accounts` | `GET /UserAccounts` | URI 必须全部使用小写字母 |
| **连字符分隔** | `GET /order-items` | `GET /orderItems` | 单词间使用连字符 `-` 分隔 |
| **避免冒号** | `GET /products` | `GET /products:detail` | 避免使用冒号 `:`, 改用路径层级 |
| **避免驼峰** | `GET /trade-records` | `GET /tradeRecords` | 不使用 camelCase 或 PascalCase |

### 1.2 URL 命名规范详解

| 规范项 | 要求 | 正确示例 | 错误示例 | 说明 |
|--------|------|---------|---------|------|
| 字母大小写 | 全小写 | `/api/v1/users` | `/api/v1/Users` | URL 对大小写敏感，统一小写可避免歧义 |
| 单词分隔 | 使用连字符 `-` | `/order-items` | `/orderItems` | 连字符是 RFC 3986 推荐的单词分隔符 |
| 特殊字符 | 避免冒号 `:` | `/api/v1/products` | `/api/v1/products:list` | 冒号在 URL 中有特殊含义（协议/端口），应避免 |
| 命名风格 | 纯小写 + 连字符 | `/trade-records` | `/TradeRecords`、`/tradeRecords` | 禁止 camelCase、PascalCase、snake_case |

**检查逻辑**：
1. 提取 URI 路径中的每个路径段（segment）
2. 检查是否包含大写字母（A-Z）→ 报错
3. 检查是否包含驼峰模式（首字母小写 + 后续大写字母）→ 报错
4. 检查是否包含冒号 `:` → 报错
5. 推荐使用 snake_case 或 kebab-case → 优先推荐 kebab-case

### 1.3 混合风格检测（RPC vs REST）

对于 `/order/genBnp`、`/order/bnp-instructions` 这类业务动作接口，需要识别并给出改造建议：

| 场景 | 当前写法 | 问题 | RESTful 改造方案 |
|------|---------|------|------------------|
| 业务动作（临时） | `POST /order/genBnp` | 动词 + 单数 | `POST /orders/{id}/actions/generate-bnp` |
| 业务动作（持久化） | `POST /order/bnp-instructions` | 动词化名词 | `POST /orders/{id}/instructions` |
| 状态变更触发 | `POST /order/startProcess` | 动词 | `PATCH /orders/{id}` + `{"action": "start"}` |

**判断原则**：
- 是否产生持久化数据？有 → 资源；无 → RPC 动作
- 是否需要独立的增删改查？有 → 资源；无 → RPC 动作
- 业务语义是否清晰？团队共识下 RPC 可接受

### 2. HTTP 方法规则

| 操作 | 方法 | 正确状态码 |
|------|------|-----------|
| 获取资源 | GET | 200 OK |
| 创建资源 | POST | 201 Created |
| 替换资源 | PUT | 200 OK |
| 局部更新 | PATCH | 200 OK |
| 删除资源 | DELETE | 204 No Content |

### 3. 响应格式规则

**正常响应**：
```json
{
  "data": { ... }
}
```

**错误响应**：
```json
{
  "error": "Unauthorized",
  "message": "token不存在，请重新登录"
}
```
- 错误时不使用 200 状态码
- 必须包含 `error` 和 `message` 字段

### 4. HATEOAS 规则（REST 成熟度 L3）

HATEOAS（Hypermedia as the Engine of Application State）要求：

| 规则 | 说明 | 示例 |
|------|------|------|
| 根入口返回链接 | 访问根路径返回所有可用端点的导航链接 | `GET /api` → 返回 `users_url`, `orders_url` 等 |
| 资源包含自描述链接 | 资源响应中包含 `_links` 或 `urls` 字段 | 响应包含 `self`, `related` 等链接 |
| 模板变量格式 | 使用 `{var}` 占位符而非 `:var` | `https://api.github.com/users/{user}` |
| 链接指向可发现路径 | 通过根入口可发现所有其他路径 | 遵循 GitHub API 模式 |

**正确示例（GitHub API 风格）**：
```json
{
  "current_user_url": "https://api.github.com/user",
  "users_url": "https://api.github.com/users/{user}",
  "repository_url": "https://api.github.com/repos/{owner}/{repo}"
}
```

**资源响应示例**：
```json
{
  "id": 1,
  "username": "zhangsan",
  "_links": {
    "self": "/api/v1/users/1",
    "orders": "/api/v1/users/1/orders",
    "roles": "/api/v1/users/1/roles"
  }
}
```

### 5. 其他规则

- Content-Type: `application/json`
- 时间使用 UTC 格式：`2021-12-31T08:00:00Z`
- 以资源为中心，无状态设计
- 优先使用 HTTPS

## 使用方法

### 检查单个 Controller

```
检查 Controller：UserController.java
```

### 检查多个 Controller

```
检查 Controller：src/main/java/com/example/controller/
```

### 检查整个项目

```
检查 REST 规范：src/main/java/
```

### 检查 HATEOAS 支持

```
检查 HATEOAS：src/main/java/com/example/controller/ApiRootController.java
```

## 输出格式

```
## REST 规范检查报告

### 检查结果：❌ 不通过 (4 个问题)

#### 1. URI 规范问题

| 文件 | 方法 | 当前路径 | 问题 | 修复后路径 |
|------|------|---------|------|-----------|
| OrderController.java | getOrderById | `/order/2` | 应使用复数 | `/orders/2` |
| ProductController.java | addProduct | `/addProduct` | 不能用动词 | `POST /products` |

#### 1.1 URL 命名规范问题

| 文件 | 方法 | 当前路径 | 问题 | 修复后路径 |
|------|------|---------|------|-----------|
| OrderController.java | getOrderDetail | `/orderDetail` | 驼峰命名 + 单数 | `/order-details` |
| UserController.java | getUserById | `/UserAccounts/{id}` | 大写字母 | `/user-accounts/{id}` |
| ProductController.java | listProducts | `/products:list` | 包含冒号 | `/products/list` |
| TradeController.java | getTradeRecord | `/tradeRecords` | 驼峰命名 | `/trade-records` |

#### 2. HTTP 方法问题

| 文件 | 方法 | 问题 | 修复建议 |
|------|------|------|----------|
| UserController.java | createUser | 创建资源应返回 201 | 添加 `ResponseEntity.created(uri).build()` |

#### 3. 响应格式问题

| 文件 | 方法 | 问题 | 修复建议 |
|------|------|------|----------|
| AuthController.java | login | 错误响应使用 200 状态码 | 返回 `ResponseEntity.status(401).body(...)` |

#### 4. HATEOAS 问题

| 文件 | 方法 | 问题 | 修复代码 |
|------|------|------|----------|
| UserController.java | getUserById | 响应缺少 `_links` 自描述链接 | 见下方代码示例 |
| (缺失) | - | 缺少根入口控制器 | 创建 `ApiRootController` 返回 API 导航链接 |

#### 5. 混合风格问题（RPC vs REST）

| 文件 | 方法 | 当前路径 | 问题 | 建议改造 |
|------|------|---------|------|----------|
| OrderController.java | genBnp | `/order/genBnp` | 动词 + 单数 | `POST /orders/{id}/actions/generate-bnp` |
| OrderController.java | bnpInstructions | `/order/bnp-instructions` | 动词化名词 | `POST /orders/{id}/instructions` |

---

### 通过的检查项 ✅

- [x] 层级深度不超过 3 层
- [x] 使用名词复数形式
- [x] Content-Type 正确
- [x] 模板变量格式正确
- [x] URL 全小写字母
- [x] URL 使用连字符分隔单词
- [x] URL 不含冒号 `:`
- [x] URL 不含驼峰命名

---

## 修复代码示例

### 修复 1：URI 规范问题

**Before（错误）**：
```java
@GetMapping("/getAllUsers")
public List<User> getAllUsers() { ... }

@PostMapping("/addUser")
public User addUser(@RequestBody User user) { ... }
```

**After（正确）**：
```java
@GetMapping("/users")
public List<User> getAllUsers() { ... }

@PostMapping("/users")
public ResponseEntity<User> createUser(@RequestBody User user) {
    User created = userService.create(user);
    URI location = URI.create("/users/" + created.getId());
    return ResponseEntity.created(location).body(created);
}
```

### 修复 2：HTTP 状态码问题

**Before（错误）**：
```java
@PostMapping("/login")
public ApiResponse<Object> login(@RequestBody LoginRequest req) {
    if (!authService.validate(req)) {
        return ApiResponse.error("Unauthorized"); // 错误使用 200
    }
    return ApiResponse.success(token);
}
```

**After（正确）**：
```java
@PostMapping("/login")
public ResponseEntity<?> login(@RequestBody LoginRequest req) {
    if (!authService.validate(req)) {
        return ResponseEntity.status(401)
            .body(new ErrorResponse("Unauthorized", "token不存在"));
    }
    return ResponseEntity.ok(token);
}
```

### 修复 3：HATEOAS 根入口

**缺失的根入口 Controller**：
```java
@RestController
@RequestMapping("/api")
public class ApiRootController {

    @GetMapping
    public ResponseEntity<Map<String, String>> index() {
        Map<String, String> links = new LinkedHashMap<>();
        links.put("users_url", "https://api.example.com/v1/users/{user}");
        links.put("orders_url", "https://api.example.com/v1/orders/{order}");
        links.put("products_url", "https://api.example.com/v1/products");
        return ResponseEntity.ok(links);
    }
}
```

### 修复 4：HATEOAS 资源链接

**Before（缺少链接）**：
```java
@GetMapping("/{id}")
public UserDTO getUserById(@PathVariable Long id) {
    return userService.getUserById(id);
}
```

**After（包含 _links）**：
```java
@GetMapping("/{id}")
public ResponseEntity<UserDTO> getUserById(@PathVariable Long id) {
    User user = userService.getUserById(id);
    UserDTO dto = convertToDTO(user);

    Map<String, String> links = new HashMap<>();
    links.put("self", "/v1/users/" + id);
    links.put("orders", "/v1/users/" + id + "/orders");
    dto.set_links(links);

    return ResponseEntity.ok(dto);
}
```

**UserDTO 需添加 _links 字段**：
```java
public class UserDTO {
    private Long id;
    private String username;
    private String email;
    private Map<String, String> _links;

    public Map<String, String> get_links() { return _links; }
    public void set_links(Map<String, String> links) { this._links = links; }
}
```

### 修复 5：混合风格接口（RPC → REST）

**场景 A：业务动作（临时操作，不持久化）**

**Before（RPC 风格）**：
```java
@PostMapping("/order/genBnp")
public ApiResponse<Object> genBnp(@RequestBody GenBnpRequest req) {
    orderService.generateBnp(req);
    return ApiResponse.success(null);
}
```

**After（RESTful + 控制器资源）**：
```java
@PostMapping("/orders/{orderId}/actions/generate-bnp")
public ResponseEntity<?> generateBnp(@PathVariable Long orderId, @RequestBody GenBnpRequest req) {
    orderService.generateBnp(orderId, req);
    return ResponseEntity.accepted().build();
}
```

**场景 B：业务动作（产生持久化资源）**

**Before（动词化名词）**：
```java
@PostMapping("/order/bnp-instructions")
public ApiResponse<Object> createBnpInstruction(@RequestBody BnpInstructionRequest req) {
    return ApiResponse.success(instructionService.create(req));
}
```

**After（标准 REST 资源）**：
```java
@PostMapping("/orders/{orderId}/instructions")
public ResponseEntity<BnpInstruction> createInstruction(
        @PathVariable Long orderId,
        @RequestBody BnpInstructionRequest req) {
    BnpInstruction instruction = instructionService.create(orderId, req);
    URI location = URI.create("/instructions/" + instruction.getId());
    return ResponseEntity.created(location).body(instruction);
}
```

**场景 C：状态变更触发动作**

**Before（动词路径）**：
```java
@PostMapping("/order/startProcess")
public ApiResponse<Object> startProcess(@PathVariable Long orderId) {
    orderService.startProcess(orderId);
    return ApiResponse.success(null);
}
```

**After（状态变更）**：
```java
@PatchMapping("/orders/{orderId}")
public ResponseEntity<Order> startProcess(
        @PathVariable Long orderId,
        @RequestBody Map<String, String> body) {
    if ("start".equals(body.get("action"))) {
        Order order = orderService.startProcess(orderId);
        return ResponseEntity.ok(order);
    }
    return ResponseEntity.badRequest().build();
}
```

**判断 flowchart**：

```
是持久化资源吗？
├── 否 → 是业务动作吗？
│   ├── 是 → POST /orders/{id}/actions/{action-name}
│   └── 否 → 可能只是状态变更，用 PATCH
└── 是 → 有独立的增删改查需求吗？
    ├── 是 → 设计为完整 REST 资源
    └── 否 → POST /orders/{id}/actions/{action-name}
```
```

## 检查逻辑实现

### URI 规则检测

```
1. 提取 @RequestMapping、@GetMapping、@PostMapping 等注解的路径
2. 检查是否包含动词（get/add/update/delete/create/remove）
3. 检查是否使用复数形式
4. 检查层级深度
5. 检查版本号格式
6. 检查 URL 命名规范（全小写、连字符分隔、无冒号、无驼峰）
```

### HTTP 方法检测

```
1. 统计每个方法使用的 @GetMapping/@PostMapping/@PutMapping/@PatchMapping/@DeleteMapping
2. 对比方法名推断的操作类型
3. 检查返回的 HTTP 状态码
```

### 响应格式检测

```
1. 检查方法返回类型
2. 检查是否使用了统一的响应封装（如 ApiResponse）
3. 检查错误处理是否使用了正确的 HTTP 状态码
```

### HATEOAS 检测

```
1. 检查是否存在根入口 Controller（映射到 /api 或 /）
2. 检查根入口的 GET 方法是否返回一个包含多个 URL 链接的对象
3. 检查资源 Controller 的响应是否包含 _links 或 urls 字段
4. 检查链接模板格式是否为 {var} 而非 :var
```

## 规则配置

可在项目中创建 `.rest-api-rules.json` 自定义规则：

```json
{
  "uri": {
    "allowVerbs": false,
    "requirePlural": true,
    "maxDepth": 3,
    "requireVersion": true,
    "versionPattern": "v\\d+",
    "naming": {
      "requireLowercase": true,
      "requireHyphen": true,
      "forbidColon": true,
      "forbidCamelCase": true
    }
  },
  "httpMethod": {
    "requireCorrectMethod": true,
    "requireCorrectStatusCode": true
  },
  "response": {
    "requireErrorFormat": true,
    "requireCorrectContentType": true
  },
  "hateoas": {
    "requireRootEntry": true,
    "requireLinksInResources": true,
    "templateFormat": "\\{[^}]+\\}"
  }
}
```

## HATEOAS 实现示例

### 根入口 Controller（必须）

```java
@RestController
@RequestMapping("/api")
public class ApiRootController {

    @GetMapping
    public Map<String, String> index() {
        Map<String, String> links = new LinkedHashMap<>();
        links.put("users_url", "https://api.example.com/v1/users/{user}");
        links.put("orders_url", "https://api.example.com/v1/orders/{order}");
        links.put("products_url", "https://api.example.com/v1/products");
        links.put("current_user_url", "https://api.example.com/v1/user");
        return links;
    }
}
```

### 资源响应 DTO（含 HATEOAS 链接）

```java
public class UserDTO {
    private Long id;
    private String username;
    private String email;

    private Map<String, String> _links;

    public Map<String, String> get_links() {
        return _links;
    }

    public void set_links(Map<String, String> links) {
        this._links = links;
    }
}
```

```java
@GetMapping("/{id}")
public ApiResponse<UserDTO> getUserById(@PathVariable Long id) {
    User user = userService.getUserById(id);
    UserDTO dto = convertToDTO(user);

    // 添加 HATEOAS 链接
    Map<String, String> links = new HashMap<>();
    links.put("self", "/api/v1/users/" + id);
    links.put("orders", "/api/v1/users/" + id + "/orders");
    links.put("roles", "/api/v1/users/" + id + "/roles");
    dto.set_links(links);

    return ApiResponse.success(dto);
}
```
