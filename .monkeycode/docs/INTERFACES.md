# 接口文档

## IApplicationService

### 包路径
`com.cicc.overseas.data.service.orm.IApplicationService`

### 描述
IPO 认购申请的统一服务接口，定义了查询、保存、审核、取消等核心操作。

### 方法定义

#### 查询接口

| 方法签名 | 说明 | 返回值 |
|----------|------|--------|
| `queryOpenIpoApplicationList(OpenIpoApplicationQryBO bo)` | 分页查询开放 IPO 申请列表 | `PageListVO<OpenIpoApplicationListVO>` |
| `queryOpenIpoApplicationDetail(String id)` | 查询申请详情 | `OpenIpoApplicationDetailVO` |
| `queryOpenIpoIpoResultList(String source, IpoAppResultApiQryBO bo)` | 查询 IPO 配售结果 | `List<OpenIpoResultVO>` |
| `getOpenIpoAllotmentResult(String source, IpoAllotResultApiQryBO bo)` | 查询配售分配结果 | `List<OpenIpoAllotmentResultVO>` |
| `getCheckNodes(String applicationId)` | 获取审核节点 | `List<CheckNodeVo>` |
| `getProcessRecords(String applicationId)` | 获取流程记录 | `List<CheckNodeVo>` |

#### 操作接口

| 方法签名 | 说明 | 返回值 |
|----------|------|--------|
| `save(String source, ApplicationSaveBO saveBO)` | 保存申请 | `OpenIpoResultVO` |
| `validate(ApplicationSaveBO saveBO)` | 校验申请数据 | `String` (错误信息，空表示通过) |
| `check(OpenIpoApplicationCheckBO bo)` | 审核操作 | `OperateResultVO` |
| `check(OpenIpoApplicationCheckIcBO bo)` | IC 节点审核 | `OperateResultVO` |
| `cancel(String source, List<String> referenceIds)` | 批量取消 | `List<OpenIpoResultVO>` |
| `updateOpenIpoApplication(String source, IpoUpdateApiBO bo)` | 更新申请 | `OpenIpoResultVO` |
| `suspendApplication(String ipoId, BigDecimal oldCost, BigDecimal newCost)` | 暂停申请 | `void` |

#### 事件通知接口

| 方法签名 | 说明 | 返回值 |
|----------|------|--------|
| `debitVoucherNotify(FiniInnerEvent event)` | 凭证扣款通知 | `void` |

#### 测试接口

| 方法签名 | 说明 | 返回值 |
|----------|------|--------|
| `test(String applicationId, String type)` | 测试方法 | `void` |

### 枚举类型

#### IpoApplicationType - 申请类型

| 值 | 说明 |
|----|------|
| CASH_SUBSCRIPTION | 现金认购 |
| MARGIN_SUBSCRIPTION | 融资认购 |

#### IpoOperateEnum - 操作类型

| 值 | 说明 |
|----|------|
| APPROVE | 通过 |
| REJECT | 驳回 |
| CANCEL | 取消 |
| VOID | 作废 |
| SUBMIT | 提交 |

#### SubmitChannel - 提交通道

| 值 | 说明 |
|----|------|
| SCB | SCB 银行通道 |
| FINI | FINI 系统通道 |
