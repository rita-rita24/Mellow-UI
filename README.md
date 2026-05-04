# Mellow UI

コーポレートサイトや業務系の Web アプリケーションを想定して作ったデザインシステム。
日本語のフォントを基準にレイアウトを組み、ライト／ダーク両方で破綻しないようにコントラストを実測して整えた。
コンポーネントは65個。
フレームワークには依存しない。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![WCAG 2.1 AA](https://img.shields.io/badge/WCAG-2.1%20AA-green.svg)](https://www.w3.org/WAI/WCAG21/quickref/)

---

## できること

- 65 のコンポーネントを Action / Input / Selection / Display / Feedback / Structure / Navigation / Surface に分けて収録
- ダークモードは OS の設定を見て自動で切り替わる。手動切替も可
- WCAG 2.1 AA のコントラスト比はすべて実測してある（最低 4.5:1）
- 派生色（hover / active）は OKLCH の relative color syntax で自動生成。ベース色は hex のまま残しているので旧ブラウザでも崩れない
- skip link、focus-visible、`prefers-contrast`、`forced-colors` まで踏み込んで対応
- 420 / 640 / 900 / 1200px のブレークポイントで折り返す
- 依存ゼロ。HTML・CSS・JS だけで動く
- 日本語フォント前提。Zen Maru Gothic と Noto Sans JP を使う
- `@media print` で UI を消し、本文だけ印刷できる
- トークンは Primitive / Semantic / Component の3階層

---

## 使いはじめる

```bash
git clone https://github.com/your-org/mellow-ui.git
cd mellow-ui
open ui-library.html  # macOS の場合
```

部品単位で取り込みたいときは、`tokens.css` を読み込んで好きなコンポーネントの HTML をコピーする。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="tokens.css">
</head>
<body>
  <button class="btn-primary">送信</button>
</body>
</html>
```

---

## ファイル構成

```
mellow-ui/
├── ui-library.html   65個のコンポーネントを並べたカタログ
├── tokens.css        CSS Custom Properties で定義したトークン
├── tokens.json       JSON 形式のトークン（DTCG / Style Dictionary 互換）
├── DESIGN.md         設計方針と運用ルール
└── README.md         このファイル
```

---

## コンポーネント一覧

**Action (5)**
Button・Icon Button・Button Group・Menu・Popover

**Input (9)**
Input・Textarea・Search Bar・Combobox・Number Stepper・Date Picker・Time Picker・OTP Input・File Upload

**Selection (9)**
Select・Checkbox・Radio・Switch・Slider・Range Slider・Toggle Group・Rating・Color Picker

**Display (13)**
Badge・Status Dot・Tag・Chip・Avatar・Avatar Stack・Bell・Kbd・Tooltip・Code・Metric・Stat Card・Timeline

**Feedback (7)**
Alert・Toast・Banner・Progress・Loader・Spinner・Skeleton

**Structure (12)**
Card・Step Card・Feature Card・Issue Card・List Item・Tree View・Stat Group・Definition List・Quote・Divider・Accordion・Empty State

**Navigation (5)**
Breadcrumb・Pagination・Tabs・Stepper・Vertical Steps

**Surface (5)**
Hero・Modal・Table・Callout・Aside Panel

---

## カラーモデルの考え方

ベース色は hex で固定し、hover と active の派生色だけ OKLCH で導出する。コントラスト比の検証は hex に対してかけているので、AA をクリアした色がそのまま残る。OKLCH 側は明度を相対的に動かすだけなので、色相はぶれない。

```css
:root {
  --action-primary: #1E5FBF;
  --action-primary-hover:  var(--color-teal-600); /* fallback */
  --action-primary-active: var(--color-teal-700); /* fallback */
}

@supports (color: oklch(from #1E5FBF l c h)) {
  :root {
    --action-primary-hover:  oklch(from var(--action-primary) calc(l - 0.06) c h);
    --action-primary-active: oklch(from var(--action-primary) calc(l - 0.12) c h);
  }
}
```

ライトでは hover で暗く、ダークでは hover で明るくなる。押した感じの方向は逆向きで揃えてある。

---

## アクセシビリティ

| 項目 | 対応 |
|---|---|
| WCAG 2.1 AA コントラスト比 | 全組み合わせを実測した |
| Skip link | Tab で「本文へスキップ」が出る |
| Focus visible | 3px のリングを全要素に |
| `prefers-reduced-motion` | アニメーションを止める |
| `prefers-contrast: more` | ボーダーを濃く、tertiary を primary に上げる |
| `forced-colors: active` | Windows ハイコントラストで枠線が OS 色に |
| `aria-label` | アイコンだけのボタンには必ず付ける |
| キーボード操作 | Tab / Enter / Space / Esc が通る |

### コントラスト比の実測値

| 組み合わせ | 比率 | 評価 |
|---|---|---|
| text-primary on bg-canvas (light) | 16.23:1 | AAA |
| text-secondary on bg-canvas (light) | 7.01:1 | AAA |
| text-tertiary on bg-canvas (light) | 5.22:1 | AA |
| 白 on action-primary (light) | 6.10:1 | AAA |
| 白 on action-primary (dark) | 4.77:1 | AA |
| 白 on action-cta (Peach 700) | 5.36:1 | AA |
| 白 on state-error-solid | 6.93:1 | AAA |

---

## トークン

### 3層で組み立てる

```
L1 Primitive  10階調のカラーやスペーシングなど
       ↓
L2 Semantic   --bg-canvas / --text-primary / --action-primary など
       ↓
L3 Component  --btn-cta-bg / --input-border-focus など
```

実装では L2 か L3 だけを参照する。L1 を直接書くと、テーマ切替やカラー変更のたびに手を入れることになる。

### 提供形式

| ファイル | 中身 |
|---|---|
| `tokens.css` | `var(--token-name)` で参照する CSS Custom Properties |
| `tokens.json` | Design Tokens Community Group 形式の JSON |

`tokens.json` を [Style Dictionary](https://amzn.github.io/style-dictionary/) などに通せば、iOS / Android / Flutter にも展開できる。

---

## 動作環境

| ブラウザ | バージョン | 備考 |
|---|---|---|
| Chrome / Edge | 119 以降 | OKLCH の relative color syntax が使える |
| Safari | 16.4 以降 | 同上 |
| Firefox | 128 以降 | 同上 |
| それ以前 | hex フォールバック | `@supports` で透過的に切り替わる |

OKLCH に対応していないブラウザでも、hex の値で同じように見える。

---

## 詳細仕様

[DESIGN.md](./DESIGN.md) に書いた内容：

- 配色比率と意味のつけ方
- タイポグラフィ5レベル
- 余白の4の倍数ルール
- 角丸スケール（最大 12px）
- アニメーションのタイミング
- 命名規則と禁止事項

---

## 自分用にカスタマイズする

色もサイズもすべて CSS 変数で外に出してあるので、`:root` を上書きすれば差し替わる。OKLCH の派生色も自動で追従する。

```css
:root {
  --color-teal-500: #2563EB;     /* ブランドの青を差し替え */
  --radius-md: 8px;              /* 角丸を強める */
  --radius-xl: 16px;
  --font-display: "Your Custom Font", sans-serif;
}
```

---

## 設計の方針

1. 明度差で情報の優先順位を表す。色相だけで分けない
2. 角丸は控えめに。最大 12px まで
3. 余白は4の倍数で取る
4. 値はトークン経由で参照する。ハードコード禁止
5. `border-left` と `border-top` は装飾に使わない（RTL で崩れる、代替手段がある）

---

## クレジット

- フォント: [Zen Maru Gothic](https://fonts.google.com/specimen/Zen+Maru+Gothic) / [Noto Sans JP](https://fonts.google.com/noto/specimen/Noto+Sans+JP) / [DM Sans](https://fonts.google.com/specimen/DM+Sans) / [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono)
- アイコン: [Material Symbols (Rounded)](https://fonts.google.com/icons)
- カラーモデル: [OKLCH by Björn Ottosson](https://bottosson.github.io/posts/oklab/)
