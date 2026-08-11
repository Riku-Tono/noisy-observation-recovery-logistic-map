# Recovering finite-level scaling quantities from finite observations: Phase A / B1 / B2 research record

**Nature of this document**: a research record for a small, fixed-design (toy) study. All measurements are operational and restricted to a single map, a single Observer implementation, and pre-registered grids. Nothing here is a claim about general theory, universality, or safety.

---

## 1. Overview (plain summary)

The period-doubling cascade of the logistic map `f_u(x)=u x(1-x)` carries finite-level quantities: the ratio of bifurcation-parameter gaps `delta_k`, and the state-space scale ratio of superstable orbits `alpha_k`. This study measured **whether a fixed procedure (the Observer), told neither the map's formula nor the physical parameter names nor any true value, can recover these finite-level quantities from finite, anonymised observations alone**.

Three stages were run.

- **Phase A (ideal conditions)**: no noise. All 4 conditions × 2 quantities × 2 levels = 16 decisions were `RECOVERED` (relative error within 5%). No wrong answers, no abstentions.
- **Phase B1 (coarse noise grid)**: the Observer was left completely unchanged; iid Gaussian noise was added to the observations only. 141 runs / 564 decisions. `RECOVERED=244 / WRONG=0 / ABSTAIN=320`. All four targets lost recovery inside the same interval `1e-9 < sigma <= 3e-9`, and within that coarse grid they lost it **by abstaining, not by answering wrongly**.
- **Phase B2 (refined grid and first-failure attribution)**: that interval was refined into 9 levels. 181 runs / 724 decisions. `RECOVERED=387 / WRONG=1 / ABSTAIN=336`. The direct cause of abstention was not a shared period-recognition layer: the delta side failed at bracket uniqueness, the alpha side at superstable geometry. And **one false acceptance appeared that Phase B1 had not seen**.

The most important results are not "Feigenbaum-like quantities were recovered", but these two points.

1. The fixed Observer's breakdown could not be reduced to a single shared mechanism (33 of the 80 attribution rows are simultaneous failures at the same depth, i.e. TIE).
2. The Observer's `ESTIMATE` label is not a guarantee that true finite-level structure was recovered. The "loss by abstention only" property observed on the coarse grid did not extend unchanged to the refined grid.

In Phase B2, one false acceptance was observed for `delta_3` at seed 41014 and sigma 1.6e-9. For the limited replay / Decimal diagnostic on the stored input, see `INCIDENT_41014_NOTE.md`.

---

## 2. Research question and scope of claims

### 2.1 Research question

The study is restricted to two questions.

1. **Can the finite-level quantities `delta_2, delta_3, alpha_2, alpha_3` be recovered operationally from finite, anonymised, passive observations?**
2. **When Gaussian noise is added to the observations, where does the fixed Observer stop (ABSTAIN) and where does it answer wrongly (WRONG)?**

The fixed question statements are pre-registered in `03_PHASE_A/PLAN_PHASE_A_FINITE_LEVEL_RECOVERY.md` §1, `04_PHASE_B1/PLAN_PHASE_B1_GAUSSIAN_BREAKDOWN.md` §1, and `05_PHASE_B2/PLAN_PHASE_B2_FIRST_FAILURE_ATTRIBUTION.md` §1.

### 2.2 What is not claimed

- High-precision estimation of the asymptotic Feigenbaum constants `delta_infinity`, `alpha_infinity`. No asymptotic constant and no extrapolation method was used in either estimation or scoring.
- Discovery of universality, or generalisation to unseen maps or other systems. The subject is the logistic map's principal cascade only.
- An information-theoretic limit of recoverability under Gaussian noise.
- Population error rates, error probabilities for untested seeds, or the safety of the Observer as a whole.
- Any performance claim that "a better ABSTAIN rule can be built". Rule design was explicitly outside B2's scope.

---

## 3. Target quantities and experimental roles (Generator / Observer / Scorer)

### 3.1 Target quantities

Only the following four **finite-level** quantities are scored.

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

### 3.2 Separation of the three roles

| Role | What it knows | What it emits |
|---|---|---|
| **Generator** | the map formula, the physical parameter `u`, the initial state, burn-in, all design values | only an anonymised payload (affine coordinate `v in [0,1]`, observation series `y`, `N`, `K`) |
| **Observer** | the payload only. It is never given the map's name or formula, `c`, `b_k`, `s_k`, the truth, or any known constant | per decision, **only** `ESTIMATE` (with a finite value) or `ABSTAIN` (with a reason code) |
| **Scorer** | holds the truth privately; scores only after every Observer decision is final | `RECOVERED` / `WRONG` / `ABSTAIN` |

**Do not conflate these two label layers.** `ESTIMATE / ABSTAIN` is the Observer's raw output; `RECOVERED / WRONG / ABSTAIN` is the classification assigned by the Scorer after the truth is opened. `ESTIMATE` does not mean "was correct" — §7 is the concrete counterexample.

The Observer's ABSTAIN reason codes are fixed at five: `PERIOD_LEVEL_UNRESOLVED`, `BIFURCATION_BRACKET_UNRESOLVED`, `CRITICAL_REGION_UNOBSERVED`, `SUPERSTABLE_GEOMETRY_UNRESOLVED`, `BRANCH_OR_SIGN_UNRESOLVED` (`03_PHASE_A/PLAN_...md` §6).

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

---

## 8. Reproducibility, sealing, and audit boundaries

### 8.1 Execution order

1. **Phase A**: 4 deterministic runs → 16 decisions → truth opened → Scorer scoring.
2. **Phase B1**: all 16 Phase A files were sealed once as an immutable manifest and re-checked before and after benchmarking and after the full run, each time into a separate audit file (`expected=16 / observed=16 / missing=0 / extra=0 / mismatch=0`; `04_PHASE_B1/results/phase_a_audit_postfullrun.json` reports `pass: true` and 27/27 agreement on the Phase A implementation manifest). The truth was opened only after every Observer output was final.
3. **Phase B2**:
   - The `sigma=0` zero-noise complete trace reference was created and sealed first.
   - For each of the 180 positive runs, the frozen Observer output was committed write-once **first**, and only then was the diagnostic tracer run.
   - For all 181 items (180 traces + 1 reference), the tracer's reconstructed decisions were compared with the frozen Observer at object level (`reconstruction_matches: 181`).
   - **The raw Observer outputs and all traces were hash-sealed in `observer_trace_seal_manifest.json` (`truth_opened: false`) before the truth was opened and the Scorer run** (`truth_access_record.json`: `purpose: SCORER_ONLY`, referencing `raw_seal_sha256`).
   - Full-run verification: `PHASE_B2_FULL_RUN_VERIFICATION_V1.json` reports `status: PASS`, implementation manifest 67/67, run artifacts 16/16, baseline checks 17/17, endpoint matches 41, and no error ledger.

Each Phase's PLAN is a **pre-registration document** fixing the permitted scope and stop state at the time of writing; it was not rewritten to `COMPLETE` afterwards. Benchmarking and full runs were each authorised by a separate, subsequent approval artifact (in the original environment: Phase A `PHASE_A_FULL_RUN_APPROVAL_V1.json`; B1 `PHASE_B1_FULL_RUN_APPROVAL_V1.json` = `7CCBD452...`; B2 `FULL_RUN_APPROVAL_V1.json` = `C93AD681...`). The B2 approval authorises `fixed_design_sha256=5B564B6E...`, 181 runs / 724 decisions / 180 positive traces / 1 zero reference / 724 classifications, with no retries and no substitute seeds. The approval documents themselves are not included in this distribution package.

### 8.2 Runtime

CPython `3.13.15` / NumPy `2.3.2` (Windows, AMD64). Both B1 and B2 require exact version agreement as a precondition for execution (`runtime_fingerprint` in `04_PHASE_B1/results/run_provenance.json`).

### 8.3 Audit boundaries (what is *not* guaranteed)

- All sealing and cross-checking was **generated and verified within the same implementation lineage**. There has been no re-verification by an independent third-party implementation and no adversarial tamper-resistance testing.
- The Observer's decisions have never been reproduced by an independent implementation.
- The diagnostic tracer is a separate module reading the same public inputs as the Observer; it is not an independent implementation.
- **The forensic framework carrying formal guarantees (a design including Stage 1–4 approvals, a manifest chain, and a self-verifier) repeatedly ended in FAIL / HOLD and was abandoned before the incident run was ever executed.** It was then replaced by a single-shot script with a deliberately narrowed claim (§7, `INCIDENT_41014_NOTE.md`). The number of tests that passed inside the abandoned framework is not added to the scientific credibility of the incident.
- The roughly 2 GB of per-run diagnostic trace (`diagnostic_trace_index.jsonl`) is not included in this distribution package. Its hash is recorded in `observer_trace_seal_manifest.json`.

---

## 9. Limitations and explicit non-claims

### 9.1 Design limitations

- A single map family (the logistic map's principal cascade), a single Observer implementation, a single payload (A3), fixed 20 paired noise fields, and fixed sigma grids.
- The 20 seeds are a **descriptive fixed repetition**, not a population sample. No p-values, confidence intervals, FPR guarantees, multiple-comparison corrections, or curve fitting of a continuous boundary were performed.
- The 5% relative error is a pre-registered operational threshold, not a statistical guarantee. No sensitivity analysis was performed.
- Observation noise only. Process noise, parameter noise, coloured noise, quantisation, missing data, and outliers are out of scope.
- Sigma-boundary resolution extends only to the grid spacing. No extrapolation to a true threshold in continuous sigma.

### 9.2 Explicit non-claims

This study claims none of the following.

- "An AI discovered Feigenbaum universality" / "discovered an unknown law". The subject is **measurement of the recoverability** of already-known finite-level quantities.
- "The Observer's safety was proved." If anything, `WRONG=1` is an observation in the opposite direction.
- "A universal limit of noise tolerance was measured." What was measured is the breakdown curve of one fixed procedure.
- "Noise induced a genuinely new bifurcation." What was observed is a failure of the detector's operational labelling.
- "B2's diagnostic quantities would enable a better ABSTAIN rule." Rule design was outside B2's scope and remains untested.

### 9.3 Not executed

Phase C, Phase B3, independent evaluation on new seeds, and the design, implementation, or evaluation of plausibility guards or cross-series consistency guards are **all unexecuted**. None of these may be presented as completed work.

---

## 10. File guide

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
| Incident note | `INCIDENT_41014_NOTE.md` | post-hoc note restricted to the single case in §7 |
| Conversation logs | `01_LOGS/log 6–8` | interpretation of Phase A/B1/B2. Not the authoritative source for any number |
| Conversation logs | `01_LOGS/log 9–13` | history of the failed and abandoned forensic framework design |
| Conversation logs | `01_LOGS/log 14` | the switch to a single-shot diagnostic and its execution result |
| Background | `02_BACKGROUND/LITERATURE_REVIEW.md`, `REVIEW_SCOPE.md` | a separate prior-work review conducted 2026-08-10. Not used for any numerical claim in this record |

**Evidence precedence**: sealed results / CSV / JSON / provenance / verification > each Phase's PLAN, configuration, and implementation > interpretations in the conversation logs > the single-shot diagnostic > failure history. Where log wording conflicts with a formal artifact, the formal artifact wins. Praise, recommendations, speculation, and future plans appearing in the logs are not measurement facts.

---

## 11. Conclusion

Under ideal conditions the fixed blind Observer recovered all four finite-level quantities within the 5% threshold from anonymised finite observations alone (Phase A, 16/16). Under observation Gaussian noise, on a coarse grid all four targets lost recovery inside `1e-9 < sigma <= 3e-9`, and within that grid the loss carried no wrong answers (Phase B1, `WRONG=0`). Refining the same interval showed that the direct cause of the breakdown was not a shared period-recognition layer but split into delta-side bracket uniqueness and alpha-side superstable geometry, with 33 of 80 rows failing simultaneously at the same depth (Phase B2, `PERIOD_COMMON=0`). And one false acceptance appeared that the coarse grid had not revealed.

The endpoint of this study is therefore twofold: the recoverability of finite-level quantities was demonstrated, and, at the same time, **a case was measured inside a fixed design in which the structural label `ESTIMATE` assigned by the Observer did not mean that the structure itself had been recovered**.

All of this consists of algorithmic and operational findings for a fixed A3 payload, a fixed Observer, fixed noise fields, and fixed sigma grids. None of it demonstrates an information-theoretic limit, a population error rate, universal safety, or any new general theory.

---

## Appendix (outside the main record): remaining limitations

The following are not claims of the research record; they are three notes to prevent over-reading the results.

1. **The precursor onset is left-censored.** B2's period-layer precursor was already detected at `1e-9`, the smallest positive sigma measured, so the true onset is `<= 1e-9`. The same traces were not recorded at B1's smaller sigma values.
2. **The 41014 replay / Decimal diagnostic is a reference diagnostic.** It is a single-shot, unaudited diagnostic of one stored input, recorded in `result_41014.txt` (the stdout of the immediately preceding execution, saved after the fact). It concludes nothing about cause or safety (`INCIDENT_41014_NOTE.md`).
3. **Whether this incident is specific to a single noise realization, or recurs with other seeds and other detectors, is unverified.**
