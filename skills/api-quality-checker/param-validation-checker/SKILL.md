---
name: param-validation-checker
description: 检查 Spring Boot 接口的参数校验是否生效，包括 @Valid/@Validated 注解使用、JSR-380 注解规范、嵌套校验、分组校验等，支持精确到方法和类的粒度扫描。
---

# Spring Boot 参数校验检查（Param Validation Checker）

**description**: 检查 Spring Boot 接口的参数校验是否生效，包括 @Valid/@Validated 注解使用、JSR-380 注解规范、嵌套校验、分组校验等，支持精确到方法和类的粒度扫描。

## 快速使用

```
检查参数校验：UserController
检查参数校验：UserController.getUserById
检查参数校验：src/main/java/com/example/
```

## 目标

检查 Controller 和 Java Bean 是否遵循 **Java Bean Validation 规范（JSR-303/JSR-380）**，确保参数校验正确生效。

## 规范依据

- **JSR-303**：Bean Validation 1.0（Java EE 6）
- **JSR-349**：Bean Validation 1.1（Java EE 7）
- **JSR-380**：Bean Validation 2.0（Java EE 8 / Jakarta EE 8）

核心依赖：
```xml
<dependency>
    <groupId>jakarta.validation</groupId>
    <artifactId>jakarta.validation-api</artifactId>
    <version>3.0.0</version>
</dependency>
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>8.0.0.Final</version>
</dependency>
```

## 核心规则

### 两种入参模式

| 模式 | 特征 | 校验生效条件 |
|------|------|------------|
| **模式 A** | POST + `@RequestBody` + 实体 | 方法参数加 `@Valid` 或 `@Validated` |
| **模式 B** | GET/DELETE + 无 `@RequestBody` + 实体 | 方法参数加 `@Valid` 或 Controller 类加 `@Validated` |

### 校验生效判定

满足任意一条即代表开启校验：

1. 方法实体入参标注了 `@Valid`
2. 方法实体入参标注了 `@Validated`
3. Controller 类标注了 `@Validated`

## JSR-380 校验注解体系（Bean Validation 2.0）

### 空值校验

| 注解 | 支持类型 | 说明 |
|------|---------|------|
| `@NotNull` | 所有类型 | 值不能为 null |
| `@NotBlank` | CharSequence | 不能是空白字符（trim 后不为空） |
| `@NotEmpty` | Collection、Map、String、数组 | 不能为空（null 或 length/size 为 0） |

### 长度校验

| 注解 | 支持类型 | 说明 |
|------|---------|------|
| `@Size(min, max)` | Collection、Map、String、数组 | 限定范围 |
| `@Length(min, max)` | String（Hibernate Validator 专用） | 同 @Size |
| `@Email` | String | 邮箱格式 |
| `@Pattern(regex)` | String | 正则表达式匹配 |

### 数值校验

| 注解 | 支持类型 | 说明 |
|------|---------|------|
| `@Min(value)` | BigDecimal、BigInteger、String 数字 | 最小值 |
| `@Max(value)` | BigDecimal、BigInteger、String 数字 | 最大值 |
| `@DecimalMin(value)` | BigDecimal、BigInteger、String | 最小值（支持边界值） |
| `@DecimalMax(value)` | BigDecimal、BigInteger、String | 最大值（支持边界值） |
| `@Positive` | BigDecimal、BigInteger、String 数字 | 正数 |
| `@PositiveOrZero` | BigDecimal、BigInteger、String 数字 | 正数或零 |
| `@Negative` | BigDecimal、BigInteger、String 数字 | 负数 |
| `@NegativeOrZero` | BigDecimal、BigInteger、String 数字 | 负数或零 |

### 布尔校验

| 注解 | 支持类型 | 说明 |
|------|---------|------|
| `@AssertTrue` | boolean | 必须为 true |
| `@AssertFalse` | boolean | 必须为 false |

### 日期校验

| 注解 | 支持类型 | 说明 |
|------|---------|------|
| `@Past` | Date、LocalDate、LocalDateTime | 必须是过去时间 |
| `@PastOrPresent` | Date、LocalDate、LocalDateTime | 过去或当前时间 |
| `@Future` | Date、LocalDate、LocalDateTime | 必须是未来时间 |
| `@FutureOrPresent` | Date、LocalDate、LocalDateTime | 未来或当前时间 |

### 其他

| 注解 | 说明 |
|------|------|
| `@Null` | 值必须为 null |
| `@Valid` | 递归校验嵌套对象 |
| `@Validated` | 分组校验（Spring 专用） |
| `@Digits(integer, fraction)` | 限定整数位和小数位 |
| `@Range(min, max)` | Hibernate Validator，数值范围 |
| `@CreditCardNumber` | 信用卡号格式 |
| `@URL` | URL 格式 |
| `@UUID` | UUID 格式 |

## JSR 规范遵循检查

### 1. 注解与类型匹配（强制规则）

| 注解 | 合法类型 | 非法类型（报错） |
|------|---------|----------------|
| `@NotBlank` | String、CharSequence | Long、Integer、Boolean |
| `@NotEmpty` | String、Collection、Map、数组 | Long、Integer |
| `@Min/@Max` | BigDecimal、BigInteger、数值 String | String（非数字） |
| `@DecimalMin/Max` | BigDecimal、BigInteger、数值 String | - |
| `@Positive/@Negative` | BigDecimal、BigInteger、数值 String | - |
| `@Past/@Future` | Date、LocalDate、LocalDateTime | - |

### 2. 嵌套校验（@Valid 必须传递）

```java
// 父对象
public class OrderDTO {
    @Valid  // 必须加，否则嵌套对象不校验
    @NotNull
    private CustomerDTO customer;

    @Valid
    @NotEmpty
    private List<OrderItemDTO> items;
}
```

### 3. 分组校验（@Validated 分组）

```java
// 定义分组
public interface CreateGroup {}
public interface UpdateGroup {}

// 实体使用分组
public class UserDTO {
    @NotNull(groups = UpdateGroup.class)  // 只在更新时校验
    private Long id;

    @NotBlank(groups = {CreateGroup.class, UpdateGroup.class})  // 两组都校验
    private String name;
}

// Controller 指定分组
@PostMapping
public Result create(@Validated(CreateGroup.class) @RequestBody UserDTO dto) {}

@PutMapping("/{id}")
public Result update(@Validated(UpdateGroup.class) @RequestBody UserDTO dto) {}
```

### 4. 组序列（Group Order）

```java
@GroupSequence({Default.class, CreateGroup.class, UpdateGroup.class})
public class UserDTO {}

// 先校验 Default，再校验 CreateGroup
```

## 常见错误

| 错误类型 | 示例 | 问题 |
|---------|------|------|
| POST 缺 @Valid | `save(@RequestBody UserDTO dto)` | 校验不生效 |
| GET 缺校验 | `list(UserQuery query)` | 校验不生效 |
| 嵌套缺 @Valid | `save(@Valid @RequestBody ParentDTO dto)` | 嵌套对象校验不生效 |
| 数值类型误用 | `@NotBlank Long id` | JSR 规范不允许 |
| String 误用 @NotEmpty | `@NotEmpty String name` | 应用 @NotBlank |
| 缺少非空注解 | 必填字段无 `@NotBlank/@NotNull` | 无法校验必填 |
| 分组校验混用 | `@NotNull Long id` 无 groups | 在默认分组中也会校验 |

## 检查逻辑

```
1. 遍历所有 *Controller.java
2. 识别 @GetMapping/@PostMapping/@PutMapping/@DeleteMapping
3. 判断参数类型：
   ├── 模式 A：有 @RequestBody → 检查方法参数是否有 @Valid/@Validated
   └── 模式 B：无 @RequestBody → 检查方法参数或类是否有 @Valid/@Validated
4. 解析实体字段，检查非空注解完整性
5. 检查嵌套对象是否有 @Valid
```

## 检查项

### 1. @Valid / @Validated 使用检查

| 场景 | 当前代码 | 问题 | 正确代码 |
|------|---------|------|----------|
| POST + @RequestBody | `save(@RequestBody UserDTO dto)` | 缺 @Valid | `save(@Valid @RequestBody UserDTO dto)` |
| GET 实体 | `list(UserQuery query)` | 缺校验 | Controller 类加 `@Validated` 或方法加 `@Valid` |
| POST 嵌套 | `save(@Valid @RequestBody ParentDTO dto)` | 嵌套缺 @Valid | ParentDTO 内部 List/对象字段加 `@Valid` |

### 2. JSR 注解与类型匹配检查

| 字段类型 | 当前注解 | 问题 | 正确注解 |
|---------|---------|------|----------|
| `Long id` | `@NotBlank` | JSR 不允许 | `@NotNull` |
| `Integer status` | `@NotBlank` | JSR 不允许 | `@NotNull` |
| `String name` | `@NotNull` | 无法校验空白字符 | `@NotBlank` |
| `String email` | 无 | 缺少格式校验 | `@NotBlank @Email` |
| `List<Item> items` | `@NotNull` | 只校验不为 null | `@NotEmpty` |
| `BigDecimal price` | 无 | 缺少数值校验 | `@Positive` 或 `@DecimalMin("0")` |

### 3. 嵌套校验检查

| 检查项 | 错误示例 | 正确示例 |
|--------|---------|---------|
| 嵌套对象缺 @Valid | `private ChildDTO child;` | `@Valid private ChildDTO child;` |
| 嵌套集合缺 @Valid | `private List<Item> items;` | `@Valid private List<Item> items;` |
| 双重嵌套 | `private ParentDTO parent;` 内含 `ChildDTO child` | 父加 `@Valid`，子也需加 `@Valid` |

### 4. 分组校验检查

| 检查项 | 错误示例 | 正确示例 |
|--------|---------|---------|
| 混用默认分组 | `@NotNull Long id` | 分组明确：`@NotNull(groups = UpdateGroup.class)` |
| create 接口校验 id | `@NotNull Long id` | create 不应校验 id（自增） |
| update 接口不校验 id | 无 | update 必须校验 id |

## 使用方法

### 扫描粒度控制

| 输入类型 | 扫描范围 | 示例 |
|---------|---------|------|
| 具体方法名 | 只扫描该方法的入参 Bean | `检查参数校验：UserController.getUserById` |
| Controller 类名 | 扫描整个类 + 关联的入参 Bean | `检查参数校验：UserController` |
| Controller 目录 | 扫描所有类 + 关联的入参 Bean | `检查参数校验：com/example/controller/` |
| 项目路径 | 扫描整个项目 | `检查参数校验：src/main/java/` |

### 具体方法名扫描（精确扫描）

```
检查参数校验：UserController.getUserById
```

- 只分析 `getUserById` 方法的参数
- 检查该方法对应的 RequestBody/QueryBean 的校验注解
- 适用场景：修复特定接口时快速定位

### Controller 类扫描（完整扫描）

```
检查参数校验：UserController
```

- 扫描 `UserController` 所有接口方法
- 分析每个方法的入参类型（@RequestBody 实体、QueryBean、PathVariable 等）
- 扫描所有关联的 DTO/Request/Query Bean
- 适用场景：检查整个 Controller 的校验规范性

### Controller 目录扫描

```
检查参数校验：src/main/java/com/example/controller/
```

- 扫描目录下所有 Controller
- 扫描所有关联的入参 Bean
- 生成完整的校验问题报告

### 项目扫描

```
检查参数校验：src/main/java/
```

- 扫描整个项目的 Controller 和 Bean
- 生成完整的问题报告和统计

### 公共架构包配置

如果项目使用了公共的校验相关架构包（如公司级 common 包），可在项目根目录创建 `.api-checker-config.json` 配置：

```json
{
  "validation": {
    "commonPackages": [
      "com.company.platform.validation",
      "com.company.common.dto"
    ],
    "baseBeans": [
      "BaseRequest",
      "BaseDTO",
      "AbstractValidatingDTO"
    ],
    "customValidators": [
      "PhoneValidator",
      "IdCardValidator"
    ]
  }
}
```

**配置说明**：

| 字段 | 说明 | 示例 |
|------|------|------|
| `commonPackages` | 公共包的包名前缀 | `com.company.platform.validation` |
| `baseBeans` | 基类/基DTO类名 | `BaseRequest`、`BaseDTO` |
| `customValidators` | 自定义校验器类名 | `PhoneValidator` |

**Skill 行为**：

| 场景 | 有配置时行为 |
|------|-------------|
| Bean 继承自基类 | 检查基类字段的校验注解 |
| import 公共包 | 识别为公共 Bean，跳过冗余检查 |
| 自定义校验器 | 检查校验器实现是否符合 JSR-380 |

## 输出格式

```
## Spring 参数校验检查报告

### 检查结果：❌ 不通过 (5 个问题)

#### 1. @Valid/@Validated 使用问题

| 文件 | 方法 | 场景 | 问题 | 修复建议 |
|------|------|------|------|----------|
| UserController.java | save | POST+@RequestBody | 缺少 @Valid | 添加 `@Valid @RequestBody` |
| OrderController.java | list | GET实体 | 缺少校验 | Controller 类加 `@Validated` |
| ProductController.java | update | POST嵌套 | 嵌套对象缺 @Valid | 字段加 `@Valid private ChildDTO child;` |

#### 2. JSR 注解与类型匹配问题

| 文件 | 实体类 | 字段 | 类型 | 当前注解 | 违反 JSR 规则 | 正确注解 |
|------|--------|------|------|---------|--------------|----------|
| UserDTO.java | UserDTO | id | Long | @NotBlank | @NotBlank 只能用于 CharSequence | @NotNull |
| UserDTO.java | UserDTO | status | Integer | @NotBlank | @NotBlank 只能用于 CharSequence | @NotNull |
| OrderDTO.java | OrderDTO | name | String | @NotNull | 无法校验空白字符 | @NotBlank |

#### 3. 嵌套校验问题

| 文件 | 实体类 | 字段 | 类型 | 问题 | 修复 |
|------|--------|------|------|------|------|
| OrderDTO.java | OrderDTO | customer | CustomerDTO | 嵌套对象缺 @Valid | 添加 `@Valid` |
| OrderDTO.java | OrderDTO | items | List<ItemDTO> | 嵌套集合缺 @Valid | 添加 `@Valid` |

#### 4. 分组校验问题

| 文件 | 方法 | 问题 | 建议 |
|------|------|------|------|
| UserController.java | create | create 时校验了 id（自增字段不应校验） | `@NotNull(groups = UpdateGroup.class)` |
| UserController.java | update | update 时未校验 id | 添加 `@NotNull(groups = UpdateGroup.class)` |

---

### 通过的检查项 ✅

- [x] POST+@RequestBody 接口正确使用 @Valid
- [x] GET 接口启用了参数校验
- [x] 非空注解类型符合 JSR 规范
- [x] 嵌套对象正确传递 @Valid
- [x] 分组校验使用正确
```

## 修复代码示例

### 修复 1：POST + @RequestBody 缺 @Valid

**Before（校验不生效）**：
```java
@PostMapping("/save")
public Result save(@RequestBody UserDTO dto) {
    return Result.success();
}
```

**After（校验生效）**：
```java
@PostMapping("/save")
public Result save(@Valid @RequestBody UserDTO dto) {
    return Result.success();
}
```

### 修复 2：GET 接口缺校验

**方案 A：Controller 类加 @Validated（推荐）**：
```java
@RestController
@RequestMapping("/user")
@Validated  // 全局开启
public class UserController {

    @GetMapping("/list")
    public Result list(UserQuery query) {  // 无需方法上加 @Valid
        return Result.success();
    }
}
```

**方案 B：方法参数加 @Valid**：
```java
@GetMapping("/list")
public Result list(@Valid UserQuery query) {  // 只对这个方法生效
    return Result.success();
}
```

### 修复 3：JSR 注解与类型匹配

**Before（违反 JSR 规范）**：
```java
public class UserDTO {
    @NotBlank  // ❌ 错误：Long 不能用 @NotBlank
    private Long id;

    @NotBlank  // ❌ 错误：Integer 不能用 @NotBlank
    private Integer status;

    @NotNull  // ⚠️ 不规范：String 应用 @NotBlank
    private String name;
}
```

**After（遵循 JSR 规范）**：
```java
public class UserDTO {
    @NotNull  // ✅ Long 用 @NotNull
    private Long id;

    @NotNull  // ✅ Integer 用 @NotNull
    private Integer status;

    @NotBlank  // ✅ String 用 @NotBlank
    private String name;

    @Email  // ✅ 字符串格式校验
    private String email;

    @Positive  // ✅ BigDecimal/数值用 @Positive
    private BigDecimal price;
}
```

### 修复 4：嵌套对象缺 @Valid

**Before（嵌套校验不生效）**：
```java
public class ParentDTO {
    private String name;
    private ChildDTO child;  // 嵌套对象无 @Valid
    private List<ChildDTO> children;  // 集合也无 @Valid
}
```

**After（嵌套校验生效）**：
```java
public class ParentDTO {
    @NotBlank
    private String name;

    @Valid  // ✅ 嵌套对象必须加 @Valid
    private ChildDTO child;

    @Valid  // ✅ 集合内对象也要加 @Valid
    @NotEmpty
    private List<ChildDTO> children;
}
```

### 修复 5：分组校验

**定义分组接口**：
```java
public interface CreateGroup {}
public interface UpdateGroup {}
```

**实体使用分组**：
```java
public class UserDTO {
    @Null(groups = CreateGroup.class)  // ✅ create 时必须为 null
    @NotNull(groups = UpdateGroup.class)  // ✅ update 时必须不为 null
    private Long id;

    @NotBlank(groups = {CreateGroup.class, UpdateGroup.class})  // ✅ 两组都校验
    private String name;

    @Email(groups = CreateGroup.class)  // ✅ 只在 create 时校验邮箱格式
    private String email;
}
```

**Controller 指定分组**：
```java
@RestController
@RequestMapping("/api/v1/users")
@Validated
public class UserController {

    @PostMapping
    public ResponseEntity<UserDTO> create(
            @Validated(CreateGroup.class) @RequestBody UserDTO dto) {
        return ResponseEntity.status(201).body(userService.create(dto));
    }

    @PutMapping("/{id}")
    public ResponseEntity<UserDTO> update(
            @PathVariable Long id,
            @Validated(UpdateGroup.class) @RequestBody UserDTO dto) {
        dto.setId(id);
        return ResponseEntity.ok(userService.update(dto));
    }
}
```

### 修复 6：完整 JSR 规范示例

```java
@RestController
@RequestMapping("/api/v1/users")
@Validated
public class UserController {

    @PostMapping
    public ResponseEntity<UserDTO> create(
            @Valid @RequestBody CreateUserRequest request) {
        return ResponseEntity.status(201).body(userService.create(request));
    }

    @GetMapping("/{id}")
    public ResponseEntity<UserDTO> getById(
            @PathVariable @NotNull Long id) {
        return ResponseEntity.ok(userService.getById(id));
    }

    @GetMapping("/list")
    public ResponseEntity<List<UserDTO>> list(
            @Valid UserQuery query) {
        return ResponseEntity.ok(userService.list(query));
    }
}
```

```java
@GroupSequence({Default.class, CreateGroup.class})
public class CreateUserRequest {
    @NotBlank(message = "用户名不能为空", groups = CreateGroup.class)
    private String username;

    @NotBlank(message = "邮箱不能为空", groups = CreateGroup.class)
    @Email(message = "邮箱格式不正确", groups = CreateGroup.class)
    private String email;

    @NotNull(message = "年龄不能为空", groups = CreateGroup.class)
    @Min(value = 0, message = "年龄不能为负数", groups = CreateGroup.class)
    @Max(value = 150, message = "年龄不能超过150", groups = CreateGroup.class)
    private Integer age;

    @Valid
    @NotNull(message = "地址信息不能为空", groups = CreateGroup.class)
    private AddressDTO address;
}
```

```java
public class AddressDTO {
    @NotBlank(message = "省份不能为空", groups = CreateGroup.class)
    private String province;

    @NotBlank(message = "城市不能为空", groups = CreateGroup.class)
    private String city;

    @Valid  // ✅ 双重嵌套也要加 @Valid
    private DistrictDTO district;
}
```

## AST 扫描要点

### 1. Controller 扫描

```java
// 检查类是否有 @Validated
hasClassAnnotation(Validated.class)

// 检查方法参数
for (MethodParameter param : method.getParameters()) {
    // 检查是否有 @Valid 或 @Validated
    param.hasAnnotation(Valid.class)
    param.hasAnnotation(Validated.class)
    // 检查是否有 @RequestBody
    param.hasAnnotation(RequestBody.class)
}
```

### 2. 实体字段扫描

```java
for (Field field : entity.getDeclaredFields()) {
    // 检查非空注解
    field.hasAnnotation(NotNull.class)
    field.hasAnnotation(NotBlank.class)
    field.hasAnnotation(NotEmpty.class)
    // 检查嵌套 @Valid
    field.hasAnnotation(Valid.class)
    // 检查字段类型
    field.getType() == Long.class
}
```

### 3. 组合校验规则

```java
// @NotBlank 只能用于
Set<Class> validForNotBlank = {
    String.class,
    CharSequence.class
}

// @NotNull 可用于任何类型
// @NotEmpty 用于 Collection、Map、String、数组
```
