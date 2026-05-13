# 文档索引

## IPO 认购系统文档

| 文档 | 路径 | 说明 |
|------|------|------|
| [系统架构](./ARCHITECTURE.md) | ARCHITECTURE.md | 技术栈、分层架构、核心模块、状态机、数据流 |
| [接口文档](./INTERFACES.md) | INTERFACES.md | IApplicationService 接口方法定义和枚举类型 |
| [开发者指南](./DEVELOPER_GUIDE.md) | DEVELOPER_GUIDE.md | 开发规范、常见场景、注意事项 |

### 模块文档

| 文档 | 路径 | 说明 |
|------|------|------|
| [IPO 认购服务](./模块/01_IPO认购服务.md) | 模块/01_IPO认购服务.md | OpenIpoApplicationServiceImpl 和 TIpoMarginApplicationServiceImpl 详解 |

### 概念文档

| 文档 | 路径 | 说明 |
|------|------|------|
| [业务概念](./专有概念/01_业务概念.md) | 专有概念/01_业务概念.md | IPO 认购类型、工作流、凭证系统、外部集成、消息事件 |

## 源码文件

| 文件 | 包路径 | 说明 |
|------|--------|------|
| OpenIpoApplicationServiceImpl.java | com.cicc.overseas.data.service.orm.impl | 开放 IPO 认购申请核心实现 |
| TIpoMarginApplicationServiceImpl.java | com.cicc.overseas.data.service.orm.impl | 融资认购申请专用实现 |
