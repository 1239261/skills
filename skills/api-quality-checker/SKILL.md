---
name: api-quality-checker
description: 输入 Controller 入口，自动串联执行 RESTful 规范检查、参数校验检查、异常处理检查、事务管理检查，输出完整的 API 质量报告。
---

# API 质量综合检查（API Quality Checker）

**description**: 输入 Controller 入口，自动串联执行 RESTful 规范检查、参数校验检查、异常处理检查、事务管理检查，输出完整的 API 质量报告。

## 快速使用

```
检查 API 质量：UserController
检查 API 质量：UserController.getUserById
检查 API 质量：src/main/java/com/example/
```

## 增量检查（节省 Token）

代码调整后，使用 `--增量` 参数只检查改动的文件：

```
检查 API 质量：UserController --增量
```

| 参数 | 说明 | Token 节省 |
|------|------|-----------|
| `--增量` | 只检查指定的 Controller 及关联链路 | 约 80% |
| `--diff` | 对比上次报告，只输出差异 | 约 60% |
| `--skill:exception` | 只检查异常处理（可选：exception/validation/transaction/rest） | 约 75% |

### 报告存储

报告存储由配置文件决定，修改 `api-quality-checker-config.json`：

```json
"report": {
  "defaultPath": "./api-quality-reports",
  "namingPattern": "{target}-{date}-{time}",
  "autoSave": false,
  "retention": {
    "enabled": true,
    "days": 7,
    "autoCleanup": true
  }
}
```

| 配置项 | 说明 | 示例 |
|--------|------|------|
| `defaultPath` | 报告存放目录，**相对于工作空间**，非本地路径 | `"./api-quality-reports"` |
| `namingPattern` | 命名格式，支持 {target} {date} {time} | `"{target}-{date}-{time}"` |
| `autoSave` | 是否自动保存，`false` 需手动加 `--报告` | `true` 或 `false` |
| `retention.enabled` | 是否启用过期清理 | `true` 或 `false` |
| `retention.days` | 保留天数 | `7` |
| `retention.autoCleanup` | 是否自动清理过期报告 | `true` 或 `false` |

**重要说明**：
- `defaultPath` 是**工作空间路径**，不是本地机器路径
- `"./api-quality-reports"` 代表工作空间根目录下的 `api-quality-reports` 文件夹
- 如需使用本地路径，先将报告下载到本地

**一天多份报告处理**：
- 命名模式 `{target}-{date}-{time}` 自动生成唯一文件名
- 示例：`UserController-20240115-143052.md`
- 如有重复，自动在文件名后追加序号：`UserController-20240115-143052-2.md`

**报告过期清理**：
- 启用后，每次生成报告时自动清理超过 7 天的报告
- `autoCleanup: true` 自动清理，`false` 只提醒不清理

### 使用示例

| 场景 | 命令 | 说明 |
|------|------|------|
| 首次全面检查 | `检查 API 质量：src/main/java/com/example/` | 完整检查 |
| 改了一个 Controller | `检查 API 质量：UserController --增量` | 只检查改动 |
| 改完验证修复 | `检查 API 质量：UserController --diff` | 对比上次报告 |
| 只想看异常问题 | `检查 API 质量：UserController --skill:exception` | 单一 Skill |
| 指定报告路径 | `检查 API 质量：UserController --报告:./report.md` | 保存到指定文件 |

### 推荐工作流

```
1. 首次检查（完整报告）
   检查 API 质量：src/main/java/com/example/
   → 输出完整报告，手动保存

2. 调整代码后（增量检查）
   检查 API 质量：UserController --增量
   → 只检查 UserController 及链路

3. 修复后验证（对比报告）
   检查 API 质量：UserController --diff
   → 自动对比 ./api-quality-reports/ 下同名报告
```

## 修改配置

Skill 配置在 `api-quality-checker-config.json` 文件中，下载后需要修改：

```bash
# 1. 打开配置文件
vi api-quality-checker-config.json

# 2. 修改公共包配置（根据你项目的实际包名）
{
  "exception": {
    "commonPackages": ["com.yourcompany.platform.exception"],
    "businessExceptions": ["YourBusinessException"]
  }
}

# 3. 保存后即可使用
```

**必须修改的项**：
| 配置项 | 说明 | 示例 |
|--------|------|------|
| `exception.commonPackages` | 公司异常包前缀 | `com.yourcompany.exception` |
| `exception.businessExceptions` | 业务异常类名 | `YourBusinessException` |
| `validation.commonPackages` | 公司校验包前缀 | `com.yourcompany.dto` |
| `transaction.commonPackages` | 公司事务包前缀 | `com.yourcompany.service` |

## 功能概述

一次检查，4 项联动：

| 检查项 | Skill | 检查内容 |
|--------|-------|---------|
| REST 规范 | rest-api-checker | URI 命名、HTTP 方法、状态码、HATEOAS、混合风格 |
| 参数校验 | param-validation-checker | @Valid/@Validated、JSR-380 注解、嵌套校验 |
| 异常处理 | exception-handler-checker | @ControllerAdvice、业务异常、异常上抛、日志 |
| 事务管理 | transaction-checker | @Transactional、传播行为、rollbackFor |
| 代码审查 | code-review-checker | 代码风格、命名规范、注释质量、复杂度、可维护性、安全与性能 |

## 自动追踪链路

```
输入：UserController
追踪链路：
  UserController
    └─> UserService
          └─> UserMapper

检查范围：
  ├─ Controller：REST 规范、参数校验、异常上抛
  ├─ Service：异常定义、事务配置、异常上抛
  └─ Mapper：SQL 异常处理（由 Service 链路触发）
```

## 输入格式

| 输入类型 | 示例 | 检查范围 |
|---------|------|---------|
| Controller 类 | `检查 API 质量：UserController` | Controller + 追踪链路 |
| Controller#方法 | `检查 API 质量：UserController.createUser` | 精确方法链路 |
| 目录 | `检查 API 质量：src/main/java/com/example/` | 目录下所有 |
| 项目 | `检查 API 质量：src/main/java/` | 整个项目 |

## 输出格式

```
## API 质量综合检查报告

**检查目标**：UserController
**追踪链路**：UserController → UserService → UserMapper
**检查时间**：2024-01-15 10:30:00

═══════════════════════════════════════════════════════

### 一、REST 规范检查 ✅ 通过 (2 项建议优化)

#### 1.1 URI 规范
| 行号 | 方法 | 路径 | 问题 | 建议 |
|------|------|------|------|------|
| 23 | getUserById | /users/{id} | - | ✅ 通过 |

#### 1.2 HTTP 方法规范
| 行号 | 方法 | 当前方法 | 问题 | 建议 |
|------|------|---------|------|------|
| 37 | createUser | POST | - | ✅ 通过 |

═══════════════════════════════════════════════════════

### 二、参数校验检查 ⚠️ 建议优化 (1 项问题)

#### 2.1 @Valid/@Validated 使用
| 行号 | 方法 | 场景 | 问题 | 建议 |
|------|------|------|------|------|
| 37 | createUser | POST+@RequestBody | 缺少 @Valid | 添加 `@Valid @RequestBody` |

#### 2.2 JSR-380 注解问题
| 实体类 | 字段 | 类型 | 当前注解 | 建议 |
|--------|------|------|---------|------|
| CreateUserRequest | age | Integer | @NotBlank | 改为 @NotNull |

═══════════════════════════════════════════════════════

### 三、异常处理检查 ❌ 不通过 (2 项问题)

#### 3.1 @ControllerAdvice
| 检查项 | 状态 | 说明 |
|--------|------|------|
| 全局异常处理器 | ❌ 缺失 | 未找到 @ControllerAdvice |

#### 3.2 异常上抛检查
| 行号 | 方法 | 问题 | 建议 |
|------|------|------|------|
| 24 | getUserById | 用 ApiResponse.error() 而非抛异常 | 改为 `throw BusinessException.userNotFound()` |

═══════════════════════════════════════════════════════

### 四、事务管理检查 ✅ 通过

#### 4.1 @Transactional 使用
| 行号 | 方法 | 操作类型 | 状态 |
|------|------|---------|------|
| 37 | createUser | 单表 INSERT | ✅ 无需事务（MySQL 自动提交） |
| 15 | getUserById | 单表 SELECT | ✅ 通过 |

═══════════════════════════════════════════════════════

## 检查结果汇总

| 检查项 | 结果 | 问题数 | 建议数 |
|--------|------|--------|--------|
| REST 规范 | ✅ 通过 | 0 | 2 |
| 参数校验 | ⚠️ 建议优化 | 1 | 1 |
| 异常处理 | ❌ 不通过 | 2 | 2 |
| 事务管理 | ✅ 通过 | 0 | 0 |

**总体评估**：⚠️ 建议优化

---

## 修复优先级

### P0 - 必须修复
1. **异常处理**：添加 @ControllerAdvice 全局异常处理器
2. **异常处理**：Controller 应上抛异常而非用 ApiResponse.error()

### P1 - 应该优化
1. **参数校验**：POST 方法添加 @Valid
2. **参数校验**：修正 JSR-380 注解类型

### P2 - 可选优化
1. **REST 规范**：考虑添加 HATEOAS 链接
2. **REST 规范**：考虑支持 HATEOAS 根入口
```

## 执行流程

```
1. 接收输入（Controller 类/方法/目录/项目）

2. 解析追踪链路
   └─> Controller → Service → Mapper

3. 串联执行检查
   ├─ 执行 rest-api-checker
   ├─ 执行 param-validation-checker
   ├─ 执行 exception-handler-checker
   ├─ 执行 transaction-checker
   └─ 执行 code-review-checker

4. 汇总结果
   ├─ 合并同类问题
   ├─ 按严重程度排序
   ├─ 输出统一报告

5. 生成修复建议
   ├─ P0: 必须修复
   ├─ P1: 应该优化
   └─ P2: 可选优化
```

## 外部配置文件

公共包配置放在 Skill 同目录的 `api-quality-checker-config.json` 文件中：

```
/root/.claude/skills/api-quality-checker/
├── SKILL.md                              ← Skill 主体
└── api-quality-checker-config.json       ← 独立配置文件
```

### 配置文件格式

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
  },
  "validation": {
    "commonPackages": [
      "com.company.platform.validation",
      "com.company.common.dto"
    ],
    "baseBeans": [
      "BaseRequest",
      "BaseDTO",
      "AbstractValidatingDTO"
    ]
  },
  "transaction": {
    "commonPackages": [
      "com.company.platform.transaction",
      "com.company.common.service"
    ],
    "baseServices": [
      "BaseService",
      "AbstractService"
    ]
  }
}
```

### 配置说明

| 字段 | 说明 | 示例 |
|------|------|------|
| `commonPackages` | 公共异常/校验/事务包的包名前缀 | `com.company.platform.exception` |
| `globalExceptionHandlers` | 全局异常处理器类名 | `GlobalExceptionHandler` |
| `businessExceptions` | 业务异常类名 | `BusinessException` |
| `baseBeans` | 基类/基DTO类名 | `BaseRequest`、`BaseDTO` |
| `baseServices` | 基类Service类名 | `BaseService` |

### 项目自定义配置

如项目有特殊的公共包，可在项目根目录创建 `.api-checker-config.json` 覆盖 Skill 配置：

```json
{
  "exception": {
    "commonPackages": ["com.mycompany.custom.exception"],
    "businessExceptions": ["MyBusinessException"]
  }
}
```

**配置优先级**：项目 `.api-checker-config.json` > Skill 目录 `api-quality-checker-config.json`

## 参数修改位置

如需修改或扩展检查参数，在 SKILL.md 中修改：

| 参数 | 位置 | 说明 |
|------|------|------|
| `--增量` | SKILL.md "增量检查" 章节 | 定义增量检查逻辑 |
| `--diff` | SKILL.md "增量检查" 章节 | 定义报告对比逻辑 |
| `--skill:xxx` | SKILL.md "增量检查" 章节 | 定义单一 Skill 跳过逻辑 |
| 公共包配置 | `api-quality-checker-config.json` | 定义公司公共包前缀 |

**修改步骤**：
1. 打开 `SKILL.md`
2. 找到对应章节（如"增量检查"）
3. 修改参数说明
4. 保存并同步到 WorkBuddy

## 关联 Skills

本 Skill 整合了以下独立 Skills：

| Skill | 说明 |
|-------|------|
| [rest-api-checker](../rest-api-checker/SKILL.md) | REST 规范检查 |
| [param-validation-checker](../param-validation-checker/SKILL.md) | 参数校验检查 |
| [exception-handler-checker](../exception-handler-checker/SKILL.md) | 异常处理检查 |
| [transaction-checker](../transaction-checker/SKILL.md) | 事务管理检查 |
| [code-review-checker](../code-review-checker/SKILL.md) | 代码质量审查 |
