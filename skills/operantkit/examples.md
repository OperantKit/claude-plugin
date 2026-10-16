# Examples

Each example gives the expression, what `check_dsl` returns as `canonical`, and the tool calls to make.

## 1. Fixed ratio

```
FR 5            -- canonical: FR5
```

`check_dsl("FR 5")` → `simulate("FR5", exit_reinforcers=20, seed=0)` to see the response and reinforcement rates.

## 2. Variable interval with reproducible values

```
VI 60-s @algorithm("fleshler-hoffman", n=12, seed=42)
```

`check_dsl(...)` → `dsl_to_method(canonical)` for the procedure text. The same seed reproduces the same interval list.

## 3. Concurrent VI VI with a changeover delay (matching studies)

```
Conc(VI 30-s, VI 60-s, COD=2-s)     -- canonical: Conc(VI30s, VI60s, COD=2s)
```

`simulate("Conc(VI30s, VI60s, COD=2s)", exit_time=3600, seed=0)` returns responses and reinforcers per operandum (one Poisson responder per component). `dsl_to_method` writes: "Responses were reinforced under a concurrent VI 30-s VI 60-s schedule. A 2-s changeover delay was in effect."

The two virtual responders respond independently, so changeovers are frequent and the COD withholds most reinforcers (seed 0, 1 h: 9 and 4 reinforcers with `COD=2s`, 118 and 58 without). Compare both before reading a matching result from the simulation.

## 4. Multiple schedule with extinction (discrimination training)

```
Mult(FR 5, EXT, BO=5-s)             -- canonical: Mult(FR5, EXT, BO=5s)
```

Components alternate with a stimulus change; the 5-s blackout separates them.

## 5. Chained schedule

```
Chain(FR 5, FI 30-s)                -- canonical: Chain(FR5, FI30s)
```

Completing the FR 5 initial link produces the FI 30-s terminal link with a stimulus change. Use `Tand(...)` for the same sequence without a stimulus change.

## 6. Differential reinforcement of other behavior

```
DRO 10-s                            -- canonical: DRO10s
```

`dsl_to_method("DRO10s")` writes: "Responses were reinforced under a differential-reinforcement-of-other-behavior (DRO) 10-s schedule."
## From a paper to the DSL

1. `method_to_dsl("<Method section text>")`
2. Read `unrecognized_fragments`; add what the extractor missed.
3. `check_dsl(<dsl>)` and use `canonical`.
