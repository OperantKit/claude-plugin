---
name: operantkit
description: Design, validate, and simulate reinforcement schedules with OperantKit instead of hand-writing FR/VI generators. Use when the user designs an operant or respondent conditioning experiment, mentions a reinforcement schedule (FR, VR, FI, VI, FT, VT, DRO, DRL, DRH, CRF, EXT, concurrent, multiple, chained, changeover delay, limited hold, Fleshler-Hoffman), asks for a Method section from a schedule or a schedule from a Method section, or has an OKL session log to summarize. 日本語の発火語: オペラント条件づけ、強化スケジュール、随伴性、実験の環境設計、累積記録、Method 節、行動分析。
when_to_use: Also use for RR, RI, RT, tandem, mixed, second-order schedules, progressive ratio, matching law, cumulative records, JEAB-style Method sections, operant chambers (Skinner boxes), and OKL v1 logs. 日本語: オペラント、レスポンデント、変動間隔、固定比率、並立、多元、連鎖、切替遅延、限定保持、マッチング法則、スキナー箱。
---

# OperantKit

OperantKit is a set of Python/Rust packages for designing and running operant and respondent conditioning experiments. A schedule is written as one [contingency-dsl](https://github.com/OperantKit/contingency-dsl) expression, such as `Conc(VI 30-s, VI 60-s, COD=2-s)`. This plugin's MCP server (`operantkit-mcp`) checks, simulates, and translates that expression.

## 1. Start here: write the schedule in the DSL and check it

1. Write the schedule as one contingency-dsl expression. See [reference.md](reference.md) for the syntax and [examples.md](examples.md) for common designs.
2. Call the MCP tool `check_dsl` with it.
3. If `ok` is `false`, read `error.message`, fix the expression, and call `check_dsl` again.
4. Use the returned `canonical` string from then on (for example, `VI 60-s` becomes `VI60s`).

## 2. Do not hand-roll schedule code

Do not write your own FR/VI random series, changeover delay, or limited hold in 50 lines of ad-hoc code. VI/VR values follow the Fleshler and Hoffman (1962) progression; make them reproducible with a postfix annotation such as `VI 30-s @algorithm("fleshler-hoffman", n=12, seed=42)`. When code is needed, use the libraries (`contingency-py` runs a parsed schedule) instead of reimplementing them.

## 3. Check behavior by simulation

Call `simulate` with the expression and exactly one of `exit_reinforcers` or `exit_time` (seconds). It returns response and reinforcement counts and rates per minute, per operandum. It runs on a virtual clock, so minutes of session time finish in under a second. The same `seed` gives the same result. A concurrent schedule gets one independent Poisson responder per component. Pass `output_path` to also write an OKL v1 event log.

## 4. Round-trip with papers

- From DSL to a Method section: `dsl_to_method` with `style` `jeab` (English, JEAB style) or `jaba` (Japanese, J-ABA style).
- From a paper's Method section to DSL: `method_to_dsl`. It uses a rule-based extractor only. Read `unrecognized_fragments` yourself and fill in what it missed, then pass the result to `check_dsl`.

## 5. Records

Record real or virtual sessions as OKL v1 event logs (`session-recorder`). Summarize and analyze them with `session-analyzer` (post-reinforcement pauses, IRTs, bouts, matching, cumulative records).

## 6. Terminology

Use behavior-analytic terms: reinforcer (not "reward"), discriminative stimulus (SD, not "trigger"), and establishing operation when describing motivational variables. Do not explain behavior by inner states ("the rat feels motivated").

## 7. Install

```bash
pip install operantkit-mcp   # the MCP server used by this plugin (Python 3.11+)
pip install operantkit       # the full library set and the `operant` command
```

Each package publishes an `llms.txt`, for example:

- https://github.com/OperantKit/contingency-dsl/blob/main/llms.txt
- https://github.com/OperantKit/contingency-py/blob/main/llms.txt

## 8. What not to do

- If the MCP tools are unavailable (`operantkit-mcp` not installed), tell the user the install command above. Do not fall back to writing your own schedule implementation.
- Do not call an LLM-based extractor for Method sections from this skill. `method_to_dsl` is rule-based by design; you read the leftovers.
- Do not invent DSL syntax. If `check_dsl` rejects an expression, follow its error message or consult [reference.md](reference.md).
