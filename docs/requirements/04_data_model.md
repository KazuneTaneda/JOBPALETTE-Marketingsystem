# 04. データモデル（ドラフト）

具体的なテーブル定義・型は、[09 未決事項](09_open_questions.md) のデータ蓄積先（Q-01）が決まってから確定する。ここでは中心概念（エンティティ）と関係のみ定義する。

## ER図（概念レベル）

```mermaid
erDiagram
    MEDIA ||--o{ ACCOUNT : "has"
    ACCOUNT ||--o{ JOB_POSTING : "publishes"
    JOB_POSTING ||--o{ APPLICANT : "receives"
    JOB_POSTING ||--o{ JOB_DAILY_STAT : "has"
    JOB_POSTING ||--o{ JOB_KEYWORD_STAT : "has"
    APPLICANT }o--|| REFERRAL_PIPELINE_STAGE : "progresses through"
    JOB_DAILY_STAT }o--|| WEEKLY_REPORT : "aggregated into"
    WEEKLY_REPORT ||--o{ WEEKLY_REPORT_ACTION : "recommends"

    MEDIA {
        string media_id PK
        string name "求人ボックス/Indeed/Engage/ジモティ等"
    }
    ACCOUNT {
        string account_id PK
        string media_id FK
        string name
    }
    JOB_POSTING {
        string job_id PK
        string account_id FK
        string title
        string occupation_category "職種大分類"
        string work_location "勤務地（一都三県/大阪/名古屋/福岡 等）"
        string body_text
        string salary
        date posted_at
        date closed_at "nullable"
    }
    JOB_DAILY_STAT {
        string stat_id PK
        string job_id FK
        date stat_date
        int impressions "nullable"
        int clicks "nullable"
        int applications
        decimal cost
    }
    JOB_KEYWORD_STAT {
        string stat_id PK
        string job_id FK
        string keyword
        date stat_date
        int impressions "nullable"
        int clicks "nullable"
        int applications "nullable"
        string source_csv_type "keyword別CSV。60日ローリングのため取込日を必ず記録"
    }
    APPLICANT {
        string applicant_id PK
        string job_id FK
        date applied_at
        int age "nullable"
        bool is_under_35
        bool is_foreign_national
        bool is_valid_application "有効応募フラグ"
        string region
    }
    REFERRAL_PIPELINE_STAGE {
        string stage_id PK
        string applicant_id FK
        string stage "IS架電/面談設定/面談実施/推薦"
        datetime occurred_at
        string source "HubSpot"
    }
    WEEKLY_REPORT {
        string report_id PK
        date week_start
        date week_end
        json summary_metrics
    }
    WEEKLY_REPORT_ACTION {
        string action_id PK
        string report_id FK
        string action_text "AIが出す改善アクション案"
        string status "提案/採用/却下"
    }
```

## 設計上の論点（未決）

- **求人の版管理**: 同一求人が期間中にタイトル・本文・給与を変更した場合、変更前後を別バージョンとして残すか（dr-system の Target/Search/Copy の Version 概念に相当）。特徴量分析（「20代活躍中」表記の効果検証など）の精度に直結するため要検討 → [09 Q-08](09_open_questions.md)
- **応募者の個人情報**: dr-system は候補者の個人情報（氏名・電話・メール・経歴）を持たない方針（D-11相当）。本システムも年齢・国籍・地域・有効応募フラグ等の**属性のみ**を蓄積し、個人を特定する情報は持たない想定 → 要確認 [09 Q-09](09_open_questions.md)
- **有効応募の定義**: 「有効応募」が何を指すか（応募後に一次選考へ進んだもの、重複・スパムを除いたもの等）を明確に定義する必要がある → [09 Q-05](09_open_questions.md)
- **物理削除しない方針**: dr-system 同様、マスタ・実績は物理削除せず無効化のみとする想定（監査性のため）
