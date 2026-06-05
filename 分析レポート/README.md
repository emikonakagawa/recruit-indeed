# 分析レポート

各種分析スキルが出力するレポートの格納フォルダ。

## 命名規則

**日本語のわかりやすいタイトル＋日付**。

- 形式: `<分析内容>_<会社>_YYYYMMDD.md`（同日に複数回なら末尾に `_2` 等の連番）
- 例: `非掲載求人分析_Xedge_20260605.md`
- 関連スクリーンショットは同フォルダに、レポートと対応する名前で保存（例: `非掲載求人分析_Xedge_20260605_1.png`）。

## 主な出力元

- **非掲載求人の理由分析**: `analyze-unlisted` スキル（→ `.claude/skills/analyze-unlisted/SKILL.md`）
  - 例: `非掲載求人分析_Xedge_20260605.md`
- **掲載求人のパフォーマンス分析**: `analyze-performance` スキル（→ `.claude/skills/analyze-performance/SKILL.md`）
  - 例: `掲載求人パフォーマンス分析_Xedge_20260605.md`
