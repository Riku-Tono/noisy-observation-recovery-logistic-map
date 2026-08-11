# INCIDENT NOTE: false acceptance at seed 41014 / sigma 1.6e-9 / delta_3

**Nature of this document**: a **post-hoc, unaudited reference diagnostic** produced by a single script. It is not a formal third-party forensic examination, not a complete verification by an independent implementation, and not a safety evaluation of the Observer as a whole.

**Scope**: exactly **one case**, `A3 / seed 41014 / sigma 1.6e-9 / run 74 / delta_3`. Other seeds, other sigma values, other quantities, and other Phases are out of scope.

**Standing of this note**: it changes nothing in the formal Phase A–B2 results. It must also not be used as grounds for raising confidence in Phase A–B2.

---

## 1. What happened (plain summary)

In one Phase B2 run, the Observer did not abstain on `delta_3` and instead emitted `13.918719211822673`. The truth is `4.656251017651357`, so the relative error is about `1.99` (roughly 199%). In the same run, `delta_2` had abstained for want of a bracket.

Afterwards, the stored input file and the frozen Observer were used to re-execute the case exactly once, and the same input was then promoted to high precision (Decimal) to redo only the downstream computation. The results were:

- The same `13.918719211822673` was reproduced, matching the official raw record at object level.
- At Decimal 50, 100, and 200 digits alike, not a single period label changed, the candidate bracket endpoints were identical, and the decision status was unchanged. The value moved only by about `-2.66e-14` in its trailing digits.

The explanation "binary64 rounding was the main cause" is therefore **weak within the scope of this diagnostic**. Numerically, the observations are consistent with the first bracket `b_2` used by `delta_3` being accepted at a position far to the left of where it should be, with that displacement then amplified by the ratio computation.

---

## 2. The single case as a formal result

Sources: `05_PHASE_B2/results/score_records.jsonl` (`run_index: 74`), `05_PHASE_B2/results/observer_records.jsonl`

| Item | Value |
|---|---|
| run_id | `sigma_1.6e-09_seed_41014` |
| condition | `A3` |
| Observer status | `ESTIMATE` |
| estimate | `13.918719211822673` |
| truth | `4.656251017651357` |
| relative error | `1.9892544794209492` |
| Scorer classification | `WRONG` |
| `delta_2` in the same run | `ABSTAIN` (`BIFURCATION_BRACKET_UNRESOLVED`) |
| `alpha_2`, `alpha_3` in the same run | both `ESTIMATE` → `RECOVERED` |

The run's `observable_audit.b_hat_v` (the bifurcation-point candidates the Observer accepted on the anonymised coordinate `v`):

| `ell` | This run | Zero-noise A3 reference |
|---|---|---|
| 1 | `null` (unresolved) | `0.07991935483870968` |
| 2 | `0.5025` | `0.8054032258064516` |
| 3 | `0.9582258064516129` | `0.9581451612903226` |
| 4 | `0.9909677419354839` | `0.9909677419354839` |

The zero-noise reference is `03_PHASE_A/results/A3.json`.

---

## 3. The diagnostic performed

Script used: `06_INCIDENT_41014/diagnose_41014.py` (SHA-256 `E54AD11346099676F60E3F57A14BF149EA820C90D52E1E5FF98EF0079CB42ADC`, confirmed to match the file in this distribution package).

The script references absolute paths in the original environment and is a single-shot script; it cannot be re-executed from this distribution package alone, which is a README-writing excerpt. The A3 payload, the noise fields, the roughly 2 GB of B2 traces, and the pinned runtime are deliberately not bundled.

The script does only two things.

1. **Frozen replay**: reconstruct the binary64 `y64` from the stored A3 payload and B1's noise field, run the frozen Observer once, and compare against the official B2 raw output at object level.
2. **Decimal re-evaluation**: promote the same stored binary64 input to Decimal 50 / 100 / 200 digits and recompute only the period-label decision, the bracket candidates, and the downstream `delta_3` arithmetic.

The script reads neither the truth nor the Scorer. It states its own scope explicitly: `REFERENCE DIAGNOSTIC ONLY; NO FORENSIC RELIABILITY CLAIM` at the top, and `one fixed stored input; no cause or safety conclusion` at the end.

### 3.1 Provenance of the output

The numbers come from this text file:

- file: `result_41014.txt`
- bytes: `3,753`
- SHA-256: `F2EDE3AF717889347A5DA224CC20AF9DCE90C5B6AE3200CB1F65A9EAD4FDA7CA`

**This file is not a new computation. It is the standard output of the immediately preceding execution of `diagnose_41014.py`, which exited `0`, saved after the fact without re-running the script.**

Environment and input hashes at diagnostic time (as recorded in the same file):

| Item | Value |
|---|---|
| Python / NumPy | CPython `3.13.15` / NumPy `2.3.2` |
| `observer.py` SHA-256 | `A72C880213D617C9479D8F4C9DFD3E7F277169CBB236FB9C4EDFEE0B800D7EB4` |
| A3 payload SHA-256 | `14D404ACC81690174682CA5B31C758A40B465DCD81104307E7A5652C6BD04D18` |
| Noise fields SHA-256 | `0EAB212E1EC8CC001DFBEF3CF8E5FAD4A15540754109B737FC0404E1861E2B3E` |

These agree with the values sealed in B1 and B2.

---

## 4. Diagnostic results (observed facts)

### 4.1 binary64 frozen replay

| Item | Result |
|---|---|
| `official_object_match` | `True` |
| `delta_3_match` | `True` |
| `delta_3` | `ESTIMATE` / `13.918719211822673` |
| elapsed | `0.377932` s |
| Candidate endpoints (grid indices) | `ell=2: (3115, 3116, gap 0)` / `ell=3: (5940, 5942, gap 1)` / `ell=4: (6143, 6145, gap 1)` |

### 4.2 Decimal 50 / 100 / 200 digits

All three precisions gave:

| Item | Result |
|---|---|
| `labels_changed_vs_binary64` | `0` |
| label_counts | `{0: 18, 1: 495, 2: 4496, 4: 946, 8: 203, 16: 43}` (consistent with the run's `observable_audit`) |
| Candidate endpoints | identical to the binary64 replay |
| status | `ESTIMATE` |
| `estimate_difference_vs_binary64_exact` | `-2.663871964896043478955143200E-14` (identical across all three precisions) |
| elapsed | `0.840536` / `0.688308` / `0.965334` s |

Bracket values recorded at Decimal 100 / 200 digits:

```
b_2 = 0.502500000000000002220446049250313080847263336181640625
b_3 = 0.958225806451612871494916134906816296279430389404296875
b_4 = 0.99096774193548387010821443254826590418815612792968750

delta_3 = (b_3 - b_2) / (b_4 - b_3) = 13.91871921182264593805302584132463227397693395010716...
```

### 4.3 Final label

| Item | Result |
|---|---|
| `precision_paths_stable_50_100_200` | `True` |
| `changed_path_from_binary64` | `False` |
| `FINAL_LABEL` | `REPLAY_MATCH_AND_PRECISION_STABLE` |

Only `b_2` has moved far to the left of its proper position (about `0.8054` in the zero-noise case). The combination of a large numerator and a small denominator amplifies the ratio to roughly 13.9.

**Incidental observation**: the only unresolved point inside the `ell=3` candidate bracket is grid index `j=5941`, the same index that appears in `first_precursor_nodes` in all 80 rows of Phase B2. This is a juxtaposition of observed facts, not a claim of causal connection.

---

## 5. Limited interpretation

Within the scope of this diagnostic, exactly one sentence can be stated.

> **The explanation that binary64 rounding alone was the main cause is weak, and the observed computational path is consistent with the proximate mechanism of false `b_2` bracket acceptance plus downstream ratio amplification.**

"Consistent with", not "proved". It is not claimed that the root cause by which the noise realization produced that label arrangement has been established.

### Proximate mechanism at the implementation level (descriptive)

`delta_3` could reach `ESTIMATE` in the same run in which `delta_2` abstained because the Observer decides each `k` independently, and only the local conditions required for `delta_3` were satisfied (one bracket candidate each for `ell=2,3,4`, positive denominator). There was no guard requiring cross-series consistency — no coupling to whether `delta_2` succeeded, no check that the bracket spacings form a plausible series, no re-check when the ratio falls far outside an expected range.

**This is a description of the implementation, not a claim that adding such guards would improve anything. The effectiveness of any new guard is entirely untested.**

### On `b_1=null`

In run 74, `b_hat_v[1]` was unresolved, which directly explains `delta_2`'s `ABSTAIN`. `delta_3` does not use `b_1`, so it reached `ESTIMATE` on its own local conditions.

**Whether the unresolved `b_1` and the false `b_2` acceptance arose from the same noise-induced label breakdown cannot be determined from the present materials.** No common cause is asserted. This is recorded only as one instance in which cross-series consistency was not checked.

---

## 6. What this note does not show

- **The dynamical root cause of the noise**: no analysis was performed tracing back to the individual observations to determine which values created the apparent `2→4` transition at `v≈0.5025`.
- **The existence of a genuinely new bifurcation**: it is not shown that noise induced a real period doubling in the underlying map. What was observed is a failure of the Observer's **operational labelling** of noisy finite observations.
- **General causation**: it is not shown that false acceptance necessarily occurs at `sigma=1.6e-9`, nor with what probability. All that follows is that it occurred for this one noise realization (seed 41014).
- **Whether this is specific to a single realization**: whether the same kind of false acceptance recurs with other seeds or other detectors is **unverified**.
- **A defect of the Observer as a whole**: no investigation was made of whether comparable failures occur at other seeds, other sigma values, or for other target quantities.
- **Third-party auditability**: the forensic framework carrying formal guarantees ended in FAIL / HOLD and was abandoned before the production run. The tests that passed inside it are not counted toward the credibility of this diagnostic. This diagnostic is a single script, a single execution, unaudited.

---

## 7. Limitations of the diagnostic itself

- It starts from the stored binary64 input; it does not redo the generation of the observations at high precision. "High precision" here means **downstream arithmetic on promoted stored binary64 input**.
- The precision-stability decision (`precision_paths_stable`) consists of matching labels, matching candidates, matching status, and agreement of `delta_3` to roughly the first 30 digits. It is not a check of exact agreement across 50/100/200 digits.
- The correspondence between noise fields and seeds is determined by array index (`fields[seed - 41001]`); it was not cross-checked against the manifest's seed ordering. That said, a wrong correspondence would immediately break the object-level match between the frozen replay and the official B2 output, so this functions as a practical consistency check.
- Executed once only. No repeated execution, no reproduction in a different environment, no verification by a different implementation.
- The output is standard output saved after the fact, not an artifact sealed at execution time.

---

## 8. If a third party wishes to reproduce this

Passing the seed and sigma and regenerating the noise will not produce the same noise sequence under a different RNG lineage, so `13.9187` is not guaranteed. To attempt reproduction, one must be given not the seed but the following:

- the stored A3 payload (SHA-256 `14D404ACC81690174682CA5B31C758A40B465DCD81104307E7A5652C6BD04D18`)
- B1's noise fields (SHA-256 `0EAB212E1EC8CC001DFBEF3CF8E5FAD4A15540754109B737FC0404E1861E2B3E`) and the correspondence for the relevant seed
- the Observer source (SHA-256 `A72C880213D617C9479D8F4C9DFD3E7F277169CBB236FB9C4EDFEE0B800D7EB4`)
- runtime: CPython `3.13.15` / NumPy `2.3.2`
- expected value: `13.918719211822673`
