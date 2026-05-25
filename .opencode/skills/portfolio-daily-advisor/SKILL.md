---
name: portfolio-daily-advisor
description: Given your portfolio holdings (from Excel or direct input) and watchlist, generates daily trading advice. Analyzes valuation, capital flow, technical indicators, industry trends, and provides "cut loss vs hold" analysis. Supports Excel-based portfolio management with natural language commands.
arguments:
  - name: source
    description: Data source: excel (read from file) or direct (from parameters). Default: direct
    required: false
  - name: excel_path
    description: Path to holdings CSV file. Default: /workspace/.opencode/skills/portfolio-daily-advisor/data/holdings.csv
    required: false
  - name: holdings
    description: (Direct mode) List of asset codes you hold (e.g., 515980,561560)
    required: false
  - name: holding_cost
    description: (Direct mode) Cost basis for each held asset (e.g., "1.050,1.380")
    required: false
  - name: holding_shares
    description: (Direct mode) Number of shares for each asset (e.g., "973,500")
    required: false
  - name: watchlist
    description: (Direct mode) Asset codes watching but not holding
    required: false
  - name: investment_style
    description: Investment style: conservative, balanced, aggressive. Default: balanced
    required: false
  - name: holding_period
    description: Holding period: short (1-4w), medium (1-6m), long (6m+). Default: medium
    required: false
  - name: action
    description: (Excel mode) Action: add_holding, add_to_watchlist, view_portfolio
    required: false
---

# Portfolio Daily Advisor

## 🚀 快速开始

**命令式调用** (推荐):
```
今日建议
```
或者
```
/portfolio-daily-advisor source=excel
```

**自动生成**:
- ✅ 自动读取 `/workspace/portfolio/holdings.csv`
- ✅ 自动获取实时净值 (天天基金网、新浪财经)
- ✅ 自动计算最新盈亏
- ✅ 自动生成操作建议
- ✅ 自动更新 CSV 文件

**持仓管理命令**:
```
添加到持仓 00700 腾讯控股 500 股 成本 380 元
添加到关注 515980
查看持仓
```

---

Generate daily trading advice and market predictions for your portfolio holdings. This skill combines real-time market data analysis with personalized recommendations based on your cost basis and investment style.

## Overview

This skill provides:
1. **Real-time Data Analysis** - Current price, valuation, capital flow
2. **Position Analysis** - Profit/loss based on your cost basis
3. **Trading Recommendations** - Buy/Hold/Sell with reasoning
4. **Target Prices** - Support/resistance levels, take-profit/stop-loss
5. **Risk Assessment** - Key risks and watch items
6. **Daily Prediction** - Short-term trend prediction
7. **Capital Flow Alerts** - Detect "大涨后资金悄悄撤退" patterns
8. **Industry Recommendations** - High liquidity, large AUM, low risk ETFs/LOFs ⭐ **NEW**

---

## 🌟 Industry Recommendation Engine

### When User Asks "推荐行业标的" (Recommend Industry ETFs/LOFs)

The skill will recommend ETFs/LOFs based on **3 core criteria**:

#### 1. 流动性高 (High Liquidity) 🟢

| Metric | Threshold | Weight |
|--------|-----------|--------|
| **日均成交额** | >5 亿元 | 35% |
| **换手率** | 5-30% (适中) | 25% |
| **买卖价差** | <0.5% | 20% |
| **成交连续性** | 交易时段连续成交 | 20% |

**评分标准**:
- ⭐⭐⭐⭐⭐ 日均>10 亿 + 换手 10-20%
- ⭐⭐⭐⭐ 日均 5-10 亿 + 换手 5-30%
- ⭐⭐⭐ 日均 1-5 亿
- ⭐⭐ 日均<1 亿

#### 2. 总量大 (Large AUM) 🟢

| Metric | Threshold | Weight |
|--------|-----------|--------|
| **基金规模** | >20 亿元 | 40% |
| **基金份额** | 稳定增长 | 30% |
| **持有人数** | >10 万户 | 30% |

**规模评分**:
- ⭐⭐⭐⭐⭐ >100 亿元 (超大规模)
- ⭐⭐⭐⭐ 50-100 亿元 (大规模)
- ⭐⭐⭐ 20-50 亿元 (中等规模)
- ⭐⭐ <20 亿元 (小规模)

#### 3. 抗风险能力强 (Strong Risk Resistance) 🟢

| Metric | Threshold | Weight |
|--------|-----------|--------|
| **最大回撤 (1 年)** | <15% | 30% |
| **波动率 (年化)** | <25% | 25% |
| **夏普比率** | >1.0 | 25% |
| **跟踪误差** | <2% | 20% |

**风险评级**:
- 🟢 **低风险**: 回撤<10%, 波动率<15%, 夏普>1.5
- 🟡 **中风险**: 回撤 10-20%, 波动率 15-25%, 夏普 0.8-1.5
- 🔴 **高风险**: 回撤>20%, 波动率>25%, 夏普<0.8

---

### Recommended ETFs/LOFs by Industry (2026 Reference)

| 行业 | 推荐标的 | 代码 | 规模 | 日均成交 | 风险等级 | 综合评分 |
|------|---------|------|------|---------|---------|---------|
| **互联网** | 港美互联网 LOF | 160644 | 3.8 亿 | 1-2 亿 | 🟡 中 | ⭐⭐⭐⭐ |
| **半导体** | 半导体 ETF | 512480 | 150 亿 | 15 亿 | 🟡 中 | ⭐⭐⭐⭐⭐ |
| **电力** | 电力 ETF | 561560 | 30 亿 | 1 亿 | 🟢 低 | ⭐⭐⭐⭐ |
| **恒生科技** | 恒生科技 ETF | 513180 | 200 亿 | 20 亿 | 🟡 中 | ⭐⭐⭐⭐⭐ |
| **医药** | 医药 ETF | 512010 | 80 亿 | 8 亿 | 🟢 低 | ⭐⭐⭐⭐ |
| **消费** | 消费 ETF | 159928 | 60 亿 | 5 亿 | 🟢 低 | ⭐⭐⭐⭐ |
| **新能源** | 新能源汽车 ETF | 515030 | 40 亿 | 4 亿 | 🟠 中高 | ⭐⭐⭐ |
| **人工智能** | 人工智能 ETF | 515980 | 35 亿 | 3 亿 | 🟠 中高 | ⭐⭐⭐ |
| **红利** | 红利 ETF | 510880 | 120 亿 | 10 亿 | 🟢 低 | ⭐⭐⭐⭐⭐ |
| **沪深 300** | 沪深 300ETF | 510300 | 800 亿 | 50 亿 | 🟢 低 | ⭐⭐⭐⭐⭐ |

---

### Output Format for Recommendations

When user asks "推荐 [行业] 标的" or "推荐流动性好的 ETF":

```markdown
## 🌟 行业标的推荐

### 推荐标的：[名称] ([代码])

**综合评分**: ⭐⭐⭐⭐⭐ (90/100)

#### 流动性分析 🟢
| 指标 | 数值 | 评级 |
|------|------|------|
| 日均成交额 | 15 亿元 | ⭐⭐⭐⭐⭐ |
| 换手率 | 12% | ⭐⭐⭐⭐ |
| 买卖价差 | 0.3% | ⭐⭐⭐⭐⭐ |

#### 规模分析 🟢
| 指标 | 数值 | 评级 |
|------|------|------|
| 基金规模 | 150 亿元 | ⭐⭐⭐⭐⭐ |
| 基金份额 | 稳定增长 | ⭐⭐⭐⭐ |

#### 抗风险能力 🟢
| 指标 | 数值 | 评级 |
|------|------|------|
| 最大回撤 (1 年) | -12% | ⭐⭐⭐⭐ |
| 波动率 (年化) | 18% | ⭐⭐⭐⭐ |
| 夏普比率 | 1.35 | ⭐⭐⭐⭐ |

**整体风险等级**: 🟢 低风险 / 🟡 中风险 / 🔴 高风险

**推荐理由**:
1. [流动性理由]
2. [规模理由]
3. [抗风险能力理由]
4. [行业前景理由]

**投资建议**:
- **建仓价位**: X.XX 元以下
- **目标仓位**: 10-20%
- **止损价位**: X.XX 元 (-X%)
- **止盈价位**: X.XX 元 (+X%)

**风险提示**:
- [风险 1]
- [风险 2]
```

---

### Filtering Logic

When recommending, apply these filters:

```python
# Pseudo-code for recommendation filtering

def recommend_etf(industry=None, risk_level=None, min_aum=None, min_volume=None):
    candidates = all_etfs
    
    # Filter by industry if specified
    if industry:
        candidates = [etf for etf in candidates if etf.industry == industry]
    
    # Filter by minimum AUM (default 20 亿)
    if min_aum is None:
        min_aum = 20  # 亿元
    candidates = [etf for etf in candidates if etf.aum >= min_aum]
    
    # Filter by minimum daily volume (default 5 亿)
    if min_volume is None:
        min_volume = 5  # 亿元
    candidates = [etf for etf in candidates if etf.avg_daily_volume >= min_volume]
    
    # Filter by risk level if specified
    if risk_level:
        candidates = [etf for etf in candidates if etf.risk_level == risk_level]
    
    # Sort by composite score
    candidates.sort(key=lambda x: x.composite_score, reverse=True)
    
    return candidates[:5]  # Return top 5
```

---

### Special Alerts for Recommendations

| Alert Type | Trigger | Action |
|-----------|---------|--------|
| **高溢价预警** | 溢价率>10% | 🔴 不建议二级市场买入 |
| **规模过小** | 规模<5 亿 | 🟠 流动性风险 |
| **成交低迷** | 日均<1 亿 | 🟠 难进出 |
| **回撤过大** | 回撤>25% | 🔴 高风险 |
| **份额大降** | 20 日份额 -20% | 🟠 资金流出 |

---

### Example User Queries

| User Query | Expected Response |
|-----------|------------------|
| "推荐半导体 ETF" | 推荐 512480 等，按流动性/规模/风险排序 |
| "推荐流动性好的科技 ETF" | 筛选日均>5 亿的科技类 ETF |
| "推荐低风险的 ETF" | 推荐红利、沪深 300 等低波动标的 |
| "推荐适合长期持有的行业基金" | 推荐规模大、波动低、行业前景好的标的 |
| "有哪些抗风险的标的推荐" | 推荐最大回撤<15%、夏普>1.0 的标的 |

---

## Special Alert: Capital Flow Divergence

### 🚨 "冲高后净赎回" Warning Pattern

This skill detects dangerous divergence patterns where price rises but capital flees:

| Pattern | Detection Criteria | Risk Level |
|---------|-------------------|------------|
| **大涨后资金撤退** | 5 日涨幅>10% AND 5 日净流出>5% | 🔴 High |
| **连续净赎回** | 连续≥5 日净流出 AND 净值创新高 | 🔴 High |
| **量价背离** | 价格上涨 AND 成交量萎缩>30% | 🟠 Medium |
| **份额大幅下降** | 20 日份额减少>10% AND 净值上涨 | 🟠 Medium |
| **主力出货** | 机构资金净流出 AND 散户资金净流入 | 🔴 High |

### 🏭 Industry Trend Analysis (行业前景分析)

For each holding and watchlist item, analyze industry prospects:

| Factor | Weight | Data Source |
|--------|--------|-------------|
| **Policy Support** | 25% | Government announcements, 5-year plans |
| **Growth Rate** | 25% | Industry revenue/profit growth forecast |
| **Valuation** | 20% | Sector PE vs historical, vs peers |
| **Capital Flow** | 15% | Sector-level net inflow/outflow |
| **Technical** | 15% | Sector index trend (above/below MA) |

**Industry Rating**:
- 🟢 **Overweight**: Score ≥80%, policy support + growth >20%
- 🟡 **Neutral**: Score 50-80%, stable growth 10-20%
- 🔴 **Underweight**: Score <50%, declining or negative growth

### ✂️ "Cut Loss vs Hold" Decision Framework (割肉还是持有)

For positions with losses, systematically evaluate:

```
Decision Matrix:

亏损幅度 | 行业前景 | 基本面 | 资金流 | 建议
--------|---------|--------|--------|------
<-10%   | 🟢 好   | 未变   | 流入   | 💚 坚定持有 + 可补仓
<-10%   | 🟢 好   | 未变   | 流出   | 🟡 持有但观察
<-10%   | 🟡 一般 | 未变   | -      | 🟡 持有等待反弹
<-10%   | 🔴 差   | 恶化   | 流出   | 🔴 坚决割肉
<-20%   | 🟢 好   | 未变   | 流入   | 🟡 持有 + 定投摊薄
<-20%   | 🟢 好   | 未变   | 流出   | 🔴 反弹减仓
<-20%   | 🔴 差   | 恶化   | -      | 🔴 立即割肉
<-30%   | 任何    | 任何   | 任何   | 🔴 重新评估是否值得
```

**Cut Loss Signals** (割肉信号):
| Signal | Criteria | Urgency |
|--------|----------|---------|
| **坚决割肉** | 行业变差 + 基本面恶化 + 亏损>15% | Immediate |
| **反弹减仓** | 行业一般 + 亏损>20% + 资金流出 | 1-2 weeks |
| **止损离场** | 跌破止损线 (如 -15%) | Per plan |
| **换仓调股** | 发现更好标的 + 当前弱势 | 1 month |

**Hold Signals** (持有信号):
| Signal | Criteria | Action |
|--------|----------|--------|
| **坚定持有** | 行业好 + 基本面未变 + 亏损<15% | 不动 + 可补仓 |
| **摊低成本** | 行业好 + 亏损>20% + 有闲钱 | 定投摊薄 |
| **等待反弹** | 行业一般 + 超跌 (RSI<30) | 等待反弹减亏 |

**Watchlist Conversion** (关注转持有):
| Signal | Criteria | Action |
|--------|----------|--------|
| **买入时机** | 行业好 + 低估 + 资金流入 | 建仓 10-20% |
| **继续观望** | 行业好 + 但高估/资金流出 | 等待更好价位 |
| **移除关注** | 行业变差 + 基本面恶化 | 从关注列表删除 |

### 🎯 Buy/Add Position Signals (加仓预警)

| Signal | Detection Criteria | Confidence |
|--------|-------------------|------------|
| **黄金加仓点** | 跌幅>15% AND 估值<历史 30% AND 资金流入 | ⭐⭐⭐ High |
| **定投加仓** | 跌破成本价>10% AND 基本面未变 | ⭐⭐ Medium |
| **突破加仓** | 突破关键压力位 AND 放量>50% | ⭐⭐ Medium |
| **低估加仓** | PE<历史 20% AND 盈利增长>20% | ⭐⭐⭐ High |
| **回调加仓** | 从高点回调>20% AND 止跌信号 | ⭐⭐ Medium |

### 🚪 Exit/Clear Position Signals (清仓预警)

| Signal | Detection Criteria | Urgency |
|--------|-------------------|---------|
| **清仓离场** | 跌幅>25% AND 跌破止损线 AND 基本面恶化 | 🔴 立即 |
| **分批清仓** | 估值>历史 80% AND 资金持续流出 | 🟠 3-5 日 |
| **调仓换股** | 发现更好标的 AND 当前持仓弱势 | 🟡 1-2 周 |
| **止盈清仓** | 达到目标价 AND 出现见顶信号 | 🟠 及时 |

### 💰 Take-Profit Signals (止盈预警)

| Level | Trigger | Action |
|-------|---------|--------|
| **第一止盈位** | 盈利 20% | 减仓 30% 锁定收益 |
| **第二止盈位** | 盈利 40% | 减仓 50% 落袋为安 |
| **第三止盈位** | 盈利 60% | 清仓 或 留底仓 10% |
| **移动止盈** | 从高点回撤>10% | 触发止盈 |

### 🛑 Stop-Loss Signals (止损预警)

| Level | Trigger | Action |
|-------|---------|--------|
| **预警线** | 亏损 8-10% | 🟡 提高警惕，准备止损 |
| **止损线** | 亏损 10-15% | 🟠 执行止损，减仓 50% |
| **强制止损** | 亏损>20% | 🔴 清仓离场 |
| **时间止损** | 持有 3 月无收益 | 🟡 考虑换仓 |

### 📊 Position Adjustment Signals (仓位调整预警)

| Signal | Condition | Adjustment |
|--------|-----------|------------|
| **仓位过重** | 单只>30% 或 单行业>50% | 减仓至合理 |
| **仓位过轻** | 现金>50% AND 市场低估 | 加仓至目标 |
| **再平衡** | 偏离目标仓位>5% | 调整回目标 |
| **风险超限** | 组合风险>承受力 | 降低权益仓位 |

### 📈 Valuation Alert (估值预警)

| Level | PE Percentile | Signal |
|-------|---------------|--------|
| **极度低估** | <10% | 🟢 大举买入 |
| **低估** | 10-30% | 🟢 分批买入 |
| **合理** | 30-70% | 🟡 持有/定投 |
| **高估** | 70-90% | 🟠 分批卖出 |
| **极度高估** | >90% | 🔴 清仓/做空 |

### ⚡ Black Swan Alert (黑天鹅预警)

| Event Type | Examples | Response |
|------------|----------|----------|
| **政策风险** | 行业监管收紧、贸易战 | 立即减仓 50% |
| **公司风险** | 财务造假、高管被查 | 清仓离场 |
| **市场风险** | 大盘暴跌>5%、熔断 | 降至低仓位 |
| **流动性风险** | 连续跌停、无法卖出 | 提前止损 |
| **地缘政治** | 战争、制裁 | 避险资产 |

### Alert Output Format

When detected, the skill will display prominent warnings:

```markdown
## ⚠️ 资金流向警示

**515980 人工智能 ETF** 🔴 高危警示

🚨 "大涨后资金悄悄撤退" 模式已触发

| 指标 | 数值 | 警示阈值 |
|------|------|----------|
| 近 5 日涨幅 | +15.26% | >10% ⚠️ |
| 近 5 日资金流 | -18.65 亿元 | 净流出 ⚠️ |
| 连续净赎回天数 | 14 天 | ≥5 天 ⚠️ |
| 份额变化 (20 日) | -16.27% | >10% ⚠️ |

**解读**: 价格上涨但资金持续流出，存在"越涨越卖"现象，警惕回调风险

**建议**: 
- 持有者：考虑分批止盈，不要追高
- 未持有者：暂观望，等待资金流向转正
```

## Analysis Framework

### 1. Data Collection

| Data Type | Sources | Metrics |
|-----------|---------|---------|
| **Price Data** | Market feeds | Current price, open, high, low, volume |
| **Valuation** | Financial reports | PE, PB, PS, dividend yield |
| **Capital Flow** | Exchange data | Net inflow/outflow, institutional activity |
| **Technical** | Chart analysis | MA, MACD, RSI, Bollinger Bands |
| **Sentiment** | News/social media | Market sentiment score |
| **Fundamentals** | Company reports | Revenue, profit, growth rate |

### 2. Position Analysis

| Metric | Calculation | Interpretation |
|--------|-------------|----------------|
| **Profit/Loss %** | (Current Price - Cost) / Cost × 100 | Green: profit, Red: loss |
| **Position Weight** | Position Value / Total Portfolio | Allocation percentage |
| **Contribution** | Weight × Return % | Contribution to portfolio return |

### 3. Recommendation Logic

#### Buy Signal Criteria

| Condition | Weight |
|-----------|--------|
| Undervalued (PE < historical 30%) | 25% |
| Positive capital flow (5-day net inflow) | 20% |
| Technical breakout (above MA20) | 20% |
| Positive sentiment/news | 15% |
| Strong fundamentals (revenue/profit growth) | 20% |

**Buy**: Score ≥ 70%  
**Hold**: Score 40-70%  
**Sell**: Score < 40%

#### Sell Signal Criteria

| Condition | Weight |
|-----------|--------|
| Overvalued (PE > historical 70%) | 25% |
| Negative capital flow (5-day net outflow) | 20% |
| Technical breakdown (below MA20) | 20% |
| Negative sentiment/news | 15% |
| Weak fundamentals | 20% |

### 4. Target Price Calculation

| Level | Calculation Method |
|-------|-------------------|
| **Support 1** | Recent low or MA20 |
| **Support 2** | MA60 or -10% from current |
| **Support 3** | MA120 or -20% from current |
| **Resistance 1** | Recent high or +5% from current |
| **Resistance 2** | +10% from current |
| **Resistance 3** | +20% from current or historical high |

### 5. Risk Assessment

| Risk Type | Indicators | Level |
|-----------|------------|-------|
| **Valuation Risk** | PE/PB vs historical | High/Medium/Low |
| **Liquidity Risk** | Daily volume | High/Medium/Low |
| **Volatility Risk** | 30-day volatility | High/Medium/Low |
| **Sector Risk** | Sector trend, policy | High/Medium/Low |
| **Concentration Risk** | Position weight | High/Medium/Low |

## Document Templates

### Daily Advice Report (`daily-advice-YYYY-MM-DD.md`)

```markdown
# 投资组合日报 - [YYYY-MM-DD]

**生成时间**: [HH:MM]  
**市场状态**: [开盘前/交易中/收盘后]  
**投资风格**: [保守型/平衡型/进取型]  
**持有周期**: [短期/中期/长期]

---

## 📊 数据源说明

| 标的 | 数据状态 | 数据来源 | 数据日期 | 说明 |
|------|----------|----------|----------|------|
| 515980 | ✅ 实时数据 | 新浪财经 | 2026-05-25 | 交易中实时价格 |
| 561560 | ✅ 最新净值 | 天天基金网 | 2026-05-25 | ETF 实时 IOPV |
| 160644 | ✅ 最新净值 | 天天基金网 | 2026-05-21 | QDII T+1 延迟净值 |
| XXXXXX | 🟡 缓存数据 | CSV 缓存 | 2026-05-20 | 网络获取失败，使用缓存值 |
| YYYYYY | ❌ 数据暂缺 | - | - | 无法获取价格，跳过分析 |

**数据新鲜度说明**:
- ✅ **实时数据**: 交易时间内实时获取（ETF/股票）
- ✅ **最新净值**: T+1 日更新的基金净值（QDII/普通基金）
- 🟡 **缓存数据**: 网络获取失败，使用上次缓存值（仅供参考）
- ❌ **数据暂缺**: 无法获取价格数据，建议手动更新

---

## 📊 持仓 vs 关注

| 类别 | 数量 | 总盈亏 | 今日变化 | 建议操作数 |
|------|------|--------|----------|------------|
| **已持有** | X 只 | +X.XX% | +X.XX% | X 只建议调整 |
| **关注中** | X 只 | - | - | X 只可买入 |

---

## 🏭 行业前景分析

| 行业 | 评级 | 政策支持 | 增长预期 | 估值 | 资金流 | 技术面 |
|------|------|----------|----------|------|--------|--------|
| 人工智能 | 🟡 中性 | ⭐⭐⭐ | +25% | 🟠 偏高 | 🔴 流出 | 🟢 上涨 |
| 电力公用 | 🟢 超配 | ⭐⭐⭐⭐ | +12% | 🟢 合理 | 🟢 流入 | 🟢 上涨 |
| 半导体 | 🟢 超配 | ⭐⭐⭐⭐⭐ | +30% | 🟡 中等 | 🟡 持平 | 🟢 上涨 |

**行业配置建议**:
- ✅ **超配**: 电力公用、半导体
- ⚖️ **标配**: 人工智能
- ❌ **低配**: [行业名称]

---

## 🚨 预警信号总览

### 加仓信号
| 代码 | 信号类型 | 置信度 | 建议加仓比例 | 触发价位 |
|------|----------|--------|-------------|----------|
| 561560 | 低估加仓 | ⭐⭐⭐ | +10% | <1.38 元 |

### 止盈信号
| 代码 | 当前盈利 | 止盈位 | 建议操作 |
|------|----------|--------|----------|
| 515980 | +15.2% | +20% | 接近目标，准备分批止盈 |

### 止损信号
| 代码 | 当前亏损 | 止损线 | 距离止损 | 建议 |
|------|----------|--------|----------|------|
| XXXXXX | -8.5% | -10% | -1.5% | 🟡 准备止损 |

### 清仓信号
| 代码 | 触发原因 | 紧急程度 | 建议操作 |
|------|----------|----------|----------|
| - | - | - | 暂无清仓信号 |

### 仓位预警
| 预警类型 | 当前值 | 合理范围 | 状态 |
|----------|--------|----------|------|
| 单只最大仓位 | 30% | <25% | ⚠️ 略高 |
| 行业集中度 | 50% | <40% | ⚠️ 偏高 |
| 现金比例 | 20% | 10-30% | ✅ 合理 |

### 估值预警
| 代码 | 当前 PE | 历史分位 | 估值状态 | 建议 |
|------|---------|----------|----------|------|
| 515980 | 48 倍 | 72% | 🟠 高估 | 不宜加仓 |
| 561560 | 16 倍 | 45% | 🟢 合理 | 可持有 |

---

## 📍 已持有仓位分析

### 持仓概览

| 代码 | 名称 | 现价 | 成本 | 盈亏% | 仓位% | 行业 | 建议 |
|------|------|------|------|-------|-------|------|------|
| 515980 | 人工智能 ETF | 1.122 | 1.050 | +6.9% | 30% | AI | 持有 |
| 561560 | 电力 ETF | 1.412 | 1.380 | +2.3% | 20% | 电力 | 买入 |

**总盈亏**: +X.XX%  
**今日变化**: +X.XX%

---

## ✂️ 割肉还是持有？(Cut Loss or Hold?)

### 亏损仓位分析

| 代码 | 亏损% | 行业前景 | 基本面 | 资金流 | **建议** | 理由 |
|------|-------|----------|--------|--------|----------|------|
| XXXXXX | -12% | 🟢 好 | 未变 | 流入 | 💚 **坚定持有 + 补仓** | 行业高景气，暂时被套 |
| XXXXXX | -18% | 🟡 一般 | 未变 | 流出 | 🟡 **持有等反弹** | 反弹至 -10% 减仓 |
| XXXXXX | -25% | 🔴 差 | 恶化 | 流出 | 🔴 **坚决割肉** | 行业下行，及时止损 |

### 详细决策分析

#### [代码] [名称] - 亏损 XX%

**现状**:
| 指标 | 数值 | 评价 |
|------|------|------|
| 持仓成本 | X.XX 元 | - |
| 当前价格 | X.XX 元 | - |
| 亏损幅度 | -XX% | 🔴 深度亏损 |
| 持仓金额 | XX,XXX 元 | - |
| 仓位占比 | XX% | [高/中/低] |

**行业前景**:
| 因素 | 评分 | 说明 |
|------|------|------|
| 政策支持 | ⭐⭐⭐⭐ | [具体政策] |
| 行业增速 | +XX% | [增长预期] |
| 竞争格局 | [集中/分散] | [说明] |
| 行业评级 | 🟢🟡🔴 | [超配/中性/低配] |

**公司/基金基本面**:
| 因素 | 变化 | 说明 |
|------|------|------|
| 营收增长 | [好转/持平/恶化] | [数据] |
| 利润增长 | [好转/持平/恶化] | [数据] |
| 估值水平 | [合理/偏高/偏低] | PE XX 倍 |
| 核心竞争力 | [增强/持平/减弱] | [说明] |

**资金流向**:
| 周期 | 净流入 | 评价 |
|------|--------|------|
| 今日 | +X.XX 亿 | [流入/流出] |
| 近 5 日 | +X.XX 亿 | [流入/流出] |
| 近 20 日 | +X.XX 亿 | [流入/流出] |

**决策矩阵**:

```
亏损幅度：-XX% → [轻度/中度/重度]
行业前景：[好/一般/差]
基本面：[未变/恶化]
资金流：[流入/流出]

→ 综合建议：[坚定持有/持有观望/反弹减仓/坚决割肉]
```

**操作策略**:

| 场景 | 操作 | 价位/条件 |
|------|------|----------|
| **最佳情况** | 继续持有 | 行业持续向好 |
| **中性情况** | 反弹减仓 | 反弹至 X.XX 元 (-X%) |
| **最坏情况** | 止损离场 | 跌破 X.XX 元 (-X%) |

**回本所需涨幅**:
| 当前亏损 | 需要上涨 | 难度评估 |
|----------|----------|----------|
| -10% | +11% | 🟢 较易 |
| -20% | +25% | 🟡 中等 |
| -30% | +43% | 🔴 困难 |
| -50% | +100% | 🔴 极难 |

**最终建议**: 

💚🟡🔴 **[坚定持有/持有观望/反弹减仓/坚决割肉]**

**理由**:
1. [Reason 1 - 行业角度]
2. [Reason 2 - 基本面角度]
3. [Reason 3 - 技术面角度]
4. [Reason 4 - 机会成本角度]

**执行计划**:
- 如果 [条件 A] → [操作 A]
- 如果 [条件 B] → [操作 B]
- 如果 [条件 C] → [操作 C]

---

### 🔴 高危警示：[代码] [名称]

🚨 **"大涨后资金悄悄撤退"** 模式已触发

| 指标 | 数值 | 警示阈值 | 状态 |
|------|------|----------|------|
| 近 5 日涨幅 | +15.26% | >10% | ⚠️ 触发 |
| 近 5 日资金流 | -18.65 亿元 | 净流出 | ⚠️ 触发 |
| 连续净赎回天数 | 14 天 | ≥5 天 | ⚠️ 触发 |
| 份额变化 (20 日) | -16.27% | >10% | ⚠️ 触发 |

**解读**: 价格上涨但资金持续流出，存在"越涨越卖"现象，警惕回调风险

**操作建议**: 
- 🟢 持有者：考虑分批止盈，不要追高
- 🔴 未持有者：暂观望，等待资金流向转正
- 📉 目标价下调：从 X.XX 元 调整为 X.XX 元

### 🟠 中危警示：[代码] [名称]

⚠️ **"量价背离"** 模式已触发

| 指标 | 数值 | 警示阈值 | 状态 |
|------|------|----------|------|
| 今日涨幅 | +3.5% | >2% | ⚠️ 触发 |
| 成交量变化 | -35% | <-30% | ⚠️ 触发 |

**解读**: 价格上涨但成交量萎缩，上涨动能不足

---

## 个股/ETF 详细分析

### 1. [代码] [名称] 🔴🟢🟡

**当前数据**:
| 指标 | 数值 | 评价 |
|------|------|------|
| 现价 | X.XX 元 | - |
| 数据状态 | ✅ 实时/🟡 缓存/❌ 暂缺 | [状态说明] |
| 数据日期 | 2026-05-25 | [T+0/T+1] |
| 数据来源 | 天天基金网/新浪财经 | [可靠性] |
| 今开 | X.XX 元 | - |
| 最高 | X.XX 元 | - |
| 最低 | X.XX 元 | - |
| 成交量 | XX 万手 | [放量/缩量/持平] |
| 成交额 | XX 亿元 | - |
| 涨跌幅 | +X.XX% | - |
| 换手率 | X.XX% | [活跃/一般/低迷] |

💡 **数据提示**: 
- ✅ **实时数据**: 交易时间内实时获取，准确可靠
- 🟡 **缓存数据**: 网络获取失败，使用上次缓存值，仅供参考
- ❌ **数据暂缺**: 无法获取价格，建议手动更新或稍后重试

**估值分析**:
| 指标 | 当前值 | 历史分位 | 评价 |
|------|--------|----------|------|
| PE(TTM) | XX 倍 | XX% | [高估/合理/低估] |
| PB | X.X 倍 | XX% | [高估/合理/低估] |
| 股息率 | X.X% | - | [高/中/低] |

**资金流向**:
| 周期 | 净流入 (亿元) | 评价 |
|------|---------------|------|
| 今日 | +X.XX | [流入/流出] |
| 近 3 日 | +X.XX | [流入/流出] |
| 近 5 日 | +X.XX | [流入/流出] |
| 近 20 日 | +X.XX | [流入/流出] |

**技术指标**:
| 指标 | 数值 | 信号 |
|------|------|------|
| MA5 | X.XX | [上穿/下穿/持平] |
| MA20 | X.XX | [上方/下方] |
| MA60 | X.XX | [上方/下方] |
| MACD | X.XX | [金叉/死叉] |
| RSI | XX | [超买/中性/超卖] |

**持仓分析**:
| 指标 | 数值 |
|------|------|
| 持仓成本 | X.XX 元 |
| 当前盈亏 | +X.XX% |
| 持仓金额 | XX,XXX 元 |
| 仓位占比 | XX% |

**今日建议**: 🔴🟢🟡 [买入/持有/卖出/观望]

**建议理由**:
1. [Reason 1]
2. [Reason 2]
3. [Reason 3]

---

### 📊 操作信号详情

#### 加仓信号
| 信号类型 | 触发条件 | 置信度 | 建议操作 |
|----------|----------|--------|----------|
| 黄金加仓点 | 跌 15% + 低估 + 资金流入 | ⭐⭐⭐ | 加仓 10-20% |
| 突破加仓 | 突破压力位 + 放量 50% | ⭐⭐ | 加仓 5-10% |
| 定投加仓 | 跌破成本>10% | ⭐⭐ | 继续定投 |

**当前状态**: 
- ✅ 黄金加仓点：未触发
- ✅ 突破加仓：未触发
- 🟡 定投加仓：可执行（跌破成本 X%）

#### 止盈信号
| 止盈位 | 触发条件 | 当前状态 | 操作 |
|--------|----------|----------|------|
| 第一止盈位 | +20% | ❌ 未触发 (当前+X%) | 减仓 30% |
| 第二止盈位 | +40% | ❌ 未触发 | 减仓 50% |
| 第三止盈位 | +60% | ❌ 未触发 | 清仓/留底仓 |
| 移动止盈 | 回撤>10% | ❌ 未触发 | 执行止盈 |

#### 止损信号
| 止损位 | 触发条件 | 当前状态 | 操作 |
|--------|----------|----------|------|
| 预警线 | -8%~ -10% | 🟡 观察中 | 准备止损 |
| 止损线 | -10%~ -15% | ❌ 未触发 | 减仓 50% |
| 强制止损 | < -20% | ❌ 未触发 | 立即清仓 |

**当前距离止损**: -X.X% (成本 X.XX 元，现价 X.XX 元)

#### 清仓信号
| 清仓类型 | 触发条件 | 当前状态 | 建议 |
|----------|----------|----------|------|
| 强制清仓 | 跌>25% + 基本面恶化 | ❌ 未触发 | - |
| 分批清仓 | 估值>80% + 资金流出 | ❌ 未触发 | - |
| 止盈清仓 | 达目标价 + 见顶信号 | ❌ 未触发 | - |

---

### 2. [代码] [名称]

[Repeat structure for each holding]

---

## 📑 关注列表分析 (Watchlist)

### 关注标的概览

| 代码 | 名称 | 现价 | 行业 | 评级 | 建议 | 关注理由 |
|------|------|------|------|------|------|----------|
| 512480 | 半导体 ETF | X.XX | 半导体 | 🟢 超配 | 可建仓 | 国产替代 + 低估 |
| 512760 | 芯片 ETF | X.XX | 芯片 | 🟢 超配 | 观望 | 等待回调 |

### 关注转持有分析

#### [代码] [名称]

**关注理由**:
1. [Reason 1 - why on watchlist]
2. [Reason 2]

**当前状态**:
| 指标 | 数值 | 买入条件 | 状态 |
|------|------|----------|------|
| 现价 | X.XX 元 | <X.XX 元 | ❌ 未满足 |
| PE | XX 倍 | <历史 30% | ✅ 满足 |
| 资金流 | +X.XX 亿 | 净流入 | ✅ 满足 |
| 技术面 | [突破/回调] | 回调到位 | ❌ 未满足 |

**行业前景**:
| 因素 | 评分 | 说明 |
|------|------|------|
| 政策支持 | ⭐⭐⭐⭐ | [政策] |
| 行业增速 | +XX% | [预期] |
| 估值水平 | 🟢 低估 | PE XX 倍 |
| 行业评级 | 🟢 超配 | - |

**买入建议**:

| 条件 | 操作 |
|------|------|
| **立即买入** | 现价< X.XX 元 AND 资金流入 |
| **分批买入** | 现价< X.XX 元，分 3 批 |
| **继续观望** | 等待回调至 X.XX 元 |
| **移除关注** | 行业变差或基本面恶化 |

**预期收益/风险**:
| 指标 | 数值 |
|------|------|
| 目标价 | X.XX 元 (+X%) |
| 止损价 | X.XX 元 (-X%) |
| 风险收益比 | 1:X |

---

## 组合整体建议

### 当前配置分析

| 资产类别 | 仓位% | 建议配置% | 偏差 |
|----------|-------|-----------|------|
| 股票/权益类 | XX% | XX% | +X% |
| 债券类 | XX% | XX% | -X% |
| 现金 | XX% | XX% | +X% |

### 组合风险评分

| 风险维度 | 评分 (1-5) | 说明 |
|----------|-----------|------|
| 集中度风险 | X | [集中/分散] |
| 波动风险 | X | [高/中/低] |
| 流动性风险 | X | [好/中/差] |
| 估值风险 | X | [高/中/低] |
| **综合风险** | **X** | [高/中/低] |

### 今日组合操作建议

| 优先级 | 操作 | 代码 | 金额/比例 | 理由 |
|--------|------|------|-----------|------|
| 1 | 加仓 | 561560 | +500 元 | 估值合理，防御属性 |
| 2 | 持有 | 515980 | - | 短期涨幅大，观望 |
| 3 | 减仓 | XXXXXX | -10% | 止盈锁定收益 |

### 市场展望

**短期 (1 周)**: [预测 + 理由]  
**中期 (1 月)**: [预测 + 理由]  
**长期 (3 月+)**: [预测 + 理由]

### 重点关注

📅 **本周事件**:
- [Event 1] - [Date]
- [Event 2] - [Date]

📊 **数据发布**:
- [Data 1] - [Date]
- [Data 2] - [Date]

---

## 免责声明

本报告仅供参考，不构成投资建议。市场有风险，投资需谨慎。

---

**下一次更新**: [Next trading day 09:00]
```

### Quick Summary (`quick-summary-YYYY-MM-DD.md`)

```markdown
# 今日持仓速览 [YYYY-MM-DD]

## 一句话总结
[Market summary in one sentence]

## 操作建议
| 代码 | 建议 | 关键价位 |
|------|------|----------|
| 515980 | 持有观望 | 支撑 1.05, 压力 1.18 |
| 561560 | 可加仓 | 支撑 1.38, 压力 1.50 |

## 风险提示
⚠️ [Key risk to watch]

## 今日预测
📈 看涨概率：XX%  
📉 看跌概率：XX%  
➖ 震荡概率：XX%
```

## Workflow

### Phase 0: Data Source Selection

1. **Determine Input Mode**
   - If `source="excel"`: Read from Excel file
   - If `source="direct"` or default: Use provided parameters

2. **Excel Mode** (source="excel")
   ```python
   # Read Excel file
   import pandas as pd
   
   excel_path = excel_path or "/workspace/portfolio/holdings.xlsx"
   
   # Read Holdings sheet
   holdings_df = pd.read_excel(excel_path, sheet_name="持仓明细")
   
   # Read Watchlist sheet
   watchlist_df = pd.read_excel(excel_path, sheet_name="关注列表")
   
   # Extract data
   positions = []
   for _, row in holdings_df.iterrows():
       positions.append({
           'code': row['代码'],
           'name': row['名称'],
           'shares': row['持仓数量'],
           'cost': row['成本单价'],
           'total_cost': row['总成本']
       })
   
   watchlist = watchlist_df['代码'].tolist() if not watchlist_df.empty else []
   ```

3. **Direct Mode** (source="direct")
   ```python
   # Parse from parameters
   holdings_list = holdings.split(',')
   costs = holding_cost.split(',') if holding_cost else []
   shares = holding_shares.split(',') if holding_shares else []
   
   positions = []
   for i, code in enumerate(holdings_list):
       positions.append({
           'code': code,
           'cost': float(costs[i]) if i < len(costs) else None,
           'shares': int(shares[i]) if i < len(shares) else None
       })
   ```

### Phase 1: Data Collection

1. **Read Holdings Data**
   - From CSV file: `/workspace/.opencode/skills/portfolio-daily-advisor/data/holdings.csv`
   - Parse: 代码，名称，持仓数量，成本单价，总成本
   - Ignore pre-calculated fields (current price, P/L will be calculated fresh)

2. **Read Watchlist Data**
   - From CSV file or direct input
   - Parse asset codes

3. **Fetch Real-Time NAV/Prices** ⭐ **CRITICAL STEP**
   
   **For Each Holding/Watchlist Item**:
   
   ```bash
   # Use websearch_aisearch to fetch latest NAV
   websearch_aisearch(
     count: 3,
     query: "{代码} {名称} 净值 2026 年 5 月 25 日"
   )
   ```
   
   **Data Sources Priority**:
   1. 天天基金网 (fund.eastmoney.com) - Most reliable for fund NAV
   2. 新浪财经 (finance.sina.com.cn) - Real-time ETF prices
   3. 上海证券交易所/深圳证券交易所 - Official ETF data
   
   **Parsing Strategy**:
   - Look for patterns like "单位净值 X.XXXX" or "报 X.XXX 元"
   - Extract date to ensure it's latest (today or most recent trading day)
   - For ETFs: use real-time price from exchange
   - For LOF/QDII funds: use NAV (may be 1-day delayed)
   
   **Fallback Logic**:
   - If today's data not available → Use yesterday's closing NAV
   - If web search fails → Use last cached value from CSV
   - Mark data source and timestamp in report
   
   **Error Handling**:
   - If price fetch fails for an asset → Mark as "数据暂缺" in report
   - Continue analysis for other assets
   - Inform user which assets lack current data
   
   **Data Freshness Indicator** ⭐:
   In the report, clearly mark data freshness for each holding:
   - ✅ **实时数据**: Today's price (交易中)
   - ✅ **最新净值**: Yesterday/T-1 day NAV (QDII/基金)
   - 🟡 **缓存数据**: Using cached value from CSV (fetch failed)
   - ❌ **数据暂缺**: No data available (analysis skipped)

4. **Update CSV with Current Prices**
   - Write fetched prices back to `/workspace/.opencode/skills/portfolio-daily-advisor/data/holdings.csv`
   - Update: 当前价，当前市值，盈亏金额，盈亏比例，最后更新
   - Preserve: 成本 data, 持仓数量 (never overwrite user input)

5. **Gather Market Data**
   - Index performance (上证指数，深证成指，创业板指)
   - Sector performance (相关行业指数)
   - Market sentiment score

6. **Fetch Technical Indicators**
   - MA5, MA20, MA60
   - MACD
   - RSI
   - Bollinger Bands
   
   **Method**: Use websearch queries like:
   - "{代码} 技术指标 MACD RSI"
   - "{代码} 均线 MA5 MA20"

7. **Gather News & Sentiment**
   - Company/sector news
   - Policy announcements
   - Market sentiment score
   
   **Method**: Use websearch_aisearch with:
   - query: "{名称} 最新动态 行业新闻"
   - time_range: week

### Phase 2: Analysis

1. **Calculate Position P/L**
   - Compare current price to cost basis
   - Calculate portfolio weight
   - Identify losing positions for "cut loss vs hold" analysis

2. **Evaluate Valuation**
   - Compare to historical percentiles
   - Compare to sector peers
   - Industry-relative valuation

3. **Assess Industry Prospects**
   - Policy support score (1-5 stars)
   - Growth rate forecast
   - Competitive landscape
   - Industry rating (Overweight/Neutral/Underweight)

4. **Detect Capital Flow Divergence** ⚠️
   - Check for "大涨后资金撤退" pattern
   - Detect consecutive net redemptions
   - Identify volume-price divergence
   - Calculate fund share changes

5. **Generate "Cut Loss vs Hold" Recommendations**
   - For each losing position:
     - Assess industry outlook
     - Evaluate fundamental changes
     - Check capital flow trend
     - Apply decision matrix
     - Generate specific action plan

6. **Score Buy/Sell Signals**
   - Apply scoring framework
   - Generate recommendation
   - Differentiate holdings vs watchlist

7. **Determine Target Prices**
   - Calculate support/resistance
   - Set take-profit/stop-loss levels
   - Estimate recovery potential for losers

8. **Assess Risks**
   - Identify key risk factors
   - Rate risk levels
   - Flag capital flow warnings prominently

### Phase 3: Report Generation

1. **Create Detailed Report**
   - Fill template sections
   - Add charts/tables
   - Include reasoning

2. **Create Quick Summary**
   - Extract key points
   - One-page overview

3. **Save Files**
   - `.monkeycode/portfolio-advice/YYYY-MM-DD/`
   - `daily-advice-YYYY-MM-DD.md`
   - `quick-summary-YYYY-MM-DD.md`

## Decision Matrix

### Recommendation Logic by Investment Style

| Style | Buy Threshold | Hold Threshold | Sell Threshold |
|-------|---------------|----------------|----------------|
| **Conservative** | Score ≥ 80% | 50-80% | < 50% |
| **Balanced** | Score ≥ 70% | 40-70% | < 40% |
| **Aggressive** | Score ≥ 60% | 30-60% | < 30% |

### Recommendation Logic by Holding Period

| Period | Focus | Key Indicators |
|--------|-------|----------------|
| **Short (1-4 weeks)** | Technical, sentiment | MA, MACD, volume, news |
| **Medium (1-6 months)** | Valuation, flow | PE, PB, capital flow |
| **Long (6+ months)** | Fundamentals | Revenue, profit, growth |

## Special Handling

### ETF Analysis

| Focus Area | Metrics |
|------------|---------|
| **Index Tracking** | Tracking error, premium/discount |
| **Underlying Assets** | Top holdings, sector allocation |
| **Liquidity** | AUM, daily volume, bid-ask spread |
| **Cost** | Expense ratio |

### Stock Analysis

| Focus Area | Metrics |
|------------|---------|
| **Valuation** | PE, PB, PEG, DCF |
| **Growth** | Revenue growth, profit growth |
| **Quality** | ROE, debt ratio, cash flow |
| **Dividends** | Dividend yield, payout ratio |

### Fund Analysis

| Focus Area | Metrics |
|------------|---------|
| **Performance** | Return vs benchmark, ranking |
| **Manager** | Track record, tenure |
| **Holdings** | Concentration, turnover |
| **Risk** | Sharpe ratio, max drawdown |

## Data Sources

### Recommended APIs

| Data Type | Source |
|-----------|--------|
| **Prices** | 同花顺，东方财富，新浪财经 |
| **Fundamentals** | 公司财报，Wind，Choice |
| **Capital Flow** | 交易所，同花顺 iFinD |
| **Technical** | 计算得出 |
| **News** | 财经媒体，公告 |

## Output Files

```
.monkeycode/portfolio-advice/
├── YYYY-MM-DD/
│   ├── daily-advice-YYYY-MM-DD.md    # Full report
│   └── quick-summary-YYYY-MM-DD.md   # Quick summary
├── YYYY-MM-DD+1/
│   └── ...
└── holdings-config.yaml              # Portfolio configuration
```

## Holdings Configuration

### Configuration File Format

```yaml
# holdings-config.yaml
portfolio:
  name: "我的投资组合"
  style: "balanced"  # conservative, balanced, aggressive
  holding_period: "medium"  # short, medium, long
  
holdings:
  - code: "515980"
    name: "人工智能 ETF"
    cost: 1.050
    target_weight: 30%
    
  - code: "561560"
    name: "电力 ETF"
    cost: 1.380
    target_weight: 20%
    
  - code: "600519"
    name: "贵州茅台"
    cost: 1800
    target_weight: 25%

thresholds:
  stop_loss: -15%
  take_profit: +20%
  rebalance_threshold: 5%
```

## Examples

### Example Input

```
/java-entry-analyzer holdings="515980,561560" holding_cost="1.050,1.380" investment_style="balanced" holding_period="medium"
```

### Example Output

See template above for expected output format.

## Notes

- Reports generated before market open (09:00) or after close (15:30)
- Real-time data during trading hours
- Use Chinese for all output
- Include both absolute and percentage changes
- Highlight significant deviations from cost basis
- Flag high-risk positions prominently
- Update recommendations based on latest news
- Preserve historical reports for tracking accuracy
- Include confidence levels for predictions
- Never guarantee specific returns
- Always include risk disclaimers

---

## Excel Management Integration

### Quick Commands for Excel Maintenance

You can maintain your portfolio Excel file with natural language:

#### Add/Update Holdings

```
持仓 160644 973 股 成本 2.05
买入 515980 500 股 价格 1.10
加仓 160644 200 股
更新持仓 515980 成本=2.10 数量=1000
```

#### Manage Watchlist

```
关注 561560
关注 512480
移除关注 512480
```

#### View Portfolio

```
查看持仓
查看关注
查看交易记录
```

#### Generate Daily Advice

```
今日建议
/portfolio-daily-advisor source="excel"
```

### Excel File Template

If Excel file doesn't exist, create with this structure:

**File**: `/workspace/portfolio/holdings.xlsx`

| Sheet Name | Purpose |
|-----------|---------|
| 持仓明细 | Holdings with cost, shares, P/L |
| 关注列表 | Watchlist with reasons and target prices |
| 交易记录 | Transaction history |
| 汇总统计 | Portfolio summary statistics |

### Auto-Sync Workflow

1. User inputs: "持仓 160644 973 股 成本 2.05"
2. Skill writes to Excel → 持仓明细 sheet
3. Calculates total cost = 973 × 2.05 = 1994.65
4. Fetches current price (e.g., 2.10)
5. Calculates P/L = (2.10 - 2.05) / 2.05 = +2.44%
6. Updates summary statistics
7. Returns confirmation

### Next Day Analysis

1. User inputs: "今日建议"
2. Skill reads Excel → 持仓明细 sheet
3. Fetches real-time prices for all holdings
4. Calculates current P/L for each position
5. Analyzes capital flow, valuation, trends
6. Generates "cut loss vs hold" recommendations
7. Writes report to `.monkeycode/portfolio-advice/`
8. Returns summary with key actions
