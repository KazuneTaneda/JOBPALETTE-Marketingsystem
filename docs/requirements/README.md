# 求人データ分析・レポーティング体制 要件定義（v0.1 レビュー用）

作成: 2026-09-28　状態: **レビュー待ち（実装未着手）**

## このシステムの目的

求人ボックス・Indeed・Engage・ジモティ等の応募者データを期間を区切らず一元的に蓄積し、
「必要な数字をその都度人が確認・集計してから判断する」状態を、
**ローデータ蓄積 → 自動分析 → 週次レポート生成 → 人間は意思決定だけ**
という状態に変える。

今回のスコープはフェーズ1（データ基盤＋自動分析＋振り返りレポート）に限定する。
上流の月次戦略計画（来月どう配分するか）はフェーズ1稼働後、見えてきた課題を踏まえて別途設計する（[ルートREADME](../../README.md)参照）。

## 読む順番

1. [01 目的と背景](01_purpose_and_background.md)
2. [02 現状と課題](02_current_state_and_issues.md)
3. [03 To-Be 業務フロー](03_to_be_workflow.md)
4. [05 KPI定義](05_kpi_definitions.md) — 最重要KPI（35歳以下有効応募）を含む
5. [08 MVPの範囲](08_mvp_scope.md)
6. [09 未決事項](09_open_questions.md) — **レビューで回答が必要な質問**

## 目次

| # | 文書 | 内容 |
|---|---|---|
| 01 | [目的と背景](01_purpose_and_background.md) | なぜ作るか、2フェーズ構成、DRシステムとの関係 |
| 02 | [現状と課題](02_current_state_and_issues.md) | As-Is、分析の浅さ、自動化されていない箇所 |
| 03 | [To-Be 業務フロー](03_to_be_workflow.md) | 週次/月次サイクル、異常検知〜改善提案〜意思決定の流れ（Mermaid） |
| 04 | [データモデル](04_data_model.md) | Media / Account / JobPosting / Applicant / Report のER図と定義 |
| 05 | [KPI定義](05_kpi_definitions.md) | 総論（A系）・各論（B系）・人材紹介事業KPI、評価ロジック |
| 06 | [機能要件](06_functional_requirements.md) | FR-DATA / ANA / REP / ACT / HUB / ADM |
| 07 | [非機能要件](07_non_functional_requirements.md) | 権限、個人情報、性能、保守性 |
| 08 | [MVPの範囲](08_mvp_scope.md) | Must / Should / Could / Not Now |
| 09 | [未決事項](09_open_questions.md) | 決定済み D-番号、未決 Q-番号 |
| 10 | [求人ボックス キーワード別分析の設計](10_kyujinbox_keyword_analysis.md) | 検索語句→求人→35歳以下有効応募→面談→CPAの分析パイプライン、直近のネクストアクション |

## dr-system との関係

[dr-system](https://github.com/KazuneTaneda/dr-system)（ダイレクトリクルーティングの送信枠・配分・実績管理）とは対象ドメインが異なる**独立システム**。
ただし要件定義を先に固めてからレビュー→実装に進む進め方、および「実績を蓄積するほど翌月の意思決定が楽になる」という思想は共通で踏襲する。
