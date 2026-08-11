# 有限観測からの有限階層スケーリング量の回復：Phase A / B1 / B2 の研究記録

**性格**：固定設計の小規模（toy）研究記録。単一写像・単一Observer実装・事前固定gridに限定した操作的な測定であり、一般理論・普遍性・安全性の主張ではない。

---

## 1. 概要（平易な要約）

ロジスティック写像 `f_u(x)=u x(1-x)` の周期倍化カスケードには、分岐点間隔の比 `delta_k` と、超安定軌道の状態スケール比 `alpha_k` という有限階層の量がある。本研究は、**写像式も物理パラメータ名も真値も知らされていない固定手続き（Observer）が、有限長・匿名化された観測データだけからこれらの有限階層量を回復できるか**を測った。

3段階の実験を行った。

- **Phase A（理想条件）**：ノイズなし。4条件 × 2量 × 2階層 = 16判定すべてが `RECOVERED`（相対誤差 5% 以内）。誤答なし、棄権なし。
- **Phase B1（粗いノイズgrid）**：Observerを一切変えず、観測値にのみ iid Gaussian noise を加えた。141 run / 564 判定。`RECOVERED=244 / WRONG=0 / ABSTAIN=320`。4対象すべてが `1e-9 < sigma <= 3e-9` の同じ区間で回復を失い、その粗いgridの範囲では**誤答ではなく棄権として失った**。
- **Phase B2（細分化gridと first-failure attribution）**：同区間を9水準へ細分化。181 run / 724 判定。`RECOVERED=387 / WRONG=1 / ABSTAIN=336`。棄権の直接原因は共通の周期認識層ではなく、delta側は bracket の一意性、alpha側は superstable geometry に分かれた。そして**B1では見えなかった誤答（false acceptance）が1件現れた**。

最も重要な結果は「Feigenbaum的な量を回復できた」ことではなく、次の2点である。

1. 固定Observerの破綻は、単一の共通機構へは縮約できなかった（80行の帰属のうち33行が同深度の同時失敗＝TIE）。
2. `ESTIMATE` という Observer のラベルは、真の有限階層構造を回復した保証ではない。粗いgridで観測された「棄権のみで失う」性質は、細分化gridへそのままは拡張できなかった。

Phase B2 では seed 41014・sigma 1.6e-9 の `delta_3` に1件の false acceptance を観測した。保存済み入力に対する限定的な replay / Decimal 診断は `INCIDENT_41014_NOTE.md` を参照。

---

## 2. 研究質問と主張範囲

### 2.1 研究質問

本研究の問いは次の2つに限定する。

1. **有限・匿名の受動観測から、有限階層量 `delta_2, delta_3, alpha_2, alpha_3` を操作的に回復できるか。**
2. **観測値へGaussian noiseを加えたとき、固定Observerはどこで停止（ABSTAIN）し、どこで誤答（WRONG）するか。**

Phase A の固定質問文は `03_PHASE_A/PLAN_PHASE_A_FINITE_LEVEL_RECOVERY.md` §1、B1 は `04_PHASE_B1/PLAN_PHASE_B1_GAUSSIAN_BREAKDOWN.md` §1、B2 は `05_PHASE_B2/PLAN_PHASE_B2_FIRST_FAILURE_ATTRIBUTION.md` §1 に事前固定されている。

### 2.2 主張しないこと

- 漸近Feigenbaum定数 `delta_infinity`, `alpha_infinity` の高精度推定。推定にも採点にも漸近定数・外挿法を用いていない。
- 普遍性の発見、または未知写像・他系への一般化。対象は principal cascade をもつロジスティック写像のみ。
- Gaussian noise に対する情報理論的な回復不能限界。
- 母集団誤り率、未試験seedの誤答確率、Observer全体の安全性。
- 「より良いABSTAIN規則が作れる」という性能主張。B2は規則設計を範囲外としている。

---

## 3. 対象量と実験役割（Generator / Observer / Scorer）

### 3.1 対象量

採点対象は次の4つの**有限階層**量のみである。

- `delta_k = (b_k - b_(k-1)) / (b_(k+1) - b_k)`（`b_k` は period `2^(k-1)` → `2^k` の分岐パラメータ）について `k=2,3`
- `alpha_k = d_k / d_(k+1)`（`s_k` は period `2^k` の superstable parameter、`d_k = f_(s_k)^(2^(k-1))(c) - c`、絶対値を取らない）について `k=2,3`

Scorer が保持する truth（`03_PHASE_A/results/truth.json`、decimal working precision 100桁、residual `<1e-60`、bracket幅 `<1e-50`）は次のとおり。

| 量 | truth（先頭桁） |
|---|---|
| `delta_2` | `4.7514462181782065490...` |
| `delta_3` | `4.6562510176513567607...` |
| `alpha_2` | `-2.5318376593375099668...` |
| `alpha_3` | `-2.5087181990346510327...` |

`b_k` と `s_k` は別系列として独立に求解・検算されている（同 `verification` ブロック）。

### 3.2 3つの役割の分離

| 役割 | 知っていること | 出力 |
|---|---|---|
| **Generator** | 写像式、物理パラメータ `u`、初期値、burn-in、設計値 | 匿名化payload（affine座標 `v in [0,1]`、観測系列 `y`、`N`、`K`）のみ |
| **Observer** | payload のみ。写像名・写像式・`c`・`b_k`・`s_k`・truth・既知定数は渡されない | 各判定について `ESTIMATE`（有限値付き）または `ABSTAIN`（reason code付き）**のみ** |
| **Scorer** | truth を非公開保持。全Observer判定確定後にのみ採点 | `RECOVERED` / `WRONG` / `ABSTAIN` |

**この2層のラベルを混同しないこと。** `ESTIMATE / ABSTAIN` は Observer の raw 出力であり、`RECOVERED / WRONG / ABSTAIN` は truth 開封後に Scorer が与える分類である。`ESTIMATE` は「正しかった」を意味しない（§7 がその実例）。

Observer の ABSTAIN reason code は5種類に固定されている：`PERIOD_LEVEL_UNRESOLVED`, `BIFURCATION_BRACKET_UNRESOLVED`, `CRITICAL_REGION_UNOBSERVED`, `SUPERSTABLE_GEOMETRY_UNRESOLVED`, `BRANCH_OR_SIGN_UNRESOLVED`（`03_PHASE_A/PLAN_...md` §6）。

---

## 4. Phase A: 理想条件での有限階層回復

### 4.1 条件

- 写像 `f_u(x)=u x(1-x)`、初期値 `x_0=0.123456789`、burn-in `B=32768`。
- 物理窓 `u in [2.9500, 3.5700]`。Observer へ渡す前に `v=(u-2.9500)/0.6200` へ正のaffine変換。Observer は `v in [0,1]` しか見ない。
- 条件は4つのみ：`A1=(N=64, Δu=0.0010, K=621)`、`A2=(1024, 0.0010, 621)`、`A3=(64, 0.0001, 6201)`、`A4=(1024, 0.0001, 6201)`。
- 反復trial、乱数、bootstrap なし。4 deterministic run、16 decision record。

### 4.2 判定閾値の性格

分類閾値は `relative_error <= 0.05` の1つだけである。これは**統計的に導出された信頼区間ではなく、最小成立性を仕分けるために事前固定した操作的（engineering）閾値**である。`03_PHASE_A/results/phase_a_score.json` にも `"tolerance_is_nonstatistical_and_arbitrary": true` として明示されている。信頼区間、coverage、FPR、多重比較補正は一切算出していない。

### 4.3 結果

出典：`03_PHASE_A/results/phase_a_score.json`

| 分類 | 件数 |
|---|---|
| `RECOVERED` | **16** |
| `WRONG` | 0 |
| `ABSTAIN` | 0 |

最も細かい格子（A3/A4）の相対誤差は `delta_2: 3.60e-4`、`delta_3: 5.77e-4`、`alpha_2: 2.42e-6`、`alpha_3: 7.30e-5` であった。A3 の Observer raw 出力は `03_PHASE_A/results/A3.json`。

**観測事実**：ノイズのない4条件では、匿名観測のみから4量すべてが 5% 閾値内で回復した。
**解釈（限定）**：この推定手続きは最小成立性を満たす。ただし4条件・deterministic run のみであり、統計的性能評価ではない。

---

## 5. Phase B1: 粗いnoise gridでの回復喪失

### 5.1 設計

- 条件は **A3 のみ**に固定。Observer、Scorer、truth、public contract、閾値はすべて SHA-256 で凍結（`04_PHASE_B1/PLAN_...md` §2）。
- 壊す情報は1つだけ：`y_obs[j,t] = y0[j,t] + sigma * Z_i[j,t]`。clipping・丸め・再標準化・欠測補完なし。`v` と metadata は bitwise 同一。
- sigma 8水準：`0, 1e-10, 3e-10, 1e-9, 3e-9, 1e-8, 3e-8, 1e-7`。正の7水準で 20 seed（`41001..41020`）。
- **paired 設計**：seed ごとに1個の標準noise field `Z_i` を作り、全ての正のsigmaで同じ `Z_i` を再利用する。
- `sigma=0` は integrity control として1回のみ。予定 run 数 `1 + 7*20 = 141`、判定数 `564`。

### 5.2 結果

出典：`04_PHASE_B1/results/summary.csv`（32行）、`04_PHASE_B1/results/breakdown_brackets.json`、`04_PHASE_B1/results/run_provenance.json`（`status: COMPLETE`, `observer_runs: 141`, `decision_records: 564`）

| 分類 | 件数 |
|---|---|
| `RECOVERED` | **244** |
| `WRONG` | **0** |
| `ABSTAIN` | **320** |

- `sigma <= 1e-9`：全20 seed・全4対象で `RECOVERED`。
- `sigma >= 3e-9`：全20 seed・全4対象で `ABSTAIN`。
- 4対象すべての運用上の回復喪失区間（operational recovery-loss bracket）は **`1e-9 < sigma <= 3e-9`**、`status: BRACKETED`、`loss_mode: ABSTAIN_ONLY`、`first_wrong_sigma: null`。

`sigma=3e-9` の reason code 内訳：

| 対象 | reason code（20件中） |
|---|---|
| `delta_2`, `delta_3` | `BIFURCATION_BRACKET_UNRESOLVED` 20 |
| `alpha_2` | `SUPERSTABLE_GEOMETRY_UNRESOLVED` 20 |
| `alpha_3` | `SUPERSTABLE_GEOMETRY_UNRESOLVED` 19、`CRITICAL_REGION_UNOBSERVED` 1 |

`sigma >= 1e-8` では全4対象が `PERIOD_LEVEL_UNRESOLVED` になる。

### 5.3 読み方の限定

**観測事実**：この粗いgridでは、回復の喪失は誤答を伴わず、棄権のみで起きた（`WRONG=0`）。

**解釈（限定）**：固定Observerは、この粗いgridの範囲では「情報不足時にもっともらしい誤答を返すより棄権する」挙動を示した。

**一般化しないこと**：`WRONG=0` は安全保証ではない。bracket は固定格子・固定20 seed・固定Observerに対する**運用上の境界**であり、「このノイズより上では数学的に推定不能」を意味しない。実際、細分化gridでは `WRONG=1` が現れた（§6、§7）。

---

## 6. Phase B2: 細分化gridとfirst-failure attribution

### 6.1 問いと設計

B2の問いは「B1で4対象が同じ区間内に喪失したのは、共通上流の period 認識が先に壊れたためか、delta側の bracket と alpha側の geometry が別々に壊れたためか、seed ごとの混合か」である。

- **Observer、判定規則、閾値、reason priority、5%採点基準はすべて変更していない。** 新しい推定器も新しいABSTAIN規則も作っていない。
- sigma を B1 の喪失区間内で9水準へ細分化：`1.0e-9, 1.2e-9, 1.4e-9, 1.6e-9, 1.8e-9, 2.0e-9, 2.3e-9, 2.6e-9, 3.0e-9`。両端はB1との再現control。
- B1 の20 paired noise fields と seed 対応をそのまま読み取り専用で再利用。
- `sigma=0` の zero-noise reference 1件を含め、Observer runs `181`、判定 `724`、positive traces `180`。
- 診断専用トレーサーは frozen Observer とは別 process・別 module で、公開入力のみを読む。**トレーサーの値は Observer の判定にも Scorer にも逆流しない**（全成果物で `observer_use_prohibited=true`, `diagnostic_only=true` を必須フィールドとする）。

### 6.2 判定結果

出典：`05_PHASE_B2/results/decision_summary.csv`（40行）、`05_PHASE_B2/results/PHASE_B2_FULL_RUN_VERIFICATION_V1.json`（`status: PASS`）

| 層 | 内訳 |
|---|---|
| **Observer raw** | `ESTIMATE` **388** / `ABSTAIN` **336**（計 724） |
| **Scorer（truth開封後）** | `RECOVERED` **387** / `WRONG` **1** / `ABSTAIN` **336** |

### 6.3 first ABSTAIN 閾値

出典：`05_PHASE_B2/results/transition_patterns.json`、`PHASE_B2_FULL_RUN_VERIFICATION_V1.json`

| 対象 | first any ABSTAIN | first majority ABSTAIN (`n>=11`) | first all-seed ABSTAIN (`n=20`) | first WRONG |
|---|---|---|---|---|
| `delta_2` | `1.4e-9` | `1.6e-9` | `1.8e-9` | null |
| `delta_3` | `1.4e-9` | `1.6e-9` | `1.8e-9` | **`1.6e-9`** |
| `alpha_2` | `1.8e-9` | `2.6e-9` | `3.0e-9` | null |
| `alpha_3` | `1.8e-9` | `2.6e-9` | `3.0e-9` | null |

delta 側が alpha 側より低いsigmaで先に棄権へ移る、という順序が20 seed中ほぼ全てで再現している（`onset_order_by_seed`：18 seed で `delta_2, delta_3` が同着先行、seed 41014 と 41017 のみ delta 内で順序が分かれる）。

この閾値は**事前固定grid上の operational first failure** であり、連続sigmaの真の閾値ではない。境界精度はgrid間隔までである。

### 6.4 80行の first-failure attribution

20 seed × 4 対象 = 80 行（`05_PHASE_B2/results/first_failure_by_seed.csv`）を、blocking layer へ写像した件数：

| blocking layer | 件数 |
|---|---|
| `DELTA_BRACKET` | **18** |
| `ALPHA_GEOMETRY` | **29** |
| `MIXED/TIE` | **33** |
| `PERIOD_COMMON` | **0** |

（`MIXED/TIE` は人間可読な表示名。`PHASE_B2_FULL_RUN_VERIFICATION_V1.json` では JSON-safe key `MIXED_OR_TIE` として同じ33行を指す。）

- **TIE の扱い**：同じ最小DAG深さで複数のblocking nodeが同時に不成立になった場合、恣意的に1つを選ばず `TIE` とし、**該当する全 canonical node ID を昇順配列で保持した**。80行中33行が `blocking_tie=True` である（例：seed 41014 の `delta_3` は `D_BRACKET_UNIQUE[k=3,ell=2]`, `[ell=3]`, `[ell=4]` の3件同着）。
- 4対象の first-abstain sigma が完全に一致した seed は **0** 件。
- decision・node ともに非単調遷移は **0** 件。
- TIE を除いた単独 blocking node の内訳は `A_MINIMUM_COUNT_EQ1[...]`（alpha側、計29）と `D_BRACKET_UNIQUE[...]`（delta側、計18）に限られ、period 層の node は1件も first blocking にならなかった。

### 6.5 precursor と blocking の区別

80行すべてで `first_precursor_sigma = 1e-9`（B2 gridの**最小の正のsigma**）、`first_precursor_nodes` は各対象について `PR_LABEL_MATCH[...,j=5941]` と `PR_PERIOD_ACCEPT[...,p=8,j=5941]` の2件同着であった。

**観測事実**：zero-noise reference で PASS していた period 層の述語は、公式ABSTAINよりかなり早い段階で、格子点 `j=5941` において既にFAILしていた。**precursor は B2 で測定した最小の正のsigmaである `1e-9` ですでに検出されており、真のonsetは `<= 1e-9` として left-censored である。**「`1e-9` で初めて発生した」とは書けない。

なお B1 の `1e-10` / `3e-10` で全判定が `RECOVERED` であったことは、これらの tracer node が PASS だったことを意味しない。B1 では同じ trace を記録していない（trace は B2 で新設）。この空白を埋める追加 run は実施していない。

**解釈（限定）**：共通の precursor は早期に見えた。しかし **first ABSTAIN を直接引き起こした blocking 原因としては、period 共通層は支持されなかった**（`PERIOD_COMMON=0`、`H_U_common_upstream.first_blocking_rows=0`）。delta 側は主に bracket の一意性、alpha 側は主に superstable geometry の一意最小値が blocking node だった。

**注意**：precursor は「先に劣化した」だけであり、因果的な原因ではない。censored 区間の外へ外挿もしない。

### 6.6 B1 の `WRONG=0` と B2 の `WRONG=1` は矛盾ではない

両者は**同じsigma集合を測っていない**。

- B1 の正のgridは `1e-10, 3e-10, 1e-9, 3e-9, 1e-8, 3e-8, 1e-7` であり、`1e-9` と `3e-9` の間には測定点がない。
- B2 はその間を7水準で埋めた。誤答が現れたのは `sigma=1.6e-9`、すなわち **B1 が測っていなかった水準**である。
- B1・B2 の重複端点（`1e-9`, `3e-9`）40 runs は object-level で一致することが検証済み（`endpoint_matches: 41`、zero-noise controlを含む）。

したがって正しい記述は「B1 の `ABSTAIN-only loss` は、B1 の粗いgridの範囲での観測であり、細分化grid全体へ拡張できない」である。B1の結果は誤りではなく、**分解能の範囲が違う**。

---

## 7. 唯一のfalse acceptance

出典：`05_PHASE_B2/results/score_records.jsonl`（`run_index: 74`）、`05_PHASE_B2/results/observer_records.jsonl`

| 項目 | 値 |
|---|---|
| run_id | `sigma_1.6e-09_seed_41014` |
| run_index | 74 |
| 対象 | `delta_3` |
| Observer status | `ESTIMATE` |
| estimate | `13.918719211822673` |
| truth | `4.656251017651357` |
| signed / absolute error | `9.262468194171316` |
| relative error | `1.9892544794209492` |
| Scorer classification | `WRONG` |

同じ run で `delta_2` は `BIFURCATION_BRACKET_UNRESOLVED` により `ABSTAIN` している。Observer の `observable_audit` によれば、この run の `b_hat_v` は `1: null, 2: 0.5025, 3: 0.9582258064516129, 4: 0.9909677419354839` であった（zero-noise A3 では `1: 0.0799..., 2: 0.8054..., 3: 0.9581..., 4: 0.9910...`）。

Phase B2 では seed 41014・sigma 1.6e-9 の `delta_3` に1件の false acceptance を観測した。保存済み入力に対する限定的な replay / Decimal 診断（出力記録：`result_41014.txt`）は `INCIDENT_41014_NOTE.md` を参照。

**この1件が示すこと（限定）**：固定Observer の `ESTIMATE` ラベルは、真の有限階層構造を安全に回復したことを意味しない。
**この1件が示さないこと**：この sigma における誤答確率、他 seed での再現性、Observer 全体の欠陥、ノイズが真の新しい分岐を作ったこと。

---

## 8. 再現性・封印・監査境界

### 8.1 実行順序

1. **Phase A**：4 deterministic run → 16 decisions → truth 開封 → Scorer 採点。
2. **Phase B1**：Phase A の全16ファイルを immutable manifest として1度だけ封印し、benchmark 前後・full run 後に別々の audit file で照合（`expected=16 / observed=16 / missing=0 / extra=0 / mismatch=0`、`04_PHASE_B1/results/phase_a_audit_postfullrun.json` で `pass: true`、Phase A 実装manifest 27/27 一致）。truth は全Observer出力確定後にのみ開封。
3. **Phase B2**：
   - `sigma=0` の zero-noise complete trace reference を先に作成・封印。
   - 正の180 run では、各 run の frozen Observer output を **先に** write-once 確定し、その後にのみ diagnostic tracer を実行。
   - 181件（180 traces + 1 reference）について、トレーサー再構成 decision を frozen Observer と object-level 照合（`reconstruction_matches: 181`）。
   - **raw Observer output と全 trace を `observer_trace_seal_manifest.json` で hash 封印（`truth_opened: false`）してから、初めて truth を開き Scorer を実行**（`truth_access_record.json`：`purpose: SCORER_ONLY`, `raw_seal_sha256` 参照）。
   - full run 検証：`PHASE_B2_FULL_RUN_VERIFICATION_V1.json` は `status: PASS`、実装manifest 67/67、run artifacts 16/16、baseline checks 17/17、endpoint matches 41、error ledger なし。

各PhaseのPLANは、作成時点の許可範囲と停止状態を固定した**事前文書**であり、実行後に `COMPLETE` へ書き換えていない。benchmark と full run は、それぞれ後続の別 approval artifact によって承認されている（原環境：Phase A `PHASE_A_FULL_RUN_APPROVAL_V1.json`、B1 `PHASE_B1_FULL_RUN_APPROVAL_V1.json` = `7CCBD452...`、B2 `FULL_RUN_APPROVAL_V1.json` = `C93AD681...`）。B2 の approval は `fixed_design_sha256=5B564B6E...`、181 runs / 724 decisions / 180 positive traces / 1 zero reference / 724 classifications、retry なし・代替seedなしを承認している。approval 本文はこの配布パッケージには同梱されていない。

### 8.2 runtime

CPython `3.13.15` / NumPy `2.3.2`（Windows, AMD64）。B1・B2 とも exact version 一致を実行前提条件としている（`04_PHASE_B1/results/run_provenance.json` の `runtime_fingerprint`）。

### 8.3 監査境界（何が保証されていないか）

- 封印・照合は**すべて同一の実装系統内で生成・検証**されている。第三者による独立実装での再検証、敵対的改変耐性の検査は行っていない。
- Observer の判定を独立実装で再現した検証は行っていない。
- 診断トレーサーは Observer と同じ公開入力を読む別moduleであり、独立実装ではない。
- **形式保証を伴う鑑識基盤（Stage 1〜4 の承認・manifest chain・self-verifier を含む設計）は、incident 本番を実行する前に繰り返し FAIL / HOLD となり中止した。** その後、主張範囲を狭めた単発 script（§7、`INCIDENT_41014_NOTE.md`）へ切り替えた。中止した基盤のテストPASS数は、incident の科学的信頼性には算入しない。
- 約2GB の per-run diagnostic trace（`diagnostic_trace_index.jsonl`）はこの配布パッケージに含まれていない。hash は `observer_trace_seal_manifest.json` に記録されている。

---

## 9. 限界と明示的な非主張

### 9.1 設計上の限界

- 単一写像族（ロジスティック写像の principal cascade）、単一 Observer 実装、単一 payload（A3）、固定20 paired noise fields、固定sigma grid。
- 20 seed は**記述的な固定反復**であり、母集団標本ではない。p値、信頼区間、FPR保証、多重比較補正、連続境界のcurve fittingは一切行っていない。
- 5% 相対誤差は事前固定の操作的閾値であり、統計的保証ではない。感度分析も行っていない。
- 観測ノイズのみ。過程ノイズ、パラメータノイズ、色付きノイズ、量子化、欠測、外れ値は扱っていない。
- sigma 境界の分解能は grid 間隔まで。連続sigmaの真の閾値へ外挿しない。

### 9.2 明示的な非主張

本研究は次のいずれも主張しない。

- 「AIがFeigenbaum普遍性を発見した」／「未知の法則を発見した」。対象は既知の有限階層量の**回復可能性の測定**である。
- 「Observer の安全性を証明した」。むしろ `WRONG=1` は反対方向の観測である。
- 「ノイズ耐性の普遍的限界を測った」。測ったのは固定手続きの破綻曲線である。
- 「ノイズが真の新しい分岐を誘起した」。観測されたのは検出器の操作的ラベル付けの失敗である。
- 「B2 の診断量を使えばより良いABSTAIN規則が作れる」。規則設計は B2 の範囲外であり、未検証である。

### 9.3 未実行のもの

Phase C、Phase B3、新seedによる独立評価、plausibility guard や系列横断整合性guardの設計・実装・評価は、**いずれも未実行**である。完了した成果として扱ってはならない。

---

## 10. ファイル案内

（相対パスはこの配布パッケージのルート基準）

| 区分 | パス | 内容 |
|---|---|---|
| Phase A PLAN | `03_PHASE_A/PLAN_PHASE_A_FINITE_LEVEL_RECOVERY.md` | 固定条件、Observer規則、truth定義、採点規則 |
| Phase A 結果 | `03_PHASE_A/results/phase_a_score.json` | 16 decision の採点、summary、truth開封後診断 |
| Phase A raw | `03_PHASE_A/results/A3.json` | A3 の Observer raw 出力（zero-noise reference） |
| Phase A truth | `03_PHASE_A/results/truth.json` | 100桁 truth と求解 verification |
| Phase A 実装 | `03_PHASE_A/implementation/` | Observer / Scorer / Generator source、契約、manifest |
| Phase B1 PLAN | `04_PHASE_B1/PLAN_PHASE_B1_GAUSSIAN_BREAKDOWN.md` | 凍結hash、noise設計、bracket判定規則 |
| Phase B1 結果 | `04_PHASE_B1/results/summary.csv` | 32行の固定集計 |
| Phase B1 境界 | `04_PHASE_B1/results/breakdown_brackets.json` | 4対象の回復喪失bracket |
| Phase B1 raw | `04_PHASE_B1/results/seed_records.jsonl` | 141 run の provenance と Observer 出力 |
| Phase B1 監査 | `04_PHASE_B1/results/phase_a_audit_postfullrun.json` | Phase A 不変性の事後監査 |
| Phase B2 PLAN | `05_PHASE_B2/PLAN_PHASE_B2_FIRST_FAILURE_ATTRIBUTION.md` | DAG、node registry、封印順序、完了条件 |
| Phase B2 判定 | `05_PHASE_B2/results/decision_summary.csv` | 40行（4対象 × 10 sigma） |
| Phase B2 帰属 | `05_PHASE_B2/results/first_failure_by_seed.csv` | 80行の first-failure / precursor |
| Phase B2 パターン | `05_PHASE_B2/results/transition_patterns.json` | 閾値、onset順序、tie、非単調 |
| Phase B2 採点 | `05_PHASE_B2/results/score_records.jsonl` | 724件の Scorer 分類 |
| Phase B2 封印 | `05_PHASE_B2/results/observer_trace_seal_manifest.json` | raw seal（`truth_opened: false`） |
| Phase B2 検証 | `05_PHASE_B2/results/PHASE_B2_FULL_RUN_VERIFICATION_V1.json` | full run 検証 `PASS` |
| Incident | `06_INCIDENT_41014/diagnose_41014.py` | 単発・非監査の参考診断 script |
| Incident 出力 | `result_41014.txt` | 上記 script の標準出力を事後保存したTXT。incident 数値の出典 |
| Incident note | `INCIDENT_41014_NOTE.md` | §7 の1件に限定した後付けnote |
| 会話ログ | `01_LOGS/ログ6〜8` | Phase A/B1/B2 に対する解釈。数値の正本ではない |
| 会話ログ | `01_LOGS/ログ9〜13` | 正式鑑識基盤の設計失敗・中止の経緯 |
| 会話ログ | `01_LOGS/ログ14` | 単発診断への切替と実行結果 |
| 背景 | `02_BACKGROUND/LITERATURE_REVIEW.md`, `REVIEW_SCOPE.md` | 2026-08-10 に別途実施した先行研究レビュー。本記録の数値主張には使用していない |

**証拠の優先順位**：sealed result / CSV / JSON / provenance / verification ＞ 各PhaseのPLAN・設定・実装 ＞ 会話ログの解釈 ＞ 単発診断 ＞ 失敗履歴。会話ログの表現と正式成果物が食い違う場合は、正式成果物を優先する。ログ中の称賛・推奨・推測・将来計画は測定事実ではない。

---

## 11. 結論

固定された blind Observer は、理想条件では匿名の有限観測のみから4つの有限階層量を 5% 閾値内で回復した（Phase A、16/16）。観測Gaussian noise 下では、粗いgridで4対象すべてが `1e-9 < sigma <= 3e-9` の区間で回復を失い、そのgridの範囲では誤答を伴わなかった（Phase B1、`WRONG=0`）。同区間を細分化すると、破綻の直接原因は共通の period 認識層ではなく delta 側の bracket 一意性と alpha 側の superstable geometry に分かれ、80行中33行は同深度の同時失敗であった（Phase B2、`PERIOD_COMMON=0`）。そして粗いgridでは見えなかった false acceptance が1件現れた。

したがって本研究の到達点は、有限階層量の回復可能性を示したことと同時に、**Observer が付けた `ESTIMATE` という構造ラベルが、構造そのものの回復を意味しない場合が固定設計内で実測された**ことである。

これらはすべて、固定 A3 payload・固定Observer・固定noise fields・固定sigma grid における algorithmic かつ operational な findings であり、情報理論限界、母集団誤り率、普遍的安全性、あるいは新しい一般理論を示すものではない。

---

## 付録（本文外）：残存する限定事項

以下は研究記録本文の主張ではなく、読者が結果を過大解釈しないための3点の注記である。

1. **precursor onset は left-censored である。** B2 の period 層 precursor は、測定した最小の正のsigma `1e-9` ですでに検出されており、真のonsetは `<= 1e-9` である。B1 のより小さいsigmaでは同じ trace を記録していない。
2. **41014 の replay / Decimal 診断は参考診断である。** 直前実行の標準出力を事後保存した `result_41014.txt` に記録された、単一の保存済み入力に対する単発・非監査の診断であり、原因も安全性も結論しない（`INCIDENT_41014_NOTE.md`）。
3. **この incident が単一 noise realization 固有か、別seed・別判定器でも反復するかは未検証である。**
