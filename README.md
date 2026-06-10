# report-replication 中文说明

## Skill 定位

`report-replication` 用于把一篇量化研报、论文、PDF、网页或文本材料，转化为完整的研究交付包。

它覆盖：

1. 研报/论文全篇翻译。
2. AI 总结与因子公式复现。
3. 因子有效性检验 HTML 报告。
4. BACKTEST 策略代码。
5. 内置本地 BACKTEST 引擎生成的实际回测 HTML 说明报告。
6. 最终交付摘要或失败报告。

默认产出根目录为：

```text
/home/coder/project/replication/report-replication
```


## 调用边界

该 Skill 在以下阶段不调用其他研究/数据 Skills：

- 研报翻译
- 因子公式复现
- 因子验证
- 数据准备
- BACKTEST 策略生成
- BACKTEST 回测

## 数据源原则

数据必须是真实、可追溯的数据。优先使用研报所需数据、用户提供数据、BACKTEST 配置所绑定的数据源，或当前项目明确记录的数据源。

禁止用合成数据、模拟行情、随机市场数据证明因子有效性。固定随机种子随机因子只能作为负控制基准，且必须建立在同一份真实收益数据上。

## 标准产出结构

```text
/home/coder/project/replication/report-replication/{report_id}/
  01_translation/
    full_translation.md
  02_factor_reproduction/
    ai_summary_and_factor_formula.md
    reference_implementation.py
  03_factor_validation/
    factor_validation_report.html
    data/
      benchmark_comparison.csv
      backtest_alignment_audit.csv
    charts/
  04_backtest_strategy/
    strategy.py
    config.json
    backtest_report.html
    backtest_report_raw.html
    backtest_logs/
      signal_log.jsonl
      equity_curve.csv
      performance_metrics.csv
      trades.csv
      position_return_detail.csv
  06_delivery/
    final_delivery_summary.md
  failure_report.md
  manifest.json
```

## 因子验证要求

因子检验报告必须包含：

- 数据覆盖率、缺失率、异常值和因子分布。
- IC、Rank IC、ICIR、年度 IC、滚动 IC。
- 分层组合收益、多空组合收益、累计净值、回撤。
- 年化收益、年化波动、Sharpe、Calmar、最大回撤、胜率、换手率。
- IS/OOS/Walk-forward 验证，若数据不足必须说明。
- 参数稳定性、交易成本敏感性、反向因子、随机因子、简单基准对照。
- 因子审计：数据可得性、信号滞后、标签构造、成交价、未来函数检查、偷价格检查、样本拆分、成本假设。
- BACKTEST 对齐审计：理论因子验证曲线和 BACKTEST 实际权益曲线的口径差异。

## 辅助脚本

```text
scripts/check_dependencies.py
```

检查并可自动安装本 Skill 所需 Python 依赖。标准用法：

```bash
python scripts/check_dependencies.py --install
```

也可以直接使用：

```bash
python -m pip install -r requirements.txt
```

```text
scripts/create_project.py
```

创建 `/home/coder/project/replication/report-replication/{report_id}` 标准研究输出目录和 `manifest.json`。

```text
scripts/check_step5_strategy.py
```

检查 BACKTEST 策略、配置、回测报告、`backtest_logs/signal_log.jsonl`、权益曲线、绩效指标和交易记录。

```text
scripts/local_backtest.py
```

内置本地回测引擎。标准用法：

```bash
python scripts/local_backtest.py /home/coder/project/replication/report-replication/{report_id} --market-data /path/to/market_data.csv
```

行情文件支持 CSV/Parquet，默认需要 `date`、`symbol`、`close` 列。信号日志默认读取：

```text
04_backtest_strategy/backtest_logs/signal_log.jsonl
```

```text
scripts/build_factor_report.py
```

根据结构化 JSON 指标生成独立 HTML 因子检验报告骨架。

```text
scripts/quality_gate_check.py
```

交付前质量门禁。会检查 RAG scorecard、benchmark comparison、BACKTEST alignment audit、每图解释、基准 CSV schema、BACKTEST 日志，以及占位内容是否被清理。

## 关键参考文件

```text
references/output_contract.md
references/factor_validation_checklist.md
references/factor_audit_and_robustness.md
references/backtest_engine.md
references/data_sources.md
```

## 验收标准

每次研报复现都应检查：

1. 是否生成完整产出结构。
2. 是否使用真实、可追溯的数据。
3. 是否完成因子审计和稳健性检验。
4. 是否生成 BACKTEST 策略。
5. 是否实际运行内置 BACKTEST 或用户提供的外部 BACKTEST，或明确记录阻塞原因。
6. 是否保存 BACKTEST HTML 回测报告或失败日志。
7. 是否生成最终交付摘要或失败报告。
