# claude-plugin

:gb: [English README](README.md)

Claude Code 用のプラグインです。オペラント条件づけやレスポンデント条件づけの実験について Claude と話すとき、Claude が [OperantKit](https://github.com/OperantKit) を使って強化スケジュールを設計・検査・シミュレーションできるようにします。入るものは 2 つです。[contingency-dsl](https://github.com/OperantKit/contingency-dsl) の記法（例: `Conc(VI 30-s, VI 60-s, COD=2-s)`）を Claude に教え、自前のスケジュールのコードを書かずに OperantKit で検査するよう指示する skill と、[operantkit-mcp](https://github.com/OperantKit/operantkit-mcp) サーバを起動する設定です。

- **Claude はスケジュールを 1 つの式で書き、検査にかけます。** 回答のたびに FR や VI のコードをその場で書くことはしません。
- **シミュレーションと方法セクション（Method 節）の段落は OperantKit が作ります。** そのため数値も文面も再現できます。
- **一度のインストールで**、Claude への指示とサーバの設定の両方が入ります。このリポジトリはプラグイン本体とその marketplace を兼ねます。

## インストール

1. [operantkit-mcp の README](https://github.com/OperantKit/operantkit-mcp#installation) のとおりに `operantkit-mcp` とその依存を入れます（Python 3.11 以上。各パッケージはまだ PyPI に登録されていません）。Claude Code の起動時に `operantkit-mcp` コマンドが `PATH` 上にある必要があります。
2. marketplace を追加し、プラグインを入れます:

```bash
claude plugin marketplace add OperantKit/claude-plugin
claude plugin install operantkit@operantkit
claude mcp list
# plugin:operantkit:operantkit: operantkit-mcp  - ✔ Connected
```

`claude mcp list` が `✘ Failed to connect — ENOENT: Executable not found in $PATH` と表示する場合、skill は入っていますが、Claude Code が `operantkit-mcp` を見つけられていません。インストールした環境を有効にしてから Claude Code を起動してください。

## 使い方

実験について普段の言葉で Claude に尋ねます。例: 「切替遅延 2 秒の並立 VI 30 秒 VI 60 秒スケジュールを設計して、方法セクションを書いて」。会話がオペラント条件づけ・レスポンデント条件づけ・強化スケジュール・方法セクションに関するものになると skill が読み込まれます。`/operantkit:operantkit` で直接呼ぶこともできます。

skill によって、Claude は次のように指示されます:

1. スケジュールを contingency-dsl の式 1 つで書いて `check_dsl` で検査し、以後は正規化された形を使う
2. FR / VI の乱数列・切替遅延・限定保持を自分で書かない
3. スケジュールが何を提示するかを `simulate` で確かめ、`dsl_to_method` / `method_to_dsl` で変換する
4. セッションを OKL（OperantKit Log）形式のイベントログに記録し、要約・分析する（強化後休止、反応間時間、反応バウト、マッチング、累積記録）
5. 日常語の言い換えではなく行動分析学の用語（強化子、弁別刺激）を使う

各道具の入力と出力は [operantkit-mcp の README](https://github.com/OperantKit/operantkit-mcp#tools) を参照してください。

## 同梱物

| 部品 | パス | 役割 |
|---|---|---|
| skill `operantkit` | `skills/operantkit/SKILL.md` | skill が読み込まれたときに Claude が読む指示。上の手順 |
| DSL 早見表 | `skills/operantkit/reference.md` | 単一・複合スケジュール、修飾子、時間の単位、注釈 |
| 例 | `skills/operantkit/examples.md` | FR、VI、切替遅延付きの並立 VI VI、多元、連鎖、DRO と、それぞれの道具の呼び出し |
| MCP サーバの設定 | `.mcp.json` | `operantkit-mcp` を標準入出力で起動する |

`claude plugin details operantkit` によると、skill は毎セッション約 200 トークン、使われるたびにさらに約 850 トークンを追加します。

## 制限

- **プラグインにサーバは含まれません。** `operantkit-mcp` は pip で別に入れ、`PATH` から探されます。プラグインの設定で特定の仮想環境を指すことはできません。
- **模擬被験体は学習しません。** シミュレーションが示すのは、一定の率で反応する被験体にスケジュールが何を提示するかであって、動物がどう振る舞うかではありません。道具のその他の制限は operantkit-mcp の README を参照してください。
- **Claude Code 専用です。** 他のアシスタントは operantkit-mcp を直接使えますが、このプラグインは使えません。

## 開発

```bash
claude plugin validate --strict .
```

## ライセンス

MIT。[LICENSE](LICENSE) を参照してください。
