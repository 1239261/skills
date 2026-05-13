---
name: code-review-checker
description: 输入 Java 类或方法，自动执行代码质量审查（规则匹配 + SonarQube 扫描），检查代码风格、命名规范、注释质量、复杂度、可维护性、安全问题和性能问题，输出完整的代码审查报告。
---

# 代码审查检查（Code Review Checker）

**description**: 输入 Java 类或方法，自动执行代码质量审查（规则匹配 + SonarQube 扫描），检查代码风格、命名规范、注释质量、复杂度、可维护性、安全问题和性能问题，输出完整的代码审查报告。

## 快速使用

```
审查代码质量：UserService
审查代码质量：UserService.createUser
审查代码质量：src/main/java/com/example/
```

## 增量检查与对比（节省 Token）

代码调整后，使用 `--增量` 参数只检查改动的文件：

```
审查代码质量：UserService --增量
```

### 参数说明

| 参数 | 说明 | Token 节省 |
|------|------|-----------|
| `--增量` | 只检查指定的类及关联链路 | 约 80% |
| `--diff` | 对比上次报告，只输出差异（自动查找同名报告） | 约 60% |
| `--diff:{target}` | 对比指定的历史报告 | 约 60% |
| `--diff --trend` | 对比报告并显示趋势分析 | - |
| `--scope:style` | 只检查代码风格（可选：style/naming/complexity/security/performance/maintainability） | 约 85% |
| `--sonar` | 强制启用 SonarQube 扫描（默认自动检测） | - |
| `--no-sonar` | 禁用 SonarQube 扫描，仅执行规则匹配 | 约 30% |

### 报告存储

报告存储在 `./code-review-reports` 目录，支持对比和自动清理：

```
报告存储：./code-review-reports
命名格式：{target}-{date}-{time}
保留期限：7 天自动清理
```

### 对比报告功能（--diff）

使用 `--diff` 参数可以自动对比历史报告：

```
审查代码质量：UserService --diff
审查代码质量：UserService --diff --trend
审查代码质量：UserService --diff:UserService-20240115-143052
```

#### A. 自动查找历史报告

```
自动查找逻辑：
1. 在 ./code-review-reports/ 目录下查找同名报告
2. 选择时间最接近的报告作为对比基准
3. 如有多个同天报告，优先选择同一天的
4. 如需指定报告，使用 --diff:{reportName}
```

#### B. 趋势分析（--trend）

```
审查代码质量：UserService --diff --trend

输出内容：
1. 问题数量趋势图（线条图/柱状图）
2. 技术债变化趋势
3. SonarQube 指标变化
4. 预测（按当前趋势，问题数会）
```

#### C. SonarQube 对比

当两次检查都执行了 SonarQube 扫描时，自动进行 SonarQube 对比：

```
SonarQube 对比内容：
1. Bug 数量变化（新增/修复）
2. 代码异味变化（新增/消除）
3. 技术债变化（增加/减少）
4. Quality Gate 状态变化
5. 各维度评分变化（可靠性、安全性、可维护性等）
```

### 对比报告输出格式

```
## 代码审查对比报告

**当前检查**：UserService (2024-01-16 14:30:00)
**上次检查**：UserService (2024-01-15 10:00:00)
**对比时间差**：1 天 4 小时

═══════════════════════════════════════════════════════

### 一、变化汇总

| 检查项 | 上次 | 当前 | 变化 | 趋势 |
|--------|------|------|------|------|
| 代码风格问题 | 3 | 1 | ↓ -2 | ↘ 改善 |
| 命名规范问题 | 0 | 0 | → 0 | → 持平 |
| 注释质量问题 | 2 | 3 | ↑ +1 | ↗ 恶化 |
| 复杂度问题 | 2 | 1 | ↓ -1 | ↘ 改善 |
| 可维护性问题 | 2 | 1 | ↓ -1 | ↘ 改善 |
| 安全与性能 | 0 | 0 | → 0 | → 持平 |
| **SonarQube Bug** | **1** | **0** | **↓ -1** | **↘ 已消除** |
| SonarQube 异味 | 4 | 2 | ↓ -2 | ↘ 改善 |
| 技术债 | 2.5h | 1.5h | ↓ -1h | ↘ 改善 |

═══════════════════════════════════════════════════════

### 二、趋势分析 📈

#### 2.1 问题数量趋势

```
问题总数趋势（近 5 次）：
  10 ┤
   8 ┤          ●
   6 ┤      ●   ●
   4 ┤  ●   ●
   2 ┤          ● ← 当前 (8)
   0 ┼────────────────────
     01-12  01-13  01-14  01-15  01-16

趋势预测：按当前改进速度，预计 5 天后问题数降至 5 以下
```

#### 2.2 技术债趋势

```
技术债变化（小时）：
  3.0 ┤
  2.5 ┤  ●
  2.0 ┤      ●
  1.5 ┤          ● ← 当前 (1.5h)
  1.0 ┤
  0.5 ┤
  0.0 ┼────────────────────
     01-12  01-13  01-14  01-15  01-16

技术债消除速度：-0.25h/天
预计消除完技术债：6 天
```

═══════════════════════════════════════════════════════

### 三、新增问题 🆕

| 检查项 | 行号 | 问题 | 严重程度 | Sonar 规则 |
|--------|------|------|----------|------------|
| 注释质量 | 55 | TODO 超过 6 个月未处理 | P1 | - |
| 代码风格 | 67 | 行长度超过 120 字符 | P2 | - |

═══════════════════════════════════════════════════════

### 四、已修复问题 ✅

| 检查项 | 行号 | 问题 | 上次严重程度 | 修复方式 |
|--------|------|------|--------------|----------|
| SonarQube Bug | 89 | 空指针异常风险 | P0 (BLOCKER) | 添加 null 检查 |
| 可维护性 | 30 | 魔法值 86400000 | P1 | 定义为常量 TIME_DAY_MS |

═══════════════════════════════════════════════════════

### 五、恶化问题 ⚠️

| 检查项 | 行号 | 问题 | 上次 | 当前 | 恶化原因 |
|--------|------|------|------|------|----------|
| 注释覆盖率 | 45% | 注释覆盖率下降 | 45% | 40% | 新增方法未添加 Javadoc |

═══════════════════════════════════════════════════════

### 六、SonarQube 对比详情

| SonarQube 指标 | 上次 | 当前 | 变化 |
|----------------|------|------|------|
| **Bugs** | 1 | 0 | ↓ -1 |
| Vulnerabilities | 0 | 0 | → 0 |
| Code Smells | 4 | 2 | ↓ -2 |
| Security Hotspots | 1 | 0 | ↓ -1 |
| 技术债 | 2.5h | 1.5h | ↓ -1h |
| 覆盖率高 | 65% | 68% | ↑ +3% |

#### 6.1 新增 SonarQube 问题

| 严重程度 | 行号 | 问题 | 规则 Key |
|----------|------|------|----------|
| MINOR | 78 | 注释覆盖率低于 70% | java:S1258 |

#### 6.2 已消除 SonarQube 问题

| 严重程度 | 行号 | 问题 | 规则 Key |
|----------|------|------|----------|
| BLOCKER | 89 | 空指针异常 | java:S2259 |

═══════════════════════════════════════════════════════

### 七、Quality Gate 状态

| 指标 | 上次 | 当前 | 状态 |
|------|------|------|------|
| **总体状态** | ❌ 失败 | ✅ 通过 | 改善 |
| 可靠性 | ⚠️ B | ✅ A | 改善 |
| 安全性 | ✅ A | ✅ A | 持平 |
| 可维护性 | ⚠️ B | ✅ A | 改善 |

═══════════════════════════════════════════════════════

## 审查结果汇总

| 检查项 | 结果 | 问题数 | 建议数 |
|--------|------|--------|--------|
| 代码风格 | ⚠️ 建议优化 | 3 | 2 |
| 命名规范 | ✅ 通过 | 0 | 0 |
| 注释质量 | ⚠️ 建议优化 | 2 | 2 |
| 复杂度 | ⚠️ 建议优化 | 2 | 2 |
| 可维护性 | ⚠️ 建议优化 | 2 | 3 |
| 安全与性能 | ✅ 通过 | 0 | 0 |
| **SonarQube** | **⚠️ 建议优化** | **5** | **5** |

**总体评估**：⚠️ 建议优化

**SonarQube 发现**：1 Bug (BLOCKER), 4 代码异味, 技术债 2.5 小时

---

## 修复优先级

### P0 - 必须修复（SonarQube Bug）
1. **SonarQube Bug**：修复空指针异常风险（行 89）

### P1 - 应该优化
1. **可维护性**：移除重复代码逻辑
2. **复杂度**：重构高复杂度方法（圈复杂度 18 → < 10）
3. **注释质量**：补充缺失的 Javadoc
4. **代码风格**：统一缩进规范
5. **SonarQube 代码异味**：方法过长（85 行 → 拆分为多个方法）

### P2 - 可选优化
1. **代码风格**：优化导入语句
2. **注释质量**：更新过期 TODO
3. **可维护性**：替换魔法值为常量
4. **SonarQube 技术债**：减少技术债至 1 小时以内

## 功能概述

一次检查，7 项联动：

| 检查项 | 说明 | 检查内容 |
|--------|------|---------|
| **代码风格** | Code Style | 缩进、空格、括号位置、行长度、导入规范 |
| **命名规范** | Naming Conventions | 类名、方法名、变量名、常量名、包名 |
| **注释质量** | Documentation | Javadoc、注释完整性、注释准确性、无过期注释 |
| **复杂度** | Complexity | 方法长度、圈复杂度、嵌套深度、参数个数 |
| **可维护性** | Maintainability | 重复代码、硬编码、魔法值、长类/长方法 |
| **安全与性能** | Security & Performance | SQL 注入、XSS、硬编码密码、循环效率 |
| **SonarQube 扫描** | SonarQube Analysis | Bug 漏洞、代码异味、技术债、Bloater、敏感点 |

### SonarQube 集成说明

本 Skill 内置 SonarQube 扫描支持，自动检测并调用 SonarScanner：

| 场景 | 行为 |
|------|------|
| 项目已配置 SonarQube | 自动调用扫描，整合结果到报告 |
| 项目未配置 SonarQube | 跳过 SonarQube 扫描，仅执行规则匹配检查 |
| SonarQube 不可用 | 显示警告，继续执行规则匹配检查 |

**注意**：SonarQube 扫描结果会自动合并到统一报告中，无需额外操作。

## 输入格式

| 输入类型 | 示例 | 检查范围 |
|---------|------|---------|
| Java 类 | `审查代码质量：UserService` | 类及关联调用 |
| Java#方法 | `审查代码质量：UserService.createUser` | 精确方法 |
| 目录 | `审查代码质量：src/main/java/com/example/` | 目录下所有 |
| 项目 | `审查代码质量：src/main/java/` | 整个项目 |

## 输出格式

```
## 代码审查报告

**审查目标**：UserService
**审查时间**：2024-01-15 10:30:00

═══════════════════════════════════════════════════════

### 一、代码风格检查 ⚠️ 建议优化 (3 项问题)

#### 1.1 缩进与空格
| 行号 | 问题 | 建议 |
|------|------|------|
| 23 | Tab 缩进混用空格 | 统一使用 4 空格缩进 |
| 45 | 行长度超过 120 字符 | 拆分为多行 |

#### 1.2 导入规范
| 行号 | 问题 | 建议 |
|------|------|------|
| 1-15 | 导入未使用的类 | 删除未使用的 import |

═══════════════════════════════════════════════════════

### 二、命名规范检查 ✅ 通过

#### 2.1 类名规范
| 行号 | 类名 | 状态 | 说明 |
|------|------|------|------|
| 12 | UserService | ✅ 通过 | 符合 PascalCase |

#### 2.2 方法名规范
| 行号 | 方法名 | 状态 | 说明 |
|------|--------|------|------|
| 25 | createUser | ✅ 通过 | 符合 camelCase |

═══════════════════════════════════════════════════════

### 三、注释质量检查 ⚠️ 建议优化 (2 项问题)

#### 3.1 Javadoc 缺失
| 行号 | 方法 | 问题 | 建议 |
|------|------|------|------|
| 25 | createUser | 缺少 Javadoc | 添加方法文档 |
| 37 | updateUser | Javadoc 不完整 | 补充 @param @return |

#### 3.2 过期注释
| 行号 | 问题 | 建议 |
|------|------|------|
| 50 | TODO 超过 6 个月未处理 | 处理或删除 |

═══════════════════════════════════════════════════════

### 四、复杂度检查 ⚠️ 建议优化 (2 项问题)

#### 4.1 方法长度
| 行号 | 方法 | 当前行数 | 建议行数 | 问题 |
|------|------|---------|---------|------|
| 25 | createUser | 85 行 | < 50 行 | 方法过长，需拆分 |

#### 4.2 圈复杂度
| 行号 | 方法 | 当前复杂度 | 建议 | 问题 |
|------|------|-----------|------|------|
| 60 | processUserData | 18 | < 10 | 复杂度过高 |

═══════════════════════════════════════════════════════

### 五、可维护性检查 ⚠️ 建议优化 (2 项问题)

#### 5.1 重复代码
| 位置 | 重复片段 | 建议 |
|------|---------|------|
| L23-27, L45-49 | UserValidation 逻辑重复 | 提取为公共方法 |

#### 5.2 硬编码与魔法值
| 行号 | 问题 | 建议 |
|------|------|------|
| 30 | magic number 86400000 | 定义常量 TIME_DAY_MS |

═══════════════════════════════════════════════════════

### 六、安全与性能检查 ✅ 通过

#### 6.1 安全检查
| 行号 | 检查项 | 状态 | 说明 |
|------|--------|------|------|
| - | SQL 注入 | ✅ 通过 | 使用参数化查询 |
| - | XSS | ✅ 通过 | 输出转义处理 |

#### 6.2 性能检查
| 行号 | 检查项 | 状态 | 说明 |
|------|--------|------|------|
| 25 | 循环效率 | ✅ 通过 | 无嵌套大循环 |

═══════════════════════════════════════════════════════

### 七、SonarQube 扫描结果 ⚠️ 建议优化

**SonarQube 版本**：Community 10.5
**扫描时间**：2024-01-15 10:32:00
**分析时间**：45 秒

#### 7.1 Bug 漏洞 ❌ 必须修复 (1 项)

| 严重程度 | 行号 | 问题 | 规则 Key | 建议 |
|---------|------|------|----------|------|
| **BLOCKER** | 89 | 空指针异常风险：user.getProfile() 可能为 null | java:S2259 | 添加 null 检查 |

#### 7.2 代码异味 ⚠️ 应该优化 (4 项)

| 严重程度 | 行号 | 问题 | 规则 Key | 建议 |
|---------|------|------|----------|------|
| **MAJOR** | 45 | 方法过长（85 行） | java:S138 | 拆分为多个方法 |
| **MAJOR** | 67 | 圈复杂度为 18 | java:S3776 | 重构降低复杂度 |
| **MINOR** | 23 | 重复代码块（相似度 85%） | java:S1192 | 提取为公共方法 |
| **MINOR** | 112 | 魔法值 "86400000" | java:S109 | 定义为常量 |

#### 7.3 技术债 💰 建议优化

| 指标 | 当前值 | 建议值 | 建议 |
|------|--------|--------|------|
| 代码异味数 | 12 | < 5 | 重构高异味代码 |
| 技术债 | 2.5 小时 | < 1 小时 | 减少新引入技术债 |
| 重复代码 | 3.2% | < 3% | 提取重复逻辑 |
| 注释覆盖率 | 45% | > 60% | 增加 Javadoc |

═══════════════════════════════════════════════════════

## 审查结果汇总

| 检查项 | 结果 | 问题数 | 建议数 |
|--------|------|--------|--------|
| 代码风格 | ⚠️ 建议优化 | 3 | 2 |
| 命名规范 | ✅ 通过 | 0 | 0 |
| 注释质量 | ⚠️ 建议优化 | 2 | 2 |
| 复杂度 | ⚠️ 建议优化 | 2 | 2 |
| 可维护性 | ⚠️ 建议优化 | 2 | 3 |
| 安全与性能 | ✅ 通过 | 0 | 0 |
| **SonarQube** | **⚠️ 建议优化** | **5** | **5** |

**总体评估**：⚠️ 建议优化

**SonarQube 发现**：1 Bug (BLOCKER), 4 代码异味, 技术债 2.5 小时

---

## 修复优先级

### P0 - 必须修复（SonarQube Bug）
1. **SonarQube Bug**：修复空指针异常风险（行 89）

### P1 - 应该优化
1. **可维护性**：移除重复代码逻辑
2. **复杂度**：重构高复杂度方法（圈复杂度 18 → < 10）
3. **注释质量**：补充缺失的 Javadoc
4. **代码风格**：统一缩进规范
5. **SonarQube 代码异味**：方法过长（85 行 → 拆分为多个方法）

### P2 - 可选优化
1. **代码风格**：优化导入语句
2. **注释质量**：更新过期 TODO
3. **可维护性**：替换魔法值为常量
4. **SonarQube 技术债**：减少技术债至 1 小时以内
```

## 执行流程

```
1. 接收输入（Java 类/方法/目录/项目）

2. 解析代码结构
   └─> 提取类、方法、字段、导入语句

3. 检测 SonarQube 环境
   ├─ 检查项目是否配置 sonar-project.properties 或 pom.xml
   ├─ 检查 SonarScanner 是否可用
   └─ 根据配置决定是否执行 SonarQube 扫描

4. 并行执行检查
   ├─ 规则匹配检查（规则匹配检查器）
   │   ├─ 代码风格检查（缩进、空格、导入）
   │   ├─ 命名规范检查（类名、方法名、变量名）
   │   ├─ 注释质量检查（Javadoc、TODO、注释准确性）
   │   ├─ 复杂度检查（方法长度、圈复杂度、嵌套深度）
   │   ├─ 可维护性检查（重复代码、硬编码、魔法值）
   │   └─ 安全与性能检查（SQL注入、XSS、循环效率）
   └─ SonarQube 扫描（如启用）
       ├─ 调用 sonar-scanner
       ├─ 解析 Sonar 报告（JSON/XML）
       └─ 提取 Bug、代码异味、技术债

5. 合并结果
   ├─ 合并同类问题（规则匹配 + SonarQube）
   ├─ 去除重复问题
   ├─ 按严重程度排序
   └─ 输出统一报告

6. 生成修复建议
   ├─ P0: 必须修复（SonarQube Bug）
   ├─ P1: 应该优化（规则匹配问题 + SonarQube 代码异味）
   └─ P2: 可选优化（技术债）
```

## 外部配置文件

公共包配置放在 Skill 同目录的 `code-review-checker-config.json` 文件中：

```
/root/.claude/skills/code-review-checker/
├── SKILL.md                              ← Skill 主体
└── code-review-checker-config.json       ← 独立配置文件
```

### 配置文件格式

```json
{
  "style": {
    "indent": "4 spaces",
    "maxLineLength": 120,
    "blankLinesBetweenMethods": 1
  },
  "naming": {
    "classSuffixes": ["Service", "Controller", "Repository", "Entity", "DTO"],
    "methodPrefixes": ["get", "set", "create", "update", "delete", "find", "process"]
  },
  "complexity": {
    "maxMethodLength": 50,
    "maxComplexity": 10,
    "maxNestingDepth": 3,
    "maxParameters": 5
  },
  "maintainability": {
    "maxDuplicateLines": 3,
    "magicNumberPatterns": ["\\d{5,}", "86400000", "3600000"]
  },
  "sonar": {
    "enabled": true,
    "autoDetect": true,
    "scannerPath": "sonar-scanner",
    "projectKey": "${project.artifactId}",
    "host": "http://localhost:9000",
    "token": "",
    "qualityGate": true,
    "qualityProfiles": ["Sonar way"],
    "includeRules": [],
    "excludeRules": ["common-java:BlindIndexing"],
    "mapping": {
      "BUG": "P0",
      "VULNERABILITY": "P0",
      "CODE_SMELL": "P1",
      "SECURITY_HOTSPOT": "P1",
      "TECHNICAL_DEBT": "P2"
    }
  }
}
```

### 配置说明

| 字段 | 说明 | 示例 |
|------|------|------|
| `style.indent` | 缩进类型 | `"4 spaces"` 或 `"tab"` |
| `style.maxLineLength` | 最大行长度 | `120` |
| `naming.classSuffixes` | 类名后缀 | `Service`, `Controller` |
| `complexity.maxMethodLength` | 方法最大行数 | `50` |
| `complexity.maxComplexity` | 圈复杂度阈值 | `10` |
| `maintainability.magicNumberPatterns` | 魔法值正则 | 超过 4 位数字 |
| `sonar.enabled` | 是否启用 SonarQube | `true` |
| `sonar.autoDetect` | 自动检测 SonarQube 配置 | `true` |
| `sonar.scannerPath` | SonarScanner 路径 | `"sonar-scanner"` |
| `sonar.host` | SonarQube 服务器地址 | `"http://localhost:9000"` |
| `sonar.token` | SonarQube 认证 Token | `""` |
| `sonar.qualityGate` | 是否检查 Quality Gate | `true` |
| `sonar.mapping` | Sonar 级别到 P0/P1/P2 的映射 | 见默认配置 |
| `sonar.excludeRules` | 排除的 Sonar 规则 | `[]` |

### 项目自定义配置

如项目有特殊规范，可在项目根目录创建 `.code-review-config.json` 覆盖 Skill 配置：

```json
{
  "style": {
    "maxLineLength": 150
  },
  "complexity": {
    "maxMethodLength": 80
  }
}
```

**配置优先级**：项目 `.code-review-config.json` > Skill 目录 `code-review-checker-config.json`

## 关联 Skills

本 Skill 可独立使用，也可与以下 Skills 配合使用：

| Skill | 说明 | 配合方式 |
|-------|------|---------|
| [api-quality-checker](../api-quality-checker/SKILL.md) | API 质量综合检查 | 互补：API 规范 + 代码质量 |
| [security-checker](../security-checker/SKILL.md) | 安全漏洞检查 | 互补：代码审查 + 安全扫描 |
| [performance-checker](../performance-checker/SKILL.md) | 性能问题检查 | 互补：代码审查 + 性能分析 |
| [gstack-ship-checker](../gstack-ship-checker/SKILL.md) | 发布前检查 | 互补：代码审查 + 发布验证 |

## 使用建议

| 场景 | 推荐命令 |
|------|---------|
| 日常代码审查 | `审查代码质量：UserService --增量` |
| 提交前检查 | `审查代码质量：UserService --diff` |
| 快速风格检查 | `审查代码质量：UserService --scope:style` |
| 全面检查（含 Sonar） | `审查代码质量：src/main/java/com/example/` |
| 仅规则匹配检查 | `审查代码质量：UserService --no-sonar` |
| 强制 SonarQube 扫描 | `审查代码质量：UserService --sonar` |
| 与 API 检查配合 | 先 `检查 API 质量：UserController`，再 `审查代码质量：UserService` |

## SonarQube 集成详解

### 工作原理

```
1. 检查项目配置
   ├─ sonar-project.properties
   ├─ pom.xml (maven)
   ├─ build.gradle (gradle)
   └─ .sonarcloud.yaml

2. 调用 SonarScanner
   └─ sonar-scanner [options]

3. 解析扫描结果
   ├─ sonar-report.json
   └─ sonar-report.xml

4. 映射到统一报告
   ├─ BUG/VULNERABILITY → P0
   ├─ CODE_SMELL → P1
   └─ SECURITY_HOTSPOT/TECHNICAL_DEBT → P2
```

### SonarQube 配置示例

**sonar-project.properties**（项目根目录）：
```properties
sonar.projectKey=my-project
sonar.projectName=My Project
sonar.projectVersion=1.0.0
sonar.sources=src/main/java
sonar.sourceEncoding=UTF-8
sonar.java.binaries=target/classes
```

**pom.xml**（Maven 项目）：
```xml
<properties>
    <sonar.projectKey>my-project</sonar.projectKey>
    <sonar.host.url>http://localhost:9000</sonar.host.url>
</properties>
```

### 本地 SonarQube 启动（如需）

```bash
# 使用 Docker 启动 SonarQube
docker run -d --name sonarqube -p 9000:9000 sonarqube:latest

# 首次访问：http://localhost:9000
# 默认账号：admin / admin
```
