# 模块文档

## OpenIpoApplicationServiceImpl

### 包路径
`com.cicc.overseas.data.service.orm.impl.OpenIpoApplicationServiceImpl`

### 继承关系
- 继承: `ServiceImpl<TIpoApplicationMapper, TIpoApplication>`
- 实现: `IApplicationService`

### 职责
开放 IPO 认购申请的核心服务实现，处理现金认购和融资认购的申请查询、保存、审核、作废、配售结果查询等全流程业务。

### 依赖服务

| 服务 | 类型 | 用途 |
|------|------|------|
| `dataFeignService` | DataFeignService | 账户代码校验 |
| `tIpoReferenceMapper` | TIpoReferenceMapper | IPO 参考数据查询 |
| `eipoApplicationService` | TIpoApplicationService | IPO 申请基础操作 |
| `applicationMappingService` | TIpoApplicationMappingService | 外部系统与内部申请映射 |
| `feeLevelService` | TFeeLevelService | 费用等级查询 |
| `primaryKeyGenerator` | PrimaryKeyGenerator | 主键生成 |
| `ipFileService` | TIpoFileService | IPO 文件管理 |
| `producer` | DefaultMQProducer | RocketMQ 消息发送 |
| `redissonClient` | RedissonClient | 分布式锁 |
| `ipoAllotmentService` | TIpoAllotmentService | 配售服务 |
| `ipoVoucherService` | TIpoVoucherService | 凭证服务 |
| `tIdentityInfoService` | TIdentityInfoService | 身份信息管理 |
| `hkhasServiceService` | HkhasServiceService | 现金余额查询 |
| `neohasService` | NeohasService | 资金系统服务 |
| `eipoConfigService` | EipoConfigService | 配置服务 |
| `workflowService` | WorkflowService | 工作流服务 |
| `ipoMarginApplicationService` | TIpoMarginApplicationServiceImpl | 融资认购服务 |
| `eipoSyncService` | EipoSyncService | 数据同步服务 |

### 核心方法

#### 查询类

| 方法 | 说明 | 返回值 |
|------|------|--------|
| `queryOpenIpoApplicationList(OpenIpoApplicationQryBO)` | 分页查询开放 IPO 申请列表，按工作流角色过滤 | `PageListVO<OpenIpoApplicationListVO>` |
| `queryOpenIpoApplicationDetail(String id)` | 查询申请详情，包含身份信息、文件、现金余额、工作流任务 | `OpenIpoApplicationDetailVO` |
| `queryOpenIpoIpoResultList(String source, IpoAppResultApiQryBO)` | 查询 IPO 配售结果（批量，每批 500 条） | `List<OpenIpoResultVO>` |
| `getOpenIpoAllotmentResult(String source, IpoAllotResultApiQryBO)` | 查询 IPO 配售分配结果 | `List<OpenIpoAllotmentResultVO>` |
| `getCheckNodes(String applicationId)` | 获取审核节点列表（含工作流定义和记录） | `List<CheckNodeVo>` |
| `getProcessRecords(String applicationId)` | 获取完整流程记录 | `List<CheckNodeVo>` |

#### 操作类

| 方法 | 说明 | 注解 |
|------|------|------|
| `save(String source, ApplicationSaveBO)` | 保存 IPO 申请 | `@Transactional`, `@RLockAnnotation` |
| `check(OpenIpoApplicationCheckBO)` | 审核操作（APPROVE/REJECT/CANCEL/VOID） | `@RLockAnnotation` |
| `check(OpenIpoApplicationCheckIcBO)` | IC（Introductory Check）节点专用审核 | `@RLockAnnotation` |
| `cancel(String source, List<String> referenceIds)` | 批量取消申请 | - |
| `updateOpenIpoApplication(String source, IpoUpdateApiBO)` | 更新暂停的申请（恢复融资利率） | - |
| `suspendApplication(String ipoId, BigDecimal, BigDecimal)` | 暂停融资成本升高的申请 | - |

#### 消息通知类

| 方法 | 说明 |
|------|------|
| `debitVoucherNotify(FiniInnerEvent)` | 凭证扣款通知，检查 Neohas + Ayers 双系统凭证状态 |
| `mqNotify(FiniStatusEvent)` | 发送状态变更 MQ 消息 |
| `mqNotifyReferenceUpdate(String ipoId)` | 发送参考数据更新 MQ 消息 |

#### 校验类

| 方法 | 说明 |
|------|------|
| `validate(ApplicationSaveBO)` | 申请保存前校验：ID 唯一性、IPO 有效期、账户有效性、金额/数量匹配、身份信息合规 |
| `checkIpoEndDate(TIpoReference)` | 检查 IPO 截止日期 |
| `checkIDTypeAndCountry(...)` | 检查证件类型与国家匹配 |
| `checkIdTypeAndIdNum(...)` | 检查证件号码格式 |
| `checkAccount(...)` | 校验账户是否存在 |
| `checkSameAccount(...)` | 检查同一账户是否有有效申请 |

#### 工具方法

| 方法 | 说明 |
|------|------|
| `test(String, String)` | 测试方法（type 1-5 对应不同场景） |
| `isExist(String, String)` | 检查申请是否已存在 |
| `getIpoApplicationMappings(...)` | 获取外部系统与内部申请的映射关系 |

### 常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `JOIN_ACCOUNT_TYPE` | `"2"` | 联名账户类型 |
| `ID_TYPE1_PATTERN` | `^[A-Z]{1,2}\d{6}([0-9A])$` | 香港身份证格式 |
| `ID_TYPE4_PATTERN` | `^[A-Z0-9]{20}$` | 护照格式 |
| `ID_TYPE8_PATTERN` | `^[A-Z0-9]{6}.[1-9]\d{0,9}$` | 其他证件格式 |

---

## TIpoMarginApplicationServiceImpl

### 包路径
`com.cicc.overseas.data.service.orm.impl.TIpoMarginApplicationServiceImpl`

### 继承关系
- 继承: `TIpoApplicationServiceImpl`
- 服务名: `@Service("ipoMarginApplicationService")`

### 职责
融资认购（Margin Subscription）专用服务实现，处理融资认购的资金提交、凭证生成、作废、重试等特有逻辑。

### 依赖服务

| 服务 | 类型 | 用途 |
|------|------|------|
| `applicationMapper` | TIpoApplicationMapper | 申请数据访问 |
| `voucherService` | EipoVoucherService | 凭证服务 |
| `ipoHisService` | TIpoHisService | 历史日志服务 |
| `dataFeignService` | DataFeignService | 数据服务 |
| `tIpoReferenceMapper` | TIpoReferenceMapper | IPO 参考数据 |
| `primaryKeyGenerator` | PrimaryKeyGenerator | 主键生成 |
| `applicationEventPublisher` | ApplicationEventPublisher | Spring 事件发布 |
| `neohasService` | NeohasService | 资金系统通知 |
| `assetManagementApiService` | AssetManagementApiService | 资产管理系统（冻结/解冻） |
| `ipoApplicationAssembler` | EipoApplicationAssembler | 实体-VO 转换器 |

### 核心方法

#### 提交认购

| 方法 | 说明 |
|------|------|
| `addSubscriptionEntries(IpoApplicationCheckBO, IpoApplyStatusEnum)` | 添加认购条目入口，根据 submitChannel 选择 FINI 或 SCB 通道 |
| `submitFiniFund(...)` | 通过 FINI 通道提交资金（调用 FINI addSubscriptionEntries API） |
| `submitScbFund(...)` | 通过 SCB 通道提交资金（股票代码#账户代码组合作为 recordId） |
| `addSubscriptionCashVoucher(...)` | 生成认购现金凭证：贷款存入（LOAN_AMOUNT_DEPOSIT）+ 认购扣款（SUBSCRIPTION_CASH）+ 冻结凭证 |

#### 作废认购

| 方法 | 说明 |
|------|------|
| `invalidateSubscription(IpoApplicationCheckBO, IpoApplyStatusEnum)` | 作废认购，撤销冻结并退款 |
| `doRevokeFrozenWithdrawVoucher(...)` | 执行冻结/取款凭证撤销 |
| `revokeFrozenWithdrawVoucher(...)` | 撤销 Neohas + Ayers 双系统凭证 |
| `genRevokeNeohasVoucher(...)` | 生成反向凭证（方向反转：W↔D） |

#### 凭证生成

| 方法 | 说明 |
|------|------|
| `buildFrozenWithdrawDto(IpoApplicationDetailVO)` | 构建冻结/取款请求 DTO，融资认购包含贷款存入 + 认购扣款两笔明细 |
| `genFrozenWithdrawVoucher(...)` | 生成冻结凭证并调用资产管理 API |
| `syncNeohasCash(...)` | 同步 Neohas 资金系统（存款/取款） |

#### 重试处理

| 方法 | 说明 |
|------|------|
| `freezeFailureRetryHandler(String applicationId)` | 冻结失败重试处理，更新 Ayers 凭证状态并发布事件 |

### 业务逻辑细节

#### 资金提交流程
```
1. 根据 submitChannel 选择通道
   ├── SCB → submitScbFund()
   └── FINI/默认 → submitFiniFund()

2. submitFiniFund():
   ├── 调用 FINI addSubscriptionEntries API
   ├── 获取 recordId
   ├── 生成现金凭证（贷款存入 + 认购扣款）
   ├── 生成冻结凭证（Ayers 系统）
   ├── 更新申请状态
   ├── 保存历史日志
   └── 发布凭证事件（如需要同步）

3. submitScbFund():
   ├── recordId = stockCode + "#" + accountCode
   ├── 生成现金凭证
   ├── 更新申请状态
   └── 发布凭证事件
```

#### 凭证类型

| 凭证类型 | 代码 | 说明 |
|----------|------|------|
| SUBSCRIPTION_CASH | - | 认购现金扣款 |
| LOAN_AMOUNT_DEPOSIT | - | 融资金额存入 |
| LOAN_AMOUNT_WITHDRAWAL | - | 融资金额取出（退款） |
| REFUND_CASH | - | 退款现金 |
| ALLOT_STOCK | - | 配售股票 |
| REVERSE_CASH | - | 反向现金凭证 |
| REVERSE_LOAN | - | 反向贷款凭证 |

#### 凭证状态

| 状态 | 说明 |
|------|------|
| PENDING | 处理中 |
| SUCCEED | 成功 |
| FAILED | 失败 |
| NA | 不适用 |
