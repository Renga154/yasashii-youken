# やさしい要件ヒアリング(yasashii-youken)

コードが書けない人が AI に「〜作って」と頼んだとき、作り始める前に **本人にしか分からないことだけ** を、専門用語なしの少ない質問で聞き出す Agent Skill です。Claude Code・Codex など、Agent Skills(`SKILL.md`)に対応したツールで使えます。

## なぜ作ったか

ざっくり「家計簿アプリ作って」と頼むと、AI が構成のほとんどを黙って決め、想像と違うもの・質の低いものができがちです。[grilling](https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md) のように AI に質問させる方法はありますが、質問が技術寄りだと、知識のない人はどれを選ぶと何が変わるのか分かりません。

ただ、質問をやさしい言葉に言い換えるだけでは足りませんでした。完成形を思い描けない人は、やさしく聞かれても「おすすめでいいです」と答えてしまい、結局 AI が決めることになるからです。そこでこのスキルは、次の4つを中心に作っています。

- **答えられる質問にする**: 「ログイン方式は?」ではなく「あなた以外に使う人はいますか?」と、本人の世界について聞く。技術の判断は答えから AI が導く
- **気づかずに決めていることの一覧を持つ**: ログインの有無、誰がどのデータを見られるか、月いくらかかるか、特定商取引法の表記、バックアップなど、非エンジニアが気づかない判断を日本向けにまとめた「決めること一覧」から、重いものだけを聞く
- **選択肢の結果を見せる**: 選択肢ごとに「どう見えるか・お金(円)・注意・後から変えられるか」を示し、見た目は画面の見本で選んでもらう
- **完成後に本人が確かめられる**: 要件書に「本人が自分の画面で確かめる手順」を書き、AI が確かめられるものは AI が先に確かめる

## 流れ

1. **ひとこと確認**: 何のため/誰が使うか(今といずれ)/完成のイメージ/あなたはスマホ派かパソコン派か・設定の作業はできそうか
2. **聞くことを絞る**: 決めること一覧から、全体で8問まで(1回4問・3回まで)に絞る。分野ならではのルール(家計簿の締め日、教室の振替など)は「よくある形で仮に埋めました。違う番号だけ教えてください」の1問にまとめる
3. **選択肢カードで聞く**: すべての質問に「おまかせ」を付ける。「よくわからない」と言われたときの扱いも決めてある
4. **要件書 `youken.md` を作る**: 先に、お金・個人情報・作り直しになる判断を、はい/いいえで最大3つ確かめてから見せる
5. **作る**: アカウント作成など、本人にしかできない作業は要件書に一覧で書く
6. **完成チェック**: 本人には、自分の画面で見ないと分からないものだけを頼む

「いいから作って」と言えば質問を省略できます(毎月のお金と他人の個人情報に関わる判断だけは確かめます)。

## 選択肢カードの例

```
Q1. 家族と一緒に使いますか?
(なぜ聞くか: 一緒に使うなら「本人だけが開ける仕組み(ログイン)」と「誰が何を見られるか」の設定が要り、作る手間が少し増えます)

A. 自分だけで使う
   見え方: スマホで開くとすぐ記録画面。ログインなし
   お金: 0円
   注意: このスマホの中だけに保存するので、機種変更やブラウザの掃除で記録が消えることがある
   後から変える: △ 家族で使いたくなったら一部作り直し
B. 家族で共有する(おすすめ)
   見え方: 最初に1回ログイン。招待した家族だけが、同じ記録を見て書ける
   お金: 0円(2人で使う量なら無料の範囲)
   注意: 家族のスマホでも1回ずつログインが要る
   後から変える: ◎ 一人用に戻すのは簡単
C. おまかせ(おすすめの B で進めます)
```

## できあがる要件書(youken.md)

1つのファイルが二層になっています。

- **上半分(あなた向け)**: ひとことで言うと/使う人/見本/できること/やらないこと/お金(毎月・毎年・最初だけ・上限)/あなたにやってもらうこと/決めたことリスト(番号付き。番号で言えば変えられる)/使い始めてから/完成チェック/あとで確かめること
- **下半分(AI 向け)**: 構成、データ、外部サービスと名義、決めたことの技術的な中身、運用の手順、素材の出典、AI が確かめた完成チェック

## インストール

Claude Code(自分のすべてのプロジェクトで使う):

```bash
git clone https://github.com/Renga154/yasashii-youken ~/.claude/skills/yasashii-youken
```

Codex:

```bash
git clone https://github.com/Renga154/yasashii-youken ~/.codex/skills/yasashii-youken
```

特定のプロジェクトだけで使うなら、そのプロジェクトの `.claude/skills/yasashii-youken` に置いてください。ほかのツールでも、`SKILL.md` と `references/` をそのツールのスキル置き場に置けば使えます。

## 使い方

「〜作って」「〜を作りたい」「〜を自動でやりたい」と頼むと自動で使われます。確実に使いたいときは、Claude Code なら `/yasashii-youken` で呼んでください。

[superpowers](https://github.com/obra/superpowers) の brainstorming スキルも入れている場合は、どちらも「作る前に使う」スキルなので重なります。`CLAUDE.md` に次のような1行を足すと、こちらが優先されます。

```
何かを新しく作る依頼では、superpowers:brainstorming ではなく yasashii-youken で要件を聞く。
```

## 中身

```
SKILL.md                         流れと基本の考え方
references/
  question-style.md              聞き方(選択肢カード、言い換え表、見本の出し方)
  spec-template.md               要件書 youken.md のひな形と完成チェックの書き方
  catalog-common.md              決めること一覧: どの種類でも見る共通の項目
  catalog-web-app.md             決めること一覧: 記録・管理・予約・会員制などのアプリ
  catalog-website.md             決めること一覧: ホームページ・LP・ブログ
  catalog-automation.md          決めること一覧: 自動化・スプレッドシート連携・ボット
  catalog-ai-feature.md          決めること一覧: AI を使う機能
```

## 注意

- 料金・無料の範囲・法律の内容は、2026年10月時点で公式の情報を確かめた **目安** です。料金は変わるので、スキルは「採用した案の料金を、要件書を書く前に公式ページで確かめ直す」よう指示しています。
- 法律(特定商取引法、個人情報保護法、電気通信事業法など)についての記述は、小さな個人・お店の作業で気づきやすくするためのもので、法的な助言ではありません。気になる点は専門家に確かめてください。一部の項目は一覧の中で「要確認」としています。
- 日本の利用者向けに作っています(円、国内のサービス、日本の法律)。

## English summary

An Agent Skill (Claude Code, Codex, and other `SKILL.md`-compatible agents) for non-technical "vibe coders" in Japan. Before an AI builds anything from a vague request, it asks only the decisions the user alone can make, in plain Japanese and in terms of the user's own world rather than technology. It draws on decision catalogs (web apps, websites, automations, AI features, plus common items such as cost, personal data, and Japanese legal notices), presents each option as a card showing what the user will see, the monthly cost in yen, the main caveat, and how hard it is to change later, and always offers "おまかせ" (let the AI decide). It then writes a two-layer `youken.md` (a plain-language spec for the user and technical notes for the AI) with a numbered list of every decision the AI made, and finishes with acceptance checks the user can do on their own screen.

## ライセンス

[MIT](LICENSE)
