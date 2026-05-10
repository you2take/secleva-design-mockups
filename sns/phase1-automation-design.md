# Phase 1: 自動化システム設計書（make.com 軸）

## 全体フロー

```
[Apify 競合スクレイプ]
       ↓ 日次 6:00
[Google Sheet（高エンゲ投稿ログ）]
       ↓
[make.com シナリオ#1: Claude API 投稿生成]
       ↓ 5型 × 3案 = 15案/日
[Slack/Discord 承認チャネル]
       ↓ Yuto がボタン押す（5秒/案）
[make.com シナリオ#2: Threads 投稿]
       ↓ 予約配信（10/18/22時）
[Threads 投稿実行]
       ↓
[make.com シナリオ#3: 反応取得]
       ↓ 30分後・3時間後・24時間後
[Google Sheet（エンゲログ）]
       ↓ 週次集計
[Claude API: 次週の型最適化レポート]
       ↓ Slackに自動送信
[Yuto 確認 → 次週のコンテンツ方針更新]
```

---

## 必要なアカウント・APIキー

| サービス | プラン | 月額 | 用途 |
|---|---|---|---|
| **make.com** | Core | 無料〜$10.59 | フロー実行（10,000 ops/月） |
| **Apify** | Free | 無料（$5 credit/月） | Threads/X スクレイプ |
| **Claude API** | Anthropic | 従量 $5〜30/月 | 投稿生成・分析 |
| **Threads API** | Meta Graph | 無料 | 投稿配信（要 App Review） |
| **Slack** | Free | 無料 | 承認チャネル |
| **Google Sheets** | 無料 | 無料 | ログ保存 |

**月コスト合計：約 $15〜45（¥2,300〜7,000）**

---

## シナリオ#1：投稿生成（make.com）

### Trigger
- Schedule：毎朝 7:00 JST

### Steps
1. **Google Sheets** から「直近7日の高エンゲ投稿ログ」取得
2. **Claude API** にプロンプト送信：
   ```
   役割：Secleva中の人（マッサージ・整体サロン向けLINE×AI秘書SaaS）
   キャラ：output/secleva/sns/character-guidelines.md 準拠
   依頼：今日の投稿候補を3本生成。
   - 型A（数字公開）×1
   - 型B（失敗・本音）×1
   - 型C（アンチテーゼ）×1
   参考：以下の高エンゲ投稿を構造的に分析して、Secleva版に翻案
   {{Sheets取得した競合投稿データ}}
   各候補：500字以内、ハッシュタグなし、絵文字最大2個
   ```
3. **Slack** に投稿候補3案＋「承認」「書き直し」「スキップ」ボタン送信

### 設定値
- Module count：~5 ops/日
- 月：150 ops（無料枠 10,000の1.5%）

---

## シナリオ#2：Threads 投稿（make.com）

### Trigger
- Slack で「承認」ボタンクリック

### Steps
1. **Schedule** で配信時刻指定（10:00 / 18:00 / 22:00 のうち未配信のスロット）
2. **Threads API**（Meta Graph API v1.0）
   ```
   POST /me/threads
   {
     "media_type": "TEXT",
     "text": "{{承認された投稿文}}"
   }
   ```
3. **Threads API**（publish）
   ```
   POST /me/threads_publish
   { "creation_id": "{{step2.id}}" }
   ```
4. **Google Sheets** に「投稿済」記録

### Threads API 申請手順
1. https://developers.facebook.com/ で Meta App 作成
2. Threads API permission 追加
3. App Review 申請（テスト用なら不要、自分のアカウントのみ投稿可）
4. アクセストークン取得 → make.com に登録

---

## シナリオ#3：反応取得（make.com）

### Trigger
- Schedule：30分後・3時間後・24時間後（投稿IDごとに）

### Steps
1. **Threads API** で投稿のエンゲ取得
   ```
   GET /{threads-id}/insights?metric=likes,replies,reposts,quotes,views
   ```
2. **Google Sheets** に追記（投稿ID / 時刻 / いいね / 返信 / リポスト / 表示）
3. **エンゲ率が中央値超え**なら Slack に通知「この投稿バズり兆候、追加投稿準備推奨」

---

## シナリオ#4：競合スクレイプ（Apify）

### Trigger
- Schedule：毎朝 5:00

### Steps
1. **Apify Actor**：Threads Scraper（または `apify/instagram-scraper`）
2. ターゲット：
   - @asuka_lymphcare
   - @aesthetic.onesan
   - @ssss_k725
   - @utako_ooe
   - @syuji00727（業界考察長文系）
3. 取得：直近7日の投稿テキスト・いいね・返信・リポスト・URL
4. **Google Sheets** の「競合ログ」シートに追記

---

## シナリオ#5：週次レポート（make.com）

### Trigger
- Schedule：毎週月曜 9:00

### Steps
1. **Google Sheets** から先週の自社投稿エンゲ集計
2. **Claude API** に分析依頼：
   ```
   先週の Secleva 投稿×7本のエンゲ率を分析し、
   - 最も反応良かった型・時刻・トーン
   - 改善すべき型
   - 次週の型分配比率の提案
   をMarkdownで300字以内
   ```
3. **Slack** に「先週のレポート」自動送信
4. Yutoが確認 → 次週の方針を承認

---

## 環境変数（make.com で設定）

```
ANTHROPIC_API_KEY=sk-ant-xxxxx
THREADS_ACCESS_TOKEN=EAAxxxxx
APIFY_TOKEN=apify_api_xxxxx
SLACK_BOT_TOKEN=xoxb-xxxxx
GSHEETS_ID=1xxxxx
```

---

## セットアップ手順（Yutoが実施）

### Step 1：アカウント作成（30分）
1. make.com → 無料登録（Googleログイン可）
2. Apify → 無料登録
3. Anthropic console → API Key 発行（Claude Sonnet 4.5 使用）
4. Slack → 専用ワークスペース or 既存に Channel 作成（#secleva-sns）

### Step 2：Threads API 申請（1〜3日）
1. https://developers.facebook.com/ ログイン（Yuto の Meta アカウントで）
2. App 作成（名前：Secleva）
3. Threads API 追加 → User Token Generator
4. アクセストークンコピー

### Step 3：make.com フロー構築（私が代行・2〜3時間）
- 上記シナリオ#1〜#5 を make.com テンプレート化
- Yuto は環境変数をコピペするだけ

### Step 4：テスト投稿（30分）
- シナリオ#1 を手動実行 → Slack 承認 → 実投稿
- エンゲ取得まで確認

### Step 5：本番運用（毎日5分）
- 朝起きて Slack で承認ボタンを押す
- 週末にレポートで方針調整

---

## リスク管理

| リスク | 対応 |
|---|---|
| **Threads APIレート制限** | 1日250投稿まで（十分） |
| **Claude API コスト爆発** | 月$30 上限アラート設定 |
| **AI生成投稿のbiz感** | 必ずYuto承認後に投稿（自動承認しない） |
| **Threads BAN リスク** | 1日3投稿以内、人間味維持、AI感ある投稿は避ける |
| **競合スクレイプの法的グレー** | 公開投稿のみ・要約のみ・引用しない |

---

## KPI

| 指標 | Phase 1（〜3ヶ月） | Phase 2（〜6ヶ月） |
|---|---|---|
| フォロワー | 500 | 2,000 |
| 平均いいね/投稿 | 20 | 80 |
| プロフィール訪問/日 | 50 | 200 |
| LINE 友達追加/月 | 10 | 50 |
| Secleva 申込/月 | 1〜3 | 10〜20 |
