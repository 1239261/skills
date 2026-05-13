# 开发者指南

## 快速开始

### 环境要求
- Java 8+
- Spring Boot
- MyBatis-Plus
- Redis（用于 Redisson 分布式锁）
- RocketMQ
- 数据库（关系型）

### 项目依赖

核心依赖包括：
- `com.baomidou:mybatis-plus-extension` - MyBatis-Plus 扩展
- `org.apache.rocketmq:rocketmq-client` - RocketMQ 客户端
- `org.redisson:redisson` - Redisson 分布式锁
- `com.alibaba:easyexcel` - Excel 处理
- `org.apache.poi:poi` - Excel 处理
- `com.google.guava:guava` - Guava 工具库

## 开发规范

### 事务管理
- 使用 `@Transactional(rollbackFor = Exception.class)` 确保异常时回滚
- 涉及多表操作的方法必须添加事务注解

### 分布式锁
- 使用 `@RLockAnnotation(key = {"#param.field"})` 实现方法级分布式锁
- 锁的 key 通常使用 `ipoId` 或 `referenceId` 防止并发冲突

### 软删除
- 使用 `disabled` 字段标记删除（`Constants.DISABLED_Y` 表示有效，`Constants.DISABLED_N` 表示已删除）
- 所有查询必须带上 `.eq(Entity::getDisabled, Constants.DISABLED_Y)` 条件

### 日志记录
- 使用 `@Slf4j` 注解引入日志
- 关键业务操作记录 `log.info()`
- 异常记录 `log.error()` 并附带堆栈

### 批量处理
- 大批量查询使用 `ListUtils.partition(list, 500)` 分批处理
- 批量保存使用 `saveBatch()` 方法

## 常见开发场景

### 1. 新增审核节点

```java
// 在 ProcessDefinition 表中添加节点定义
// 确保 workflowService 能正确获取下一节点
List<ProcessDefinition> processDefinitions = processDefinitionService.list(
    new LambdaUpdateWrapper<ProcessDefinition>()
        .eq(ProcessDefinition::getProcessName, typeName)
        .eq(ProcessDefinition::getDisabled, Constants.DISABLED_Y)
);
```

### 2. 新增凭证类型

```java
// 在 VoucherTypeEnum 中添加新类型
// 在相关业务方法中处理新凭证的生成逻辑
voucherService.saveOrUpdateVoucher(detailVO, sys, direction, voucherType, status, remark);
```

### 3. 新增外部通道

```java
// 在 check() 方法的 switch 中新增 case
// 实现对应的 submitXxxFund() 方法
// 处理 recordId 生成规则和凭证逻辑
```

## 测试方法

`test(String applicationId, String type)` 方法支持以下测试场景：

| type | 场景 |
|------|------|
| "1" | 添加认购条目 |
| "2" | 作废认购 |
| "3" | 凭证推送 |
| "4" | 配售分配失败处理 |
| "5" | 冻结失败重试 + 经纪资金重试 |

## 注意事项

1. **并发安全**: 保存和审核操作使用了分布式锁，确保相同 ipoId/referenceId 的操作串行化
2. **状态一致性**: 申请状态变更必须同时更新 `status` 和记录到 `ProcessRecord`
3. **双系统同步**: 凭证操作需要同步 Neohas 和 Ayers 两个系统，任一失败都需记录
4. **历史追溯**: 所有状态变更必须调用 `ipoHisService.saveHisLog()` 记录历史
5. **邮件通知**: 凭证失败时自动发送邮件提醒（`voucherService.sendVoucherFaildEmail()`）
6. **证件号校验**: 使用正则表达式验证不同证件类型格式
