# 環境構築ガイド（引き継ぎ用）

このリポジトリをクローンして、Indeed 求人の作成・アップロード・分析を始められる状態にするまでの手順です。
上から順に進めてください。所要時間の目安は 30 分程度です。

> リポジトリ全体の概要は [README.md](README.md)、作業ルールの全体像は [.claude/CLAUDE.md](.claude/CLAUDE.md) を参照してください。

---

## 0. 事前に前任者からもらうもの

| もの | 用途 | 備考 |
| --- | --- | --- |
| GitHub リポジトリへの招待 | クローン・更新 | プライベートリポジトリのため、コラボレーターとして招待してもらう |
| Indeed 採用企業アカウントのログイン情報 | アップロード・分析 | メールアドレス・パスワード。**チャットやリポジトリには書かず、安全な方法で受け取る** |
| 2 段階認証・パスキーの受け取り方法 | 初回ログイン | Google アカウント経由の場合、認証コードやパスキーが必要になる。受け取り先（端末・メール）を前任者と確認しておく |
| Claude のアカウント | Claude Code の利用 | Pro / Max / Team などのプラン |

---

## 1. 必要なソフトをインストールする

Mac を前提としています。

| ソフト | 確認コマンド | 用途 |
| --- | --- | --- |
| Git | `git --version` | リポジトリの取得・更新 |
| GitHub CLI（gh） | `gh --version` | GitHub へのログイン（任意だが推奨） |
| Node.js（v18 以上） | `node -v` | Playwright MCP の実行（`npx`） |
| Python 3 ＋ openpyxl | `python3 -c "import openpyxl"` | xlsx の生成 |
| Claude Code | `claude --version` | 作業全体（CLI またはデスクトップアプリ） |
| Google Chrome | ― | Playwright が操作するブラウザ |

まとめて入れる場合（Homebrew を使用）:

```bash
brew install git gh node python
```

```bash
python3 -m pip install openpyxl
```

Claude Code は公式サイト（https://claude.com/claude-code）からデスクトップアプリをインストールするか、CLI を入れてください。

```bash
npm install -g @anthropic-ai/claude-code
```

---

## 2. リポジトリをクローンする

GitHub にログインします（初回のみ）。

```bash
gh auth login
```

作業したい場所でクローンします。

```bash
git clone https://github.com/emikonakagawa/recruit-indeed.git
```

```bash
cd recruit-indeed
```

---

## 3. Indeed のログイン情報を設定する（`.env`）

サンプルをコピーして `.env` を作成します。

```bash
cp .env.example .env
```

`.env` をテキストエディタで開き、受け取ったログイン情報を記入します。

```
INDEED_EMAIL=<Indeed ログイン用メールアドレス>
INDEED_PASSWORD=<パスワード>
```

> ⚠️ `.env` は `.gitignore` で除外されています。**絶対にコミット・共有しないでください。**

---

## 4. Playwright MCP を登録する

Claude が Indeed の管理画面をブラウザ操作するために使います。リポジトリのフォルダ内で次を実行してください。

```bash
claude mcp add playwright -- npx -y @playwright/mcp@latest
```

登録できたか確認します（`playwright` が表示されれば OK）。

```bash
claude mcp list
```

---

## 5. 初回ログインを手動で済ませる（重要）

Playwright で開いたブラウザで Indeed 採用企業ダッシュボードに入るとき、**初回は手動でのログインが必要です。**
Google アカウント経由のログインでは、パスキー・2 段階認証・セキュリティコードを求められることがあり、Claude は自動で突破できません（突破しないルールにしています）。

1. リポジトリのフォルダで Claude Code を起動する（デスクトップアプリならこのフォルダを開く）。
2. Claude に次のように依頼する。
   > Playwright で https://employers.indeed.com を開いて
3. 開いたブラウザで、**自分で**ログインし、2 段階認証・パスキー認証を通す。
4. ダッシュボードが表示されたら完了。以降はブラウザにセッションが保存され、ログインを求められずに進めることがほとんどです。

> セッションは一定期間で切れます。スキル実行中に「ログイン画面で停止」「2 段階認証でブロック」と報告されたり、`分析レポート/*_実行エラー_*.md` が作られていたりしたら、この手順でログインし直してから再実行してください。

---

## 6. 動作確認

Claude Code で次のように話しかけて、それぞれ動くか確認します。

| 確認すること | 話しかける例 |
| --- | --- |
| 求人作成（xlsx 生成） | 「UPBUILD の求人を 1 件作って」 |
| 管理画面の操作 | 「iRup の非掲載求人の理由を調べて」 |

求人作成では `<会社>/<会社>_YYYYMMDD.xlsx` が作られれば OK です（テスト用に作ったファイルは不要なら削除してください）。

---

## 7. 日々の運用

### 主なスキル

| やりたいこと | 使い方 |
| --- | --- |
| 求人 xlsx を作成 | `draft-jobs` スキル（例:「Xedge の求人を 5 件作って」） |
| Indeed へ一括アップロード | `/upload-indeed <会社>` |
| 非掲載になった理由を分析 | `analyze-unlisted` スキル |
| 掲載中求人の成果を分析 | `analyze-performance` スキル |

### 作業後は GitHub に反映する

作業した内容（xlsx・レポート・ルール変更）はコミットして共有します。

```bash
git add -A
```

```bash
git commit -m "20260929"
```

```bash
git push
```

作業を始める前には、最新の状態を取り込んでください。

```bash
git pull
```

### 定期実行について

前任者の PC で設定していた定期実行（非掲載分析・パフォーマンス分析など）は、**リポジトリには含まれていません**。引き継ぎ後も定期実行したい場合は、Claude に「毎週〇曜日に analyze-unlisted を実行して」のように依頼し、自分の環境で設定し直してください。

---

## 8. 守ってほしいルール

- ルールの詳細は [.claude/CLAUDE.md](.claude/CLAUDE.md) の「ドキュメント地図」にある各ファイル（SSoT）に 1 箇所ずつ書かれています。変更するときもそこを直す。
- `テンプレート/` のマスターは直接編集しない。
- iRup / UPBUILD / Xedge の 3 社は別々に扱い、ファイル・内容を混在させない。
- `.env`・ログイン情報・個人のメールアドレスはリポジトリに書かない。

---

## トラブルシューティング

| 症状 | 対処 |
| --- | --- |
| `claude mcp list` に playwright が出ない | 手順 4 をリポジトリのフォルダ内で実行し直す |
| ログイン画面で止まる／2 段階認証でブロックされる | 手順 5 の手動ログインを行う |
| `ModuleNotFoundError: openpyxl` | `python3 -m pip install openpyxl` |
| 一括アップロードで全行エラー | 会社ごとに応募メール（BM）・画像 ID（BD）が異なる。`.claude/companies/<会社>/設定.md` の値を使っているか確認 |
| `git push` が拒否される | 先に `git pull` してから再度 push |
