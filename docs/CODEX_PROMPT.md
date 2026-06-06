# Codex 用プロンプト（レシピ＆画像生成）

`docs/CODEX_RECIPE_BRIEF.md` の作業をCodexに依頼するための、**そのまま貼り付けて使えるプロンプト**です。
用途に応じて A / B を使い分けてください。

---

## A. リポジトリで作業させる場合（推奨 / Codexがこのリポジトリにアクセスできる前提）

```text
あなたはカクテルデータのキュレーター兼フードフォトのアートディレクターです。
このリポジトリの EvenG2向けカクテルアプリ「BarTender」の内蔵レシピデータと画像を作成してください。

# 最初に読むもの（これが唯一の正です）
- docs/CODEX_RECIPE_BRIEF.md   ← 要件・配分・受け入れ基準
- docs/schema/recipe.schema.json
- docs/schema/ingredient.schema.json
- docs/examples/sample-data.json  ← 出力フォーマットの見本

# 作るもの
1) data/ingredients.json … Ingredient[]（ingredient.schema.json 準拠）
2) data/recipes.json     … Recipe[]（recipe.schema.json 準拠、ユニーク100種以上）
3) assets/recipes/<recipe_id>/hero.webp … 各レシピのメイン画像（必須）
   （任意で thumb.webp / glass.svg も）

# 厳守事項（詳細はブリーフ §3〜§7）
- 件数100種以上。ブリーフ §3.1 のベース別配分（gin/rum/whisky/vodka/sour/beer/non_alcohol）を概ね満たす。
- 全レシピが recipe.schema.json にバリデーション通過（additionalProperties:false に注意）。
- 全 ingredientId が data/ingredients.json に存在（参照整合性）。
- name/steps は en 必須＋ja。steps は1ステップ1動作で簡潔（G2の狭い画面向け）。
- 分類: groups / flavors / strength / method / tags を定義語彙の範囲で付与。strength と estimatedAbv を整合。
- 主要レシピ（最低50種）に casual（ゆる版）を付与。casual.visualSteps は色規約 赤=ベース/黄=酸味/青=割材/灰=氷 に沿う。
- 安全（§6）: 未成年飲酒・飲酒運転・過度飲酒を想起させる表現/画像を入れない。全レシピに estimatedAbv（ノンアルは0）。
- 画像（§5）: バー/スピークイージーのダーク基調で全レシピ統一。正しいグラスとガーニッシュ。画像内に文字・ロゴ・透かしを入れない。hero は1024×1024 webp。

# 進め方
1. ingredients.json を先に確定（代替 substitutes・常備品 isStaple・別名 aliases を含む）。
2. グループごとにレシピを起こし、casual を付与。
3. スキーマ検証＋参照整合性チェックを実行し、エラーを潰す。
4. 画像はシリーズで一貫したスタイル（プロンプト/シード固定）で生成し、recipe_id 配下に配置。

# 完了時の報告
- 生成件数とグループ別内訳、スキーマ検証結果、受け入れチェックリスト（ブリーフ §7）の達成状況を箇条書きで報告。
- 既存語彙で表現できず追加が必要だった項目があれば一覧で提示（enum は勝手に拡張しない）。

コード以外の散文は最小限にし、まず data/ と assets/ を生成してください。
```

---

## B. 単体で渡す場合（リポジトリなし / プロンプトだけで完結させたいとき）

> A のテキストに加えて、`docs/CODEX_RECIPE_BRIEF.md` ・ `recipe.schema.json` ・ `ingredient.schema.json` ・ `sample-data.json` の**中身を添付**してください。冒頭を次に差し替えます。

```text
あなたはカクテルデータのキュレーター兼フードフォトのアートディレクターです。
EvenG2向けカクテルアプリ「BarTender」の内蔵レシピデータ（100種以上）と各レシピ画像を作成します。
仕様・スキーマ・形式見本は本メッセージに添付したとおりです（CODEX_RECIPE_BRIEF / recipe.schema.json /
ingredient.schema.json / sample-data.json）。これを唯一の正として、ingredients.json・recipes.json と
各レシピの hero 画像を生成してください。
```
（以降は A の「厳守事項」以下と同じ）

---

## 画像だけ別ツールで作る場合の補助プロンプト（任意）
画像生成を画像特化モデルで行う場合の1枚あたりプロンプト雛形:

```text
A single <COCKTAIL_NAME> cocktail served in a <GLASS_TYPE>, photographed on a dark moody
speakeasy bar counter with warm amber rim light and soft bokeh. Garnish: <GARNISH>.
Elegant, premium, editorial cocktail photography. Centered, full glass in frame.
No text, no logo, no watermark, no people. Square 1:1.
```
- `<COCKTAIL_NAME>` `<GLASS_TYPE>` `<GARNISH>` をレシピごとに差し込み、照明・彩度・背景は全レシピで固定して統一感を出す。
