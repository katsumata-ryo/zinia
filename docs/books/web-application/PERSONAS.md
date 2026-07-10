# 執筆用資料: 読者像・題材・骨組み規約

> この文書は**開発用**。読者向けHTML（`books/web-application/index.html`）本文には、この文書やRESEARCH.mdへの言及を一切書かないこと。

## 後続執筆者へ（最初に読む）

各章枠は既に `index.html` に空で用意済み。本文を各 `<section class="chapter" id="chN">` の
`<p class="placeholder">（執筆予定）</p>` を置き換える形で書き込むだけで埋まる。

### id採番規則
- 章: `id="ch1"` 〜 `id="ch32"`（部をまたいで通し番号。0部=ch1〜7, 1部=ch8〜12, 2部=ch13〜14, 3部=ch15〜22, 4部=ch23〜26, 5部=ch27〜29, 6部=ch30〜32）
- 部の扉: `id="part0"` 〜 `id="part6"`
- 章間参照は `<a href="#ch16">第16章</a>` の形。

### スタイルクラス名一覧（HTML冒頭コメントにも記載）
| 用途 | マークアップ |
|---|---|
| 章の枠 | `<section class="chapter" id="chN">` |
| 部の扉 | `<section class="part-cover" id="partK">`（`.part-lead`で導入文） |
| 章タイトル | `<h2>第N章　タイトル</h2>` |
| 節/小節 | `<h3>` / `<h4>` |
| この章で埋まる穴 | `<div class="chap-hole"><span class="label">この章で埋まる穴</span>…</div>` |
| MongoDB対比コラム | `<aside class="mongo-note"><span class="tag">MongoDB脳の翻訳</span>…</aside> `|
| 一般の補足コラム | `<aside class="eng-note"><span class="tag">補足</span>…</aside>` |
| 理解度チェック（章末3問） | `<div class="check-wrap"><h3>理解度チェック</h3><details class="check"><summary>Q1. …</summary><p>…</p></details>×3</div>` |
| コード | `<pre><code class="lang-ruby">…</code></pre>`（lang: `ruby`/`sql`/`js`。`<>&`はエスケープ） |
| 図の枠 | `<div class="figure">…<div class="cap">キャプション</div></div>` |
| 横スクロール表 | `<div class="scroll-x"><table>…</table></div>` |

### シンタックスハイライトの仕様
- インラインJSが `pre code[class*="lang-"]` を走査し、`textContent`を取り出してエスケープ→トークン化する。
- 対応言語: `lang-ruby` / `lang-sql` / `lang-js`。コメント・文字列・数値・キーワードを色分け（`.tok-cmt/.tok-str/.tok-num/.tok-kw`）。
- SQLのキーワードは大文字小文字を無視。Ruby/JSは区別する。
- **HTMLに書く時点で必ず `<`→`&lt;` 等にエスケープすること**（JS側でもエスケープするが、生の`<`はタグと誤認される）。

### 守ること / 壊さないこと
- 外部CDN・localStorage禁止。CSS/JSは全てインライン。
- テーマは `prefers-color-scheme` ＋ ライト/ダーク手動トグル（`data-theme`属性）。新規に色を足すなら `:root` / dark media / `[data-theme=light]` / `[data-theme=dark]` の**4箇所すべて**にCSS変数を追加する。
- 目次（`#toc`）に章を足したら、対応する `id` のセクションが存在すること（scrollspyが参照）。
- HOWでなくWHY。各章2,000〜4,000字目安、**第3部（ch15〜22）は4,000〜6,000字**と厚めに。

## 読者像（要約）
- Rails中心の現役Webエンジニア。実装力は高いが基礎に歯抜けの自覚あり。
- Rails / Pundit / GCP（Cloud Run, Firestore, Cloud Tasks）などの実務経験。
- **長年MongoDB中心のプロダクトに関わり、RDBの常識（正規化・JOIN・トランザクション・インデックス設計）の感覚がずれている自覚がある。** これが本書の対比コラムの主軸。
- ActiveRecordが隠蔽している部分を「下から」理解したい。
- 初心者向けの過剰な説明は不要。足りる箇所は読み飛ばす前提。

## 題材: 架空のRailsアプリ「Zephyr」

社内向けの**学習イベント管理サービス**。イベント告知→参加申込→開催後レビュー、という
EC・予約・SNSに通じる普遍ドメイン。正規化・JOIN・トランザクション・N+1を自然に例示できる。

### 主要モデル（第3部で具体スキーマを詰めていく。全章で使い回す）
- `User` — 利用者（社員）。`has_many :registrations, :reviews`。カラム例: `name`, `email(unique)`, `department`
- `Event` — 勉強会/イベント。`belongs_to :organizer(class_name: 'User')` / `has_many :registrations, :reviews`。カラム例: `title`, `starts_at`, `capacity`, `organizer_id`
- `Registration` — 参加申込（User×Eventの中間）。`belongs_to :user, :event`。状態 `status: applied/confirmed/canceled`。`(user_id, event_id)` に一意制約を想定
- `Review` — 開催後レビュー。`belongs_to :user, :event`。`rating`(1..5), `body`
- `Tag` / `EventTag` — イベント分類の多対多（`Event ⇄ EventTag ⇄ Tag`）

### 題材の使いどころ（章別の想定・後続への提案。強制ではない）
- 第16章 JOIN/GROUP BY: 「各イベントの参加確定人数」「平均レビュー点」
- 第17章 正規化: `events`にタグ名や主催者名を持たせる非正規化 vs 参照。MongoDB埋め込みとの翻訳表
- 第18章 インデックス: `registrations(event_id, status)` 複合インデックスの左端一致
- 第19章 トランザクション: 定員制の申込で「capacity超過を防ぐ」ロック
- 第20章 N+1: イベント一覧で各イベントの主催者名・参加人数を出す典型N+1
- 第21章 ActiveRecord逆引き: `Event.includes(:registrations)` が出すSQL、`counter_cache`で確定人数
- 第24章 認可: 「主催者(organizer)だけがEventを編集できる」Punditポリシー
- 第27章 冪等性: 参加申込のダブルクリック二重送信を冪等キーで防ぐ
