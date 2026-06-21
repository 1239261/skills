---
name: transaction-checker
description: 检查 Spring @Transactional 事务使用是否正确，包括传播行为、隔离级别、rollbackFor 配置、事务失效场景（内部调用、private 方法）、单表 vs 多表操作判断等，支持 Controller 入口自动追踪 Service/Mapper 链路。
---

# Spring 事务管理检查（Transaction Checker）

**description**: 检查 Spring @Transactional 事务使用是否正确，包括传播行为、隔离级别、rollbackFor 配置、事务失效场景（内部调用、private 方法）、单表 vs 多表操作判断等，支持 Controller 入口自动追踪 Service/Mapper 链路。

## 快速使用

```
检查事务管理：UserController
检查事务管理：UserController.createUser
检查事务管理：src/main/java/com/example/
```

## 目标

检查 @Transactional 的使用是否正确，确保数据一致性和事务生效。

## 核心规则

### 1. 事务传播行为

| 传播行为 | 说明 | 适用场景 |
|---------|------|---------|
| REQUIRED（默认） | 加入当前事务，无则创建新事务 | 绝大多数场景 |
| REQUIRES_NEW | 创建新事务，挂起当前事务 | 日志记录等独立操作 |
| NESTED | 嵌套事务（savepoint） | 部分操作独立回滚 |
| SUPPORTS | 跟随当前事务，无则无事务 | 只读查询 |
| MANDATORY | 必须在事务中，不存在则抛异常 | 强制事务 |
| NOT_SUPPORTED | 无事务执行，挂起当前事务 | 异步操作 |
| NEVER | 无事务，不存在则正常执行 | 禁止事务 |

### 2. 事务隔离级别

| 隔离级别 | 说明 | 问题 |
|---------|------|------|
| DEFAULT（默认） | 使用数据库默认 | - |
| READ_UNCOMMITTED | 脏读、不可重复读、幻读 | 可能读到未提交数据 |
| READ_COMMITTED | 不可重复读、幻读 | 只能读到已提交数据 |
| REPEATABLE_READ | 幻读 | 同一事务内多次读取一致 |
| SERIALIZABLE | 完全串行 | 性能最差 |

### 3. 事务失效场景

| 场景 | 问题 | 解决方案 |
|------|------|---------|
| 内部方法调用 | @Transactional 在同一个类内调用不生效 | 注入自身或使用 AOP |
| private 方法 | 事务基于代理，private 方法无法代理 | 改为 public 方法 |
| rollbackFor 缺失 | 默认只回滚 RuntimeException | 指定 rollbackFor = Exception.class |
| 异常被 catch | 异常被捕获未上抛，事务无法回滚 | catch 后重新 throw 或 TransactionAspectSupport |
| 多数据源 | 未指定 transactionManager | 使用 @Transactional(transactionManager = "xxx") |
| 默认只读 | @Transactional 无事务，select 可能复用连接 | 使用 readOnly = true 优化 |

### 4. @Transactional 注解位置

| 位置 | 正确性 | 说明 |
|------|--------|------|
| Controller | ⚠️ 不推荐 | Controller 一般不涉及数据操作 |
| Service | ✅ 推荐 | 业务逻辑应在 Service 层 |
| ServiceImpl | ✅ 推荐 | 实现类上或方法上 |
| Mapper | ❌ 不推荐 | 过于细粒度 |

## 检查项

### 1. @Transactional 使用判断规则

| 场景 | 是否需要 | 原因 |
|------|---------|------|
| **纯查询（单条 SELECT）** | ❌ 不需要 | 只读操作，无需回滚，数据库保证原子性 |
| **单条增删改** | ❌ 不需要 | MySQL 自动提交机制保证原子性 |
| **多条写操作组合** | ✅ 需要 | 保证原子性，一条失败全部回滚 |
| **需要回滚业务逻辑** | ✅ 需要 | 数据库+非数据库操作需统一回滚 |
| **批量查询需强一致性** | ✅ 需要 readOnly=true | 保证隔离性，看到一致快照 |
| **读写分离读从库** | ✅ 需要 readOnly=true | 确保读到从库一致数据 |
| **主操作成功后异步通知** | ❌ 不需要 | 主操作已提交，异步失败不影响数据 |

### 2. MySQL 自动提交机制

```
autocommit=1（默认）：
每条独立 SQL → 自动开启 → 执行 → 自动提交
执行失败 → 自动回滚
```

**单条 SQL 的原子性由数据库保证，无需 @Transactional**

### 3. 需要 @Transactional 的场景

```
多条写操作必须在一个事务内：
@Transactional
public void createOrder() {
    orderMapper.insert(order);   // 成功
    stockMapper.decrease(item); // 失败 → 回滚 order.insert
}
```

### 4. rollbackFor 配置

| 场景 | 配置 |
|------|------|
| 多表操作 | `@Transactional(rollbackFor = Exception.class)` |
| 批量操作 | `@Transactional(rollbackFor = Exception.class)` |
| 单表单操作（可不加） | 可省略 rollbackFor |

### 2. 传播行为检查

| 检查项 | 问题 | 建议 |
|--------|------|------|
| 默认 REQUIRED 不当 | 多操作共用事务导致部分失败 | 需要独立回滚用 REQUIRES_NEW |
| 日志操作在事务内 | 日志应独立，用 REQUIRES_NEW | 日志方法单独加 REQUIRES_NEW |
| 远程调用在事务内 | 远程失败导致本地回滚 | 远程调用前提交事务 |

### 3. 隔离级别检查

| 检查项 | 问题 | 建议 |
|--------|------|------|
| 未指定隔离级别 | 使用数据库默认 | 高并发场景指定 READ_COMMITTED |
| 脏读风险 | READ_UNCOMMITTED | 金融场景用 SERIALIZABLE |
| 性能问题 | SERIALIZABLE 滥用 | 非必要不用 |

### 4. 事务失效检查

| 检查项 | 问题 | 修复 |
|--------|------|------|
| 内部方法调用 | AOP 代理限制，同类调用不生效 | 注入自身调用 或 拆分到另一个类 |
| private 方法 | 代理无法拦截 private | 改为 public |
| catch 后未 throw | 异常被吞掉 | catch 内重新 throw 或手动回滚 |

### 5. 多数据源检查

| 检查项 | 问题 | 建议 |
|--------|------|------|
| 未指定 transactionManager | 可能使用默认数据源 | 使用 transactionManager = "xxxDataSource" |
| @Transactional 在 Mapper | 多数据源切换无效 | 在 Service 层使用 |

## 使用方法

### 入口类型与自动追踪

| 入口类型 | 示例 | 自动追踪 |
|---------|------|---------|
| Controller 类 | `检查事务管理：UserController` | Controller → Service → Mapper |
| Controller#方法 | `检查事务管理：UserController.createUser` | 精确到方法链路 |
| Service 类 | `检查事务管理：UserService` | 只检查 Service |
| 目录/项目 | `检查事务管理：src/main/java/` | 检查所有 |

### Controller 入口（自动追踪完整链路）

```
检查事务管理：UserController
```

**自动追踪链路**：
```
UserController
  └─> @Autowired UserService
        └─> @Autowired UserMapper
```

**检查范围**：
1. **Service 层**：@Transactional 使用、传播行为、rollbackFor
2. **Service → Mapper**：多表操作识别
3. **事务失效场景**：内部调用、private 方法

### Controller#方法 入口（精确链路）

```
检查事务管理：UserController.createUser
检查事务管理：UserController.updateUser
```

**只检查指定方法**：
```
UserController.createUser()
  └─> UserService.createUser()
        └─> UserMapper.insert()
```

### Service 入口（直接检查）

```
检查事务管理：UserService
检查事务管理：src/main/java/com/example/service/
```

### 目录/项目入口

```
检查事务管理：src/main/java/com/example/
检查事务管理：src/main/java/
```

### 公共架构包配置

如果项目使用了公共的事务处理架构包（如公司级 common 包），可在项目根目录创建 `.api-checker-config.json` 配置：

```json
{
  "transaction": {
    "commonPackages": [
      "com.company.platform.transaction",
      "com.company.common.service"
    ],
    "baseServices": [
      "BaseService",
      "AbstractService"
    ],
    "transactionManagers": [
      "transactionManager",
      "dsTransactionManager",
      "xaTransactionManager"
    ]
  }
}
```

**配置说明**：

| 字段 | 说明 | 示例 |
|------|------|------|
| `commonPackages` | 公共包的包名前缀 | `com.company.platform.transaction` |
| `baseServices` | 基类 Service 类名 | `BaseService`、`AbstractService` |
| `transactionManagers` | 事务管理器 Bean 名称 | `transactionManager` |

**Skill 行为**：

| 场景 | 有配置时行为 |
|------|-------------|
| Service 继承自基类 | 检查基类的事务配置 |
| import 公共包 | 识别为公共 Service，跳过冗余检查 |
| 指定事务管理器 | 检查 @Transactional 是否正确指定 |

## 输出格式

```
## Spring 事务管理检查报告

### 检查结果：❌ 不通过 (5 个问题)

#### 1. @Transactional 使用问题

| 文件 | 方法 | 问题 | 建议 |
|------|------|------|------|
| UserService.java | createUser | 缺少事务 | 添加 @Transactional |
| OrderService.java | batchInsert | 批量操作缺少事务 | 添加 @Transactional(rollbackFor = Exception.class) |
| UserService.java | getUserById | 只读操作有事务 | 添加 readOnly = true |

#### 2. 传播行为问题

| 文件 | 方法 | 问题 | 建议 |
|------|------|------|------|
| OrderService.java | createOrder + log | 日志在事务内 | 日志方法单独加 REQUIRES_NEW |
| PaymentService.java | pay + updateBalance | 远程调用在事务内 | 远程调用前添加事务提交 |

#### 3. 事务失效问题

| 文件 | 方法 | 问题 | 建议 |
|------|------|------|------|
| UserService.java | updateUser (内部调用) | 内部方法调用事务不生效 | 注入自身后调用或拆分到别的类 |
| UserService.java | private method | private 方法事务不生效 | 改为 public 方法 |

#### 4. rollbackFor 问题

| 文件 | 方法 | 问题 | 建议 |
|------|------|------|------|
| OrderService.java | create | 业务异常不回滚 | 添加 rollbackFor = Exception.class |
| ProductService.java | reduceStock | 数据库异常不回滚 | 添加 rollbackFor = Exception.class |

#### 5. 日志记录问题

| 文件 | 方法 | 问题 | 建议 |
|------|------|------|------|
| UserService.java | updateUser | 异常未记录日志 | 添加 log.error("更新失败", e) |

---

### 通过的检查项 ✅

- [x] @Transactional 使用位置正确
- [x] 传播行为选择合理
- [x] rollbackFor 指定完整
- [x] 无事务失效场景
- [x] 多数据源配置正确
```

## 修复代码示例

### 修复 1：单表操作（无需事务）

```java
@Service
public class UserService {

    @Autowired
    private UserMapper userMapper;

    // 单表插入，无需 @Transactional（MySQL 自动提交）
    public User createUser(User user) {
        validateUser(user);
        userMapper.insert(user);
        // 异步通知，主操作已提交，通知失败不影响数据
        asyncService.sendWelcomeNotification(user.getId());
        return user;
    }

    // 单表查询，无事务（可读从库时加 readOnly）
    @Transactional(readOnly = true)
    public User getUserById(Long id) {
        return userMapper.selectById(id);
    }

    // 单表更新，无需事务（MySQL 自动提交）
    public void updateUser(User user) {
        validateUser(user);
        userMapper.update(user);
    }

    // 单表删除，无需事务（MySQL 自动提交）
    public void deleteUser(Long id) {
        userMapper.deleteById(id);
    }
}
```

### 修复 2：多操作原子性（需要事务）

```java
@Service
public class OrderService {

    @Autowired
    private OrderMapper orderMapper;

    @Autowired
    private StockMapper stockMapper;

    // 多操作必须加事务，保证原子性
    @Transactional(rollbackFor = Exception.class)
    public void createOrder(Order order) {
        orderMapper.insert(order);      // 成功
        stockMapper.decrease(order);    // 成功
        // 任一失败则全部回滚
    }
}
```

### 修复 2：独立事务（REQUIRES_NEW）

```java
@Service
public class OrderService {

    @Autowired
    private OrderMapper orderMapper;

    @Autowired
    private LogService logService;  // 注入而非内部调用

    @Transactional(rollbackFor = Exception.class)
    public Order createOrder(Order order) {
        Order created = orderMapper.insert(order);
        
        // 日志需要独立事务，用 REQUIRES_NEW
        logService.logOrderCreate(order.getId());
        
        return created;
    }
}
```

```java
@Service
public class LogService {

    @Autowired
    private LogMapper logMapper;

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void logOrderCreate(Long orderId) {
        Log log = new Log();
        log.setAction("CREATE_ORDER");
        log.setOrderId(orderId);
        logMapper.insert(log);
    }
}
```

### 修复 3：解决内部方法调用事务失效

**Before（事务不生效）**：
```java
@Service
public class UserService {

    public void updateUser(User user) {
        validateUser(user);  // 内部调用，事务不生效
        saveUser(user);
    }

    @Transactional
    private void saveUser(User user) {
        userMapper.update(user);
    }
}
```

**After（注入自身调用）**：
```java
@Service
public class UserService {

    @Autowired
    private UserService self;  // 注入自身

    public void updateUser(User user) {
        validateUser(user);
        self.saveUser(user);  // 通过代理调用，事务生效
    }

    @Transactional(rollbackFor = Exception.class)
    public void saveUser(User user) {
        userMapper.update(user);
    }
}
```

**After（拆分到另一个类）**：
```java
@Service
public class UserService {

    @Autowired
    private UserDao userDao;  // 另一个类

    public void updateUser(User user) {
        validateUser(user);
        userDao.update(user);  // 跨类调用，事务生效
    }
}
```

### 修复 4：catch 后手动回滚

**Before（事务不回滚）**：
```java
@Transactional(rollbackFor = Exception.class)
public void updateUser(User user) {
    try {
        validateUser(user);
        userMapper.update(user);
    } catch (Exception e) {
        // 异常被吞掉，事务不会回滚 ❌
    }
}
```

**After（手动回滚）**：
```java
@Transactional(rollbackFor = Exception.class)
public void updateUser(User user) {
    try {
        validateUser(user);
        userMapper.update(user);
    } catch (Exception e) {
        log.error("更新用户失败", e);
        throw e;  // 重新抛出，事务回滚 ✅
    }
}
```

**After（使用 TransactionAspectSupport）**：
```java
@Transactional(rollbackFor = Exception.class)
public void updateUser(User user) {
    try {
        validateUser(user);
        userMapper.update(user);
    } catch (Exception e) {
        log.error("更新用户失败", e);
        TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
        throw new BusinessException("更新失败");
    }
}
```

### 修复 5：指定事务管理器（多数据源）

```java
@Service
public class OrderService {

    @Transactional(transactionManager = "orderDataSourceTransactionManager", 
                  rollbackFor = Exception.class)
    public Order createOrder(Order order) {
        orderMapper.insert(order);
        return order;
    }

    @Transactional(transactionManager = "userDataSourceTransactionManager",
                  readOnly = true)
    public User getUser(Long userId) {
        return userMapper.selectById(userId);
    }
}
```

### 修复 6：完整示例

```java
@Service
@Transactional(rollbackFor = Exception.class)
public class UserService {

    @Autowired
    private UserMapper userMapper;

    @Autowired
    private RoleMapper roleMapper;

    @Autowired
    private LogService logService;

    @Transactional(readOnly = true)
    public UserDTO getUserById(Long id) {
        User user = userMapper.selectById(id);
        if (user == null) {
            throw BusinessException.userNotFound();
        }
        return convertToDTO(user);
    }

    @Transactional(readOnly = true)
    public List<UserDTO> listUsers(UserQuery query) {
        return userMapper.selectList(query);
    }

    @Transactional(rollbackFor = Exception.class)
    public UserDTO createUser(CreateUserRequest request) {
        // 业务校验
        if (userMapper.existsByUsername(request.getUsername())) {
            throw BusinessException.usernameExists();
        }

        User user = new User();
        user.setUsername(request.getUsername());
        user.setEmail(request.getEmail());
        userMapper.insert(user);

        // 独立日志事务
        logService.logUserCreate(user.getId());

        return convertToDTO(user);
    }

    @Transactional(rollbackFor = Exception.class)
    public UserDTO updateUser(Long id, UpdateUserRequest request) {
        User user = userMapper.selectById(id);
        if (user == null) {
            throw BusinessException.userNotFound();
        }

        user.setEmail(request.getEmail());
        userMapper.update(user);

        return convertToDTO(user);
    }

    @Transactional(rollbackFor = Exception.class)
    public void deleteUser(Long id) {
        User user = userMapper.selectById(id);
        if (user == null) {
            throw BusinessException.userNotFound();
        }

        // 删除关联
        roleMapper.deleteByUserId(id);
        userMapper.deleteById(id);

        logService.logUserDelete(id);
    }

    private UserDTO convertToDTO(User user) {
        UserDTO dto = new UserDTO();
        dto.setId(user.getId());
        dto.setUsername(user.getUsername());
        dto.setEmail(user.getEmail());
        return dto;
    }
}
```

## 检查逻辑

### 0. Controller → Service → Mapper 自动追踪

```
当入口是 Controller 时，自动追踪完整链路：

1. 解析 Controller 类
   - 扫描所有 @Autowired / @Resource 注入的字段
   - 根据类型推断关联的 Service 类

2. 解析 Service 类
   - 扫描所有 @Autowired / @Resource 注入的字段
   - 根据类型推断关联的 Mapper 类

3. 构建调用链路
   UserController
     └─> field: userService (UserService 类型)
           └─> field: userMapper (UserMapper 类型)

4. 链路上的事务检查
   - Service 方法的 @Transactional 配置
   - 多表操作识别（链路上有多个 Mapper）
   - 事务失效场景检测
```

### 1. @Transactional 检测

```
1. 扫描链路上的 @Transactional 注解的方法和类
2. 检测参数：
   - rollbackFor 是否指定（默认只回滚 RuntimeException）
   - readOnly 是否正确（查询用 readOnly = true）
   - propagation 是否正确
   - isolation 是否指定
3. 检测方法修饰符（private 方法事务不生效）
```

### 2. 事务生效检测

```
1. 扫描同类内部方法调用
2. 检测是否存在 private 方法有 @Transactional
3. 检测 catch 块内是否吞掉异常
```

### 3. 传播行为检测

```
1. 检测是否存在 REQUIRES_NEW 用于日志等独立操作
2. 检测是否存在远程调用在事务内
3. 检测嵌套事务使用是否合理
```

### 4. 多数据源检测

```
1. 检测是否存在多数据源配置
2. 检测 @Transactional 是否指定 transactionManager
3. 检测 Mapper 层是否有 @Transactional（应上移到 Service）
```
