# claude-plugin

:gb: [English README](README.md)

OperantKit で強化スケジュールを設計・検査・シミュレーションするための Claude Code プラグインです。Claude に [contingency-dsl](https://github.com/OperantKit/contingency-dsl) の記法（例: `Conc(VI 30-s, VI 60-s, COD=2-s)`）を教える skill 1 本と、[operantkit-mcp](https://github.com/OperantKit/operantkit-mcp) サーバの設定を同梱しています。このリポジトリはプラグイン本体とその marketplace を兼ねます。

## 導入

```bash
pip install operantkit-mcp
claude plugin marketplace add OperantKit/claude-plugin
claude plugin install operantkit@operantkit
```

`operantkit-mcp` が `PATH` 上にある必要があります（Python 3.11 以上）。`claude mcp list` で、サーバ `plugin:operantkit:operantkit` が `✔ Connected` と表示されることを確認してください。

## 同梱物

| 部品 | パス | 役割 |
|---|---|---|
| skill `operantkit` | `skills/operantkit/SKILL.md` | 会話がオペラント／レスポンデント条件づけ、強化スケジュール、Method 節に関わるときに読み込まれる。スケジュールのコードを自作せず、DSL で書いて `check_dsl` で検査するよう Claude に指示する |
| DSL 早見表 | `skills/operantkit/reference.md` | 単一スケジュールと複合スケジュール、修飾、時間単位、注釈 |
| 例 | `skills/operantkit/examples.md` | FR、VI、COD 付き並立 VI VI、多元、連鎖、DRO と、それぞれの道具の呼び方 |
| MCP サーバ設定 | `.mcp.json` | `operantkit-mcp` を stdio で起動する |

MCP サーバの道具は `check_dsl`、`simulate`、`dsl_to_method`、`method_to_dsl` です。入力と出力は operantkit-mcp の README を参照してください。

## 使い方

導入後、実験について Claude に尋ねます。例:「切替遅延 2 秒の並立 VI 30 秒 VI 60 秒スケジュールを設計して、Method 節を書いて」。skill は `/operantkit:operantkit` で直接起動することもできます。

## 必要なもの

- プラグインに対応した Claude Code
- Python 3.11 以上と、`PATH` 上の `operantkit-mcp`

`operantkit-mcp` が無い場合も skill は読み込まれ、MCP サーバの接続だけが失敗します。

## 開発

```bash
claude plugin validate --strict .
claude plugin marketplace add ./
claude plugin install operantkit@operantkit
claude plugin details operantkit
claude plugin marketplace remove operantkit
```

## ライセンス

MIT
