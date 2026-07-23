# kian-lua ― 公文書起案用 LaTeX マクロ kian の LuaLaTeX 版

kian-lua は、秦誠一郎（Seiichiro HATA）氏が公開している公文書起案用 LaTeX マクロ
**kian**（<https://hata-o.jp/kian/>）を、LuaLaTeX（luatexja）向けに改編した派生物です。
訴状・準備書面・陳述書・証拠説明書など、日本の裁判所提出書面によく使う書式
（当事者欄、宛先、署名欄、公用文の見出し番号体系等）をマクロとして提供します。

- 原著: kian ― Copyright (C) 2016- Seiichiro HATA
- 改変: kian-lua ― Copyright (C) 2026 Shinichi Noro
- ライセンス: **GNU General Public License version 3、またはそれ以降**（GPLv3-or-later）
  - ライセンス全文は同梱の `LICENSE` を参照
  - 各 `.sty` ファイルのヘッダに著作権表示・改変告知を記載

---

## 必要環境

- **LuaLaTeX**（TeX Live 等に同梱。`luatexja` パッケージが必要）
- 和文フォント: **原ノ味明朝**（HaranoAjiMincho-Regular）
- （任意）IVS異体字を使う場合のみ: **IPAmj明朝**（入手方法は後述）

---

## テンプレート一覧

| ファイル | 書面 | 表題コマンド | 固有の注意点 |
|---|---|---|---|
| `template-junbi-lua.tex` | 準備書面 | `\Tit{準備書面}` | 第2準備書面以降は番号を付ける。事件番号・当事者は `\Idcs` 統合書式、宛先は `\Recs[n]`、署名は `\Sens[0.5]` |
| `template-sojo-lua.tex` | 訴状（従来型・表紙あり） | `\Tit{訴　状}` | **紙提出・他庁向け**。当事者欄・訴訟物価額は本文中で組む（`\Jus[4]{…}`）。事件番号は未付番のため `\Idcs` なし。宛先は `\Recs[n]` |
| `template-sojo-shushi-lua.tex` | 訴状―請求の趣旨（IT化） | ―（表紙なし） | **電子提出専用**。`\SecZ{請求の趣旨}` で番号なし冒頭見出し |
| `template-sojo-genin-lua.tex` | 訴状―請求の原因（IT化） | ―（表紙なし） | **電子提出専用**。独立コンパイルで第１から自動採番 |
| `template-chinjutsu-lua.tex` | 陳述書 | `\Tit{陳述書（氏名）}` | 一人称で記載。末尾に住所・氏名・押印欄。事件番号・当事者は `\Idcs` 統合書式、宛先は `\Recs[n]` |
| `template-shoko-lua.tex` | 証拠説明書 | `\Tit{証拠説明書（甲）}` | 乙号証なら `（乙）` に変更。`\shokorow{}{}{}{}{}` で各行を追加。事件番号・当事者は `\Idcs` 統合書式、代理人署名は `\Sens[0.5]` |
| `template-shokai-lua.tex` | 照会書 | `\Tit{照会書}` | 事件番号なし（`\Idcs` は使わない）。宛先は `\Recs[n]`、差出人（事務所・弁護士）は `\Sens[0.5]` |
| `template-iken-lua.tex` | 意見書 | `\Tit{意見書}` | 作成者（専門家）は `\Sens[0.5]{…}` で表示。事件番号・当事者は `\Idcs` 統合書式（裁判所宛先はなし） |

---

## コンパイル方法

### A. コマンドライン（ターミナル）

リポジトリのルートディレクトリで以下を実行:

```sh
latexmk -r latexmkrc-lua template-XXXX-lua.tex
```

例:
```sh
latexmk -r latexmkrc-lua template-sojo-lua.tex
```

中間ファイルの削除:
```sh
latexmk -r latexmkrc-lua -c template-sojo-lua.tex
```

> **注意**: `latexmkrc-lua` を `.latexmkrc` としてルートに置かないこと。
> 他の `.tex` ファイルまで意図せず LuaLaTeX で処理しようとして失敗することがあります。

### B. VS Code（LaTeX Workshop）

各テンプレートの先頭に `% !TEX recipe = LuaLaTeX (kian-lua)` を記述しておくと、
VS Code 上で開いてビルドするだけでレシピが自動選択されます。

---

## 異体字（IVS）の使い方

全テンプレートで `\usepackage{kian-ivs,luatexja-ruby}` は**既定でコメントアウト**されています
（通常の書面ではIVS・ルビを使わないため）。異体字が必要な場合のみ、該当テンプレート先頭の
コメントを外してから使用してください。

```tex
\usepackage{kian-lua}
\usepackage{kian-ivs,luatexja-ruby}  % ← 先頭の % を外す
```

氏名などにIVS異体字が必要な場合は `\ivs{文字}` マクロを使います。

```tex
% 「角」の突き出し字形（U+89D2 U+E0101）
\ivs{角󠄁}田　一郎
```

- `\ivs{}` 内の文字には IVS セレクタ（見えない制御文字）が付いています
- IPAmj明朝フォントで対象の字形が出力されます
- 異体字が不要な文字はそのまま通常フォント（原ノ味明朝）で組まれます

---

## 主要コマンド一覧

`\Idcs`／`\Recs`／`\Sens` の複数形（`s` 付き）で統一しています
（`\Sen{…}`・単発 `\Rec` は現在未使用）。

| コマンド | 用途 | 例 |
|---|---|---|
| `\Tit{…}` | 書面の表題 | `\Tit{準備書面}` |
| `\Idcs{[o]{…}}` | 事件番号・当事者（統合1ブロック） | `\Idcs{[o]{R８(　)　　:【事件名】\\原告:【氏/名】:\\被告:【氏/名】}}` |
| `\Recs{[n]{…}}` | 宛先（裁判所・照会先等） | `\Recs{[n]{:【裁判所名】【支部名】支部:御中}}` |
| `\Sens{[0.5]{…}}` | 差出人・署名・作成者（複数行可） | `\Sens{[0.5]{【原告被告】訴訟代理人/弁護士:【氏名】\\}}` |
| `\Dat{…}` | 作成日 | `\Dat{令和８年　　月　　日}` |
| `\SecZ{…}` | 番号なし見出し | `\SecZ{請求の趣旨}` |
| `\SecA{…}` | 第1階層見出し（第１、第２…） | `\SecA{請求の原因}` |
| `\SecB{…}` | 第2階層見出し（１、２…） | `\SecB{当事者}` |
| `\SecC{…}` | 第3階層見出し（⑴、⑵…） | `\SecC{事故の状況}` |
| `\Jus[4]{…}` | 項目名の字詰め揃え（訴状当事者欄） | `\Jus[4]{住　所}` |
| `\ivs{…}` | IVS異体字切替（IPAmj明朝、要パッケージ有効化） | `\ivs{角󠄁}` |

### `\Idcs`／`\Recs`／`\Sens` のデータ内の区切り記号

- `:`（コロン）… 1行内を最大3つの部分（肩書／本体／続き等）に区切る
- `/`（スラッシュ）… 氏と名の間の字送り区切り（例：`【氏】/【名】`）。表示上スラッシュ自体は出力されない
- `\\` … データ内で複数行に分ける
- `[n]`（`\Recs`）… 宛先を右寄せ表示するオプション
- `[0.5]`（`\Sens`）… 行間を詰めた署名欄表示のオプション

---

## 見出し番号体系と注意点（`\SecZ`〜`\SecG`）

番号書式は `kian-lua/kian-sect.sty` 側で固定されており、本文を空スケルトンにしても崩れない。

| コマンド | 番号書式 |
|---|---|
| `\SecZ` | 番号なし |
| `\SecA` | 第１，第２，第３… |
| `\SecB` | １，２，３… |
| `\SecC` | ⑴，⑵，⑶… |
| `\SecD` | ア，イ，ウ… |
| `\SecE` | (ア)，(イ)，(ウ)… |
| `\SecF` | a，b，c… |
| `\SecG` | (a)，(b)，(c)… |

### ⚠️ 空タイトルの見出しを連続させない

`\SecA{}` のように**タイトルを空**にした見出しを、間に本文（実テキスト）を挟まずに連続させると、

1. `Package kian-sect Error: Bad section order from "X" to "Y"` でコンパイルが止まる
2. 仮にエラーだけ回避しても、見出し番号が１行に混線して表示が壊れる
   （例：5個の空見出しを連続させると「第３⑴」のような崩れた１行になる）

**タイトルが非空の見出し**（`\SecA{当事者}` など）は本文が無くても常に独立した正しい行で表示され、
この問題は起きない。

タイトルを空のまま起案の下書き（スケルトン）にしたい場合は、各見出しの直後に最小限の
プレースホルダー本文を入れること:

```tex
\SecA{}

【ここに記載】

\SecB{}

【ここに記載】
```

---

## ディレクトリ構成

```
（リポジトリルート）
├── kian-lua/          ← LuaLaTeX 用スタイル一式（編集不要）
│   ├── kian-lua.sty   ← 正式パッケージ本体（\usepackage{kian-lua}）
│   ├── kian.sty       ← 後方互換エイリアス（\usepackage{kian}）
│   ├── kian-base.sty
│   ├── kian-ivs.sty
│   ├── kian-page.sty
│   ├── kian-sect.sty
│   ├── kian-titl.sty
│   └── kian-opti.sty
├── latexmkrc-lua      ← latexmk 設定（pdf_mode=4, TEXINPUTS 設定）
├── template-junbi-lua.tex
├── template-sojo-lua.tex          ← 訴状（従来型・表紙あり・紙提出向け）
├── template-sojo-shushi-lua.tex   ← 訴状・請求の趣旨（IT化電子提出向け）
├── template-sojo-genin-lua.tex    ← 訴状・請求の原因（IT化電子提出向け）
├── template-chinjutsu-lua.tex
├── template-shoko-lua.tex
├── template-shokai-lua.tex
├── template-iken-lua.tex
├── LICENSE            ← GNU General Public License v3
└── README.md          ← この文書
```

---

## 訴状のIT化（電子提出）対応

裁判のIT化により、電子提出する訴状は以下の形式になった:

- 表紙部分（表題「訴状」・当事者欄・提出先・訴訟物価額）は**不要**（当事者等はシステムのフォーム入力）
- **請求の趣旨と請求の原因を別々のPDFファイル**として提出する
- 段落番号は連続させず、**請求の原因も第１から開始**する
- 証拠方法・附属書類は記載しない（証拠は証拠説明書 `template-shoko-lua.tex` で提出）

### 使用テンプレートと流れ

```
起案                コンパイル              提出
─────────────────────────────────────────────────────
件名-sojo-shushi-lua.tex  →  件名-sojo-shushi-lua.pdf  → 請求の趣旨として提出
件名-sojo-genin-lua.tex   →  件名-sojo-genin-lua.pdf   → 請求の原因として提出
件名-shoko-lua.tex        →  件名-shoko-lua.pdf         → 証拠説明書として提出
```

### コンパイル手順（電子提出版）

各ファイルを**別々に**コンパイルする（2つを同時でも可）:

```sh
latexmk -r latexmkrc-lua example-sojo-shushi-lua.tex
latexmk -r latexmkrc-lua example-sojo-genin-lua.tex
```

各PDFは独立したコンパイルのため:
- ページ番号は各PDF独立して１ページ目から始まる
- `\SecA`（第１…）の番号も各PDF独立して第１から始まる（手動リセット不要）

### IT化版の技術的ポイント

- **表紙系コマンド（`\Tit`/`\Dat`/`\Sen`/`\Idcs`/`\Recs`）・`\maketitle` は置かない**
- **`\SecZ{請求の趣旨}`** で冒頭に番号なし見出しを付ける（`\SecA` は第１…が付くため）
- `\SecA` は独立コンパイルにより自動で第１から始まる

### 従来型（表紙あり）との使い分け

| 提出方法 | 使用テンプレート |
|---|---|
| **電子提出（IT化）** | `template-sojo-shushi-lua.tex` ＋ `template-sojo-genin-lua.tex` |
| 紙提出・他庁・簡裁等 | `template-sojo-lua.tex`（表紙あり一体型） |

---

## 起案ファイルの命名規則（推奨）

```
件名-書面種別-番号.tex
例（従来型）: example-sojo-lua.tex
例（IT化）:   example-sojo-shushi-lua.tex  ← 請求の趣旨
              example-sojo-genin-lua.tex   ← 請求の原因
例（準備書面）: example-junbi-02-lua.tex
```

コンパイル:
```sh
latexmk -r latexmkrc-lua example-junbi-02-lua.tex
```

---

## フォント入手方法

IVS 異体字（`\ivs{文字}`）を使うには **IPAmj明朝**（`ipamjm.ttf`）が別途必要です。
GPL の対象外（別ライセンス）のため、本配布物にはフォント自体を同梱していません。

- 配布元: 一般社団法人 文字情報技術促進協議会
  <https://moji.or.jp/mojikiban/font/>
- ライセンス: **IPAフォントライセンス**（IPA Font License）
  - 商用・非商用を問わず無償で利用・再配布可
  - ただし改変フォントの再配布時は名称変更が必要等、GPLとは別条件のため、
    フォント自体を再配布する場合は同ライセンス文面を別途確認すること
- インストール後、`kian-ivs.sty` から `IPAmjMincho` の名前で自動参照される

メインフォント（本文用）の**原ノ味明朝**（HaranoAjiMincho）も別途必要です。

---

## ライセンス

GNU General Public License version 3、またはそれ以降（GPLv3-or-later）。
全文は `LICENSE` を参照してください。原著者・改変者の著作権表示は各 `.sty` ファイルの
ヘッダに記載しています。
