# claude-plugin

:jp: [日本語版 README](README.ja.md)

A Claude Code plugin for designing, validating, and simulating reinforcement schedules with OperantKit. It bundles one skill that teaches Claude the [contingency-dsl](https://github.com/OperantKit/contingency-dsl) notation (for example `Conc(VI 30-s, VI 60-s, COD=2-s)`) and the configuration for the [operantkit-mcp](https://github.com/OperantKit/operantkit-mcp) server. This repository is both the plugin and its marketplace.

## Install

```bash
pip install operantkit-mcp
claude plugin marketplace add OperantKit/claude-plugin
claude plugin install operantkit@operantkit
```

`operantkit-mcp` must be on `PATH` (Python 3.11+). Check with `claude mcp list`: the server `plugin:operantkit:operantkit` should show `✔ Connected`.

## What is included

| Part | Path | Purpose |
|---|---|---|
| Skill `operantkit` | `skills/operantkit/SKILL.md` | Loaded when a conversation is about operant or respondent conditioning, reinforcement schedules, or Method sections. Tells Claude to write the schedule in the DSL and check it with `check_dsl` instead of hand-writing schedule code |
| DSL quick reference | `skills/operantkit/reference.md` | Atomic and compound schedules, modifiers, time units, annotations |
| Examples | `skills/operantkit/examples.md` | FR, VI, concurrent VI VI with COD, multiple, chained, DRO, and the tool calls for each |
| MCP server config | `.claude-plugin/plugin.json` (`mcpServers`) | Starts `operantkit-mcp` over stdio |

The MCP server provides `check_dsl`, `simulate`, `dsl_to_method`, and `method_to_dsl`. See the operantkit-mcp README for inputs and outputs.

## Usage

After installing, ask Claude about an experiment, for example "Design a concurrent VI 30-s VI 60-s schedule with a 2-s changeover delay and write the Method section." The skill can also be invoked directly as `/operantkit:operantkit`.

## Requirements

- Claude Code with plugin support
- Python 3.11+ and `operantkit-mcp` on `PATH`

If `operantkit-mcp` is missing, the skill still loads and only the MCP server fails to connect.

## Development

```bash
claude plugin validate --strict .
claude plugin marketplace add ./
claude plugin install operantkit@operantkit
claude plugin details operantkit
claude plugin marketplace remove operantkit
```

## License

MIT
