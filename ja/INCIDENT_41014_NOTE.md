# INCIDENT NOTE: seed 41014 / sigma 1.6e-9 / delta_3 の false acceptance

**性格**：単一 script による**事後的・非監査の参考診断**。正式な第三者鑑識でも、独立実装による完全検証でも、Observer 全体の安全性評価でもない。

**対象**：`A3 / seed 41014 / sigma 1.6e-9 / run 74 / delta_3` の **1件のみ**。他のseed、他のsigma、他の量、他のPhaseは対象外。

**このnoteの位置づけ**：Phase A〜B2 の正式結果を一切変更しない。また、Phase A〜B2 の信頼性を上げる根拠として使ってはならない。

---

## 1. 何が起きたか（平易な要約）

Phase B2 の1件で、Observer は `delta_3` に対して棄権せず `13.918719211822673` という値を出した。truth は `4.656251017651357` なので、相対誤差は約 `1.99`（約199%）である。同じ run の `delta_2` は bracket 不足で棄権していた。

後から、保存済みの入力ファイルと凍結された Observer を使って1回だけ再実行し、さらに同じ入力を高精度（Decimal）へ持ち上げて後段の計算だけをやり直した。結果は次のとおりだった。

- 同じ `13.918719211822673` が再現し、公式 raw record と object-level で一致した。
- Decimal 50 / 100 / 200 桁のいずれでも、周期ラベルは1点も変化せず、候補となる bracket 端点も判定 status も同一だった。値の変化は末尾の約 `-2.66e-14` に留まった。

したがって、**「binary64 の丸めが主因」という説明はこの診断の範囲では弱い**。数値としては、`delta_3` が使う最初の bracket `b_2` が本来よりかなり左の位置で受理され、その差が比の計算で増幅された、という経路と整合する。

---

## 2. 正式結果としての1件

出典：`05_PHASE_B2/results/score_records.jsonl`（`run_index: 74`）、`05_PHASE_B2/results/observer_records.jsonl`

| 項目 | 値 |
|---|---|
| run_id | `sigma_1.6e-09_seed_41014` |
| condition | `A3` |
| Observer status | `ESTIMATE` |
| estimate | `13.918719211822673` |
| truth | `4.656251017651357` |
| relative error | `1.9892544794209492` |
| Scorer classification | `WRONG` |
| 同 run の `delta_2` | `ABSTAIN`（`BIFURCATION_BRACKET_UNRESOLVED`） |
| 同 run の `alpha_2`, `alpha_3` | いずれも `ESTIMATE` → `RECOVERED` |

同 run の `observable_audit.b_hat_v`（Observer が匿名座標 `v` 上で受理した分岐点候補）：

| `ell` | この run | zero-noise A3 参照値 |
|---|---|---|
| 1 | `null`（未解決） | `0.07991935483870968` |
| 2 | `0.5025` | `0.8054032258064516` |
| 3 | `0.9582258064516129` | `0.9581451612903226` |
| 4 | `0.9909677419354839` | `0.9909677419354839` |

zero-noise 参照は `03_PHASE_A/results/A3.json`。

---

## 3. 実施した診断

使用 script：`06_INCIDENT_41014/diagnose_41014.py`（SHA-256 `E54AD11346099676F60E3F57A14BF149EA820C90D52E1E5FF98EF0079CB42ADC`、配布パッケージ内のファイルと一致することを確認済み）

script は原環境の絶対パスを参照する単発 script であり、この配布パッケージ（README執筆用の抜粋）だけでは再実行できない。A3 payload、noise fields、約2GBのB2 trace、固定runtimeは意図的に同梱されていない。

script が行うのは次の2点だけである。

1. **frozen replay**：保存済み A3 payload と B1 の noise field から binary64 の `y64` を再構成し、凍結 Observer を1回実行して、公式 B2 raw output と object-level 比較する。
2. **Decimal 再評価**：同じ保存済み binary64 入力を Decimal 50 / 100 / 200 桁へ持ち上げ、period label 判定と bracket 候補・`delta_3` の後段演算だけを再計算する。

script は truth も Scorer も読まない。script 自身が冒頭で `REFERENCE DIAGNOSTIC ONLY; NO FORENSIC RELIABILITY CLAIM`、末尾で `one fixed stored input; no cause or safety conclusion` と範囲を明示している。

### 3.1 出力の出典

数値の出典は次のTXTである。

- file: `result_41014.txt`
- bytes: `3,753`
- SHA-256: `F2EDE3AF717889347A5DA224CC20AF9DCE90C5B6AE3200CB1F65A9EAD4FDA7CA`

**このTXTは新しい計算結果ではなく、`diagnose_41014.py` が exit `0` で完了した直前実行の標準出力を、再実行せずに事後保存した記録である。**

診断時の環境と入力 hash（同TXT記載）：

| 項目 | 値 |
|---|---|
| Python / NumPy | CPython `3.13.15` / NumPy `2.3.2` |
| `observer.py` SHA-256 | `A72C880213D617C9479D8F4C9DFD3E7F277169CBB236FB9C4EDFEE0B800D7EB4` |
| A3 payload SHA-256 | `14D404ACC81690174682CA5B31C758A40B465DCD81104307E7A5652C6BD04D18` |
| noise fields SHA-256 | `0EAB212E1EC8CC001DFBEF3CF8E5FAD4A15540754109B737FC0404E1861E2B3E` |

これらは B1 / B2 の封印値と一致する。

---

## 4. 診断結果（観測事実）

### 4.1 binary64 frozen replay

| 項目 | 結果 |
|---|---|
| `official_object_match` | `True` |
| `delta_3_match` | `True` |
| `delta_3` | `ESTIMATE` / `13.918719211822673` |
| elapsed | `0.377932` 秒 |
| candidate endpoints（格子index） | `ell=2: (3115, 3116, gap 0)` / `ell=3: (5940, 5942, gap 1)` / `ell=4: (6143, 6145, gap 1)` |

### 4.2 Decimal 50 / 100 / 200 桁

3つの精度すべてで次が成立した。

| 項目 | 結果 |
|---|---|
| `labels_changed_vs_binary64` | `0` |
| label_counts | `{0: 18, 1: 495, 2: 4496, 4: 946, 8: 203, 16: 43}`（公式 run の `observable_audit` と整合） |
| candidate endpoints | binary64 replay と完全一致 |
| status | `ESTIMATE` |
| `estimate_difference_vs_binary64_exact` | `-2.663871964896043478955143200E-14`（3精度で同一） |
| elapsed | `0.840536` / `0.688308` / `0.965334` 秒 |

Decimal 100 / 200 桁で記録された bracket 値：

```
b_2 = 0.502500000000000002220446049250313080847263336181640625
b_3 = 0.958225806451612871494916134906816296279430389404296875
b_4 = 0.99096774193548387010821443254826590418815612792968750

delta_3 = (b_3 - b_2) / (b_4 - b_3) = 13.91871921182264593805302584132463227397693395010716...
```

### 4.3 最終ラベル

| 項目 | 結果 |
|---|---|
| `precision_paths_stable_50_100_200` | `True` |
| `changed_path_from_binary64` | `False` |
| `FINAL_LABEL` | `REPLAY_MATCH_AND_PRECISION_STABLE` |

`b_2` だけが本来の位置（zero-noise では約 `0.8054`）から大きく左へ移動しており、分子が大きく分母が小さいという組合せが比の値を約13.9まで増幅している。

**付随的な観測**：`ell=3` の候補 bracket に含まれる唯一の未解決点は格子index `j=5941` であり、これは Phase B2 の80行すべてで `first_precursor_nodes` に現れた index と同じである。これは並置された観測事実であり、因果関係の主張ではない。

---

## 5. 限定解釈

この診断の範囲で言えるのは次の1文だけである。

> **binary64 の丸めだけが主因という説明は弱く、観測された計算経路は false `b_2` bracket acceptance と downstream ratio amplification という直近機構に整合する。**

「整合する」であって「証明した」ではない。noise realization がそのラベル配置を生じさせた根本原因まで確定した、とは書かない。

### 実装上の直近機構（記述として）

同じ run で `delta_2` が棄権しているのに `delta_3` が `ESTIMATE` になり得たのは、Observer が各 `k` を独立に判定し、`delta_3` に必要な局所条件（`ell=2,3,4` の bracket 候補が各1件、分母が正）だけが満たされたためである。系列横断の整合性を要求する guard（`delta_2` の成否との連動、bracket 間隔の系列としての妥当性、比が想定範囲を大きく外れた場合の再確認など）は存在しなかった。

**ただしこれは実装の記述であり、そのような guard を追加すれば改善するという主張ではない。新 guard の有効性は一切試験していない。**

### `b_1=null` について

run 74 では `b_hat_v[1]` が未解決であり、これが `delta_2` の `ABSTAIN` を直接説明する。一方 `delta_3` は `b_1` を使わないため、別の局所条件だけで `ESTIMATE` になった。

**`b_1` の未解決と false `b_2` 受理が同一の noise 由来のラベル崩れから生じたかどうかは、現在の資料からは判定できない。** 共通原因とは断定しない。これは系列横断的な整合性が検査されていなかった一例として記載するに留める。

---

## 6. このnoteが示さないこと

- **noise の力学的な根本原因**：どの観測値が `v≈0.5025` に見かけ上の `2→4` 遷移を作ったのか、観測列まで遡る解析は行っていない。
- **真の新しい分岐の存在**：ノイズが元の写像に真の周期倍化を誘起したことは示していない。観測されたのは、ノイズ入り有限観測に対する Observer の**操作的ラベル付け**の失敗である。
- **一般的因果**：`sigma=1.6e-9` で false acceptance が必ず起きること、あるいはその確率がいくらかは示していない。今回分かるのは、この1つの noise realization（seed 41014）でそれが起きた、ということだけである。
- **単一 realization 固有かどうか**：別seed・別判定器でも同種の false acceptance が反復するかは**未検証**である。
- **Observer 全体の欠陥**：他 seed、他 sigma、他の対象量における同種の失敗の有無は調べていない。
- **第三者監査可能性**：形式保証を伴う鑑識基盤は本番実行前に FAIL / HOLD となり中止された。そこで得られたテストPASS数は、この診断の信頼性には算入しない。この診断は単一 script・単一実行・非監査である。

---

## 7. 診断自体の限界

- 保存済み binary64 入力を出発点としており、observation 生成そのものを高精度でやり直したものではない。「高精度」とは**保存済み binary64 入力を持ち上げた後段演算**を指す。
- 精度安定性の判定（`precision_paths_stable`）は、labels 一致・candidate 一致・status 一致・`delta_3` の先頭約30桁一致で構成されている。50/100/200 桁の完全一致を検査したものではない。
- noise field と seed の対応は配列 index（`fields[seed - 41001]`）で決めており、manifest による seed 順の明示照合は行っていない。ただし対応が誤っていれば直後の frozen replay が公式 B2 output と object-level 一致しないため、実地の整合チェックにはなっている。
- 実行は1回のみ。反復実行、別環境での再現、別実装による検証は行っていない。
- 出力は事後保存された標準出力であり、実行時に封印された成果物ではない。

---

## 8. 第三者が再現する場合

seed と sigma を渡して noise を作り直しても、乱数系統が異なれば同じ noise 列にはならず、`13.9187` は保証されない。再現を試みる場合は、seed ではなく次を渡す必要がある。

- 保存済み A3 payload（SHA-256 `14D404ACC81690174682CA5B31C758A40B465DCD81104307E7A5652C6BD04D18`）
- B1 の noise fields（SHA-256 `0EAB212E1EC8CC001DFBEF3CF8E5FAD4A15540754109B737FC0404E1861E2B3E`）と該当 seed の対応
- Observer source（SHA-256 `A72C880213D617C9479D8F4C9DFD3E7F277169CBB236FB9C4EDFEE0B800D7EB4`）
- runtime：CPython `3.13.15` / NumPy `2.3.2`
- 期待値：`13.918719211822673`
