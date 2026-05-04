# Mellow UI

---

## 目次

1.  [はじめに](#1-はじめに)
2.  [設計原則](#2-設計原則)
3.  [トークンアーキテクチャ](#3-トークンアーキテクチャ)
4.  [カラー](#4-カラー)
5.  [タイポグラフィ](#5-タイポグラフィ)
6.  [スペーシング](#6-スペーシング)
7.  [角丸・影・モーション](#7-角丸影モーション)
8.  [アクセシビリティ](#8-アクセシビリティ)
9.  [コンポーネント一覧](#9-コンポーネント一覧)

---

## 1. はじめに

Mellow UI はサイト全体のデザイン判断を一本化するための共通言語。色・書体・間合い・部品をトークンに分解しているので、1箇所の変更が全体に届く。

### 含まれるもの

| カテゴリ | 内容 | 数 |
|---|---|---|
| Foundation | カラー・タイポ・スペース・角丸・影・モーション | 6カテゴリ |
| Component | 8カテゴリ（Action / Input / Selection / Display / Feedback / Structure / Navigation / Surface） | 65部品 |
| Tokens | Primitive(L1) + Semantic(L2) + Component(L3) + Dark Theme | 計550+ |
| Theme | Light + Dark | 2テーマ |
| Documentation | 本書・UIライブラリ・トークンファイル | 4ファイル |

### ファイル構成

```
design-system/
├── DESIGN.md          ← 本書
├── ui-library.html    ← UIライブラリ（Storybook風カタログ）
├── tokens.css         ← CSS変数（実装で読み込み）
└── tokens.json        ← Figma/Style Dictionary用
```

### 使い方（クイックスタート）

```html
<!-- 1. フォント読み込み -->
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&family=Noto+Sans+JP:wght@400;500;700&family=Zen+Maru+Gothic:wght@400;500;700;900&display=swap" rel="stylesheet">

<!-- 2. アイコン読み込み -->
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Rounded" />

<!-- 3. トークン読み込み -->
<link rel="stylesheet" href="tokens.css">

<!-- 4. 実装で参照 -->
<button style="background: var(--btn-cta-bg); color: var(--btn-cta-text);">
  アクション
</button>
```

---

## 2. 設計原則

デザインの判断はこの5つに揃える。

### 原則01: 温かさ優先

迷ったら温かい方を選ぶ。冷たいネイビーやピュアグレーは使わず、わずかに暖色を含む色を選ぶ。

| 避ける | 選ぶ |
|---|---|
| `#000000`（純黒） | `#1F1C1A`（暖色を含む黒） |
| `#808080`（純グレー） | `#6B6560`（暖色を含むグレー） |
| 冷たいネイビー | 穏やかなティール |

### 原則02: シャープなコーポレート角丸

角丸は控えめに。最大でも 12px までに留める。

```
○ border-radius: 2-4px   ボタン・入力
○ border-radius: 6-8px   カード・モーダル
○ border-radius: 9999px  Avatar・Switch などの円形だけ
✗ border-radius: 20px+   カジュアルに振れすぎる
```

### 原則03: 余白優先

詰める前に余白を確認する。

| 場所 | 余白 |
|---|---|
| 段落と段落 | 24-32px |
| カード内 | 24px以上 |
| セクション間 | 96-128px |

### 原則04: 静かさ優先

強い色や派手な動きは選ばない。モーションは180-280msのトランジションで揃える。

### 原則05: 一貫性

新しい部品を追加するときも、既存のトークンを参照する。色・余白・角丸を勝手に作らない。

### border の禁止ルール

`border-left` と `border-top` は装飾に使わない。

| 禁止プロパティ | 主な理由 |
|---|---|
| `border-left` | RTL（右→左言語）で反転させる必要がある、左右非対称で乱雑に見える、別の表現で済む |
| `border-top` | 上端の細線は要素の所属が読み取りにくい、下線（`border-bottom`）の方が視線の動きに合う |

代わりに使う手：

| 用途 | 代替 |
|---|---|
| アラートのカテゴリ表示 | アイコン + 背景色 |
| アクティブタブ | 下線（border-bottom） |
| 引用・警告 | 背景色 + 左アイコン |
| カードのカテゴリ | バッジ・タグでラベル化 |
| 横方向の区切り | 直前要素の `border-bottom` |
| 点線装飾 | `background-image` の linear-gradient |

---

## 3. トークンアーキテクチャ

### 3層構造

```
┌──────────────────────────────────────────────┐
│ L3 Component                                 │
│ --btn-cta-bg                                 │
│ --modal-shadow                               │
└──────────────────────────────────────────────┘
                   ↓ 参照
┌──────────────────────────────────────────────┐
│ L2 Semantic                                  │
│ --action-cta                                 │
│ --bg-canvas                                  │
└──────────────────────────────────────────────┘
                   ↓ 参照
┌──────────────────────────────────────────────┐
│ L1 Primitive                                 │
│ --color-peach-500: #F87B57                 │
│ --space-4: 16px                              │
└──────────────────────────────────────────────┘
```

### 変更ルール

| 変更内容 | 修正する層 | 影響範囲 |
|---|---|---|
| ブランド色全体を変える | L1 のみ | 全コンポーネント |
| 用途の方針を変える | L2 のみ | 関連部品 |
| 特定の部品だけ変える | L3 のみ | その部品のみ |

### 命名規則

```
--{カテゴリ}-{サブカテゴリ}-{バリアント}-{状態}

例:
  --color-teal-500          ← L1
  --bg-canvas               ← L2
  --btn-cta-bg              ← L3 default
  --btn-cta-bg-hover        ← L3 hover state
  --btn-cta-bg-disabled     ← L3 disabled state
```

---

## 4. カラー

### 4.1 パレット概要

| パレット | 役割 | Base値 | 階調 |
|---|---|---|---|
| Blue | 主軸・見出し・主操作 | `#1E5FBF` | 50-900（10階調） |
| Peach | CTA・アクセント | `#F87B57` | 50-900（10階調） |
| Sunshine | 装飾・ハイライト | `#FFCB47` | 50-700（5階調） |
| Warm Gray | 背景・テキスト・罫線 | - | 0-900（12階調） |
| Success | 成功状態 | `#5DBB7E` | 50-900（4階調） |
| Warning | 警告状態 | `#F0A933` | 50-900（4階調） |
| Error | エラー状態 | `#E36363` | 50-900（4階調） |

### 4.2 配色比率（推奨）

```
Neutral    65%  ← ベース
Blue       20%  ← 構造
Peach      10%  ← 強調
Sunshine    5%以下  ← 装飾
```

### 4.3 セマンティックトークン

実装で参照するのは L2。L1 を直接書かない。

| L2トークン | 参照先 | 用途 |
|---|---|---|
| `--bg-canvas` | warm-0 | ページ背景 |
| `--bg-muted` | warm-50 | サブ背景 |
| `--bg-emphasized` | warm-100 | 強調背景 |
| `--text-primary` | warm-800 | 本文 |
| `--text-secondary` | warm-600 | 補助テキスト |
| `--text-tertiary` | warm-400 | キャプション |
| `--text-disabled` | warm-300 | 無効状態 |
| `--text-brand` | blue-700 | 見出し |
| `--action-primary` | blue-500 | 主操作 |
| `--action-cta` | peach-500 | 主CTA |

### 4.4 コントラスト比

すべて実測した値。

| 組み合わせ | 比率 | 評価 |
|---|---|---|
| `--text-primary` on `--bg-canvas` | 16.23 : 1 | AAA |
| `--text-secondary` on `--bg-canvas` | 7.01 : 1 | AAA |
| `--text-tertiary` on `--bg-canvas` | 5.22 : 1 | AA |
| 白 on `--action-primary` (light) | 6.10 : 1 | AAA |
| 白 on `--action-primary` (dark) | 4.77 : 1 | AA |
| 白 on `--action-cta` (Peach 700) | 5.36 : 1 | AA |
| 白 on `--state-success-solid` (700) | 5.27 : 1 | AA |
| 白 on `--state-warning-solid` (700) | 6.01 : 1 | AA |
| 白 on `--state-error-solid` (700) | 6.93 : 1 | AAA |
| 白 on `--state-info-solid` (700) | 8.97 : 1 | AAA |

### 4.5 ハイブリッド・カラーモデル

ベース色は hex で固定し、hover と active の派生色だけ OKLCH で導出する。コントラスト比は hex に対して検証してあるので、AA をクリアした色がそのまま残る。OKLCH 側は明度を相対的に動かすだけで、色相はぶれない。

**理由**
- 基準色（500・700）は hex のまま。AA を満たした値が固定される
- 派生色は `oklch(from <base> calc(l - 0.06) c h)` で出す。書き手が手動で選ばない
- OKLCH 非対応ブラウザ（Chrome <119 / Safari <16.4 / Firefox <128）では hex フォールバックがそのまま使われる

**生成ルール**

| 用途 | 変換式 |
|---|---|
| ライト時 hover | `calc(l - 0.06)` |
| ライト時 active | `calc(l - 0.12)` |
| ダーク時 hover | `calc(l + 0.06)` |
| ダーク時 active | `calc(l + 0.12)` |

ライトでは hover で暗くなり、ダークでは hover で明るくなる。押した感じの方向を揃えてある。

**適用範囲**

- `--action-primary-hover` / `--action-primary-active`
- `--action-cta-hover` / `--action-cta-active`
- `--btn-danger-bg-hover`
- `--text-link-hover`

**フォールバック**
`@supports (color: oklch(from #1E5FBF l c h))` で対応ブラウザを判定する。非対応のときは `:root` 内の hex がそのまま効く。

---

## 5. タイポグラフィ

### 5.1 フォント

| 役割 | フォント | 用途 |
|---|---|---|
| Display | Zen Maru Gothic | 見出し（丸ゴシック） |
| Body | Noto Sans JP | 本文（読みやすさ） |
| English | DM Sans | 英字・数値 |
| Mono | JetBrains Mono | コード・数値 |

### 5.2 階層

| 名称 | サイズ | 用途 |
|---|---|---|
| Display | 60-76px | ヒーロータイトル |
| Heading 1 | 38-48px | ページ主見出し |
| Heading 2 | 30px | セクション見出し |
| Heading 3 | 20-24px | サブ見出し |
| Body | 16px | 本文標準 |
| Body Small | 14px | 補助情報 |
| Caption | 12px | 注釈・日付 |
| 2X-Small | 10px | バッジ内テキストなど |

### 5.3 行間

| 用途 | 行間 |
|---|---|
| 見出し | 1.25-1.45 |
| 本文（標準） | **1.8** ★ |
| 長文記事 | 2.0 |

---

## 6. スペーシング

### 6.1 4pxグリッド

| トークン | 値 | 主な用途 |
|---|---|---|
| `--space-1` | 4px | アイコンと文字の間 |
| `--space-2` | 8px | バッジ内 |
| `--space-4` | 16px | 段落間 |
| `--space-6` | 24px | カード内余白 |
| `--space-8` | 32px | サブセクション |
| `--space-12` | 48px | セクション内段落 |
| `--space-16` | 64px | 中規模セクション間 |
| `--space-24` | 96px | **セクション間（標準）** |
| `--space-32` | 128px | 大セクション間 |

---

## 7. 角丸・影・モーション

### 7.1 Radius

| トークン | 値 | 用途 |
|---|---|---|
| `--radius-xs` | 0px | シャープな境界線 |
| `--radius-sm` | 2px | **Primary系ボタン** ★ |
| `--radius-md` | 4px | **入力 / CTAボタン / Tag** ★ |
| `--radius-lg` | 6px | 小カード |
| `--radius-xl` | 8px | **標準カード / モーダル** ★ |
| `--radius-2xl` | 12px | ヒーロー・大ブロック |
| `--radius-pill` | 9999px | Avatar / Switch / Slider トラック等の円形要素のみ |

### 7.2 Shadow

| トークン | 用途 |
|---|---|
| `--shadow-xs` | 微細な浮き |
| `--shadow-sm` | カード通常時 |
| `--shadow-md` | カードホバー時 |
| `--shadow-lg` | フローティング要素 |
| `--shadow-xl` | モーダル・ダイアログ |
| `--shadow-2xl` | 大型オーバーレイ |
| `--shadow-glow` | **メインCTA専用** |
| `--shadow-inset` | 凹み表現 |

### 7.3 Motion

| トークン | 値 | 用途 |
|---|---|---|
| `--dur-fast` | 180ms | hover時の色変化 |
| `--dur-base` | 280ms | **標準トランジション** ★ |
| `--dur-slow` | 480ms | モーダル・ステージング |
| `--dur-slower` | 640ms | ヒーロー要素登場 |
| `--ease-out` | `cubic-bezier(0.16, 1, 0.3, 1)` | 標準 |
| `--ease-spring` | `cubic-bezier(0.34, 1.56, 0.64, 1)` | 注目演出 |

---

## 8. アクセシビリティ

### 8.1 キーボード操作

インタラクティブ要素は Tab / Shift+Tab で全部たどれる。focus-visible で 3px のリングを出す。

```css
button:focus-visible {
  outline: none;
  box-shadow: var(--focus-ring);
}
```

### 8.2 スクリーンリーダー

| 部品 | 対応 |
|---|---|
| ボタン | テキストか `aria-label` |
| アイコンだけ | `aria-label="検索"` を必ず付ける |
| 画像 | `alt` を入れる（装飾なら `alt=""`） |
| Modal | `role="dialog"` `aria-modal="true"` `aria-labelledby` |
| Tooltip | `role="tooltip"` |
| Alert | `role="alert"` |
| Form | `<label>` を `<input>` に紐付ける |

### 8.3 reduced motion

```css
@media (prefers-reduced-motion: reduce) {
  /* duration を 0ms に */
}
```

`tokens.css` に組み込み済み。

### 8.4 タッチターゲット

WCAG 2.5.5 に合わせて 44×44px を確保する。Checkbox など小さい部品は周囲の余白で稼ぐ。

### 8.5 色だけに頼らない

色だけで情報を伝えない。エラーや成功はアイコンと併用する。

---

## 9. コンポーネント一覧

合計 65 部品。役割で 8 カテゴリに分けてある。実装と仕様は `ui-library.html` を見る。

### 9.1 Action（操作）— 5部品

ユーザーが直接「実行」する部品。最も使用頻度が高い。

| 部品 | 説明 |
|---|---|
| Button | 操作実行（cta/primary/outline/ghost/danger） |
| Icon Button | アイコンのみのボタン（aria-label必須） |
| Button Group | 関連操作の連結グループ |
| Menu | コンテキストメニュー |
| Popover | クリック起点の小型オーバーレイ |

### 9.2 Input（自由入力）— 9部品

ユーザーが任意のテキスト・数値・日付を入力する部品。

| 部品 | 説明 |
|---|---|
| Input | 1行テキスト入力 |
| Textarea | 複数行テキスト入力 |
| Search Bar | 検索バー（クリア機能付） |
| Combobox | オートコンプリート付セレクト |
| Number Stepper | 数値増減（−／＋ボタン付） |
| Date Picker | 日付選択カレンダー（年/月/日） |
| Time Picker | 時刻選択（時/分/AM-PM） |
| OTP Input | ワンタイムパスワード入力 |
| File Upload | ドラッグ&ドロップ対応のファイル選択 |

### 9.3 Selection（選択）— 9部品

定義済み選択肢から選ぶ部品。

| 部品 | 説明 |
|---|---|
| Select | ドロップダウン選択 |
| Checkbox | 複数選択 |
| Radio | 単一選択 |
| Switch | ON/OFF切替（即時反映） |
| Slider | 数値範囲選択（単一値） |
| Range Slider | 範囲指定（最小/最大の2点） |
| Toggle Group | セグメント切替（タブ的単一選択） |
| Rating | 星評価（レビュー入力／表示） |
| Color Picker | プリセットスウォッチからカラー選択 |

### 9.4 Display（情報表示）— 13部品

データを「読ませる」ための部品。

| 部品 | 説明 |
|---|---|
| Badge | ステータス強調（6バリアント） |
| Status Dot | オンライン状態などのドット表示 |
| Tag | 削除可能ラベル |
| Chip | 選択状態を持つフィルタ用 |
| Avatar | ユーザー画像（4サイズ） |
| Avatar Stack | アバター重ね表示 |
| Notification Bell | 未読数バッジ付通知 |
| KBD | キーボードショートカット表示 |
| Tooltip | ホバー補足説明 |
| Code Block | コードハイライト表示 |
| Metric | 数値ラベルペア |
| Stat Card | 数値・トレンド表示カード |
| Timeline | 時系列の履歴・進捗 |

### 9.5 Feedback（応答／状態）— 7部品

システムの状態をユーザーに伝える部品。

| 部品 | 説明 |
|---|---|
| Alert | 重要メッセージ（4バリアント） |
| Toast | 短時間表示の通知 |
| Banner | サイト上部の通知バナー |
| Progress | 進捗バー（確定的） |
| Loader Bar | バー型インラインローダー（不確定） |
| Spinner | ローディングスピナー |
| Skeleton | ローディング時のプレースホルダ |

### 9.6 Structure（構造／コンテナ）— 12部品

コンテンツを「まとめる／区切る」ための部品。

| 部品 | 説明 |
|---|---|
| Card | 汎用ベースカード |
| Step Card | ナンバリング付き手順カード |
| Feature Card | メディア+説明型カード |
| Issue Card | アイコン+リスト型カード |
| List Item | リスト項目 |
| Tree View | 階層構造ツリー（フォルダ展開） |
| Stat Group | 統計値の連結表示 |
| Definition List | プロパティ/値の対応表 |
| Quote | 引用ブロック |
| Divider | 区切り線（テキスト付対応） |
| Accordion | 折りたたみコンテンツ |
| Empty State | データなし時の表示 |

### 9.7 Navigation（ナビゲーション）— 5部品

ページ／状態を「移動する」ための部品。

| 部品 | 説明 |
|---|---|
| Breadcrumb | 階層ナビ |
| Pagination | ページ送り |
| Tabs | タブ切替（下線方式） |
| Stepper | 多段プロセス進捗（横型） |
| Vertical Steps | 縦型ステッパー（詳細説明付） |

### 9.8 Surface（特殊サーフェス）— 5部品

ページ全体を「占有する」大型部品。

| 部品 | 説明 |
|---|---|
| Hero | ヒーローセクション |
| Modal | モーダルダイアログ |
| Table | 表形式データ |
| Callout | 重要情報の囲み枠 |
| Aside Panel | 補助情報パネル |
