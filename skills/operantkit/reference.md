# contingency-dsl quick reference

Full syntax guide: https://github.com/OperantKit/contingency-dsl/blob/main/docs/en/syntax-guide.md

## Atomic schedules

The first letter is the distribution, the second the domain.

|              | R (ratio: responses) | I (interval: time + response) | T (time only) |
|---|---|---|---|
| F (fixed)    | `FR 5`  | `FI 30-s` | `FT 10-s` |
| V (variable) | `VR 20` | `VI 60-s` | `VT 15-s` |
| R (random)   | `RR 10` | `RI 45-s` | `RT 20-s` |

- `CRF`: continuous reinforcement (every response).
- `EXT`: extinction (no response is reinforced).

## Time units

All of these are the same value: `VI 60-s`, `VI 60 s`, `VI 60-sec`, `VI 60s`, `VI60s`, `VI 60`. Minutes: `FI 5-min`. The canonical form from `check_dsl` is compact (`VI60s`).

## Compound schedules

| Form | Meaning |
|---|---|
| `Conc(S1, S2, COD=2-s)` | Concurrent: both available at once; `COD` is the changeover delay |
| `Alt(S1, S2)` | Alternative: whichever is satisfied first reinforces |
| `Conj(S1, S2)` | Conjunctive: both must be satisfied |
| `Chain(S1, S2)` | Chained: sequential links with a stimulus change |
| `Tand(S1, S2)` | Tandem: sequential links without a stimulus change |
| `Mult(S1, S2, BO=5-s)` | Multiple: alternating components with a stimulus change; `BO` is a blackout |
| `Mix(S1, S2)` | Mixed: alternating components without a stimulus change |

Combinators nest: `Conc(Chain(FR 5, VI 60-s), Alt(FR 10, FT 30-s), COD=2-s)`.

## Modifiers

| Form | Meaning |
|---|---|
| `DRL 5-s` | Reinforce a response only if the IRT is at least 5 s |
| `DRH 2-s` | Reinforce a response only if the IRT is at most 2 s |
| `DRO 10-s` | Reinforce 10 s without the target response |
| `FI 30-s LH 10-s` | Limited hold: the reinforcer stays available for 10 s |
| `Lag 1` | Reinforce a response that differs from the previous one |
| `PR(linear, start=1, increment=5)` | Progressive ratio (also `hodos`, `exponential`, `geometric`) |
| `FI 120-s(FR 10)` | Second-order: FR 10 units under an FI 120-s overall schedule (not a function call) |

## Program-level features

```
let baseline = VI 60-s        -- named binding (expanded at parse time)
Mult(baseline, EXT)
```

```
COD = 2-s                     -- program-level default for every Conc
Conc(VI 30-s, VI 60-s)
```

## Annotations

Annotations add metadata after the expression, for example reproducible VI values:

```
VI 30-s @algorithm("fleshler-hoffman", n=12, seed=42)
```

Other annotations describe the reinforcer, discriminative stimuli, apparatus, and subjects: https://github.com/OperantKit/contingency-dsl/blob/main/docs/en/annotations.md

## Beyond this sheet

Respondent (Pavlovian) primitives such as `Pair.ForwardDelay(tone, shock, isi=10-s, cs_duration=15-s)`, composed operant-respondent procedures, and multi-phase experiments (`phase`, `progressive`, `shaping`) are covered in the syntax guide.
