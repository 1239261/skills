# 系统架构文档

## 项目概述

本项目为 **cicc-overseas（中金海外）** 的 IPO 认购业务系统，提供港股/IPO 现金认购（Cash Subscription）与融资认购（Margin Subscription）的全流程管理。系统基于 Spring Boot + MyBatis-Plus 构建，采用分布式锁、RocketMQ 消息队列、Redisson 分布式锁、Feign 远程调用等技术栈。

## 技术栈

| 类别 | 技术 |
|------|------|
| 框架 | Spring Boot |
| ORM | MyBatis-Plus |
| 数据库 | 关系型数据库（未确认具体类型） |
| 消息队列 | Apache RocketMQ |
| 分布式锁 | Redisson |
| 远程调用 | Feign（DataFeignService, HkhasServiceService, NeohasService） |
| Excel 处理 | EasyExcel, Apache POI |
| 工具库 | Lombok, Guava, Apache Commons, Jodd |

## 架构分层

```
┌─────────────────────────────────────────────────┐
│                   VO 层（视图对象）                 │
│   OpenIpoApplicationListVO, OpenIpoResultVO     │
├─────────────────────────────────────────────────┤
│                   BO 层（业务对象）                 │
│   OpenIpoApplicationQryBO, ApplicationSaveBO    │
├─────────────────────────────────────────────────┤
│              Service 接口层（IApplicationService） │
├─────────────────────────────────────────────────┤
│          Service 实现层（核心业务逻辑）              │
│   OpenIpoApplicationServiceImpl                  │
│   TIpoMarginApplicationServiceImpl               │
├─────────────────────────────────────────────────┤
│              Mapper 层（数据访问）                  │
│   TIpoApplicationMapper, TIpoReferenceMapper    │
├─────────────────────────────────────────────────┤
│              Entity 层（数据库实体）                │
│   TIpoApplication, TIpoVoucher, TIpoReference   │
└─────────────────────────────────────────────────┘
```

## 核心模块

### 1. IPO 认购申请管理

- **OpenIpoApplicationServiceImpl**: 开放 IPO 认购申请的核心服务实现，继承 `ServiceImpl<TIpoApplicationMapper, TIpoApplication>`，实现 `IApplicationService` 接口
- **TIpoMarginApplicationServiceImpl**: 融资认购申请服务，继承 `TIpoApplicationServiceImpl`，处理融资相关的资金冻结、凭证生成等

### 2. 工作流引擎

- **ProcessDefinitionService**: 流程定义管理
- **ProcessTaskService**: 流程任务管理
- **ProcessRecordService**: 流程记录管理
- **WorkflowService**: 工作流编排服务

### 3. 凭证（Voucher）管理

- **TIpoVoucherService**: 凭证服务，处理认购、退款、冻结、解冻等财务凭证
- 支持双系统同步：Neohas（资金系统） + Ayers（经纪系统）

### 4. 消息通知

- **RocketMQ**: 用于内部事件通知（FiniEvent、FiniStatusEvent、FiniReferenceEvent 等）
- **ApplicationEventPublisher**: Spring 事件发布器，用于内部事件触发

### 5. 外部服务集成

- **DataFeignService**: 账户代码校验等数据服务
- **HkhasServiceService**: 现金余额查询
- **NeohasService**: 资金系统通知（存款/取款）
- **AssetManagementApiService**: 资产管理系统接口（冻结/解冻）

## 业务状态机

```
PENDING → SUBMITTING → DONE
   ↓          ↓         ↓
  REJ       FAIL     VOIDING → CAN
   ↓
SUSPENDED → (恢复) → DONE
```

### 状态码定义

| 状态码 | 枚举 | 说明 |
|--------|------|------|
| PENDING | IpoApplyStatusEnum.PENDING | 待审核 |
| SUBMITTING | IpoApplyStatusEnum.SUBMITTING | 提交中 |
| DONE | IpoApplyStatusEnum.DONE | 已完成 |
| REJ | IpoApplyStatusEnum.REJ | 已驳回 |
| FAIL | IpoApplyStatusEnum.FAIL | 失败 |
| VOID | IpoApplyStatusEnum.VOID | 已作废 |
| CAN | IpoApplyStatusEnum.CAN | 已取消 |
| SUSPENDED | IpoApplyStatusEnum.SUSPENDED | 已暂停 |
| VOIDING | IpoApplyStatusEnum.VOIDING | 作废中 |
| ALLOTTED | IpoApplyStatusEnum.ALLOTTED | 已配售 |

## 数据流

```
外部系统 ──→ Open API ──→ OpenIpoApplicationServiceImpl
                              │
                              ├── 校验 → validate()
                              ├── 保存 → save() (分布式锁)
                              ├── 审核 → check() (工作流)
                              └── 查询 → queryOpenIpoApplicationList()
                                        │
                                        ↓
                              TIpoMarginApplicationServiceImpl
                                        │
                                        ├── 提交认购 → addSubscriptionEntries()
                                        ├── 作废认购 → invalidateSubscription()
                                        └── 重试处理 → freezeFailureRetryHandler()
```
