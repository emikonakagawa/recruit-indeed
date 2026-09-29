# recruit-indeed

Indeed に掲載する求人情報を xlsx 形式で作成・アップロード・分析するための作業フォルダです。
コードではなく、**Claude Code のスキル＋ドキュメント（SSoT）＋テンプレート xlsx** で運用しています。

対象は次の 3 社です。掲載内容は会社ごとに異なるため、ファイル・内容を混在させないでください。

| 会社 | 主な職種 | 勤務地 |
| --- | --- | --- |
| iRup（アイラップ） | エンジニア / ITコンサルタント | 東京 |
| UPBUILD（アップビルド） | クラウド / セキュリティエンジニア | 東京・愛知（名古屋）・大阪 |
| Xedge（エッジ） | IT事務スタッフ / クリエイティブ（業務委託） | 東京・大阪・愛知 |

> 詳細なルール・最新の運用方針は [`.claude/CLAUDE.md`](.claude/CLAUDE.md) とそこからリンクされる各 SSoT ファイルを参照してください。

## フォルダ構成

```
.
├── テンプレート/      職種別テンプレート xlsx（マスター。直接編集しない）
├── iRup/             iRup の成果物（iRup_YYYYMMDD.xlsx）とアップロードログ
├── UPBUILD/          UPBUILD の成果物
├── Xedge/            Xedge の成果物
├── 分析レポート/      非掲載理由・パフォーマンス分析のレポート
├── archive/          旧テンプレート（参照用）
├── .env.example      Indeed ログイン情報のサンプル
└── .claude/          Claude 用のルール・スキル・会社別設定（SSoT）
```

## セットアップ

### 1. Claude Code

[Claude Code](https://claude.com/claude-code)（CLI またはデスクトップアプリ）でこのフォルダを開きます。

### 2. Playwright MCP

Indeed 管理画面の操作（一括アップロード・分析）には Playwright MCP を使います。未登録の場合は、このフォルダで次を実行してください。

```bash
claude mcp add playwright -- npx -y @playwright/mcp@latest
```

### 3. `.env`

`.env.example` をコピーして `.env` を作り、Indeed のログイン情報を記入します。

```bash
cp .env.example .env
```

```
INDEED_EMAIL=indeedログイン用のemail
INDEED_PASSWORD=パスワード
```

> `.env` は `.gitignore` で除外済みです。**絶対にコミットしないでください。**

## ⚠️ 注意: 初回ログインは手動で行う

Playwright MCP で起動したブラウザ（Chrome）で Indeed 採用企業ダッシュボードに入る際、**初回（またはセッション切れ後）は手動でログインする必要があります。**

- Google アカウント経由のログインでは、**パスキー・2 段階認証（2FA）・セキュリティコード**を求められることがあり、Claude は自動で突破できません（突破しないルールにしています）。
- そのため、スキル実行が「ログイン画面で停止」「2FA でブロック」となった場合は、Playwright が開いたブラウザ上で **本人が手動でログインを完了**させてください。
- 一度ログインすると、ブラウザのプロファイルに Cookie（セッション）が保存されるため、以降の実行ではログインを求められずに進めることがほとんどです。
- ただしセッションは一定期間で切れます。**スケジュール実行（定期分析）は本人不在のためログインできず失敗する**ので、`分析レポート/*_実行エラー_*.md` が出力されていたら、手動でログインし直してから再実行してください。

### おすすめの手順

1. Claude に「Playwright で Indeed 採用企業ダッシュボード（https://employers.indeed.com）を開いて」と依頼する。
2. 開いたブラウザで、メール／Google アカウントでログインし、2FA・パスキー認証を自分で通す。
3. ダッシュボードが表示されたら、Claude に元の作業（アップロード・分析）を続けるよう伝える。

## 使い方

| やりたいこと | 使い方 |
| --- | --- |
| 求人 xlsx を作成 | `draft-jobs` スキル（例:「UPBUILD の求人を 6 件作って」） |
| Indeed へ一括アップロード | `/upload-indeed <会社>` |
| 非掲載になった理由を分析 | `analyze-unlisted` スキル（例:「Xedge の非掲載理由を調べて」） |
| 掲載中求人の成果を分析 | `analyze-performance` スキル（例:「iRup の掲載求人を分析して」） |

成果物は `<会社>/<会社>_YYYYMMDD.xlsx` に保存され、そのまま Indeed の管理画面にアップロードできます。

## ルールの置き場所（SSoT）

| 用途 | ファイル |
| --- | --- |
| 全体の地図 | [`.claude/CLAUDE.md`](.claude/CLAUDE.md) |
| 求人作成ワークフロー | [`.claude/skills/draft-jobs/SKILL.md`](.claude/skills/draft-jobs/SKILL.md) |
| 列の分類・入力規則 | [`.claude/列定義.md`](.claude/列定義.md) |
| 本文スタイル | [`.claude/本文スタイル.md`](.claude/本文スタイル.md) |
| エンジニア職種名・技術スタック | [`.claude/エンジニア専門領域.md`](.claude/エンジニア専門領域.md) |
| 職種別ブリーフ | [`.claude/briefs/`](.claude/briefs/) |
| 会社別設定 | [`.claude/companies/<会社>/設定.md`](.claude/companies/) |

同じルールは 1 箇所にだけ書き、他のファイルからはリンクで参照します。

## 注意事項

- `テンプレート/` のマスターは直接編集せず、必ずコピーして使う。
- 3 社は別々に扱い、ファイル・内容を混在させない。
- ルート直下の Playwright 作業用スナップショット（`*.yml` / `*.png`）は `.gitignore` で除外しています。
