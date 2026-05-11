# Secleva 店舗ページ 管理画面（admin）実装ガイド

本ディレクトリは **既存 Secleva 管理画面（`mockups/A_pure-line/admin.html`）にマージする前提のモック** です。  
本番取り込み時に必要な実装情報をまとめます。

---

## 📂 ファイル構成

```
shop-page/admin/
├── 01_template-select.html   テンプレート選択（5種）＋プレビューモーダル
├── 02_block-edit.html        ブロック編集（メインビジュアル・ロゴ・タグ・ギャラリー・メニュー写真・キャッチコピー・SNS）
├── 03_preview.html           プレビュー＋公開
├── thumbs/                   テンプレ選択用サムネJPEG（thumb-T1.jpg〜thumb-T5.jpg）
└── README.md                 本ファイル
```

依存：Tailwind CDN / Noto Sans JP / Phosphor Icons / `../branding/logo_A.png` / `../branding/dummy/default/*`

マージ先：`mockups/A_pure-line/settings.html` の「店舗運営」カテゴリ内に「店舗ページ」項目を追加し、本3画面へ遷移させる。

---

## 🎨 デフォルトアセット

`branding/dummy/default/` に **中立的なフォールバック画像** を配置。  
オーナーが写真をアップロードしなくても見栄えするための保険。

| パス | 用途 |
|---|---|
| `default/00_hero.webp`（16:9） | メインビジュアル fallback |
| `default/01_plants.webp`〜`06_stones.webp`（1:1） | ギャラリー6枚 fallback |
| `default/menu/01_oil.webp`〜`04_stones.webp`（1:1） | メニュー写真 fallback（4枚） |

公開ページ側でも同じパスを参照する想定（オーナーが未アップロードなら自動で使用）。

---

## 🔁 メニュー写真の3 state パターン

`02_block-edit.html` の **Block 5: メニュー写真** で、登録メニュー1件あたり3つの状態を出し分ける。

### state="custom"（マイ写真）
- バックエンドの `menu.photo_url` が存在する場合
- サムネ：オーナー写真をそのまま `<img src="{{ menu.photo_url }}">` 表示
- ラベル：`マイ写真`
- ボタン：`✏ 編集`

### state="default"（デフォルト表示中）
- `menu.photo_url` がない & 業界デフォルトカテゴリにマッチする場合
- サムネ：`branding/dummy/default/menu/{matched}.webp` 表示＋ `DEFAULT` バッジ
- ラベル：`デフォルト表示中`
- ボタン：`✏ 編集`

### state="empty"（写真未設定）
- `menu.photo_url` がない & デフォルトもマッチしない場合（業種が特殊・カテゴリ未設定など）
- サムネ：破線枠＋カメラアイコン
- ラベル：`写真未設定`
- ボタン：`+ 写真を追加`

→ 各stateの完全マークアップは `02_block-edit.html` の Block 5 直下の HTML コメントを参照。

### マッチングロジック（推奨）
バックエンドで `menu.name` または `menu.category` をキーにデフォルト画像を選択：
```
"もみほぐし" "整体" "リンパ" → default/menu/01_oil.webp
"アロマ" "ハーブ" "オイル"   → default/menu/02_aroma.webp
"フェイシャル" "クレンジング" → default/menu/03_towels.webp
"ホットストーン" "リフレ"    → default/menu/04_stones.webp
それ以外                     → state="empty"
```

---

## 🔁 メインビジュアル・ギャラリー・ロゴの fallback

### Block 1: メインビジュアル
- `photo_url` あり：オーナーアップロード写真を表示
- なし：`branding/dummy/default/00_hero.webp` を自動表示し、編集画面に「デフォルト表示中」バッジ＋注釈

### Block 4: ギャラリー（6枚）
- 各スロット個別判定：個別アップロード写真 → デフォルト6枚から自動補完
- 編集画面では各スロットに `DEFAULT` バッジ＋一括「自分の写真をアップロード」CTA

### Block 2: ロゴ
- デフォルトなし（オーナー設定必須）
- 未設定時の挙動は別途検討（テンプレ固有のシンボル？店名イニシャル自動生成？）

---

## 🪟 編集モーダル（メニュー写真用）

`02_block-edit.html` 末尾の `#menu-photo-modal` がメニュー写真の編集ボトムシート。

### 構造
- 現在の写真プレビュー（state バッジ付き）
- 「自分の写真をアップロード」CTA（メイン）
- 「デフォルトから選ぶ」4サムネ（タップで反映）
- 「元の写真に戻す」「写真を削除」（その他操作）
- フッター：キャンセル／保存

### JS フック
- `.menu-photo-edit[data-menu]` クリックで開く
- `data-state` 属性で state バッジ表示を切替
- `data-menu` 属性でモーダル内のメニュー名を動的セット

実装時はモーダル内の入力をフォーム化し、保存時に `PATCH /api/menus/{id}/photo` に送信する想定。

---

## 🔗 メニュー本体（名前・料金）の管理

**ここ（Block 5）の責務は写真のみ**。メニュー追加・削除・料金変更は本流の `mockups/A_pure-line/settings.html`「設定 > メニュー管理」階層で行う。

Block 5 内に誘導リンクカード「メニューの追加・料金変更 → 設定 > メニュー管理」を設置済み。

メニュー数は固定3件ではなく、登録済みメニュー全件をループ表示する想定（API: `GET /api/shops/{id}/menus`）。

---

## 🌐 SNS リンク

`Block 7: SNSリンク` で LINE 公式 / Instagram / X / TikTok の URL を登録。  
公開ページの以下に自動反映：
- ヘッダー右上のアイコン群（`href="#"` を入力値で置換）
- 「Instagramで見る」CTA（T3など）
- 「LINEで予約する」CTA（全テンプレ・LINE公式URLに直リンク）

入力欄が空のSNSは公開ページに表示しない（要素ごと非出力）。

---

## 🎨 テンプレート切替

`01_template-select.html` で5テンプレ（T1〜T5）から選択。

- 各カードに実機キャプチャ（`thumbs/thumb-T*.jpg`）
- サムネタップで `<iframe>` プレビューを bottom sheet に展開
- カード内CTA「このテンプレートを選ぶ」で選択切替
- 下部CTA「次へ進む（編集する）」で `02_block-edit.html` に進む

選択結果は `shop.template_id` として保存し、公開ページのレンダリングテンプレ切替に使用。

---

## ✅ 公開ページへの反映マップ

| 編集画面ブロック | 公開ページの反映先 |
|---|---|
| Block 1 メインビジュアル | テンプレ上部のヒーロー画像 |
| Block 2 ロゴ | ヒーローに重なる円形ロゴ |
| Block 3 店舗特徴タグ | 店舗名下の横スクロールチップ |
| Block 4 ギャラリー（6枚） | ギャラリーセクション（テンプレ依存：横スクロール or 2x3グリッド） |
| Block 5 メニュー写真 | メニューセクションの各カードのサムネ |
| Block 6 キャッチコピー | 所在地下の一文 |
| Block 7 SNSリンク | ヘッダーのSNSアイコン群 ＋ CTA の href |

---

## 📋 マイグレーション TODO（本番取り込み時）

- [ ] settings.html の「店舗運営」に「店舗ページ」項目を追加（リンク先：01_template-select.html）
- [ ] 各HTMLの Tailwind CDN を本流のビルドに統合
- [ ] 画像パス `../../branding/dummy/default/` を本番アセットCDNに差し替え
- [ ] JS（template-card 選択切替、modal開閉、メニュー写真state切替）を本流のフロントフレームワーク（React/Vue 等）に書き換え
- [ ] 各state の API スキーマ確定（GET / PATCH / DELETE 写真）
- [ ] WebP 自動変換パイプライン（アップロード時）
- [ ] LocalBusiness schema 自動付与（SEO/MEO 対策）
- [ ] sitemap.xml に店舗ページ追加
- [ ] Googleビジネスプロフィール連携（Phase D / 任意）

---

## 📐 デザインルール

- 全画面 mobile-first 390px固定
- 配色：line green `#06C755` ／ zinc系の白カード ／ red `#dc2626`（破壊的アクション）
- フォント：Noto Sans JP（既存brandと統一）
- アイコン：Phosphor Icons（`@phosphor-icons/web`）
- ボタン高さ：最低 44px（タップ領域確保）
- 本文サイズ：13-15px（リテラシー低層も読みやすく）
