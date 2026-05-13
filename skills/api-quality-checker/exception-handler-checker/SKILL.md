---
name: exception-handler-checker
description: 检查 Spring 接口的异常处理是否符合规范，包括 @ControllerAdvice 全局异常处理、业务异常定义、HTTP 状态码、异常日志记录、敏感信息脱敏等，支持 Controller 入口自动追踪 Service/Mapper 链路。
---

# Spring 异常处理检查（Exception Handler Checker）

**description**: 检查 Spring 接口的异常处理是否符合规范，包括 @ControllerAdvice 全局异常处理、业务异常定义、HTTP 状态码、异常日志记录、敏感信息脱敏等，支持 Controller 入口自动追踪 Service/Mapper 链路。

## 快速使用

```
检查异常处理：UserController
检查异常处理：UserController.getUserById
检查异常处理：src/main/java/com/example/
```

## 目标

检查 Controller 和 Service 的异常处理是否符合规范，确保接口错误响应的一致性。

## 核心规则

### 1. 异常处理体系

| 异常类型 | 说明 | 示例 |
|---------|------|------|
| 业务异常 | 可预期的业务错误 | `UserNotFoundException`、`BalanceInsufficientException` |
| 系统异常 | 不可预期的技术错误 | `NullPointerException`、`DatabaseException` |
| 参数异常 | 用户输入错误 | `ValidationException`、`ConstraintViolationException` |
| 权限异常 | 认证/授权错误 | `UnauthorizedException`、`AccessDeniedException` |

### 2. 统一异常响应格式

**正确格式**：
```json
{
  "code": 400,
  "error": "BadRequest",
  "message": "请求参数错误",
  "detail": "username 不能为空",
  "timestamp": "2024-01-15T10:30:00Z",
  "path": "/api/v1/users"
}
```

**业务异常响应**：
```json
{
  "code": 10001,
  "error": "USER_NOT_FOUND",
  "message": "用户不存在",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

### 3. @ControllerAdvice 必须存在

| 检查项 | 要求 |
|--------|------|
| 全局异常处理 | 必须有 @ControllerAdvice 或 @RestControllerAdvice |
| 业务异常捕获 | 捕获自定义业务异常 |
| 系统异常捕获 | 捕获 RuntimeException、Exception |
| 参数异常捕获 | 捕获 ValidationException、ConstraintViolationException |

## 检查项

### 1. @ControllerAdvice 检查

| 检查项 | 问题 | 正确写法 |
|--------|------|----------|
| 缺少全局处理 | 异常直接抛给容器 | 必须有 @ControllerAdvice |
| 缺少业务异常处理 | 业务异常未统一处理 | @ExceptionHandler(BusinessException.class) |
| 缺少参数异常处理 | 校验异常未统一处理 | @ExceptionHandler(ValidationException.class) |
| 缺少系统异常处理 | 系统异常未统一处理 | @ExceptionHandler(Exception.class) |
| 异常未返回统一格式 | 响应格式不一致 | 返回统一的 ErrorResponse |

### 2. 异常分类检查

| 检查项 | 问题 | 建议 |
|--------|------|------|
| 业务异常未定义 | 直接抛 RuntimeException | 定义业务异常类 |
| 异常信息不友好 | 堆栈信息暴露给用户 | 返回友好错误信息 |
| 异常未记录日志 | 异常丢失 | 使用 log.error 记录异常 |
| 敏感信息泄露 | 密码、token 在异常中 | 异常信息脱敏 |

### 3. HTTP 状态码规范

| 场景 | 状态码 | 说明 |
|------|--------|------|
| 参数错误 | 400 Bad Request | ValidationException |
| 未认证 | 401 Unauthorized | AuthenticationException |
| 无权限 | 403 Forbidden | AccessDeniedException |
| 资源不存在 | 404 Not Found | ResourceNotFoundException |
| 业务错误 | 200 OK（业务码） | BusinessException 或 4xx |
| 服务器错误 | 500 Internal Server Error | 未捕获的系统异常 |

### 4. 异常处理正确性

| 检查项 | 错误示例 | 正确示例 |
|--------|---------|---------|
| 用 RuntimeException 替代业务异常 | `throw new RuntimeException("用户不存在")` | `throw new UserNotFoundException("用户不存在")` |
| 在 Controller 捕获异常 | try-catch 在 Controller 层 | 异常上抛，由 @ControllerAdvice 统一处理 |
| 异常信息暴露 | `throw new Exception("SQL: " + sql)` | `throw new SystemException("服务暂时不可用")` |
| 未记录日志 | 异常被吞掉 | `log.error("操作失败", e); throw e;` |

## 使用方法

### 入口类型与自动追踪

| 入口类型 | 示例 | 自动追踪 |
|---------|------|---------|
| Controller 类 | `检查异常处理：UserController` | Controller → Service → Mapper |
| Controller#方法 | `检查异常处理：UserController.getUserById` | 精确到方法链路 |
| Service 类 | `检查异常处理：UserService` | 只检查 Service |
| 目录/项目 | `检查异常处理：src/main/java/` | 检查所有 |

### Controller 入口（自动追踪完整链路）

```
检查异常处理：UserController
```

**自动追踪链路**：
```
UserController
  └─> @Autowired UserService
        └─> @Autowired UserMapper
```

**检查范围**：
1. **Controller 层**：异常是否上抛、是否吞异常
2. **Service 层**：异常定义、异常上抛、try-catch 处理
3. **Mapper 层**：SQL 异常处理

### Controller#方法 入口（精确链路）

```
检查异常处理：UserController.getUserById
检查异常处理：UserController.createUser
```

**只检查指定方法**：
```
UserController.getUserById()
  └─> UserService.getUserById()
        └─> UserMapper.selectById()
```

### Service 入口（直接检查）

```
检查异常处理：UserService
检查异常处理：src/main/java/com/example/service/
```

### 目录/项目入口

```
检查异常处理：src/main/java/com/example/
检查异常处理：src/main/java/
```

### 公共架构包配置

如果项目使用了公共的异常处理架构包（如公司级 common 包），可在项目根目录创建 `.api-checker-config.json` 配置：

```json
{
  "exception": {
    "commonPackages": [
      "com.company.platform.exception",
      "com.company.common.exception",
      "org.springframework.http"
    ],
    "globalExceptionHandlers": [
      "GlobalExceptionHandler",
      "PlatformExceptionHandler",
      "BaseExceptionHandler"
    ],
    "businessExceptions": [
      "BusinessException",
      "ServiceException",
      "AppException"
    ]
  }
}
```

**配置说明**：

| 字段 | 说明 | 示例 |
|------|------|------|
| `commonPackages` | 公共异常包的包名前缀 | `com.company.platform.exception` |
| `globalExceptionHandlers` | 全局异常处理器类名 | `GlobalExceptionHandler` |
| `businessExceptions` | 业务异常类名 | `BusinessException` |

**Skill 行为**：

| 场景 | 有配置时行为 |
|------|-------------|
| 检测到 import 公共包 | 跳过 @ControllerAdvice 存在性检查 |
| Controller 使用公共异常 | 检查是否正确上抛异常 |
| Service 使用公共异常 | 检查异常定义是否符合规范 |
| 未使用公共包 | 执行完整检查（@ControllerAdvice 必须存在） |

### 多项目配置示例

```json
{
  "exception": {
    "commonPackages": [
      "com.example.platform.exception",
      "com.example.cloud.exception",
      "com.alibaba.xxx.common.exception"
    ],
    "globalExceptionHandlers": [
      "GlobalExceptionHandler",
      "CloudExceptionHandler",
      "FeignExceptionHandler"
    ],
    "businessExceptions": [
      "BusinessException",
      "RemoteException",
      "CheckedException"
    ]
  }
}
```

## 输出格式

```
## Spring 异常处理检查报告

### 检查结果：❌ 不通过 (4 个问题)

#### 1. @ControllerAdvice 问题

| 文件 | 问题 | 修复建议 |
|------|------|----------|
| (缺失) | 缺少全局异常处理器 | 创建 GlobalExceptionHandler 类 |
| UserController.java | 在 Controller 内 catch 异常 | 移除 try-catch，改为上抛 |

#### 2. 异常分类问题

| 文件 | 方法 | 问题 | 建议 |
|------|------|------|------|
| UserService.java | getUser | 用 RuntimeException | 定义 UserNotFoundException |
| OrderService.java | create | 异常信息暴露 SQL | 返回友好错误信息 |

#### 3. 响应格式问题

| 文件 | 问题 | 建议 |
|------|------|------|
| GlobalExceptionHandler.java | 缺少 timestamp 字段 | 添加 timestamp 字段 |
| GlobalExceptionHandler.java | 缺少 path 字段 | 添加 path 字段 |

#### 4. 日志记录问题

| 文件 | 方法 | 问题 | 建议 |
|------|------|------|------|
| UserService.java | updateUser | 异常未记录日志 | 添加 log.error("更新失败", e) |

---

### 通过的检查项 ✅

- [x] @ControllerAdvice 存在
- [x] 业务异常定义完整
- [x] HTTP 状态码使用正确
- [x] 异常信息已脱敏
```

## 修复代码示例

### 修复 1：创建全局异常处理器

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ErrorResponse> handleBusinessException(BusinessException e, HttpServletRequest request) {
        ErrorResponse error = ErrorResponse.builder()
            .code(e.getCode())
            .error(e.getError())
            .message(e.getMessage())
            .timestamp(Instant.now().toString())
            .path(request.getRequestURI())
            .build();
        return ResponseEntity.status(e.getHttpStatus()).body(error);
    }

    @ExceptionHandler(ValidationException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(ValidationException e, HttpServletRequest request) {
        ErrorResponse error = ErrorResponse.builder()
            .code(400)
            .error("BadRequest")
            .message("参数校验失败")
            .detail(e.getMessage())
            .timestamp(Instant.now().toString())
            .path(request.getRequestURI())
            .build();
        return ResponseEntity.badRequest().body(error);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleException(Exception e, HttpServletRequest request) {
        log.error("系统异常", e);
        ErrorResponse error = ErrorResponse.builder()
            .code(500)
            .error("InternalServerError")
            .message("服务暂时不可用，请稍后重试")
            .timestamp(Instant.now().toString())
            .path(request.getRequestURI())
            .build();
        return ResponseEntity.status(500).body(error);
    }
}
```

### 修复 2：定义业务异常

```java
@Getter
public class BusinessException extends RuntimeException {
    private final int code;
    private final String error;
    private final HttpStatus httpStatus;

    public BusinessException(String error, String message, HttpStatus httpStatus) {
        super(message);
        this.code = httpStatus.value();
        this.error = error;
        this.httpStatus = httpStatus;
    }

    public static BusinessException userNotFound() {
        return new BusinessException("USER_NOT_FOUND", "用户不存在", HttpStatus.NOT_FOUND);
    }

    public static BusinessException balanceInsufficient() {
        return new BusinessException("BALANCE_INSUFFICIENT", "余额不足", HttpStatus.BAD_REQUEST);
    }
}
```

### 修复 3：错误响应 DTO

```java
@Getter
@Builder
public class ErrorResponse {
    private int code;
    private String error;
    private String message;
    private String detail;
    private String timestamp;
    private String path;
}
```

### 修复 4：Service 层异常上抛

**Before（错误：Controller 层捕获）**：
```java
@GetMapping("/{id}")
public UserDTO getUserById(@PathVariable Long id) {
    try {
        return userService.getUserById(id);
    } catch (Exception e) {
        return null;  // ❌ 错误：异常被吞掉
    }
}
```

**After（正确：异常上抛）**：
```java
@GetMapping("/{id}")
public ResponseEntity<UserDTO> getUserById(@PathVariable Long id) {
    return ResponseEntity.ok(userService.getUserById(id));
}
```

### 修复 5：异常日志记录

**Before（错误：异常未记录）**：
```java
public User getUserById(Long id) {
    if (user == null) {
        throw new RuntimeException("用户不存在");  // ❌ 异常未记录日志
    }
}
```

**After（正确：记录日志）**：
```java
public User getUserById(Long id) {
    User user = userMapper.selectById(id);
    if (user == null) {
        log.warn("用户不存在, id={}", id);
        throw BusinessException.userNotFound();
    }
    return user;
}
```

## 检查逻辑

### 0. 公共包检测（优先）

```
1. 检查项目根目录是否存在 .api-checker-config.json
2. 读取 exception.commonPackages 配置的包前缀列表
3. 扫描 Controller 的 import 语句
4. 如果 import 语句匹配配置的包前缀：
   - 识别为使用了公共异常架构包
   - 标记：跳过 @ControllerAdvice 存在性检查
   - 继续检查 Controller 是否正确上抛异常
5. 如果未匹配任何配置：
   - 执行完整的 @ControllerAdvice 检查
```

### 1. Controller → Service → Mapper 自动追踪

```
当入口是 Controller 时，自动追踪完整链路：

1. 解析 Controller 类
   - 扫描所有 @Autowired / @Resource 注入的字段
   - 根据类型推断关联的 Service 类

2. 解析 Service 类
   - 扫描所有 @Autowired / @Resource 注入的字段
   - 根据类型推断关联的 Mapper/Repository 类

3. 构建调用链路
   UserController
     └─> field: userService (UserService 类型)
           └─> field: userMapper (UserMapper 类型)

4. 按链路节点检查
   - Controller：异常上抛、try-catch
   - Service：异常定义、异常抛出、日志记录
   - Mapper：SQL 异常处理
```

### 2. @ControllerAdvice 检测

```
1. 扫描所有 @ControllerAdvice 或 @RestControllerAdvice 注解的类
2. 检查是否实现了以下 @ExceptionHandler：
   - BusinessException 或自定义业务异常
   - ValidationException 或 ConstraintViolationException
   - Exception（兜底）
3. 检查 @ExceptionHandler 的返回类型是否为 ResponseEntity
4. 检查响应格式是否包含必要字段
```

### 3. 异常分类检测

```
1. 扫描所有 Service 层方法
2. 检测是否直接 throw new RuntimeException()
3. 检测是否 throw 了未定义的自定义异常
4. 检测异常信息是否包含敏感词（password、token、sql 等）
```

### 4. 链路异常处理检测

```
基于追踪到的链路，检测以下问题：

Controller 层：
- 是否在 Controller 内 try-catch 吞异常
- 是否用 ApiResponse.error() 而非抛异常
- 异常是否上抛到 @ControllerAdvice

Service 层：
- 是否直接 throw RuntimeException（应用自定义异常）
- 异常是否有友好信息
- catch 后是否记录日志

Mapper 层：
- 是否有异常转换（SQLException → 自定义异常）
```

### 3. 异常处理位置检测

```
1. 扫描所有 Controller 方法
2. 检测方法内部是否有 try-catch 块
3. 如果有 try-catch，检测是否将异常上抛
4. 检测 catch 块内是否有日志记录
```
