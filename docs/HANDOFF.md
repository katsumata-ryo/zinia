# 引き継ぎ: zinia プロジェクト（2026-07-10時点）

## このリポジトリは何か

自家製の技術書棚。`docs/proposals/` の指示書をもとにHTML書籍を生成し、
リポジトリ直下の `index.html`（本棚ポータル）からリンクして読む。

- リポジトリ: `katsumata-ryo/zinia`（private）
- 作業ブランチ: `book/year-end-adjustment`（main未マージ。PRはまだ作っていない）

## ディレクトリ構造の方針（重要・ユーザー確定済み）

```
zinia/
├── index.html                  ← 本棚ポータル（公開中/準備中の書籍カード一覧）
├── books/<book-name>/          ← 読者向け成果物のみ（index.htmlだけ置く）
└── docs/                       ← 開発ドキュメント専用
    ├── books/<book-name>/      ← 執筆用資料（RESEARCH.md, PERSONAS.md 等）
    ├── proposals/              ← 各書籍の指示書
    └── HANDOFF.md              ← このファイル
```

- **読者が見ない作業資料を `books/` に置かない**（ユーザーの明確な要望）
- 本文中に RESEARCH.md 等の内部資料への言及を書かない（レビューで4箇所修正した経緯あり）

## 完了したこと: 『年末調整を体系的に理解する』

- 指示書: `docs/proposals/year-end-adjustment.md`
- 成果物: `books/year-end-adjustment/index.html`（単一HTML・全6部27章＋用語集42語）
- GitHub Issues: Epic #1 + サブ #2〜#10 すべてクローズ済み
- 品質検証済み: 内部リンク切れゼロ / HTMLタグ整合 / localStorage不使用 /
  計算例の検算一致（ペルソナ3人: 佐藤・田中・鈴木で章横断の計算を積み上げ）
- Fableによるレビュー済み、指摘2件は修正済み
- **残TODO（軽微）**: 令和8年分の数値（基礎控除62万/給与所得控除特例74万/課税最低限178万）は
  執筆時にnta.go.jpから取得したがモデルの知識カットオフ後の情報。
  広く共有する前に `docs/books/year-end-adjustment/RESEARCH.md` の出典URLを人間が目視確認するのが安心。

## 確立した執筆ワークフロー（次の本もこれで）

1. 指示書を確認 → **Epic Issue** を作成（gh CLI、ラベル `epic` `book` は作成済み）
2. 章立てベースで**サブIssueに分割**（基盤 / 部ごと / 用語集 / 仕上げ）。
   各Issueに章チェックリスト＋執筆方針＋制度・内容上の注意を書き、エージェントが直接読める粒度にする
3. **基盤Issueを最初に**: 一次情報リサーチ（RESEARCH.md）→ HTML骨組み・共通CSS・空章枠 → ペルソナ設定（PERSONAS.md）
4. 部ごとに**Opusサブエージェントへ順次投入**（並列にしない。前の部の文体・計算例を引き継がせるため）。
   プロンプトには「必ず読むファイル一覧」「既存スタイルクラス名」「壊してはいけないもの」を明記
5. 完了ごとに Issue クローズ → 全部終わったら検証（リンク・タグ整合・placeholder残）→ commit & push
6. コミットは日本語、`Co-Authored-By: Claude <モデル名> <noreply@anthropic.com>` を付与

## 完了したこと: 『Webアプリケーションを支えるもの』

- 指示書: `docs/proposals/web-application.md`（全7部32章構成）
- 成果物: `books/web-application/index.html`（単一HTML・全32章）+ `docs/books/web-application/`（PERSONAS.md, RESEARCH.md）
- GitHub Issues: Epic #11 + サブ #12〜#21 すべてクローズ済み
- 題材は架空Railsアプリ「Zephyr」（学習イベント管理）で章横断のコード例を統一
- 品質検証済み: 内部リンク切れゼロ / タグ整合（section開1差はHTMLコメント内の記述例で無害）/
  localStorage不使用（ヒットは説明文とコメントのみ）/ プレースホルダ残ゼロ /
  理解度チェック35セット / MongoDB対比コラム27本（第3部DB編に集中）
- HOWでなくWHYを貫き、部をまたいで伏線を張って回収する構成
- 本棚ポータル `index.html` は「公開中」に更新済み
- **残TODO（軽微）**: 未レビュー（前作はFableでレビュー実施）。広く共有する前に技術記述の目視確認が安心

## その他の注意

- ユーザーとの会話は親しい友人口調の日本語（output style設定あり）
- 実行系ツールの安全判定（Opus classifier）が一時的に落ちることがあった。
  その場合は読み取り専用ツールで作業を続け、少し待ってリトライで復帰する
- `.idea/` は .gitignore 済み
