# claude-plugin

:jp: [日本語版 README](README.ja.md)

This is a plugin for Claude Code that lets Claude design, check, and simulate reinforcement schedules with [OperantKit](https://github.com/OperantKit) when you talk to it about an operant or respondent conditioning experiment. It installs two things: a skill that teaches Claude the [contingency-dsl](https://github.com/OperantKit/contingency-dsl) notation (for example `Conc(VI 30-s, VI 60-s, COD=2-s)`) and tells it to check schedules with OperantKit instead of writing its own schedule code, and the configuration that starts the [operantkit-mcp](https://github.com/OperantKit/operantkit-mcp) server.

- **Claude writes the schedule as one expression and has it checked,** instead of improvising FR or VI code in each answer.
- **Simulations and Method-section paragraphs come from OperantKit,** so the numbers and wording are reproducible.
- **One install** gives you both the instructions for Claude and the server; this repository is both the plugin and its marketplace.

## Installation

1. Install `operantkit-mcp` and its dependencies as described in the [operantkit-mcp README](https://github.com/OperantKit/operantkit-mcp#installation) (Python 3.11 or newer; the packages are not on PyPI yet). The `operantkit-mcp` command must be on your `PATH` when Claude Code starts.
2. Add the marketplace and install the plugin:

```bash
claude plugin marketplace add OperantKit/claude-plugin
claude plugin install operantkit@operantkit
claude mcp list
# plugin:operantkit:operantkit: operantkit-mcp  - ✔ Connected
```

If `claude mcp list` shows `✘ Failed to connect — ENOENT: Executable not found in $PATH`, the skill is installed but Claude Code cannot find `operantkit-mcp`; activate the environment you installed it into before starting Claude Code.

## Usage

Ask Claude about an experiment in your own words, for example *"Design a concurrent VI 30-s VI 60-s schedule with a 2-s changeover delay and write the Method section."* The skill is loaded when the conversation is about operant or respondent conditioning, reinforcement schedules, or Method sections; you can also call it directly with `/operantkit:operantkit`.

With the skill, Claude is told to:

1. write the schedule as one contingency-dsl expression and check it with `check_dsl`, then use the normalized form;
2. not write its own random FR / VI series, changeover delay, or limited hold;
3. check what the schedule delivers with `simulate`, and translate with `dsl_to_method` / `method_to_dsl`;
4. record sessions as OKL (OperantKit Log) event logs and summarize or analyze them (post-reinforcement pauses, inter-response times, bouts, matching, cumulative records);
5. use behavior-analytic terms (reinforcer, not "reward"; discriminative stimulus, not "trigger").

See the [operantkit-mcp README](https://github.com/OperantKit/operantkit-mcp#tools) for the inputs and outputs of each tool.

## What is included

| Part | Path | Purpose |
|---|---|---|
| Skill `operantkit` | `skills/operantkit/SKILL.md` | What Claude reads when the skill is loaded: the steps above |
| DSL quick reference | `skills/operantkit/reference.md` | Simple and compound schedules, modifiers, time units, annotations |
| Examples | `skills/operantkit/examples.md` | FR, VI, concurrent VI VI with a changeover delay, multiple, chained, DRO, and the tool calls for each |
| MCP server configuration | `.mcp.json` | Starts `operantkit-mcp` over standard input and output |

`claude plugin details operantkit` reports that the skill adds about 200 tokens to every session and about 850 more each time it is used.

## Limitations

- **The plugin does not include the server.** `operantkit-mcp` is installed separately with pip and is found through `PATH`; the plugin configuration cannot point at a specific virtual environment.
- **The simulated subject does not learn.** Simulations show what the schedule delivers to a subject that responds at a constant rate, not how an animal would behave. See the operantkit-mcp README for the other limits of the tools.
- **Claude Code only.** Other assistants can use operantkit-mcp directly but not this plugin.

## Development

```bash
claude plugin validate --strict .
```

## License

MIT. See [LICENSE](LICENSE).
