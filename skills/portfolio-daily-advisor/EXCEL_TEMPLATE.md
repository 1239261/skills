# Portfolio Excel Template

This directory contains the Excel template for portfolio management.

## File Location

**Default Path**: `/workspace/portfolio/holdings.xlsx`

## Excel Structure

### Sheet 1: 持仓明细 (Holdings)

| Column | Field Name | Type | Formula |
|--------|------------|------|---------|
| A | 代码 | Text | - |
| B | 名称 | Text | - |
| C | 持仓数量 | Number | - |
| D | 成本单价 | Number | - |
| E | 总成本 | Number | `=C2*D2` |
| F | 当前价 | Number | - |
| G | 当前市值 | Number | `=C2*F2` |
| H | 盈亏金额 | Number | `=G2-E2` |
| I | 盈亏比例 | Percentage | `=H2/E2` |
| J | 仓位占比 | Percentage | `=G2/SUM(G:G)` |
| K | 行业 | Text | - |
| L | 备注 | Text | - |
| M | 最后更新 | Date | - |

### Sheet 2: 关注列表 (Watchlist)

| Column | Field Name | Type |
|--------|------------|------|
| A | 代码 | Text |
| B | 名称 | Text |
| C | 关注日期 | Date |
| D | 关注理由 | Text |
| E | 目标买入价 | Number |
| F | 当前价 | Number |
| G | 行业 | Text |
| H | 状态 | Text (观望/可买入/移除) |
| I | 备注 | Text |
| J | 最后更新 | Date |

### Sheet 3: 交易记录 (Transactions)

| Column | Field Name | Type |
|--------|------------|------|
| A | 交易日期 | Date |
| B | 代码 | Text |
| C | 名称 | Text |
| D | 交易类型 | Text (买入/卖出) |
| E | 交易价格 | Number |
| F | 交易数量 | Number |
| G | 交易金额 | Number |
| H | 手续费 | Number |
| I | 交易后持仓 | Number |
| J | 交易后成本 | Number |
| K | 备注 | Text |

### Sheet 4: 汇总统计 (Summary)

| Row | Metric | Formula |
|-----|--------|---------|
| 1 | 总资产 | `=SUM(持仓明细！G:G)` |
| 2 | 总成本 | `=SUM(持仓明细！E:E)` |
| 3 | 总盈亏金额 | `=SUM(持仓明细！H:H)` |
| 4 | 总盈亏比例 | `=H3/E3` |
| 5 | 持仓数量 | `=COUNTA(持仓明细！A:A)-1` |
| 6 | 最大仓位 | `=MAX(持仓明细！J:J)` |
| 7 | 行业数量 | `=COUNTA(UNIQUE(持仓明细！K:K))` |

## Usage Examples

### Add Holding

```
持仓 160644 973 股 成本 2.05
```

**Result in Excel**:
| 代码 | 名称 | 持仓数量 | 成本单价 | 总成本 |
|------|------|----------|----------|--------|
| 160644 | 鹏华地产 | 973 | 2.05 | 1994.65 |

### Add to Watchlist

```
关注 561560
```

**Result in Excel**:
| 代码 | 名称 | 关注日期 | 状态 |
|------|------|----------|------|
| 561560 | 电力 ETF | 2026-05-25 | 观望 |

### View Portfolio

```
查看持仓
```

**Output**:
```
📊 持仓汇总

总资产：8,173.20 元
总成本：7,978.55 元
总盈亏：+194.65 元 (+2.44%)

持仓数量：3 只
```

## Backup Strategy

- **Auto-backup**: Before each update, backup current file
- **Naming**: `holdings_backup_YYYYMMDD_HHMMSS.xlsx`
- **Retention**: Keep last 10 backups
- **Location**: `/workspace/portfolio/backups/`
