# Report Replication

English | [中文](README.zh-CN.md)

`report-replication` is a self-contained Codex/Agent skill for turning a quantitative research report, paper, PDF, webpage, or text source into a complete research replication package.

It covers:

1. Full translation of the source report or paper.
2. AI summary and factor formula reconstruction.
3. Factor effectiveness validation with an HTML report.
4. BACKTEST strategy code generation.
5. Actual local backtest execution with the bundled backtest engine.
6. Final delivery summary or failure report.

The default output root is:

```text
/home/coder/project/replication/report-replication
```

## Boundaries

This skill should not call other research or data skills during these stages:

- Report translation.
- Factor formula reconstruction.
- Factor validation.
- Data preparation.
- BACKTEST strategy generation.
- BACKTEST execution.

## Data Rules

Data must be real and traceable. Prefer data required by the source report, user-provided data, data bound to the BACKTEST configuration, or data sources explicitly recorded in the current project.

Do not use synthetic data, simulated market data, or random market data to prove factor effectiveness. Random factors with a fixed seed may only be used as negative controls against the same real return data.

## Output Layout

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

## Factor Validation Requirements

The factor validation report should include:

- Data coverage, missing values, outliers, and factor distribution.
- IC, Rank IC, ICIR, annual IC, and rolling IC.
- Quantile portfolio returns, long-short returns, cumulative net value, and drawdown.
- Annual return, annual volatility, Sharpe, Calmar, maximum drawdown, win rate, and turnover.
- IS/OOS/Walk-forward validation, or an explanation when data is insufficient.
- Parameter stability, transaction cost sensitivity, reverse factor, random factor, and simple benchmark comparison.
- Factor audit: data availability, signal lag, label construction, execution price, look-ahead checks, price leakage checks, sample split, and cost assumptions.
- BACKTEST alignment audit between the theoretical factor validation curve and actual BACKTEST equity curve.

## Helper Scripts

```bash
python scripts/check_dependencies.py --install
```

Installs or checks required Python dependencies.

```bash
python scripts/create_project.py
```

Creates the standard output directory and `manifest.json`.

```bash
python scripts/local_backtest.py /home/coder/project/replication/report-replication/{report_id} --market-data /path/to/market_data.csv
```

Runs the bundled local backtest engine. Market data may be CSV or Parquet and should include `date`, `symbol`, and `close` columns by default.

Other quality and delivery helpers:

```text
scripts/check_step5_strategy.py
scripts/build_factor_report.py
scripts/quality_gate_check.py
```

## Key References

```text
references/output_contract.md
references/factor_validation_checklist.md
references/factor_audit_and_robustness.md
references/backtest_engine.md
references/data_sources.md
```

## Acceptance Criteria

Each report replication should verify:

1. The complete output structure was generated.
2. Real, traceable data was used.
3. Factor audit and robustness checks were completed.
4. BACKTEST strategy code was generated.
5. The bundled BACKTEST or a user-provided external BACKTEST actually ran, or a blocking reason was recorded.
6. A BACKTEST HTML report or failure logs were saved.
7. A final delivery summary or failure report was generated.

## License

This project is licensed under the GNU General Public License v3.0. See [LICENSE](LICENSE).
