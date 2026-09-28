# CLAUDE.md

このファイルは、このリポジトリで Claude Code (claude.ai/code) を使って作業する際のガイダンスです。

## 概要

非エンジニアのバイブコーダー向けに「AIにアプリを作らせる前の段取り」を解説する1ページのガイド。
題材は架空の「オフ会の出欠管理アプリ」。ソースは `index.html` の単一ファイルで、ビルド・lint・テストは存在しない。

- 確認方法: `index.html` をブラウザで直接開く（または `python3 -m http.server` などの静的サーバー）
- 文章・コメント・コミットメッセージはすべて日本語

## 公開先が2つある

同じ `index.html` を2経路で公開している。片方だけ更新しないよう注意。

| 公開先 | 反映方法 |
|---|---|
| GitHub Pages: https://coban.github.io/vibe-coder-workflow-guide/ | `main` ブランチのルートから配信（legacy build）。main に入れば数十秒で反映 |
| Claude Artifact: https://claude.ai/code/artifact/5e1c437b-5c92-4c23-9dfb-d56300b38c04 | Artifact ツールで `file_path: index.html` と上記 `url` を指定して再公開 |

Artifact の更新では **必ず `url` を渡す**。渡さないと別の新しい Artifact が作られる。
先頭の `<!doctype html>` と `<meta charset>` / `<meta name="viewport">` は Pages 用に置いてあるもので、Artifact のラッパー内に入っても無害なので削らない。

## ページの構造

`index.html` は `<style>` → 本文 → `<script>` の順。本文は `.page` グリッド（1000px 以上で左に固定目次 `.toc`、右に `header.intro` と `main`）。

- 段（stage）は `section.stage#s0` 〜 `#s6` の7つ。段0は事前準備、段1〜5が本題、段6（`class="stage thin"`）は実行。
- 各段は `.stage-head`（`.stage-num` + h2 + `.sub` + `.weight`）のあと、`.part` ブロックを並べる。基本形は
  **目的 → 入力と出力 → プロンプト → 例 → 完了チェック → よくある失敗** で、段によってはその間に固有の `.part` を差し込む
  （段0は「1. アカウント」「2. 道具とリポジトリ」、段4は「切り方の決まり」、段5は「Issueの型」「親と子の関係」「gh コマンド」、段6は「手順」）。
  新しい段や項目を足すときも、この基本形を崩さずに差し込む。
- 段を追加・改番するときは、`.toc` の `li.stage` 項目と、`#flow` セクションの流れ図 SVG も合わせて更新する。
- プロンプト雛形は `.prompt` > `.prompt-head`（`.kind` + `button.copy`）+ `pre`。差し替え箇所は `<span class="var">{…}</span>` で示す。
  コピー処理の JS は「最寄りの `.prompt` 内の `pre.textContent` をコピーする」汎用実装なので、プロンプトを増やしても JS の変更は不要。
- 「よくある失敗」は `.pitfalls` > `.pitfall`、完了チェックは `ul.done`、手順は `ol.steps`。

## スタイルの決まりごと

- 色は `:root` のトークンだけを使う。ライトを素の `:root` に、ダークを `@media (prefers-color-scheme: dark)` 内の `:root:not([data-theme="light"])` と `:root[data-theme="dark"]` の両方に定義してある。新しい色を足すときも3か所すべてに入れる。
- リストのマーカー（`ul.done`、`ol.steps`、`ul.check`）は `li { position: relative }` + 絶対配置の `::before` で描く。`display: grid` を使うと項目が1文字ずつ縦に並ぶ崩れが再発する。
- 横に広い要素（流れ図の SVG、表）は必ず `.fig-scroll` / `.table-scroll` で包み、ページ本体を横スクロールさせない。
- 本文の行幅は `max-width: 66ch` に揃える。

## Git

- コミットメッセージは `docs:` / `fix:` などの接頭辞 + 日本語の要約（例: `docs: 段0 事前準備を追加`）。
- Pages は main から配信されるため、ブランチで作業して PR を main にマージすると公開される。
