# Secleva アプリプロダクトUI — デザインリサーチ

作成日：2026-05-07  
リサーチ起点カテゴリ：**B（アプリ・UI/UX）**＋ 直接競合・LINE公式リファレンス

## ヒアリング前提（DESIGN.md確認済）

| 項目 | 内容 |
|---|---|
| サイトタイプ | アプリプロダクト UI（LP ではない、L1+L2+L3+L4） |
| 業種 | 個人サロン向けSaaS（整体・マッサージ＋スナック多業種） |
| カラー方針 | 3案ともLINEグリーン #06C755 ＋白を共通土台、差し色で塗り分け |
| デザインテイスト | LINEに溶け込む親しみ／低リテラシー対応（モダンSaaS排除） |

## 推奨参考リファレンス（5件＋補助）

### 1. LINE for Business 公式（最重要・案A基盤）
- **URL**: https://www.lycbiz.com/jp/service/line-official-account/
- **出典**: LINEヤフー公式
- **業種**: LINE OAサービス本家
- **採用理由**:
  - **構成**: モバイルファーストのリスト型ナビ／「お悩み→解決→実績→CTA」のシンプル順序
  - **配色**: ダークネイビー #000048 ベース + LINEグリーン CTA、シンプル極まる
  - **テイスト**: 公式ブランド感 + 角丸ボタン（border-radius: 100px完全丸）
- **重要発見**: フォントが **「LINESeedJPStd Regular」** ＝LINE公式ブランドフォント。Sclevaは**Noto Sans JPで近似**することで「LINE文化との連続性」を担保できる
- **参考ポイント**: 完全角丸ボタン／ナビ階層／信頼バー数字の見せ方（70%, 80%等）／文字色#111111
- **3案への適用**: **案A（純正LINE延長）の最重要リファレンス**。案A はこのトーンを忠実に踏襲

### 2. ChatGPT モバイル（案C基盤）
- **URL**: https://chatgpt.com/
- **出典**: OpenAI公式
- **業種**: AI チャットアプリ
- **採用理由**:
  - **構成**: 極限のシンプル（中央チャット入力＋サイドバー）／ヘッダーは隠れる動線
  - **配色**: 白背景 #FFFFFF + 黒文字 #0D0D0D + ロゴ／アイコンのみアクセント
  - **テイスト**: System Font (apple-system) ＋ 完全角丸ボタン（pill shape）
- **重要発見**: AI Bot UIの現代標準＝「白背景＋System Font＋角丸pill」。**Sclevaがティールやペールブルーを「やわらかいAI感」として差し色する根拠**
- **参考ポイント**: 入力欄の角丸＋影／空状態の余白／OS純正感
- **3案への適用**: **案C（LINE×やわらかAI）の参考**。AI 先進感を「派手にしない」表現方法

### 3. Dribbble Chatbot UI 検索（案C補強・15事例）
- **URL**: https://dribbble.com/search/chatbot
- **出典**: Dribbble（デザイナーUIショット集）
- **採用理由**:
  - 上位ヒット15件中、特に注目：
    - `25822858-Minimal-AI-Chatbot-Chat-Mobile-Interface` — ミニマルAI チャット
    - `25845244-Super-Simple-AI-Chatbot-Chat-Intefrace` — 超シンプル AI チャット
    - `25109551-Genie-Chatbot-Personal-AI-Assistant` — パーソナルAIアシスタント
  - **構成**: チャット画面＋カード型応答（Sclevaの Flex Message と同構造）
  - **配色**: 白＋単色アクセント＋黒テキストが多数
  - **テイスト**: ミニマル・親しみ＋AI先進感
- **3案への適用**: 案C のFlex Message カード設計参考（カードの余白／フォントサイズ／アクションボタン配置）
- **副次効果**: AI チャットUI のトレンドを把握、「派手にしない」現代表現

### 4. L Message（エルメ）— 逆張り参考
- **URL**: https://lme.jp/
- **出典**: 株式会社ミショナ運営
- **業種**: LINE 公式アカウント自動化ツール
- **特徴解析**:
  - フォント：游ゴシック体（システム）
  - メインCTA: オレンジ #FE4D01（LINEグリーンを脇役に）
  - 訴求トーン：「お悩みありませんか？」型の煽り・派手な見出し
  - 全体：派手なB2B SaaS LP、訴求過剰
- **採用理由（逆張り）**: **「これと違う方向に行く」事例**として明示。Sclevaの個人店オーナー（リテラシー低層）にはこの煽りトーンと派手色は逆効果
- **3案への適用**: **3案すべてで「やってはいけない例」**。Sclevaは煽らない／派手色入れない／LINEグリーンを脇役にしない

### 5. vision.ts（自社内、案B基盤）
- **URL**: https://github.com/Diodon2026/secleva/blob/main/src/routes/vision.ts
- **出典**: Sclevaリポジトリ内（5/4 demo構想スライド、評判良好）
- **業種**: Secleva自身
- **採用理由**:
  - **構成**: 朝→営業中→インバウンド→営業後→月末の物語型／LINEトークUIモック中心
  - **配色**: ベース #FAF6EE / アクセント テラコッタ #B85B27 / 墨黒 #2A2520
  - **テイスト**: Noto Serif JP の重み＋息づかい、左罫線引用
- **重要発見**: 既存の line-card / line-msg / line-bubble / schedule-list の CSS構造はそのまま3案で再利用可能。**3案のFlex MessageカードもこのCSS構造を継承**
- **3案への適用**: **案B（LINE×温もり）の最重要リファレンス**。ベージュ＋テラコッタの温もりをLINEグリーンと共存させる方向

---

### 補助参考（深掘り対象、aidesigner段階で必要に応じて再リサーチ）

| # | サイト | URL | 用途 |
|---|---|---|---|
| 6 | LINE OA Manager | https://manager.line.biz/ | ログイン要、Web管理画面（L4）参考。実機スクショは友人の本物アカウントから取得想定 |
| 7 | リピッテ | https://repitte.jp/ | 直接競合、SPAでJSロード待ち必要、aidesigner段階で再訪 |
| 8 | らくらくホン公式 | https://www.fcnt.com/raku-raku-phone/ | 高齢者向けUI（リテラシー低層）、案A の極限親しみ表現の参考 |
| 9 | Lステップ | https://linestep.net/ | ドメイン変更で詰まり、別経路で再調査 |
| 10 | Beauty Plus | （URL未確定） | 中長期競合、LINE ヤフー本体、リリース後リファレンス再調査 |

---

## ロゴ参考リサーチ（aidesigner brand kit生成にて深掘り）

直接サイト巡回ではなく、aidesigner MCPの `generate_branding_kit_variations` で以下方向で生成して参考収集：

| 方向 | 参考イメージワード |
|---|---|
| 案A | LINE純正アイコン風／緑バブル内「S」白文字／Helvetica or Noto Sans Bold |
| 案B | テラコッタ角丸内「S」／手書き感少々／Noto Serif JP的な筆致 |
| 案C | ティール角丸内「S→L」リガチャ／てこモチーフ／モダンミニマル |

aidesigner で9バリエ生成 → ユーザー絞り込み → 確定。

---

## 3案リファレンス対応表

| 案 | 主要参考 | 副次参考 | 避ける例 |
|---|---|---|---|
| **案A：純正LINE延長** | LINE for Business 公式 | らくらくホン UI | L Message（派手SaaS）、Stripe/Linear |
| **案B：LINE×温もり** | vision.ts（自社） | 和カフェLP系 | L Message、ChatGPT（やわらかすぎ） |
| **案C：LINE×やわらかAI** | ChatGPT モバイル＋Dribbble Chatbot 15事例 | Notion AI／Claude | L Message、過剰AI先進感 |

---

## マーケター判断の補強

リサーチを通じて判明：
1. **LINE公式自身もダークネイビー＋緑＋角丸** = LINE文化の本質はこの組み合わせ
2. **L Message型の派手SaaS** がLINE×SaaSの典型悪例＝Yutoの「個人店毛嫌い」懸念は妥当
3. **AI チャットUIは「白＋System Font＋ミニマル」**が現代標準＝「派手なAI」は古い
4. **既存vision.ts の温もり** は LINE グリーンと共存可能（テラコッタは小面積アクセントとして）

→ **DESIGN.md の3案定義は妥当**。aidesigner段階で実際のUIモックを生成してYutoがレビュー判断、で進める。

---

## 改訂履歴

| 日付 | 内容 |
|---|---|
| 2026-05-07 | 初版（カテゴリB＋直接競合＋LINE公式の5+件リファレンス収集） |
