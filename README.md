# Recovering finite-level scaling quantities from finite observations: Phase A / B1 / B2 research record

**Updated**: 2026-08-13 (including a non-confirmatory Phase C follow-up, an exploratory Phase D0 fresh-seed recurrence probe, and a separately identified post-hoc reconstruction of the D0 candidate sets)

**Nature of this document**: the primary research record is a small, fixed-design (toy) study comprising Phases A/B1/B2, using one logistic-map payload and one fixed Observer. A later Phase C exercise used a different map-derived curve and two separately developed Observers, but its execution history and unquantified Observer independence give it **limited evidential weight**. Phase C is retained only as a non-confirmatory, hypothesis-generating follow-up and is not used to support the primary conclusions. Phase D0 is a still narrower, pre-specified exploratory probe asking only whether the B2 `delta_3` false-acceptance outcome recurred among 40 fixed fresh seeds at the same sigma. It found no such recurrence, which is a fixed-sample observation rather than evidence of safety or a population error probability. A later candidate-set reconstruction regenerated those same 40 inputs post hoc; it is explicitly separate from the original sealed D0 outputs and supplies only a descriptive computational decomposition of the recorded outcomes. All measurements are operational and restricted to the stated payloads, implementations, noise fields, and fixed grids. Nothing here is a claim about general theory, universality, population performance, or safety.

---

## 1. Overview (plain summary)

The period-doubling cascade of the logistic map `f_u(x)=u x(1-x)` carries finite-level quantities: the ratio of bifurcation-parameter gaps `delta_k`, and the state-space scale ratio of superstable orbits `alpha_k`. Phases A/B measured **whether a fixed procedure (the Observer), told neither the map's formula nor the physical parameter names nor any true value, can recover these finite-level quantities from finite, anonymised observations alone**.

Three primary phases were run.

- **Phase A (ideal conditions)**: no noise. All 4 conditions × 2 quantities × 2 levels = 16 decisions were `RECOVERED` (relative error within 5%). No wrong answers, no abstentions.
- **Phase B1 (coarse noise grid)**: the Observer was left completely unchanged; iid Gaussian noise was added to the observations only. 141 runs / 564 decisions. `RECOVERED=244 / WRONG=0 / ABSTAIN=320`. All four targets lost recovery inside the same interval `1e-9 < sigma <= 3e-9`, and within that coarse grid they lost it **by abstaining, not by answering wrongly**.
- **Phase B2 (refined grid and first-failure attribution)**: that interval was refined into 9 levels. 181 runs / 724 decisions. `RECOVERED=387 / WRONG=1 / ABSTAIN=336`. The direct cause of abstention was not a shared period-recognition layer: the delta side failed at bracket uniqueness, the alpha side at superstable geometry. And **one false acceptance appeared that Phase B1 had not seen**.

A later **exploratory Phase C follow-up** is documented separately in §8. Its numerical summaries are preserved for transparency, but it is not treated as a fourth primary phase or as confirmatory evidence. If its observed tendency reflects a real phenomenon, it is qualitatively consistent with the observation-space interpretation in `DISCUSSION_NOTE_OBSERVATION_SPACE_STRUCTURE_EN.md`; that consistency remains a hypothesis, not a result of the primary study.

An **exploratory Phase D0 fresh-seed recurrence probe** is documented in §9. At the exact B2 incident condition (`A3`, `sigma=1.6e-9`), the unchanged Observer produced `delta_3: ABSTAIN=39 / RECOVERED=1 / WRONG=0` across the pre-fixed unused seeds 41021–41060. Thus no second 41014-type false acceptance was observed in this fixed set. A separate post-hoc reconstruction found that all 40 `ell=2` cells retained the zero-reference-associated candidate: 39 also contained one or more alternative candidates and were nonunique, matching all 39 `ABSTAIN` outcomes, while seed 41042 retained the exact reference-only unique tuple and was `RECOVERED`. No false-only or unique false-only `ell=2` cell was observed. These fixed-set observations neither establish non-recurrence nor estimate a population probability.

The most important primary results are not "Feigenbaum-like quantities were recovered", but these two points.

1. The fixed logistic-map Observer's breakdown could not be reduced to a single shared mechanism (33 of the 80 attribution rows are simultaneous failures at the same depth, i.e. TIE).
2. The Observer's `ESTIMATE` label is not a guarantee that true finite-level structure was recovered. The "loss by abstention only" property observed on the B1 coarse grid did not extend unchanged to the B2 refined grid.

In Phase B2, one false acceptance was observed for `delta_3` at seed 41014 and sigma 1.6e-9. For the limited replay / Decimal diagnostic on the stored input, see `INCIDENT_41014_NOTE.md`; for the fixed-runtime reproduction of the local acceptance path for that same stored input, see §7.1. The exploratory Phase C follow-up is not a direct replication of that incident: its target, signal, noise scale, scoring unit, and Observers differ (§8). Phase D0 instead reused the A3 task, frozen Observer and exact incident sigma with 40 fixed fresh seeds, but remained a one-condition descriptive recurrence probe (§9).

---

## 2. Research question and scope of claims

### 2.1 Research question

The primary study is restricted to two questions.

1. **Can the finite-level quantities `delta_2, delta_3, alpha_2, alpha_3` be recovered operationally from finite, anonymised, passive observations?**
2. **When Gaussian noise is added to the observations, where does the fixed Observer stop (ABSTAIN) and where does it answer wrongly (WRONG)?**

The fixed primary question statements are registered in `03_PHASE_A/PLAN_PHASE_A_FINITE_LEVEL_RECOVERY.md` §1, `04_PHASE_B1/PLAN_PHASE_B1_GAUSSIAN_BREAKDOWN.md` §1, and `05_PHASE_B2/PLAN_PHASE_B2_FIRST_FAILURE_ATTRIBUTION.md` §1. Phase C asked an additional cross-Observer question, but that question and its results are retained only in the exploratory follow-up (§8), not added to the primary claims.

### 2.2 What is not claimed

- High-precision estimation of the asymptotic Feigenbaum constants `delta_infinity`, `alpha_infinity`. No asymptotic constant and no extrapolation method was used in either estimation or scoring.
- Discovery of universality, or generalisation to unseen maps or other systems. Phases A/B concern only the logistic map's principal cascade; Phase C adds one deliberately simple second map-derived curve, not a representative system family.
- An information-theoretic limit of recoverability under Gaussian noise.
- Population error rates, error probabilities for untested seeds, or the safety of the Observer as a whole.
- Any performance claim that "a better ABSTAIN rule can be built". Rule design was explicitly outside B2's scope.
- That Phase C is a statistical replication of A/B1/B2, that its sigma values share a common physical scale with B1/B2, or that two implementations constitute broad algorithmic independence.
- That Phase C identified a winning Observer or supplied confirmatory evidence. Its own fixed experiment-level result was `INCONCLUSIVE`, and this document assigns the exercise only exploratory standing.
- That Phase D0's `WRONG=0` establishes non-recurrence, safety, a population error rate, or an upper bound applicable beyond its 40 fixed seeds and one sigma.
- That the post-hoc D0 candidate-set reconstruction was part of the original seal, proves a low recurrence probability, establishes the cause of seed 41014, or validates a guard.

---

## 3. Target quantities and experimental roles (Generator / Observer / Scorer)

### 3.1 Target quantities

In Phases A/B, only the following four **finite-level** quantities are scored.

- `delta_k = (b_k - b_(k-1)) / (b_(k+1) - b_k)` for `k=2,3`, where `b_k` is the parameter at which period `2^(k-1)` bifurcates to `2^k`.
- `alpha_k = d_k / d_(k+1)` for `k=2,3`, where `s_k` is the superstable parameter of period `2^k`, `d_k = f_(s_k)^(2^(k-1))(c) - c`, and no absolute value is taken.

The truth held by the Scorer (`03_PHASE_A/results/truth.json`; decimal working precision 100 digits, residual `<1e-60`, bracket width `<1e-50`):

| Quantity | Truth (leading digits) |
|---|---|
| `delta_2` | `4.7514462181782065490...` |
| `delta_3` | `4.6562510176513567607...` |
| `alpha_2` | `-2.5318376593375099668...` |
| `alpha_3` | `-2.5087181990346510327...` |

`b_k` and `s_k` are distinct sequences, solved and cross-checked independently (see the `verification` block of the same file).

Phase C instead scores one observation-coordinate destabilisation location, with private truth `37/71`. It neither re-estimates the four quantities above nor adds another finite-level scaling quantity.

### 3.2 Separation of the three roles

| Role | What it knows | What it emits |
|---|---|---|
| **Generator** | the map formula, the physical parameter `u`, the initial state, burn-in, all design values | only an anonymised payload (affine coordinate `v in [0,1]`, observation series `y`, `N`, `K`) |
| **Observer** | the payload only. It is never given the map's name or formula, `c`, `b_k`, `s_k`, the truth, or any known constant | per decision, **only** `ESTIMATE` (with a finite value) or `ABSTAIN` (with a reason code) |
| **Scorer** | holds the truth privately; scores only after every Observer decision is final | `RECOVERED` / `WRONG` / `ABSTAIN` |

**Do not conflate these two label layers.** `ESTIMATE / ABSTAIN` is the Observer's raw output; `RECOVERED / WRONG / ABSTAIN` is the classification assigned by the Scorer after the truth is opened. `ESTIMATE` does not mean "was correct" — §7 is the concrete counterexample.

The Observer's ABSTAIN reason codes are fixed at five: `PERIOD_LEVEL_UNRESOLVED`, `BIFURCATION_BRACKET_UNRESOLVED`, `CRITICAL_REGION_UNOBSERVED`, `SUPERSTABLE_GEOMETRY_UNRESOLVED`, `BRANCH_OR_SIGN_UNRESOLVED` (`03_PHASE_A/PLAN_...md` §6).

The exploratory Phase C follow-up uses the same separation principle but a different task and contract. Its Generator privately defines a second map-derived scalar observation curve; each Observer sees only an opaque target ID and 401 ordered `(coordinate, observed_value)` pairs, and must return one destabilisation-location `ESTIMATE` or `ABSTAIN`. The two Observers own different reason-code vocabularies and diagnostics, so Phase C compares only final decision status and estimated location. Their internal diagnostic terms are not treated as semantically commensurate. None of this changes the scope of the primary A/B study.

---

## 4. Phase A: finite-level recovery under ideal conditions

### 4.1 Conditions

- Map `f_u(x)=u x(1-x)`, initial state `x_0=0.123456789`, burn-in `B=32768`.
- Physical window `u in [2.9500, 3.5700]`. Before being handed to the Observer, this is transformed by the positive affine map `v=(u-2.9500)/0.6200`. The Observer sees only `v in [0,1]`.
- Exactly four conditions: `A1=(N=64, Δu=0.0010, K=621)`, `A2=(1024, 0.0010, 621)`, `A3=(64, 0.0001, 6201)`, `A4=(1024, 0.0001, 6201)`.
- No repeated trials, no randomness, no bootstrap. 4 deterministic runs, 16 decision records.

### 4.2 Nature of the decision threshold

There is exactly one classification threshold, `relative_error <= 0.05`. This is **not a statistically derived confidence interval; it is a pre-registered operational (engineering) threshold** for triaging minimal viability. `03_PHASE_A/results/phase_a_score.json` states this explicitly as `"tolerance_is_nonstatistical_and_arbitrary": true`. No confidence interval, coverage, FPR, or multiple-comparison correction was computed at any point.

### 4.3 Results

Source: `03_PHASE_A/results/phase_a_score.json`

| Classification | Count |
|---|---|
| `RECOVERED` | **16** |
| `WRONG` | 0 |
| `ABSTAIN` | 0 |

On the finest grid (A3/A4) the relative errors were `delta_2: 3.60e-4`, `delta_3: 5.77e-4`, `alpha_2: 2.42e-6`, `alpha_3: 7.30e-5`. The Observer's raw output for A3 is `03_PHASE_A/results/A3.json`.

**Observed fact**: under the four noise-free conditions, all four quantities were recovered within the 5% threshold from anonymised observations alone.
**Interpretation (limited)**: the estimation procedure meets minimal viability. This covers four conditions and deterministic runs only; it is not a statistical performance evaluation.

---

## 5. Phase B1: loss of recovery on a coarse noise grid

### 5.1 Design

- Fixed to condition **A3 only**. Observer, Scorer, truth, public contract, and thresholds are all frozen by SHA-256 (`04_PHASE_B1/PLAN_...md` §2).
- Exactly one thing is broken: `y_obs[j,t] = y0[j,t] + sigma * Z_i[j,t]`. No clipping, rounding, rescaling, or imputation. `v` and all metadata remain bitwise identical.
- Eight sigma levels: `0, 1e-10, 3e-10, 1e-9, 3e-9, 1e-8, 3e-8, 1e-7`. Twenty seeds (`41001..41020`) at each of the seven positive levels.
- **Paired design**: one standard noise field `Z_i` is drawn per seed and reused at every positive sigma, so comparisons across strengths are paired.
- `sigma=0` is run once as an integrity control. Planned runs `1 + 7*20 = 141`; planned decisions `564`.

### 5.2 Results

Sources: `04_PHASE_B1/results/summary.csv` (32 rows), `04_PHASE_B1/results/breakdown_brackets.json`, `04_PHASE_B1/results/run_provenance.json` (`status: COMPLETE`, `observer_runs: 141`, `decision_records: 564`)

| Classification | Count |
|---|---|
| `RECOVERED` | **244** |
| `WRONG` | **0** |
| `ABSTAIN` | **320** |

- `sigma <= 1e-9`: `RECOVERED` for all 20 seeds and all four targets.
- `sigma >= 3e-9`: `ABSTAIN` for all 20 seeds and all four targets.
- The operational recovery-loss bracket for all four targets is **`1e-9 < sigma <= 3e-9`**, with `status: BRACKETED`, `loss_mode: ABSTAIN_ONLY`, `first_wrong_sigma: null`.

Reason-code breakdown at `sigma=3e-9`:

| Target | Reason codes (out of 20) |
|---|---|
| `delta_2`, `delta_3` | `BIFURCATION_BRACKET_UNRESOLVED` 20 |
| `alpha_2` | `SUPERSTABLE_GEOMETRY_UNRESOLVED` 20 |
| `alpha_3` | `SUPERSTABLE_GEOMETRY_UNRESOLVED` 19, `CRITICAL_REGION_UNOBSERVED` 1 |

At `sigma >= 1e-8` all four targets fall to `PERIOD_LEVEL_UNRESOLVED`.

### 5.3 Limits on how to read this

**Observed fact**: on this coarse grid, recovery was lost without any wrong answers — by abstention only (`WRONG=0`).

**Interpretation (limited)**: within this coarse grid, the fixed Observer behaved so as to abstain rather than return a plausible-looking wrong answer when information was insufficient.

**Do not generalise**: `WRONG=0` is not a safety guarantee. The bracket is an **operational boundary** for a fixed grid, fixed 20 seeds, and a fixed Observer; it does not mean "above this noise level estimation is mathematically impossible". Indeed, the refined grid produced `WRONG=1` (§6, §7).

---

## 6. Phase B2: refined grid and first-failure attribution

### 6.1 Question and design

B2 asks: when all four targets lost recovery inside the same interval in B1, was it because a shared upstream period-recognition layer broke first, because the delta-side bracket and the alpha-side geometry broke separately, or because the transition is a per-seed mixture?

- **The Observer, its decision rules, thresholds, reason priority, and the 5% scoring criterion were all left unchanged.** No new estimator and no new ABSTAIN rule was created.
- Sigma was refined into 9 levels inside B1's loss interval: `1.0e-9, 1.2e-9, 1.4e-9, 1.6e-9, 1.8e-9, 2.0e-9, 2.3e-9, 2.6e-9, 3.0e-9`. Both endpoints serve as reproduction controls against B1.
- B1's 20 paired noise fields and their seed correspondence were reused read-only, unchanged.
- Including one `sigma=0` zero-noise reference: Observer runs `181`, decisions `724`, positive traces `180`.
- The diagnostic tracer runs as a separate process and module from the frozen Observer and reads only public inputs. **No tracer value flows back into the Observer's decisions or into the Scorer** (`observer_use_prohibited=true` and `diagnostic_only=true` are mandatory fields on every artifact).

### 6.2 Decision results

Sources: `05_PHASE_B2/results/decision_summary.csv` (40 rows), `05_PHASE_B2/results/PHASE_B2_FULL_RUN_VERIFICATION_V1.json` (`status: PASS`)

| Layer | Breakdown |
|---|---|
| **Observer raw** | `ESTIMATE` **388** / `ABSTAIN` **336** (724 total) |
| **Scorer (after truth opened)** | `RECOVERED` **387** / `WRONG` **1** / `ABSTAIN` **336** |

### 6.3 First-ABSTAIN thresholds

Sources: `05_PHASE_B2/results/transition_patterns.json`, `PHASE_B2_FULL_RUN_VERIFICATION_V1.json`

| Target | First any ABSTAIN | First majority ABSTAIN (`n>=11`) | First all-seed ABSTAIN (`n=20`) | First WRONG |
|---|---|---|---|---|
| `delta_2` | `1.4e-9` | `1.6e-9` | `1.8e-9` | null |
| `delta_3` | `1.4e-9` | `1.6e-9` | `1.8e-9` | **`1.6e-9`** |
| `alpha_2` | `1.8e-9` | `2.6e-9` | `3.0e-9` | null |
| `alpha_3` | `1.8e-9` | `2.6e-9` | `3.0e-9` | null |

The ordering in which the delta side moves to abstention at lower sigma than the alpha side reproduces across nearly all 20 seeds (`onset_order_by_seed`: in 18 seeds `delta_2` and `delta_3` lead as a tie; only seeds 41014 and 41017 split the order within delta).

These thresholds are **operational first failures on a pre-registered grid**, not true thresholds in continuous sigma. Boundary precision extends only to the grid spacing.

### 6.4 The 80-row first-failure attribution

The 20 seeds × 4 targets = 80 rows (`05_PHASE_B2/results/first_failure_by_seed.csv`), mapped to blocking layers:

| Blocking layer | Count |
|---|---|
| `DELTA_BRACKET` | **18** |
| `ALPHA_GEOMETRY` | **29** |
| `MIXED/TIE` | **33** |
| `PERIOD_COMMON` | **0** |

(`MIXED/TIE` is the human-readable display name; `PHASE_B2_FULL_RUN_VERIFICATION_V1.json` uses the JSON-safe key `MIXED_OR_TIE` for the same 33 rows.)

- **Handling of TIE**: when several blocking nodes fail simultaneously at the same minimal DAG depth, no single one is picked arbitrarily. The row is marked `TIE` and **every applicable canonical node ID is retained as an ascending array**. 33 of the 80 rows have `blocking_tie=True` (for example, seed 41014's `delta_3` ties across `D_BRACKET_UNIQUE[k=3,ell=2]`, `[ell=3]`, and `[ell=4]`).
- Seeds where all four targets share the same first-abstain sigma: **0**.
- Non-monotone transitions, in decisions and in nodes alike: **0**.
- Excluding ties, the single blocking nodes are confined to `A_MINIMUM_COUNT_EQ1[...]` (alpha side, 29 total) and `D_BRACKET_UNIQUE[...]` (delta side, 18 total). Not one period-layer node was ever the first blocker.

### 6.5 Distinguishing precursor from blocker

In all 80 rows `first_precursor_sigma = 1e-9` (the **smallest positive sigma** in the B2 grid), and `first_precursor_nodes` is a two-way tie per target between `PR_LABEL_MATCH[...,j=5941]` and `PR_PERIOD_ACCEPT[...,p=8,j=5941]`.

**Observed fact**: period-layer predicates that PASSed in the zero-noise reference were already FAILing at grid point `j=5941` well before the official ABSTAIN. **The precursor was already detected at `1e-9`, the smallest positive sigma measured in B2, so the true onset is left-censored at `<= 1e-9`.** It cannot be written as "first occurred at `1e-9`".

Note also that all decisions being `RECOVERED` at B1's `1e-10` / `3e-10` does not imply that these tracer nodes PASSed there. B1 did not record the same traces (tracing was introduced in B2). No additional run was performed to fill that gap.

**Interpretation (limited)**: a shared precursor was visible early. However, **as the blocking cause that directly triggered the first ABSTAIN, the shared period layer was not supported** (`PERIOD_COMMON=0`; `H_U_common_upstream.first_blocking_rows=0`). The blocking node was predominantly bracket uniqueness on the delta side, and the uniqueness of the superstable-geometry minimum on the alpha side.

**Caution**: a precursor only means "degraded earlier"; it is not a causal cause. No extrapolation is made outside the censored interval.

### 6.6 B1's `WRONG=0` and B2's `WRONG=1` are not a contradiction

The two **did not measure the same set of sigma values**.

- B1's positive grid is `1e-10, 3e-10, 1e-9, 3e-9, 1e-8, 3e-8, 1e-7`; there is no measurement point between `1e-9` and `3e-9`.
- B2 filled that gap with seven levels. The wrong answer appeared at `sigma=1.6e-9` — **a level B1 never measured**.
- At the overlapping endpoints (`1e-9`, `3e-9`), the 40 shared runs were verified to match B1 at object level (`endpoint_matches: 41`, including the zero-noise control).

The correct statement is therefore: "B1's `ABSTAIN-only loss` is an observation within B1's coarse grid and cannot be extended to the whole refined grid." B1's result is not wrong; its **resolution covers a different range**.

---

## 7. The single false acceptance

Sources: `05_PHASE_B2/results/score_records.jsonl` (`run_index: 74`), `05_PHASE_B2/results/observer_records.jsonl`

| Item | Value |
|---|---|
| run_id | `sigma_1.6e-09_seed_41014` |
| run_index | 74 |
| Target | `delta_3` |
| Observer status | `ESTIMATE` |
| estimate | `13.918719211822673` |
| truth | `4.656251017651357` |
| signed / absolute error | `9.262468194171316` |
| relative error | `1.9892544794209492` |
| Scorer classification | `WRONG` |

In the same run, `delta_2` abstained with `BIFURCATION_BRACKET_UNRESOLVED`. According to the Observer's `observable_audit`, this run's `b_hat_v` was `1: null, 2: 0.5025, 3: 0.9582258064516129, 4: 0.9909677419354839` (against zero-noise A3's `1: 0.0799..., 2: 0.8054..., 3: 0.9581..., 4: 0.9910...`).

In Phase B2, one false acceptance was observed for `delta_3` at seed 41014 and sigma 1.6e-9. For the limited replay / Decimal diagnostic on the stored input (output record: `result_41014.txt`), see `INCIDENT_41014_NOTE.md`.

**What this one case shows (limited)**: the fixed Observer's `ESTIMATE` label does not mean that true finite-level structure was safely recovered.
**What it does not show**: the error probability at this sigma, reproducibility at other seeds, a defect of the Observer as a whole, or that noise created a genuinely new bifurcation.

### 7.1 Fixed-runtime reproduction of the local acceptance path

On 2026-08-12 the local stored-data path

```
observed value pair -> period residual -> threshold decision -> period label -> bracket candidate -> delta_3
```

was re-executed once for this same stored input in the original fixed runtime (CPython `3.13.15`, NumPy `2.3.2`, frozen `observer.py` SHA-256 `A72C880213D617C9479D8F4C9DFD3E7F277169CBB236FB9C4EDFEE0B800D7EB4`). The incident input was reconstructed from the existing A3 payload and the existing seed-41014 noise slice; the truth and the Scorer were neither read nor used; no new seed and no new simulation were introduced. Source: `SEED_41014_FIXED_RUNTIME_LOCAL_PATH_REPRODUCTION.txt`.

This is not a new Phase, not a new simulation, not a formal third-party forensic examination, and not the discovery of a general failure mechanism.

All checks in that execution reported `True`, and the final status was `PASS`: the frozen Observer output matched the official raw Observer record; the frozen incident labels matched the stored B2 trace; the frozen zero-noise labels matched the stored zero reference; a small independent residual/label calculation matched the frozen Observer; independently enumerated bracket candidates matched the stored and frozen path; and the independent residual arrays matched the stored trace arrays.

Exactly four of the 6201 grid points changed period label relative to the zero-noise reference:

| `j` | `v` | Label change |
|---|---|---|
| 126 | `0.020322580645161289` | `1 -> 2` |
| 3116 | `0.50258064516129031` | `2 -> 4` |
| 4994 | `0.80548387096774199` | `4 -> 8` |
| 5941 | `0.95822580645161293` | `8 -> 0` |

The threshold was `tau = 1.00000000000000002e-08` for both the zero-noise and the incident array.

At the two points that govern the `b_2` candidate set:

| Point | Period that failed | residual − tau | Period that passed | Maximum-residual pair | Zero-noise difference | Noise difference |
|---|---|---|---|---|---|---|
| `j=3116` | 2 (`1.09344222565255222e-8`) | `+9.34422256525522034e-10` | 4 (`6.92810875335680976e-9`) | `t=31`, `t+2=33` | `0` | `-1.09344221885107072e-8` |
| `j=4994` | 4 (`1.01635164173607961e-8`) | `+1.63516417360795931e-10` | 8 (`9.49821643558834694e-9`) | `t=16`, `t+4=20` | `+1.61993712888275354e-9` | `+8.54357929372000360e-9` |

At `j=3116` the zero-noise contribution to the maximum pair is exactly `0`, so the period-2 threshold crossing for that pair is essentially the noise difference alone. At `j=4994` the observed signed difference is `+1.01635164173607961e-8`, of which the noise difference accounts for roughly 84.1%.

The `b_2` candidate set moved as follows (a bookkeeping reconstruction from the two stored label arrays, not a separately simulated sequence):

| Stage | `ell=2` candidates |
|---|---|
| Zero-noise baseline | `(4993, 4994, gap=0)` |
| After applying the stored `j=3116` label change | `(3115, 3116, gap=0)`, `(4993, 4994, gap=0)` |
| After additionally applying the stored `j=4994` label change | `(3115, 3116, gap=0)` |

For this one fixed stored input, the false `b_2` candidate was created at `j=3116`, the zero-noise `b_2` candidate was removed at `j=4994`, and the false candidate thereby became unique and was accepted. The same local path was reproduced in the original fixed runtime and matched the stored trace and the official raw Observer record. The resulting brackets `b_2=0.5025`, `b_3=0.9582258064516129`, `b_4=0.9909677419354839` give `delta_3=13.918719211822673`, and an independent calculation from those reproduced bracket values returned the same number.

**This is a local reproducible path for one fixed stored input. It is not a root-cause proof, not a general failure mechanism, not a frequency, and not an Observer-wide safety conclusion.**

Two ancillary observations, recorded without causal claims: `j=126` added a false `1 -> 2` candidate, leaving two `b_1` candidates, which is consistent with `delta_2` abstaining; and `j=5941` turned `8 -> 0`, changing the `b_3` bracket from gap 0 to gap 1. `j=5941` is not described here as a cause of the `13.9187` estimate.

### 7.2 Read-only neighbourhood and fixed-20-seed candidate-set comparison

After the incident path had been reproduced, a separate lightweight comparison was made from the already sealed zero-noise and B2 trace arrays. It did not generate observations, rerun the Observer, change any stored artifact, add seeds, or add sigma values. The comparison used `margin = residual - tau`, so `margin <= 0` is PASS, and retained the Observer's search order `p=1,2,4,8,16`. The term **critical-margin predicate** below means the predicate with minimum `|margin|` among those examined through the first PASS (or among all five periods if there is no PASS); it is not necessarily the first PASS itself.

For seed 41014, the 6,201 grid points were partitioned mechanically as follows:

| Class | Operational definition | Count |
|---|---|---:|
| A | Final period label changed | 4 |
| B | Label unchanged despite a sign change at or before the first PASS | 0 |
| C | All period-predicate PASS/FAIL signs were unchanged | 6,191 |
| D | Label unchanged; only predicates after the first PASS changed sign | 6 |

`B=0` is a consequence of this first-PASS label rule, not a newly observed robustness property: changing a sign at or before the first PASS necessarily changes where the first PASS occurs. Within C, 29 points had `|critical margin| <= 1e-9` in the incident trace. These are **near-threshold non-crossings**, not 29 observed label-flip events. The six D points were `j=419, 498, 501, 1242, 2329, 3480`; they account for the period-predicate changes hidden behind an earlier PASS.

The same calculation was then applied at the same stored level, `sigma=1.6e-9`, to all 20 fixed B2 noise fields:

| Seed | Label changes A | C points within `1e-9` | D points | All predicate crossings | Maximum critical-margin movement |
|---:|---:|---:|---:|---:|---:|
| 41001 | 4 | 27 | 15 | 21 | `1.085e-8` |
| 41002 | 5 | 17 | 12 | 17 | `1.063e-8` |
| 41003 | 6 | 16 | 11 | 17 | `1.146e-8` |
| 41004 | 7 | 14 | 12 | 19 | `1.053e-8` |
| 41005 | 3 | 17 | 11 | 16 | `1.051e-8` |
| 41006 | 7 | 20 | 12 | 20 | `1.112e-8` |
| 41007 | 8 | 18 | 7 | 20 | `1.131e-8` |
| 41008 | 3 | 23 | 11 | 15 | `1.147e-8` |
| 41009 | 6 | 23 | 14 | 21 | `1.045e-8` |
| 41010 | 6 | 23 | 10 | 16 | `1.097e-8` |
| 41011 | 4 | 24 | 11 | 17 | `1.068e-8` |
| 41012 | 6 | 25 | 11 | 21 | `1.093e-8` |
| 41013 | 7 | 24 | 18 | 26 | `1.075e-8` |
| **41014** | **4** | **29** | **6** | **11** | **`1.093e-8`** |
| 41015 | 6 | 14 | 15 | 21 | `1.076e-8` |
| 41016 | 4 | 22 | 12 | 18 | `1.091e-8` |
| 41017 | 2 | 20 | 13 | 15 | `1.003e-8` |
| 41018 | 6 | 27 | 13 | 20 | `1.048e-8` |
| 41019 | 7 | 24 | 7 | 15 | `1.062e-8` |
| 41020 | 8 | 28 | 10 | 19 | `1.095e-8` |

Across these fixed 20 fields, A ranged from 2 to 8 (median 6), the near-threshold C subset from 14 to 29 (median 23), D from 6 to 18 (median 11.5), and all predicate crossings from 11 to 26 (median 18.5). Seed 41014 had the largest near-threshold C subset, but it had only four label changes, the fewest D points, and the fewest predicate crossings; its maximum critical-margin movement ranked seventh. Thus the stored comparison does not support describing 41014 as simply the most broadly disrupted field.

The four incident positions also separated across seeds. `j=5941` changed `8 -> 0` in all 20 fields at this sigma and was therefore not incident-specific. The label changes at `j=126`, `j=3116`, and `j=4994` occurred only in seed 41014. At `j=4994`, 14 other seeds changed only a later predicate (class D), while five retained all predicate signs.

The bracket-candidate comparison used the frozen rule of locating a left label `2^(ell-1)`, skipping at most four zero labels, and accepting a following label `2^ell`. The zero-noise candidate sets were unique:

| `ell` | Zero-noise candidate `(left, right, gap)` |
|---:|---|
| 1 | `(495, 496, 0)` |
| 2 | `(4993, 4994, 0)` |
| 3 | `(5940, 5941, 0)` |
| 4 | `(6143, 6145, 1)` |

Because the common `j=5941` change extends the `ell=3` bracket to `(5940, 5942, 1)`, exact tuple identity alone would misleadingly call the same local boundary a removed and newly false candidate. For this comparison, a current candidate was therefore called **zero-reference-associated** when its inclusive `[left,right]` interval overlapped the zero-noise candidate interval; candidates without such overlap were called **non-reference candidates**. This is an operational association rule, not access to physical truth.

Across the 80 seed-by-`ell` cells, there were 104 exact candidate-tuple additions and 22 exact removals. Of the removals, 21 were shifts or extensions of the same zero-reference-associated boundary. There were 83 non-reference candidate additions, 35 direct `unique -> nonunique` changes, no `nonunique -> unique` changes, 79/80 cells retaining a zero-reference-associated candidate, and one cell containing only a non-reference candidate. No cell had an empty candidate set. The candidate uniqueness and midpoint reconstructed from the stored label arrays matched the stored Observer audit in 80/80 cells.

The unique exceptional cell was seed 41014 at `ell=2`:

```
zero noise: {(4993, 4994, gap=0)}
seed 41014: {(3115, 3116, gap=0)}
```

At `ell=2`, 18 other seeds retained the zero-reference-associated candidate while adding one or more non-reference candidates and therefore became nonunique; seed 41017 retained the reference candidate alone; only seed 41014 lost the reference-associated candidate while retaining a different candidate uniquely. For seed 41014, `ell=1` was nonunique because a new candidate was added while the reference-associated candidate remained, `ell=3` remained uniquely associated with the same boundary after its one-point gap extension, and `ell=4` was unchanged. The resulting `delta_3` input topology was therefore a unique non-reference `ell=2` candidate plus unique reference-associated `ell=3` and `ell=4` candidates.

The directly observed endpoint change at `ell=2` is **unique reference-only -> unique non-reference-only**. The intermediate sequence `{reference} -> {reference, non-reference} -> {non-reference}` in §7.1 is explicitly a bookkeeping decomposition obtained by applying the two stored label changes in a chosen order. It is not an observed `nonunique -> unique` transition in the zero and final traces, and it does not establish temporal or causal order.

These comparisons are descriptive results for one stored sigma level, one fixed payload, one Observer, and 20 fixed noise fields selected before B2. They do not turn the fields into a population sample, estimate an error probability, show that near-threshold counts caused the wrong estimate, establish a general failure mechanism, or validate a guard.

---

## 8. Exploratory follow-up (Phase C; limited evidential weight)

**Standing of this section**: Phase C is not part of the confirmatory core of this record. Its results are preserved as a transparent exploratory record only. They do not strengthen, validate, or independently replicate the conclusions of Phases A/B1/B2. Any connection drawn below to the Discussion Note is explicitly conditional: **if the reported tendency reflects the underlying phenomenon**, it is compatible with that interpretation and may motivate a future strict test.

### 8.1 Question and fixed design

Phase C moved away from the logistic-map finite-level quantities. It asked a narrower exploratory comparative question: when two separately developed and sealed Observers receive the same anonymised noisy curve, do their final decisions support a pre-specified Observer-specific pattern, a cross-Observer pattern, or neither?

This is motivated by the observation-space/target-structure distinction in `DISCUSSION_NOTE_OBSERVATION_SPACE_STRUCTURE_EN.md`, but it addresses that distinction only indirectly. It changes both the target system and the task; it does not test the B2 seed-41014 input with a second detector.

The private Generator uses

```
f_mu(x) = mu*x*(1-x^2)
signal(mu) = 1 - |3 - 2*mu|
u_i = i/400,  mu_i = 1.63 + 0.71*u_i,  i=0,...,400
```

The signal is the local-stability margin of the registered fixed-point branch. On the registered coordinate interval it has one positive-to-nonpositive transition, at the private truth `u = 37/71 = 0.5211267605633803...`. The Observers were not given the map, formula, original parameter, truth, noise construction, sigma, scoring tolerance, or relationships among cases.

- 20 fixed deterministic Rademacher (`+1/-1`) noise fields generated by SplitMix64.
- Sigma grid `[0, 0.01, 0.025, 0.05, 0.10]`; the same field is reused across sigma for a paired design.
- 100 targets (`20 fields x 5 sigma levels`), each containing 401 ordered observation points. At `sigma=0`, the 20 targets are repeated copies of the same clean curve under different opaque IDs; they are not 20 independent clean realisations.
- Exactly two sealed Observers, O1 and `O2-independent-multiscale-v1`, each run in a fresh process per target. They received byte-identical public specifications and payload bytes.
- An `ESTIMATE` is `RECOVERED` when `|estimate - truth| <= 0.015`; another valid estimate is `WRONG`; a valid abstention is `ABSTAIN`; invalid or failed execution is `TECHNICAL_FAILURE`.
- Two valid estimates have `LOCATION_AGREEMENT` when `|e_1-e_2| <= 0.02`. Two abstentions are status-concordant but their locations are not comparable.

The fixed noise fields are descriptive repetitions, not a probability sample. The tolerance and comparison cutoffs are operational choices, not confidence bounds.

### 8.2 Pre-registered experiment-level rule

Before the Phase C payload was generated or either Observer was run, Stage 1 v4 fixed three possible conclusions: `OBSERVER_SPECIFIC_PATTERN`, `CROSS_OBSERVER_PATTERN`, or `INCONCLUSIVE`. In abbreviated form:

- `OBSERVER_SPECIFIC_PATTERN` required an absolute O1/O2 estimate-rate or recovery-rate difference of at least `0.25` after the technical-validity gate.
- `CROSS_OBSERVER_PATTERN` required both recovery rates to be at least `0.60`, both rate differences to be at most `0.15`, status concordance to be at least `0.70`, and location agreement among joint estimates to be at least `0.70`.
- Otherwise the result was `INCONCLUSIVE` (with additional technical gates for excessive failures, too few comparable targets, or too few joint estimates).

These thresholds are fixed decision rules, not statistically calibrated hypothesis tests. Passing or failing them has no p-value interpretation.

### 8.3 Reported Run 4 results (descriptive only)

Source: Phase C Run 4 `STAGE6/scoring_output.json` (SHA-256 `bd0c275caa84c544eaa9f8b293a6bd614ba9d2fc20b25185938fdd2c90b6e297`; `schema_version: phase-c-score-v4`, 200 scored rows, experiment conclusion `INCONCLUSIVE`). The Stage 6 completion marker SHA-256 is `61bf5dc0db3f8ae7a57c4dc3fd6a4d1bcf21f69e4b99f5bf4c50dd45bbc87c03`.

| Observer | Scored | `ESTIMATE` | `ABSTAIN` | `RECOVERED` | `WRONG` | `TECHNICAL_FAILURE` |
|---|---:|---:|---:|---:|---:|---:|
| O1 | 100 | 52 | 48 | 50 | 2 | 0 |
| O2 | 100 | 59 | 41 | 57 | 2 | 0 |

| Sigma | O1 E/A/R/W | O2 E/A/R/W | Location agreement / joint estimates | Status-concordant / 20 |
|---:|---:|---:|---:|---:|
| `0` | 20/0/20/0 | 20/0/20/0 | 20/20 | 20 |
| `0.01` | 20/0/20/0 | 19/1/19/0 | 19/19 | 19 |
| `0.025` | 9/11/9/0 | 14/6/14/0 | 7/7 | 11 |
| `0.05` | 3/17/1/2 | 6/14/4/2 | 1/1 | 13 |
| `0.10` | 0/20/0/0 | 0/20/0/0 | not comparable (0 joint estimates) | 20 |

Across all 100 targets, there were 47 joint estimates, all 47 satisfying the fixed location-agreement rule. Status concordance was `0.83`; O2's estimate and recovery rates exceeded O1's by `0.07` each. The `0.07` difference did not meet the `0.25` Observer-specific threshold, while the two recovery rates (`0.50`, `0.57`) did not meet the `0.60` cross-Observer requirement. Even within Phase C's own rules, the result is **`INCONCLUSIVE`**. It is not a win for O2, not a confirmation of an Observer-independent pattern, and not evidence used by the main record.

### 8.4 Conditional comparison with Phases A/B1/B2

| Question | Phases A/B1/B2 | Phase C | Supported reading |
|---|---|---|---|
| Recovery on a clean input | Phase A: 16/16 `RECOVERED` | Both Observers: 20/20 at `sigma=0` | If reliable, the same qualitative clean-recovery shape; Phase C's 20 clean cases are duplicate curves |
| Degradation with added observation noise | Recovery falls in B1/B2 | Recovery falls from `0.01` through `0.05` | If reliable, qualitatively similar; no common numerical noise scale is implied |
| Abstention at the highest tested noise | B1: all targets abstain at sufficiently high tested sigma | Both Observers: 20/20 `ABSTAIN` at `0.10` | If reliable, qualitatively similar within each fixed grid |
| Agreement after changing the Observer | Not tested in A/B1/B2 | 47/47 location agreement among joint estimates | Exploratory observation under one operational agreement radius |
| Observer-independent breakdown pattern | Not tested in A/B1/B2 | Formal conclusion `INCONCLUSIVE` | Not established |

The absolute sigma values, recovery rates, and reason codes must not be compared numerically across the two studies. The generated signal, observation scale, noise distribution, target quantity, tolerance, and decision rules all differ. Phase C is therefore an exploratory related exercise, not a replication of the B1/B2 threshold. The table records a possible qualitative analogy, not additional evidence for the primary result.

Conditionally, the tendency is compatible with the Discussion Note's decomposition

```
noise realisation + finite observation + decision rules -> operational structure
```

The suggestive feature is that clear cases were reportedly located similarly, whereas all four Phase C false acceptances were paired with abstention by the other rule. If reliable, that would fit the hypothesis that an ambiguous pattern becomes certified structure through its interaction with a decision rule. Phase C does **not** demonstrate this proposition: it did not test the seed-41014 input with another Observer, and its own evidential limitations prevent promotion of the tendency beyond hypothesis formation.

### 8.5 Post-hoc description of the four Phase C wrong estimates

A read-only analysis of the sealed Stage 6 output found four `WRONG` rows, all at `sigma=0.05`: two from O1 and two from O2. In every one of those four targets, the paired Observer abstained. The analysis artifact has SHA-256 `4f954dd8944d750031bfca2edfa0ff64a1840295e091a0049d52ce91afe39d2a`. The 100-target joint classification was:

| Pair outcome | Count |
|---|---:|
| Both `RECOVERED` | 47 |
| O1 `RECOVERED` / O2 `WRONG` | 0 |
| O1 `WRONG` / O2 `RECOVERED` | 0 |
| Both `WRONG` | 0 |
| O1 `ESTIMATE` / O2 `ABSTAIN` | 5 |
| O1 `ABSTAIN` / O2 `ESTIMATE` | 12 |
| Both `ABSTAIN` | 36 |

Thus the four false acceptances did not recur as false acceptances under the other Phase C decision rule on the same targets. This is a **post-hoc descriptive result**, not part of the pre-registered experiment conclusion. It does not show that one Observer can generally guard the other, nor does it establish that the B2 incident and the Phase C errors share a mechanism.

### 8.6 Execution history and strictness qualifications

Phase C contains useful mechanical controls: the Stage 1 v4 generator, truth, fixed cases, scoring rule, comparison rule, and experiment-level conclusion were sealed before payload generation and before Observer outputs; the Observer inference implementations were sealed before payload release; and Run 4 raw outputs were sealed before truth was opened. Run 4 completed 100/100 targets for each Observer with no retry, no technical failure, no raw modification, and no post-result threshold change. These controls support the integrity of the reported Run 4 files; they do not make the exercise confirmatory or cure the broader reliability limitations below.

It is nevertheless **not a pristine single-shot independent replication**. Three earlier run attempts were preserved and failed for operational reasons:

1. Run 1: O1's seal metadata duplicated entry-point arguments, so its estimator never started; O2 completed.
2. Run 2: both batches completed, but Stage 5 rejected all O2 rows because the seal identity `O2` did not match the raw identity `O2-independent-multiscale-v1`. The public output schema allowed either string; the exact internal identity-binding requirement had not been caught in preflight.
3. Run 3: O1 stopped before its first process because an attempt parent directory was absent; O2 completed 37 recorded targets before an external 120-second caller limit terminated the batch.

Before Run 4, the execution infrastructure was revised and tested on dummy inputs: command construction and identity binding were checked, target and batch timeouts were separated, the caller timeout requirement was raised, and a versioned infrastructure manifest was sealed. The scientific design, payload, truth, Scorer, scoring thresholds, and Observer inference code were reported unchanged; only seal metadata and execution infrastructure were corrected. The same fixed payload bytes were then used in Run 4. No truth was opened and no scoring was performed in Runs 1–3.

This history does not by itself overturn the Run 4 counts, but it weakens any claim that Phase C was an untouched one-opportunity trial. The infrastructure manifest was recorded in Run 4 batch evidence but was not itself bound inside `STAGE3_COMPLETE`. In addition, the two Observers were separate implementations produced in related AI-assisted workflows, not independent laboratories; the degree of algorithmic independence was not quantified. All seals and most cross-checks remain within the same implementation and operator lineage.

The appropriate reading is therefore modest: Run 4 preserves a fixed-design descriptive output after infrastructure repair, while its formal conclusion remains `INCONCLUSIVE` and its evidential weight is insufficient for the primary claims. Hash and completion checks should not be confused with scientific validation.

---

## 9. Exploratory Phase D0: fresh-seed recurrence probe at the 41014 condition

### 9.1 Fixed question and design

Phase D0 asked one deliberately narrow question: **does the unchanged Observer produce another `WRONG` classification for `delta_3` on previously unused noise fields at the exact B2 incident condition?** It was not designed to map a sigma-dependent failure region, identify a cause, evaluate a guard, compare Observers, or estimate a general error probability.

Before execution, the following were fixed and hash-sealed:

- the original A3 payload, frozen Phase A/B Observer, frozen Scorer and private truth;
- `sigma=1.6e-9` only;
- fresh seeds 41021–41060, with the B2 seeds 41001–41020 excluded;
- 40 Observer runs and 160 decisions, with `N_WRONG_DELTA3` as the sole primary outcome;
- no retry, replacement seed, adaptive sigma, threshold change, Observer change, Scorer change, or automatic extension;
- raw Observer records must be sealed before truth access and scoring; and
- CPython `3.13.15`, NumPy `2.3.2`, executable SHA-256 `72B29481593C5DA37C99248C82777FBFB56217EA7809B771BC760D0A9ECB179B`.

The probe's standing was fixed as `EXPLORATORY_PRE_SPECIFIED_RECURRENCE_PROBE`. The pre-run plan, design and runner hashes were subsequently reproduced exactly by the raw seal.

### 9.2 Result

The recorded execution completed all 40 fixed seeds and all 160 decisions with `retry_count=0` and `replacement_seed_count=0`. The `delta_3` primary rows were:

| Scorer classification | Count |
|---|---:|
| `RECOVERED` | 1 |
| `WRONG` | **0** |
| `ABSTAIN` | 39 |

The one recovered row was seed 41042: estimate `4.667487684729068`, truth `4.656251017651357`, relative error `0.0024132434` (about `0.241%`). All 39 abstentions used `BIFURCATION_BRACKET_UNRESOLVED`. Because there were no `WRONG` rows, there was no fresh wrong case on which to observe the pre-specified 41014-shaped downstream fingerprint; `path_resembles_41014_count=0` should not be read as a separate negative mechanism test.

The fixed interpretation key is `NO_RECURRENCE_OBSERVED_IN_FIXED_40_SEEDS`. In plain language, the probe did **not** catch a second 41014-type false acceptance. It does not show that the failure cannot recur, that its frequency is zero, or that the Observer is safe. It also leaves all other sigma values and all other Observers untested.

### 9.3 Seal and audit checks

The 40 raw records contain exactly seeds 41021–41060, 40 distinct noise-field hashes, one common sigma, and four Observer decisions per seed. Their SHA-256 is `148A738D984CAF5592B80E7C1845A43518AA06E30A92BC1CE959BEDA0E1F22B4`. `RAW_SEAL.json` records `truth_opened:false` and binds that raw file to the sealed plan, design, runner, A3 payload, Observer source and runtime executable; its own SHA-256 is `D9CFA8E46F4F9F92F992F9F83AE24418D9567BF6AFEE17F4F5D024FFE01DF89B`. The truth-access record references this raw-seal hash before scoring. The final score-record and summary hashes are `413A0F66E5BE613E15EF052CA0B678A66FEA1AF46F8B9C480F23867038912866` and `7084B7240A191042E469FBDC558FD511C5A9C43FFF06583DA1354E947AD14730`, respectively; all current files reproduce the hashes recorded in `COMPLETE.json`.

These checks support identity and execution-order consistency within the same local operator and implementation lineage. They are not an independent replication, a formal tamper-proof audit, or scientific validation of the Observer.

### 9.4 Post-hoc derived candidate-set reconstruction (not part of the original D0 seal)

These traces were reconstructed post hoc from the fixed Phase D0 inputs and frozen generation/Observer rules. They were not part of the original sealed Phase D0 output artifacts.

The reconstruction regenerated seeds 41021–41060 at `sigma=1.6e-9` from the sealed A3 identity and fixed `SeedSequence([20260811, seed])` / `PCG64DXSM` rule. Before candidate analysis, all 40 regenerated noise-field SHA-256 values matched the hashes in the original D0 raw records. The frozen Observer outputs, reconstructed `b_hat_v`, and labels then matched the stored records or frozen label function 40/40. The derived arrays contain one `(6201,)` label vector and one `(6201,5)` residual array for periods `1,2,4,8,16` per seed.

The candidate rule and reference-association rule were unchanged from the fixed-20-seed comparison in §7.2: a candidate is a left label `2^(ell-1)`, at most four intervening zero labels, and a right label `2^ell`; a current candidate is reference-associated when its inclusive index interval overlaps the zero-reference candidate interval. `Reference` here denotes the zero-noise computational reference, not physical truth.

For `ell=2`, the zero-reference candidate was `(4993,4994,0)`. The 40 reconstructed cells were:

| `ell=2` state | Seeds |
|---|---:|
| reference-associated candidate retained | **40/40** |
| reference lost | **0/40** |
| alternative candidate(s) added | **39/40** |
| unique → nonunique | **39/40** |
| false-only | **0/40** |
| unique false-only | **0/40** |
| candidate count zero | **0/40** |
| exact reference-only unique | **1/40** |

All 39 `delta_3 ABSTAIN` rows had the same sufficient `ell=2` condition: the reference-associated candidate remained, but one or more alternatives were also present, so the candidate set was nonunique. Their `ell=2` candidate counts ranged from 2 to 8. The sole `RECOVERED` row, seed 41042, retained exactly `(4993,4994,0)` with midpoint `0.8054032258064516` and no alternative candidate.

Across all 40 seeds × four levels = 160 cells:

| `ell` | reference retained | alternative added | U→N | N→U | false-only | unique false-only | candidate 0 |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | 40 | 9 | 9 | 0 | 0 | 0 | 0 |
| 2 | 40 | 39 | 39 | 0 | 0 | 0 | 0 |
| 3 | 40 | 19 | 19 | 0 | 0 | 0 | 0 |
| 4 | 40 | 5 | 5 | 0 | 0 | 0 | 0 |
| **Total** | **160** | **72** | **72** | **0** | **0** | **0** | **0** |

The 160 cells comprised 67 exact reference-only unique states, 21 reference-associated unique states with a changed tuple, and 72 reference-plus-alternative nonunique states. There were 203 strict tuple additions and 40 strict tuple removals. All 40 removals occurred at `ell=3`; the replacement interval still overlapped the zero-reference interval, so reference association was retained in every case.

Unlike seed 41014's stored `ell=2` endpoint—where `(4993,4994,0)` was absent and the alternative `(3115,3116,0)` remained uniquely—the fresh 40 contained no reference-lost or unique false-only `ell=2` cell. The bounded conclusion is: **no unique false-only `ell=2` candidate state was observed among the 40 post-hoc reconstructed fresh-seed traces**. This does not establish non-recurrence, a probability, safety, or a general causal mechanism.

The derived package is stored separately at `C:/Users/yauki/Documents/Codex/2026-08-13/p/outputs/phase_d0_posthoc_candidate_reconstruction_2026-08-13/`. Its `MANIFEST_SHA256.txt` SHA-256 is `FC435F96D0D961BFE399F54ABD66A08AD6BD4757D50BD2BC4BE9EA9919FBBFD4`; `derived_trace_arrays.npz` is `979047706699A1A33B4ACD2B7BD934A2C313B420CEEC4F9CA312DD558287F7F5`; and `summary.json` is `8E0770646FFA82104D3E3C16CC0D916EC0751CE88C772ADC9A04382A3F55D499`. The original D0 source tree remained byte-identical before and after reconstruction.

---

## 10. Reproducibility, sealing, and audit boundaries

### 10.1 Execution order

1. **Phase A**: 4 deterministic runs → 16 decisions → truth opened → Scorer scoring.
2. **Phase B1**: all 16 Phase A files were sealed once as an immutable manifest and re-checked before and after benchmarking and after the full run, each time into a separate audit file (`expected=16 / observed=16 / missing=0 / extra=0 / mismatch=0`; `04_PHASE_B1/results/phase_a_audit_postfullrun.json` reports `pass: true` and 27/27 agreement on the Phase A implementation manifest). The truth was opened only after every Observer output was final.
3. **Phase B2**:
   - The `sigma=0` zero-noise complete trace reference was created and sealed first.
   - For each of the 180 positive runs, the frozen Observer output was committed write-once **first**, and only then was the diagnostic tracer run.
   - For all 181 items (180 traces + 1 reference), the tracer's reconstructed decisions were compared with the frozen Observer at object level (`reconstruction_matches: 181`).
   - **The raw Observer outputs and all traces were hash-sealed in `observer_trace_seal_manifest.json` (`truth_opened: false`) before the truth was opened and the Scorer run** (`truth_access_record.json`: `purpose: SCORER_ONLY`, referencing `raw_seal_sha256`).
   - Full-run verification: `PHASE_B2_FULL_RUN_VERIFICATION_V1.json` reports `status: PASS`, implementation manifest 67/67, run artifacts 16/16, baseline checks 17/17, endpoint matches 41, and no error ledger.
4. **Exploratory Phase C Run 4 (not part of the primary evidential chain)**:
   - Stage 1 v4 sealed the private design, Generator, Truth, Scorer, comparison rules, experiment-conclusion rules, and public interface before payload generation.
   - O1 and O2 were separately developed and sealed before the 100-target release. The release was byte-identical for both.
   - After the failed Runs 1–3 (§8.6), a sealed Execution Infrastructure v2 was regression-tested on dummy targets. Run 4 then started 200 fresh Observer processes, producing 100/100 valid raw outputs for each Observer with no retry.
   - Stage 5 verified and sealed all 200 raw outputs before truth access. Stage 6 materialised truth and invoked the frozen Scorer once. The Stage 6 marker records no raw-hash mismatch, modification, regeneration, Observer rerun, technical failure, or post-result threshold addition.
5. **Exploratory Phase D0 (not part of the primary evidential chain)**: the plan, fixed design and runner were sealed before the run; exactly 40 fresh seeds at one fixed sigma produced 160 decisions; the raw records were sealed with `truth_opened:false`; the truth-access record bound that raw seal; and the frozen Scorer then produced 160 classifications. `COMPLETE.json` records `status:COMPLETE`, zero retries and zero replacement seeds.
6. **Post-hoc D0 candidate reconstruction (not part of the original D0 seal)**: after D0 was complete, the same 40 deterministic inputs were regenerated in a separate output tree. Noise hashes and Observer outputs matched 40/40 before the reconstructed labels, residuals and candidate sets were used descriptively. No original D0 artifact was modified.

Each Phase's PLAN is a **pre-registration document** fixing the permitted scope and stop state at the time of writing; it was not rewritten to `COMPLETE` afterwards. Benchmarking and full runs were each authorised by a separate, subsequent approval artifact (in the original environment: Phase A `PHASE_A_FULL_RUN_APPROVAL_V1.json`; B1 `PHASE_B1_FULL_RUN_APPROVAL_V1.json` = `7CCBD452...`; B2 `FULL_RUN_APPROVAL_V1.json` = `C93AD681...`). The B2 approval authorises `fixed_design_sha256=5B564B6E...`, 181 runs / 724 decisions / 180 positive traces / 1 zero reference / 724 classifications, with no retries and no substitute seeds. The approval documents themselves are not included in this distribution package.

### 10.2 Runtime

Phases A/B1/B2 and the original D0 run used CPython `3.13.15` / NumPy `2.3.2` (Windows, AMD64). Both B1 and B2 require exact version agreement as a precondition for execution (`runtime_fingerprint` in `04_PHASE_B1/results/run_provenance.json`); D0 additionally bound the executable SHA-256 in its pre-run and raw seals. The later post-hoc D0 candidate reconstruction used CPython `3.12.13` / NumPy `2.3.5` with the same hash-bound A3 payload and Observer source; its regenerated noise hashes and complete stored Observer outputs nevertheless matched 40/40. Phase C O1/O2 were sealed to CPython `3.14.5` on the same local Windows host; the executable path, binary hash, and version-output hash were recorded in each Observer seal.

### 10.3 Audit boundaries (what is *not* guaranteed)

- All sealing and cross-checking was **generated and verified within the same implementation lineage**. There has been no re-verification by an independent third-party implementation and no adversarial tamper-resistance testing.
- A narrow independent calculation reproduced the local residual/label/bracket path for the stored 41014 input (§7.1), but the Observer as a whole has not been independently reimplemented or third-party verified. That checker recomputes one narrow path in separate code; it is not a complete independent reimplementation of the Observer and not a forensic examination carrying formal guarantees.
- The diagnostic tracer is a separate module reading the same public inputs as the Observer; it is not an independent implementation.
- **The forensic framework carrying formal guarantees (a design including Stage 1–4 approvals, a manifest chain, and a self-verifier) repeatedly ended in FAIL / HOLD and was abandoned before the incident run was ever executed.** It was then replaced by a single-shot script with a deliberately narrowed claim (§7, `INCIDENT_41014_NOTE.md`). The number of tests that passed inside the abandoned framework is not added to the scientific credibility of the incident.
- The roughly 2 GB of per-run diagnostic trace (`diagnostic_trace_index.jsonl`) is not included in this distribution package. Its hash is recorded in `observer_trace_seal_manifest.json`.
- Phase C's audit trail is internally detailed but remains same-lineage. The same operator environment built the design, coordinated the separate Observer work, repaired the execution infrastructure, and ran the final scoring. Hash consistency demonstrates artifact identity, not institutional independence or freedom from coordinated design bias.
- Phase D0 reused the same Observer, Scorer, payload family, local host and operator lineage as A/B. Fresh seeds add new noise realisations, not an independent implementation or laboratory.
- The post-hoc D0 reconstruction used the same Observer implementation and deterministic input rules. Its exact matches demonstrate reproducibility of these 40 computational paths across the recorded environments, not independent algorithmic or institutional replication.

---

## 11. Limitations and explicit non-claims

### 11.1 Design limitations

- A single map family (the logistic map's principal cascade), a single Observer implementation, a single payload (A3), fixed 20 paired noise fields, and fixed sigma grids.
- The 20 seeds are a **descriptive fixed repetition**, not a population sample. No p-values, confidence intervals, FPR guarantees, multiple-comparison corrections, or curve fitting of a continuous boundary were performed.
- The 5% relative error is a pre-registered operational threshold, not a statistical guarantee. No sensitivity analysis was performed.
- Observation noise only. Process noise, parameter noise, coloured noise, quantisation, missing data, and outliers are out of scope.
- Sigma-boundary resolution extends only to the grid spacing. No extrapolation to a true threshold in continuous sigma.
- Phase C adds one second map-derived curve, not a broad family of systems. All 100 targets share one underlying noiseless curve and one truth location; only the fixed field and sigma vary.
- Phase C's 20 deterministic fields are not a population sample, its 20 `sigma=0` cases are duplicate clean curves, and its operational cutoffs (`0.015`, `0.02`, and the experiment-level rate thresholds) were not statistically calibrated.
- Phase C used two distinct sealed implementations, but did not quantify their algorithmic dependence or establish independence in the stronger sense of separate laboratories, teams, or implementation lineages.
- Phase D0 used 40 pre-fixed fresh seeds at one incident-matched sigma. They are a descriptive fixed set, not a calibrated population sample; the probe has no resolution across sigma and no independent Observer. The later candidate-set reconstruction is post hoc and adds mechanism-level description of the same 40 deterministic inputs, not 40 additional observations.

### 11.2 Explicit non-claims

This study claims none of the following.

- "An AI discovered Feigenbaum universality" / "discovered an unknown law". The subject is **measurement of the recoverability** of already-known finite-level quantities.
- "The Observer's safety was proved." If anything, `WRONG=1` is an observation in the opposite direction.
- "A universal limit of noise tolerance was measured." What was measured is the breakdown curve of one fixed procedure.
- "Noise induced a genuinely new bifurcation." What was observed is a failure of the detector's operational labelling.
- "B2's diagnostic quantities would enable a better ABSTAIN rule." Rule design was outside B2's scope and remains untested.
- "Phase C replicated the logistic-map threshold." Its observation and sigma scales are different and not numerically comparable.
- "Phase C proved a cross-Observer pattern" or "O2 won". The pre-registered conclusion is `INCONCLUSIVE`.
- "The four Phase C false acceptances share the B2 incident's mechanism." No common mechanism was tested.
- "Phase D0 proved that 41014 was unique or non-recurrent." It observed no additional `delta_3` wrong answer in one fixed set of 40 seeds; the post-hoc reconstruction additionally observed no unique false-only `ell=2` state in those same 40 inputs. Neither zero count establishes zero risk.

### 11.3 Not executed

Phase D0 supplies one narrow fresh-seed evaluation at `sigma=1.6e-9`; a broader Phase B3 across pre-fixed sigma levels remains unexecuted. A third Observer, an independent-laboratory replication, and the design, implementation, or evaluation of plausibility guards or cross-series consistency guards are also unexecuted. Phase C itself is complete only as an exploratory execution record in the qualified Run 4 sense described in §8.6; it is not a completed confirmatory study.

---

## 12. File guide

(Relative paths are from the root of this distribution package.)

| Category | Path | Content |
|---|---|---|
| Phase A PLAN | `03_PHASE_A/PLAN_PHASE_A_FINITE_LEVEL_RECOVERY.md` | fixed conditions, Observer rules, truth definitions, scoring rules |
| Phase A results | `03_PHASE_A/results/phase_a_score.json` | scoring of the 16 decisions, summary, post-truth diagnostics |
| Phase A raw | `03_PHASE_A/results/A3.json` | Observer raw output for A3 (zero-noise reference) |
| Phase A truth | `03_PHASE_A/results/truth.json` | 100-digit truth and root-finding verification |
| Phase A implementation | `03_PHASE_A/implementation/` | Observer / Scorer / Generator source, contracts, manifest |
| Phase B1 PLAN | `04_PHASE_B1/PLAN_PHASE_B1_GAUSSIAN_BREAKDOWN.md` | frozen hashes, noise design, bracket decision rules |
| Phase B1 results | `04_PHASE_B1/results/summary.csv` | the fixed 32-row aggregation |
| Phase B1 brackets | `04_PHASE_B1/results/breakdown_brackets.json` | recovery-loss brackets for the four targets |
| Phase B1 raw | `04_PHASE_B1/results/seed_records.jsonl` | provenance and Observer outputs for 141 runs |
| Phase B1 audit | `04_PHASE_B1/results/phase_a_audit_postfullrun.json` | post-hoc audit of Phase A immutability |
| Phase B2 PLAN | `05_PHASE_B2/PLAN_PHASE_B2_FIRST_FAILURE_ATTRIBUTION.md` | DAG, node registry, sealing order, completion conditions |
| Phase B2 decisions | `05_PHASE_B2/results/decision_summary.csv` | 40 rows (4 targets × 10 sigma) |
| Phase B2 attribution | `05_PHASE_B2/results/first_failure_by_seed.csv` | 80 rows of first-failure / precursor |
| Phase B2 patterns | `05_PHASE_B2/results/transition_patterns.json` | thresholds, onset order, ties, non-monotonicity |
| Phase B2 scoring | `05_PHASE_B2/results/score_records.jsonl` | 724 Scorer classifications |
| Phase B2 seal | `05_PHASE_B2/results/observer_trace_seal_manifest.json` | the raw seal (`truth_opened: false`) |
| Phase B2 verification | `05_PHASE_B2/results/PHASE_B2_FULL_RUN_VERIFICATION_V1.json` | full-run verification `PASS` |
| Incident | `06_INCIDENT_41014/diagnose_41014.py` | single-shot, unaudited reference diagnostic script |
| Incident output | `result_41014.txt` | the script's stdout, saved after the fact. Source for the incident numbers |
| Local-path reproduction | `SEED_41014_FIXED_RUNTIME_LOCAL_PATH_REPRODUCTION.txt` | record of the 2026-08-12 fixed-runtime local-path reproduction (§7.1). SHA-256 `361E1F5A951785DD6096CBD0FE7A4235C68092D65C7F32DEC21BDDF8116F6E15` |
| Local-path checker | `SEED_41014_FIXED_RUNTIME_LOCAL_PATH_CHECK.py` | the read-only checker that produced that record. SHA-256 `68C7AD4EE3371106D60526F64C6E2C53DF26461527EA4FC729F62F602C59CEA2` |
| Incident note | `INCIDENT_41014_NOTE.md` | post-hoc note restricted to the single case in §7 |
| Discussion note | `DISCUSSION_NOTE_OBSERVATION_SPACE_STRUCTURE_EN.md` | separates input pattern, operational certification, and target-system structure; motivates but does not determine Phase C |
| Conversation logs | `01_LOGS/log 6–8` | interpretation of Phase A/B1/B2. Not the authoritative source for any number |
| Conversation logs | `01_LOGS/log 9–13` | history of the failed and abandoned forensic framework design |
| Conversation logs | `01_LOGS/log 14` | the switch to a single-shot diagnostic and its execution result |
| Conversation log | `log 16` | read-only 41014 neighbourhood and fixed-20-field comparison reflected in §7.2 |
| Conversation logs | `logs 17–22` | Phase C design, Observer preparation, Runs 1–4, scoring, and post-hoc comparison; useful history but not authoritative over sealed JSON |
| Background | `02_BACKGROUND/LITERATURE_REVIEW.md`, `REVIEW_SCOPE.md` | a separate prior-work review conducted 2026-08-10. Not used for any numerical claim in this record |

Phase C artifacts currently reside outside the original distribution package under `C:/Users/yauki/Documents/Codex/2026-08-12/phase-c-generator-truth-scorer-observer/`. The principal local sources are:

| Phase C category | Path under that root | Content |
|---|---|---|
| Fixed design | `work/phase_c_codexa_20260812_v4/PRIVATE_DO_NOT_SHARE/` | generation, scoring, comparison, and experiment-conclusion specifications |
| Public contract | `work/phase_c_codexa_20260812_v4/01_INTERFACE_ONLY_FOR_OBSERVERS/` | public interface and schemas given before payload release |
| Run 4 scoring | `work/phase_c_codexa_20260812_run4/STAGE6/scoring_output.json` | 200 scoring rows and pre-registered experiment conclusion |
| Run 4 completion | `work/phase_c_codexa_20260812_run4/STAGE6/STAGE6_COMPLETE.json` | completion and immutability checks |
| Post-hoc wrong-case analysis | `work/phase_c_codexa_20260812_run4/POST_HOC_ANALYSIS/wrong_case_descriptive_analysis.json` | the four wrong rows and paired outcome counts |
| Execution history | Run 1–4 work/evidence directories and logs 20–22 | preserved failures, infrastructure revision, and final run history |

Phase D0 artifacts currently reside outside the original distribution package under `C:/Users/yauki/Documents/Codex/2026-08-13/readme-en-2026-08-12-2/work/phase_d0_fresh_seed_recurrence/`:

| Phase D0 category | Path under that root | Content |
|---|---|---|
| Pre-run specification | `PLAN_PHASE_D0_FRESH_SEED_RECURRENCE.md`, `fixed_design.json`, `PRE_RUN_SEAL.json` | fixed question, seeds, prohibitions, runtime and pre-run hashes |
| Runner | `run_phase_d0.py` | single fixed-run implementation |
| Raw output and seal | `results_once/observer_records.jsonl`, `results_once/RAW_SEAL.json` | 40 raw Observer records and pre-truth seal |
| Truth-access record | `results_once/truth_access_record.json` | binds scoring access to the raw seal |
| Scoring and summary | `results_once/score_records.jsonl`, `results_once/summary.json` | 160 classifications and fixed primary summary |
| Completion | `results_once/COMPLETE.json` | final hashes, counts, retry/replacement zeros and completion state |

The later D0 candidate reconstruction is stored separately under `C:/Users/yauki/Documents/Codex/2026-08-13/p/outputs/phase_d0_posthoc_candidate_reconstruction_2026-08-13/` and is not part of the root above:

| Post-hoc D0 category | File | Content |
|---|---|---|
| Derived arrays | `derived_trace_arrays.npz` | 40 label vectors, 40 period-residual arrays, and the zero-reference labels |
| Candidate records | `candidate_sets.jsonl`, `seed_ell_classification.csv` | candidate tuples and 160 seed-by-level classifications |
| Outcome join | `ell2_delta3_correspondence.csv` | `ell=2` states joined to the recorded `delta_3` outcomes |
| Verification | `noise_hash_verification.json`, `reconstruction_audit.json` | 40/40 noise, Observer, `b_hat_v`, and label-function checks |
| Summary and integrity | `summary.json`, `MANIFEST_SHA256.txt` | fixed aggregates and payload hashes |

**Evidence precedence**: sealed results / CSV / JSON / provenance / verification > each Phase's PLAN or fixed specification, configuration, and implementation > interpretations in the conversation logs > the single-shot diagnostic > failure history. Where log wording conflicts with a formal artifact, the formal artifact wins. Praise, recommendations, speculation, and future plans appearing in the logs are not measurement facts.

For the original D0 decisions, the sealed D0 artifacts retain precedence. The separate derived package is authoritative only for the reproducibly reconstructed labels, residuals and candidate-set classifications reported in §9.4; it does not retroactively become part of the original D0 seal.

---

## 13. Conclusion

Under ideal conditions the fixed blind Observer recovered all four finite-level quantities within the 5% threshold from anonymised finite observations alone (Phase A, 16/16). Under observation Gaussian noise, on a coarse grid all four targets lost recovery inside `1e-9 < sigma <= 3e-9`, and within that grid the loss carried no wrong answers (Phase B1, `WRONG=0`). Refining the same interval showed that the direct cause of the breakdown was not a shared period-recognition layer but split into delta-side bracket uniqueness and alpha-side superstable geometry, with 33 of 80 rows failing simultaneously at the same depth (Phase B2, `PERIOD_COMMON=0`). And one false acceptance appeared that the coarse grid had not revealed.

The read-only comparison at `sigma=1.6e-9` further located that B2 case within the fixed 20 fields. Seed 41014 was not the field with the most label or predicate changes. Its distinguishing stored bracket topology was narrower: among 80 seed-by-level cells, its `ell=2` cell alone replaced the zero-reference-associated unique candidate with a unique non-reference candidate, while the comparison supplied no evidence that this topology is a population-level risk marker or general causal mechanism.

The pre-specified exploratory D0 probe then applied the unchanged Observer to 40 unused seeds at that same A3/sigma condition. It observed `delta_3: RECOVERED=1 / WRONG=0 / ABSTAIN=39`; no second 41014-type false acceptance was found. A separate post-hoc reconstruction showed that all 39 abstentions retained the reference-associated `ell=2` candidate but also contained alternatives and were therefore nonunique; seed 41042 alone retained the exact reference-only unique tuple and was recovered. All 40 retained a reference-associated `ell=2` candidate, and none had the unique false-only endpoint observed for 41014. This makes the correct record "no recurrence observed in these fixed 40 seeds, and no unique false-only `ell=2` state observed in their post-hoc reconstructed traces," not "the incident cannot recur." The result supplies no population rate and does not alter the primary B2 fact that a false acceptance occurred.

The primary judgment is therefore twofold: operational recoverability was demonstrated under the finite clean conditions tested, and added observation noise was shown to move the fixed procedure from recovery toward abstention while also permitting a false acceptance. In particular, **an `ESTIMATE` is an algorithmic certification, not proof that target-system structure was recovered**.

These primary findings remain tied to one logistic-map payload, one Observer, fixed noise fields, operational thresholds, and fixed sigma grids. Nothing in the primary study demonstrates an information-theoretic limit, a population error rate, universal safety, or a new general theory.

**Exploratory note only**: Phase C reported a similar descriptive progression from clean recovery to degradation and high-noise abstention. It also reported 47/47 location agreement among joint estimates, while each Observer produced two false acceptances and the paired Observer abstained in all four cases. Because Phase C is non-confirmatory and its own fixed conclusion was `INCONCLUSIVE`, none of those observations is used to strengthen the conclusion above. If the tendency is real, it is compatible with the Discussion Note's hypothesis that operational structure depends on the interaction of finite observations and decision rules; a new strict experiment would be required to test that hypothesis.

---

## 14. Reader notes (outside the main record): remaining limitations

The following are not claims of the research record; they are notes to prevent over-reading the results.

1. **The precursor onset is left-censored.** B2's period-layer precursor was already detected at `1e-9`, the smallest positive sigma measured, so the true onset is `<= 1e-9`. The same traces were not recorded at B1's smaller sigma values.
2. **The 41014 replay / Decimal diagnostic and the 41014 fixed-runtime local-path reproduction are two different things, and neither is a formal forensic examination.** The earlier one (`result_41014.txt`, `INCIDENT_41014_NOTE.md`) is a single-shot, unaudited reference diagnostic that replayed the stored input and re-evaluated the downstream arithmetic at Decimal 50/100/200 digits; it concludes nothing about cause or safety, and it is not promoted to a formal forensic result by anything in §7.1. The later one (`SEED_41014_FIXED_RUNTIME_LOCAL_PATH_REPRODUCTION.txt`, §7.1) re-executed the local path in the original fixed runtime and cross-checked it against a narrow independent calculation, the stored B2 trace, and the official raw record. Both are confined to the same one fixed stored input.
3. **§7.1 records a local reproducible path, not a cause.** It does not establish a root cause, a general failure mechanism, a frequency, or an Observer-wide safety conclusion, and no guard or repair was validated.
4. **Same-Observer recurrence now has one narrow fresh-seed probe plus a post-hoc decomposition of the same inputs, not a frequency study.** The earlier read-only comparison found the unique non-reference-only `ell=2` topology only for seed 41014 among the original 20 fields. Phase D0 then tested 40 unused seeds at the same sigma and observed `WRONG=0`. Their later reconstructed traces retained the reference-associated `ell=2` candidate 40/40; 39 were nonunique because alternatives were also present, while seed 41042 remained exact reference-only unique. This did not add seeds, sigma values, detectors, or a probability model, and it does not establish non-recurrence or safety.
5. **Phase C's clean count is repeated-case bookkeeping.** The 20 targets at `sigma=0` contain identical observation values because the noise field is multiplied by zero. `20/20` confirms stable handling across opaque target IDs and isolated processes; it is not evidence from 20 distinct clean signals.
6. **Phase C's `INCONCLUSIVE` result has priority over attractive descriptive patterns.** The 47/47 joint-estimate location agreement and the qualitative clean-to-abstain curve are reported fixed-sample summaries, but neither overrides the fixed experiment-level rule or the decision to treat Phase C as non-confirmatory.
7. **Phase C's wrong-case join is post-hoc.** The fact that all four wrong estimates were paired with abstention, with `both WRONG=0`, was extracted after Stage 6 for comparison with B2. It may motivate a later registered hypothesis, but it is not a Phase C primary conclusion.
8. **Run 4 followed infrastructure repair.** Runs 1–3 remain part of the record. The repairs were confined to seal identity/arguments and execution infrastructure, with the scientific design and Observer inference code reported unchanged, but this history still prevents presentation as a pristine single-attempt replication.
