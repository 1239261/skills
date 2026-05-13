# Skills 仓库

> 从 [skills.sh](https://skills.sh) 收集的 AI Agent Skills 集合，共 **43 个仓库**、**620+ 个 Skills**。

## 快速使用

```bash
# 克隆本仓库
git clone https://github.com/1239261/skills.git
cd skills
```

Skills 按 `owner/skill-name` 目录结构组织，每个 Skill 的核心文件是 `SKILL.md`。

---

## Skills 清单

### 一、自定义 Skills (6 个)

| Skill | 描述 |
|-------|------|
| [api-quality-checker](./api-quality-checker/SKILL.md) | API 质量综合检查：RESTful 规范、参数校验、异常处理、事务管理 |
| [code-review-checker](./api-quality-checker/code-review-checker/SKILL.md) | 代码质量审查：风格、命名、注释、复杂度、安全、性能 |
| [param-validation-checker](./api-quality-checker/param-validation-checker/SKILL.md) | Spring Boot 参数校验检查：@Valid、JSR-380、嵌套/分组校验 |
| [exception-handler-checker](./api-quality-checker/exception-handler-checker/SKILL.md) | Spring 异常处理检查：@ControllerAdvice、HTTP 状态码、日志脱敏 |
| [transaction-checker](./api-quality-checker/transaction-checker/SKILL.md) | Spring 事务管理检查：@Transactional、传播行为、隔离级别 |
| [rest-api-checker](./api-quality-checker/rest-api-checker/SKILL.md) | REST 接口规范检查：URI 命名、HTTP 方法、状态码、HATEOAS |
| [deploy-website](./deploy-website/SKILL.md) | 部署并本地预览 Web 项目 |
| [feature-design](./feature-design/SKILL.md) | 引导用户通过 EARS 模式生成需求文档和技术设计 |
| [feature-implementer](./feature-implementer/SKILL.md) | 根据技术方案执行开发工作 |
| [implementation-planner](./implementation-planner/SKILL.md) | 根据需求和设计方案创建实施计划 |
| [project-wiki](./project-wiki/SKILL.md) | 根据代码仓库生成项目文档 |

### 二、Anthropic 官方 Skills (16 个)

| Skill | 描述 |
|-------|------|
| [frontend-design](./anthropics/skills/skills/frontend-design/SKILL.md) | 创建高设计质量的前端界面 |
| [canvas-design](./anthropics/skills/skills/canvas-design/SKILL.md) | 创建视觉艺术 .png/.pdf |
| [web-artifacts-builder](./anthropics/skills/skills/web-artifacts-builder/SKILL.md) | 创建多组件 HTML artifacts |
| [webapp-testing](./anthropics/skills/skills/webapp-testing/SKILL.md) | 使用 Playwright 测试 Web 应用 |
| [mcp-builder](./anthropics/skills/skills/mcp-builder/SKILL.md) | 创建 MCP 服务器 |
| [pdf](./anthropics/skills/skills/pdf/SKILL.md) | 读取和操纵 PDF |
| [docx](./anthropics/skills/skills/docx/SKILL.md) | 创建和编辑 Word 文档 |
| [pptx](./anthropics/skills/skills/pptx/SKILL.md) | 创建 PowerPoint 演示文稿 |
| [xlsx](./anthropics/skills/skills/xlsx/SKILL.md) | 操作电子表格 |
| [skill-creator](./anthropics/skills/skills/skill-creator/SKILL.md) | 创建和管理 Skills |
| [theme-factory](./anthropics/skills/skills/theme-factory/SKILL.md) | 主题样式工具包 |
| [brand-guidelines](./anthropics/skills/skills/brand-guidelines/SKILL.md) | 应用品牌颜色和排版 |
| [algorithmic-art](./anthropics/skills/skills/algorithmic-art/SKILL.md) | 创建算法艺术 |
| [slack-gif-creator](./anthropics/skills/skills/slack-gif-creator/SKILL.md) | 创建 GIF 动图 |
| [claude-api](./anthropics/skills/skills/claude-api/SKILL.md) | Claude API / Anthropic SDK 开发 |
| [doc-coauthoring](./anthropics/skills/skills/doc-coauthoring/SKILL.md) | 结构化文档协作指南 |

### 三、Vercel Labs Skills (10 个)

| 仓库 | Skill | 描述 |
|------|-------|------|
| vercel-labs/agent-skills | vercel-react-best-practices | React/Next.js 最佳实践 |
| vercel-labs/agent-skills | web-design-guidelines | Web 界面设计指南检查 |
| vercel-labs/agent-skills | vercel-composition-patterns | React 组合模式 |
| vercel-labs/agent-skills | vercel-react-native-skills | React Native 开发指南 |
| vercel-labs/agent-skills | deploy-to-vercel | 部署到 Vercel |
| vercel-labs/next-skills | next-best-practices | Next.js 最佳实践 |
| vercel-labs/next-skills | next-cache-components | Next.js 16 缓存组件 |
| vercel-labs/next-skills | next-upgrade | Next.js 版本升级 |
| vercel-labs/agent-browser | agent-browser | AI Agent 浏览器自动化 |
| vercel-labs/skills | find-skills | 发现和安装 Agent Skills |

### 四、Microsoft Azure Skills (29 个)

| Skill | 描述 |
|-------|------|
| microsoft-foundry | Azure AI Foundry 代理管理 |
| azure-compute | Azure VM 和 VMSS 建议 |
| azure-kubernetes | 生产级 AKS 集群 |
| azure-messaging | Event Hubs 和 Service Bus |
| azure-storage | Azure 存储服务 |
| azure-ai | Azure AI: Search, Speech, OpenAI |
| azure-compliance | Azure 合规和安全审计 |
| azure-cost | Azure 成本管理 |
| azure-deploy | 执行 Azure 部署 |
| azure-diagnostics | 调试 Azure 生产问题 |
| azure-enterprise-infra-planner | 企业级 Azure 基础设施架构 |
| azure-hosted-copilot-sdk | 构建 GitHub Copilot SDK |
| azure-kusto | Azure Data Explorer (KQL) |
| azure-prepare | 准备 Azure 应用部署 |
| azure-quotas | Azure 配额和使用管理 |
| azure-rbac | Azure RBAC 角色查找 |
| azure-upgrade | 升级 Azure 工作负载 |
| azure-cloud-migrate | 跨云迁移到 Azure |
| entra-app-registration | Entra ID 应用注册和 OAuth |
| entra-agent-id | 配置 Entra Agent 身份 |
| appinsights-instrumentation | 使用 Application Insights 监控 |
| aigateway | Azure API Management 作为 AI 网关 |
| airunway-aks-setup | 在 AKS 上设置 AI Runway |

### 五、obra/superpowers - 开发工作流 (14 个)

| Skill | 描述 |
|-------|------|
| brainstorming | 创造性工作前的头脑风暴 |
| dispatching-parallel-agents | 并行执行独立任务 |
| executing-plans | 执行实现计划 |
| finishing-a-development-branch | 集成已完成工作 |
| receiving-code-review | 处理代码审查反馈 |
| requesting-code-review | 请求代码审查 |
| subagent-driven-development | 使用子代理驱动开发 |
| systematic-debugging | 系统化调试 |
| test-driven-development | 测试驱动开发 |
| using-git-worktrees | 使用 git worktrees 隔离开发 |
| verification-before-completion | 完成前验证 |
| writing-plans | 编写多步任务计划 |
| writing-skills | 创建和编辑 Skills |
| using-superpowers | 开始对话 |

### 六、mattpocock/skills - 工程实践 (14 个)

| Skill | 描述 |
|-------|------|
| diagnose | 系统化调试诊断循环 |
| improve-codebase-architecture | 改进代码库架构 |
| tdd | 测试驱动开发 |
| to-prd | 创建产品需求文档 |
| to-issues | 将计划拆解为 GitHub Issues |
| triage | 问题分类管理 |
| zoom-out | 获取更广泛的上下文 |
| grill-me | 严格追问计划细节 |
| write-a-skill | 创建新的 Agent Skills |
| prototype | 构建一次性原型 |
| handoff | 将对话压缩为交接文档 |
| review | 代码审查 |
| edit-article | 编辑和改进文章 |
| obsidian-vault | 管理 Obsidian 笔记 |

### 七、coreyhaines31/marketingskills - 营销 (41 个)

| Skill | 描述 |
|-------|------|
| seo-audit | SEO 审计和诊断 |
| copywriting | 营销文案撰写 |
| content-strategy | 内容策略规划 |
| paid-ads | Google/Meta/LinkedIn 广告投放 |
| social-content | 社交媒体内容创作 |
| email-sequence | 邮件序列优化 |
| pricing-strategy | 定价和变现策略 |
| programmatic-seo | 规模化 SEO 页面 |
| ab-test-setup | A/B 测试设置 |
| churn-prevention | 减少流失 |
| competitor-alternatives | 竞品对比页 |
| competitor-profiling | 竞品分析 |
| marketing-ideas | 营销创意和策略 |
| marketing-psychology | 营销心理学 |
| landing-page-cro | 落地页转化优化 |
| onboarding-cro | 新用户引导优化 |
| popup-cro | 弹窗优化 |
| signup-flow-cro | 注册流程优化 |
| form-cro | 表单优化 |
| paywall-upgrade-cro | 付费墙优化 |
| referral-program | 推荐/联盟计划 |
| schema-markup | Schema 标记优化 |
| analytics-tracking | 分析跟踪设置 |
| ai-seo | AI 搜索引擎优化 |
| ad-creative | 广告创意生成 |
| cold-email | B2B 冷邮件 |
| community-marketing | 社区营销 |
| co-marketing | 联合营销 |
| customer-research | 用户调研 |
| directory-submissions | 启动目录提交 |
| free-tool-strategy | 免费工具策略 |
| launch-strategy | 产品发布策略 |
| lead-magnets | 引导磁铁 |
| onboarding-cro | 注册后引导 |
| paid-ads | 付费广告 |
| product-marketing-context | 产品营销背景文档 |
| revops | 收入运营 |
| sales-enablement | 销售赋能 |
| site-architecture | 网站架构规划 |
| aso-audit | 应用商店优化 |
| copy-editing | 文案编辑 |

### 八、Expo Skills - 移动端开发 (13 个)

| Skill | 描述 |
|-------|------|
| expo-tailwind-setup | Expo 中设置 Tailwind CSS v4 |
| expo-deployment | 部署到 iOS/Android/Web |
| expo-dev-client | 构建开发客户端 |
| expo-module | 创建 Expo 原生模块 |
| expo-api-routes | Expo Router API 路由指南 |
| expo-cicd-workflows | EAS 工作流 |
| native-data-fetching | 网络请求和数据获取 |
| upgrading-expo | Expo SDK 版本升级 |
| building-native-ui | 构建原生 UI |
| use-dom | Web 代码在 WebView 中使用 |
| eas-update-insights | EAS 更新健康检查 |
| expo-ui-jetpack-compose | 使用 Jetpack Compose |
| expo-ui-swift-ui | 使用 SwiftUI |

### 九、Firebase Agent Skills (15 个)

| Skill | 描述 |
|-------|------|
| firebase-basics | Firebase 基础 |
| firebase-auth-basics | Firebase 认证 |
| firebase-firestore | Firestore 数据库 |
| firebase-hosting-basics | Firebase Hosting 部署 |
| firebase-app-hosting-basics | Firebase App Hosting |
| firebase-crashlytics | Crashlytics 崩溃监控 |
| firebase-remote-config-basics | 远程配置 |
| firebase-ai-logic-basics | Firebase AI Logic (Gemini) |
| firebase-data-connect | Firebase SQL Connect |
| firebase-security-rules-auditor | 安全规则审计 |
| developing-genkit-js | Genkit AI (Node.js/TS) |
| developing-genkit-python | Genkit AI (Python) |
| developing-genkit-go | Genkit AI (Go) |
| developing-genkit-dart | Genkit AI (Dart) |
| xcode-project-setup | Xcode 项目设置 |

### 十、Supabase Skills (2 个)

| Skill | 描述 |
|-------|------|
| supabase | Supabase 产品全支持 |
| supabase-postgres-best-practices | Postgres 优化最佳实践 |

### 十一、Convex Skills (6 个)

| Skill | 描述 |
|-------|------|
| convex-quickstart | Convex 快速入门 |
| convex-setup-auth | 设置认证 |
| convex-create-component | 创建组件 |
| convex-migration-helper | 迁移助手 |
| convex-performance-audit | 性能审计 |
| convex | Convex 全功能支持 |

### 十二、shadcn/ui (1 个)

| Skill | 描述 |
|-------|------|
| shadcn | shadcn/ui 组件管理 |

### 十三、Better Auth Skills (5 个)

| Skill | 描述 |
|-------|------|
| better-auth-best-practices | 配置 Better Auth 服务端和客户端 |
| create-auth-skill | 脚手架认证功能 |
| email-and-password-best-practices | 邮箱验证和密码策略 |
| organization-best-practices | 多租户组织配置 |
| two-factor-authentication-best-practices | TOTP、OTP、备份码配置 |

### 十四、Playwright Best Practices (1 个)

| Skill | 描述 |
|-------|------|
| playwright-best-practices | 编写和调试 Playwright 测试 |

### 十五、Firecrawl Skills (9 个)

| Skill | 描述 |
|-------|------|
| firecrawl-agent | Firecrawl Agent |
| firecrawl | Firecrawl CLI |
| firecrawl-crawl | 爬取网站 |
| firecrawl-download | 下载和提取网页内容 |
| firecrawl-interact | 与网页交互 |
| firecrawl-map | 网站地图映射 |
| firecrawl-parse | 解析 HTML 为 Markdown/结构化数据 |
| firecrawl-scrape | 抓取单个页面 |
| firecrawl-search | 搜索网页 |

### 十六、Browser-Use Skills (4 个)

| Skill | 描述 |
|-------|------|
| browser-use | 浏览器自动化：测试、填表、截图、数据提取 |
| cloud | 云端浏览器 |
| open-source | 开源版本 |
| remote-browser | 远程浏览器控制 |

### 十七、Lark/飞书 Skills (25 个)

| Skill | 描述 |
|-------|------|
| lark-doc | 飞书云文档 |
| lark-im | 飞书即时通讯 |
| lark-mail | 飞书邮箱 |
| lark-calendar | 飞书日历 |
| lark-base | 飞书多维表格 |
| lark-sheets | 飞书电子表格 |
| lark-approval | 飞书审批 |
| lark-attendance | 飞书考勤打卡 |
| lark-task | 飞书任务 |
| lark-okr | 飞书 OKR |
| lark-drive | 飞书云空间 |
| lark-vc | 飞书视频会议 |
| lark-vc-agent | 飞书视频会议代参会 |
| lark-wiki | 飞书知识库 |
| lark-slides | 飞书幻灯片 |
| lark-markdown | 飞书 Markdown |
| lark-minutes | 飞书妙记 |
| lark-whiteboard | 飞书白板 |
| lark-event | 飞书实时事件订阅 |
| lark-openapi-explorer | 飞书 OpenAPI 探索 |
| lark-skill-maker | 创建自定义 Skill |
| lark-shared | 飞书 CLI 共享基础 |
| lark-workflow-meeting-summary | 会议纪要整理工作流 |
| lark-workflow-standup-report | 日程待办摘要 |

### 十八、inference.sh Skills (100+ 个)

分为以下几大类：

#### 内容创作指南 (30+)
AI 内容管道、播客制作、内容复用、提示词工程、视频提示词、社交媒体内容、LinkedIn 内容、Twitter 线程、案例研究、技术博客写作、新闻通讯、新闻稿、SEO 内容简报等。

#### 设计 (10+)
应用商店截图、书籍封面、角色设计、数据可视化、邮件设计、落地页、Logo 设计、OG 图片、路演视觉、YouTube 缩略图。

#### 图片生成 (10+)
50+ 模型图片生成、FLUX、GPT-Image-2、Qwen-Image、Nano-Banana、背景移除、图片放大等。

#### 视频生成 (12+)
40+ 模型视频、Google Veo、Seedance、HappyHorse、AI 头像视频、Remotion 渲染、故事板等。

#### 音频处理 (12+)
AI 音乐生成、ElevenLabs TTS/STT/音乐/音效/变声、语音克隆、多说话人对话等。

#### 工具 (10+)
Agent 工具集、Python 执行器、浏览器自动化、Web 搜索、LLM 模型访问、RAG 管道等。

#### UI 组件 (4 个)
Chat UI、Agent UI、Tools UI、Widgets UI。

### 十九、HyperFrames - 视频动画 (12 个)

| Skill | 描述 |
|-------|------|
| hyperframes | 创建视频合成、动画、标题卡 |
| hyperframes-cli | HyperFrames CLI 开发循环 |
| hyperframes-media | 资产预处理 |
| hyperframes-registry | 安装和连接注册表模块 |
| animejs | Anime.js 适配器 |
| css-animations | CSS 动画适配器 |
| gsap | GSAP 动画参考 |
| lottie | Lottie 适配器 |
| three | Three.js/WebGL 适配器 |
| waapi | Web Animations API 适配器 |
| tailwind | Tailwind CSS v4.2 |
| remotion-to-hyperframes | Remotion 转换为 HyperFrames |

### 二十、agentspace-so - AI 生成 (14 个)

| 仓库 | Skill | 描述 |
|------|-------|------|
| runcomfy-agent-skills | video-edit | 视频编辑 |
| runcomfy-agent-skills | image-to-video | 图片转视频 |
| runcomfy-agent-skills | seedance-v2 | Seedance 2.0 视频生成 |
| runcomfy-agent-skills | nano-banana-2 | Gemini 3.1 图片生成 |
| runcomfy-agent-skills | flux-kontext | Flux Kontext 图片 |
| runcomfy-agent-skills | flux-2-klein | Flux 2 Klein 图片 |
| runcomfy-agent-skills | happyhorse-1.0 | HappyHorse 视频 |
| runcomfy-agent-skills | wan-2-7 | WAN 2.7 视频 |
| runcomfy-agent-skills | kling-3.0 | Kling 3.0 视频 |
| runcomfy-agent-skills | gpt-image-2 | GPT Image 2 |
| runcomfy-agent-skills | gpt-image-edit | GPT Image 编辑 |
| runcomfy-agent-skills | image-edit | 图片编辑 |
| runcomfy-agent-skills | nano-banana-edit | Nano Banana 编辑 |
| skills | agentspace | Agent 平台 |

### 二十一、Google Labs / Stitch Skills (8 个)

| Skill | 描述 |
|-------|------|
| stitch-design | Stitch 设计统一入口 |
| stitch-loop | Stitch 迭代式网站构建 |
| design-md | 设计系统分析生成 DESIGN.md |
| enhance-prompt | UI 提示词优化 |
| react:components | Stitch 转 Vite+React 组件 |
| shadcn-ui | shadcn/ui 组件指南 |
| remotion | Remotion 视频生成 |
| taste-design | Google Stitch 语义设计系统 |

### 二十二、Design Taste Skills (12 个)

| Skill | 描述 |
|-------|------|
| design-taste-frontend | 高级前端 UI/UX |
| high-end-visual-design | 高端视觉设计 |
| redesign-existing-projects | 现有项目升级 |
| minimalist-ui | 极简编辑风格界面 |
| brandkit | 高级品牌套件生成 |
| image-to-code | 图片转代码 |
| imagegen-frontend-web | 前端图片方向 |
| imagegen-frontend-mobile | 移动端图片生成 |
| stitch-design-taste | Google Stitch 语义设计 |
| gpt-taste | 高级 UX/UI 和 GSAP 动效 |
| industrial-brutalist-ui | 原始机械风格界面 |
| full-output-enforcement | 覆盖 LLM 截断行为 |

### 二十三、AI 论文复现 Skills (11 个)

| Skill | 描述 |
|-------|------|
| ai-research-reproduction | 深度学习论文复现主调度器 |
| ai-research-explore | 探索模式端到端调度 |
| analyze-project | 可信模式代码分析 |
| explore-code | 探索模式代码适配 |
| explore-run | 探索模式实验执行 |
| run-train | 可信模式训练执行 |
| safe-debug | 可信模式调试 |
| env-and-assets-bootstrap | 环境和资产引导 |
| minimal-run-and-audit | 可信模式执行和报告 |
| paper-context-resolver | 论文上下文解析 |
| repo-intake-and-plan | 仓库扫描和规划 |

### 二十四、Julius Brussee / Caveman (7 个)

| Skill | 描述 |
|-------|------|
| caveman | 原始风格开发助手 |
| caveman-commit | 原始风格提交 |
| caveman-review | 原始风格代码审查 |
| caveman-compress | 文件压缩 |
| caveman-help | 帮助文档 |
| caveman-stats | 统计信息 |
| cavecrew | 团队管理 |

### 二十五、其他 Skills

| 来源 | Skill | 描述 |
|------|-------|------|
| arvindrk | extract-design-system | 从网站提取设计令牌 |
| browser-use | browser-use | 浏览器自动化 |
| degausai | wonda-cli | 终端生成图片/视频/音乐 |
| emilkowalski | emil-design-eng | Emil Kowalski 的 UI 打磨哲学 |
| nextlevelbuilder | ui-ux-pro-max | UI/UX 设计智能 |
| pbakaus | impeccable | 设计/审查/打磨/审计/优化 |
| remotion-dev | remotion-best-practices | Remotion 最佳实践 |
| scrapegraphai | just-scrape | 爬取和提取结构化数据 |
| sleekdotdesign | sleek-design-mobile-apps | 移动应用设计 |
| soultrace-ai | soultrace | 人格评估 API |
| squirrelscan | audit-website | 网站审计 (230+ 规则) |
| hugmouse | value | 空 Skill (占位符) |
| roin-orca | find-skills | 发现和安装 Agent Skills |
| roin-orca | fun-brainstorming | 创造性头脑风暴 |
| roin-orca | simple | 简化工作流 |

### 二十六、wshobson/agents - 全栈插件集 (150+ 个 Skills)

按领域分类：

| 领域 | 包含 Skills |
|------|------------|
| **后端开发** | API 设计、CQRS、事件溯源、微服务、Saga、Temporal 工作流 |
| **云基础设施** | 成本优化、多云、服务网格、Terraform |
| **开发必备** | 认证、代码审查、调试、E2E 测试、Git 工作流、单体仓库、SQL 优化 |
| **Python 开发** | 异步、反模式、设计模式、错误处理、可观测性、打包、测试、类型安全 |
| **LLM 应用** | Embeddings、混合搜索、LangChain、RAG、提示词工程、向量调优 |
| **Kubernetes** | GitOps、Helm、K8s 清单、安全策略 |
| **UI 设计** | 无障碍、设计系统、交互设计、iOS/Android 移动设计、响应式、Web 组件 |
| **安全扫描** | 攻击树、SAST、STRIDE、威胁缓解 |
| **Shell 脚本** | Bash 防御模式、Bats 测试、ShellCheck |
| **其他** | 区块链、数据分析、文档生成、游戏开发、Incident Response、量化交易、逆向工程等 |

### 二十七、xixu-me/skills (11 个)

| Skill | 描述 |
|-------|------|
| github-actions-docs | GitHub Actions 文档 |
| xget | 文件下载工具 |
| tzst | 时区转换工具 |
| xdrop | 文件上传工具 |
| develop-userscripts | 开发用户脚本 |
| skills-cli | Skills 命令行工具 |
| use-my-browser | 浏览器控制 |
| secure-linux-web-hosting | 安全 Linux Web 托管 |
| openclaw-secure-linux-cloud | 安全 Linux 云 |
| opensource-guide-coach | 开源指南教练 |
| readme-i18n | README 国际化 |

---

## 使用方式

### 1. 作为 AI Agent Skill 安装

如果你使用的是 Claude Code、Cursor、Cline 等支持 Agent Skills 的工具：

```bash
# 使用 npx skills add 安装指定仓库
npx skills add 1239261/skills
```

### 2. 手动使用

直接在仓库中找到对应的 `SKILL.md` 文件，将其内容添加到你的 Agent 配置中：

```
skills/
├── anthropics/
│   └── skills/
│       └── skills/
│           ├── frontend-design/
│           │   └── SKILL.md      ← 复制此文件内容到你的 Agent 配置
│           └── ...
├── microsoft/
│   └── azure-skills/
│       └── skills/
│           ├── azure-compute/
│           │   └── SKILL.md
│           └── ...
└── ...
```

### 3. 作为参考文档

直接浏览对应的 `SKILL.md` 文件获取最佳实践指南：

```bash
# 查看 React 最佳实践
cat vercel-labs/agent-skills/skills/react-best-practices/SKILL.md

# 查看 TDD 指南
cat obra/superpowers/skills/test-driven-development/SKILL.md

# 查看 API 质量检查器
cat api-quality-checker/SKILL.md
```

---

## 目录结构

```
skills/
├── api-quality-checker/          # 自定义 API 质量检查
├── deploy-website/               # 网站部署
├── feature-design/               # 需求设计
├── feature-implementer/          # 功能实现
├── implementation-planner/       # 实施计划
├── project-wiki/                 # 项目文档
├── agentspace-so/                # AI 内容生成
├── anthropics/                   # Anthropic 官方 Skills
├── arvindrk/                     # 设计系统提取
├── better-auth/                  # 认证框架
├── browser-use/                  # 浏览器自动化
├── coreyhaines31/                # 营销 Skills (41个)
├── currents-dev/                 # Playwright 最佳实践
├── degausai/                     # Wonda CLI
├── emilkowalski/                 # UI 打磨哲学
├── expo/                         # Expo 移动端开发
├── firebase/                     # Firebase 开发
├── firecrawl/                    # 网页爬取
├── get-convex/                   # Convex 后端
├── google-labs-code/             # Stitch 设计
├── heygen-com/                   # HyperFrames 动画
├── hugmouse/                     # 占位符
├── inference-sh-skills/          # inference.sh 工具集 (100+)
├── juliusbrussee/                # Caveman 原始风格
├── larksuite/                    # 飞书/ Lark (25个)
├── leonxlnx/                     # Design Taste
├── lllllllama/                   # AI 论文复现
├── mattpocock/                   # 工程实践
├── microsoft/                    # Azure Skills (29个)
├── nextlevelbuilder/             # UI/UX Pro Max
├── obra/                         # Superpowers 工作流
├── pbakaus/                      # Impeccable 打磨
├── remotion-dev/                 # Remotion 视频
├── roin-orca/                    # 工具 Skills
├── scrapegraphai/                # 数据爬取
├── sentry/                       # Sentry 监控 (未克隆)
├── shadcn/                       # shadcn/ui
├── sleekdotdesign/               # 移动设计
├── soultrace-ai/                 # 人格评估
├── squirrelscan/                 # 网站审计
├── supabase/                     # Supabase 开发
├── vercel-labs/                  # Vercel 开发
├── wshobson/                     # 全栈插件集 (150+)
└── xixu-me/                      # 综合工具集
```

---

## 统计

| 指标 | 数量 |
|------|------|
| GitHub 仓库 | 43 |
| Skills 总数 | 620+ |
| 覆盖领域 | 前端、后端、移动端、DevOps、营销、设计、AI、安全等 |

## 来源

所有 Skills 均来自 [skills.sh](https://skills.sh) 官方技能市场。
