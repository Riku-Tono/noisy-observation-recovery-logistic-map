# Discussion note: structure appearing in observation space, and a stably reproducible misidentification

## Standing of this note

This note is not a formal report of the Phase A, B1, or B2 results. It is a discussion piece organising what came into view from the single false acceptance observed in Phase B2 and from the limited replay / Decimal diagnostic that followed.

It keeps three things apart: measured facts, the interpretations those facts permit, and hypotheses that remain unverified.

## 1. What happened

The subject is the following single case from Phase B2.

- condition: A3
- seed: 41014
- observation-noise scale: \(\sigma=1.6\times10^{-9}\)
- estimand: \(\delta_3\)
- Observer estimate: \(13.918719211822673\)
- truth: \(4.656251017651357\)
- Scorer classification: `WRONG`

What the noise was added to in this experiment was not the internal state of the logistic map. Writing \(y_0\) for the noise-free generated observation series, the values handed to the Observer were

\[
y_{\mathrm{obs}}=y_0+\sigma Z
\]

The map's parameter, its initial state, its internal states, and the truth were all left unchanged.

No new \(2\to4\) bifurcation therefore occurred in the map itself. What did occur is that the noisy observation series actually handed to the Observer contained a local arrangement that the frozen Observer certified as a \(2\to4\) boundary.

## 2. Within the observation series, it *was* structure

The brackets the Observer adopted here were approximately

\[
b_2=0.5025,\qquad
b_3\approx0.95823,\qquad
b_4\approx0.99097
\]

which yielded

\[
\delta_3=\frac{b_3-b_2}{b_4-b_3}\approx13.9187
\]

The anomalous estimate did not erupt out of unrelated arithmetic runaway. There is a computational path: the Observer accepted a candidate well to the left as \(b_2\), and the subsequent ratio computation greatly amplified that displacement in position.

In a replay using the stored binary64 input, the official raw result was reproduced. Furthermore, within the scope of promoting that input to Decimal 50, 100, and 200 digits and re-evaluating the downstream arithmetic, the period labels, candidate endpoints, and status did not change, and the numerical difference in \(\delta_3\) stayed at roughly \(2.66\times10^{-14}\) in the trailing digits.

In this sense, for seed 41014's stored observation series, the local pattern the Observer certified was stable. The same input combined with the same decision rules reproduced the same structural certification and the same erroneous estimate.

## 3. "False" and "real" are not in contradiction

This case shows the need to separate three layers.

| Layer | Its standing in this case |
|---|---|
| Structure of the noise-free map | \(b_2=0.5025\) is not a true finite-level boundary that was to be recovered |
| The noisy observation series | A local transition-like pattern satisfying the Observer's period conditions was present |
| The Observer's certification | It accepted that pattern as true finite-level structure and emitted an erroneous \(\delta_3\) |

The structure is therefore not a hallucination the machine conjured out of nothing. The Observer was reading an actual arrangement of input values. But the meaning it assigned to that arrangement did not correspond to the structure of the noise-free map that was supposed to be recovered.

One can put it this way: as a feature of the observation space it was real; as a correspondence to the estimand it was false.

## 4. Stable reproduction does not guarantee truth

Ordinarily, if a result reproduces and the decision does not change as computational precision is raised, confidence in that result increases. Here, however, what was stable was not "recovery of the correct structure" but "the same structural certification for the same observation series".

That is, the following two are distinct.

1. That the decision path is computationally stable
2. That the certified structure corresponds correctly to what was to be recovered

For seed 41014, (1) was confirmed while (2) was denied by the Scorer.

This does not mean reproducibility should be discounted. It means the object that reproducibility guarantees has to be delimited precisely. Read the same input under the same rules, and it is not only correct certifications that reproduce — stable misidentifications reproduce too.

## 5. What did the Observer "create"?

The Observer did not generate the observation values themselves. The noisy values were produced by combining the noise-free observation series with a noise field.

On the other hand, which local arrangement gets carved out as period-2, as period-4, or as a transition bracket is determined by the Observer's rules. It is therefore more accurate to regard the structure in this case as arising within the relation

\[
\text{noise realization}
+
\text{finite observation}
+
\text{decision rules}
\longrightarrow
\text{operational structure}
\]

rather than as belonging to either the observation series alone or the Observer alone.

The Observer did not fabricate values that were nowhere in the input. But it selected a pattern in the input that fit its local conditions, and certified that pattern as structure of the same kind as the recovery target. This "selection and assignment of meaning" is what led to the false acceptance.

## 6. Local consistency and global consistency

The Observer here did check individual local conditions: that the required period labels exist, that bracket candidates are obtained, that the denominator of the ratio is positive, and so on.

At least along the path taken here, however, cross-series questions like the following were not among its decision conditions.

- Are \(b_2,b_3,b_4\) consistent as a single bifurcation series?
- Is it acceptable to pass \(\delta_3\) alone as `ESTIMATE` when \(\delta_2\) is `ABSTAIN`?
- Is the resulting ratio extreme compared with the estimate at the adjacent level?

As a result, it is possible that no distinction was drawn between each part satisfying its local conditions and their combination constituting a meaningful finite-level structure.

This is not a result demonstrating the effectiveness of additional guards. Which consistency conditions ought to be introduced, and whether they would send correct estimates too readily to `ABSTAIN`, remain untested.

## 7. What is currently unresolved

The following cannot be settled from this single incident.

- Whether this pattern is a noise realization specific to seed 41014
- Whether it recurs statistically at the same noise scale and in the neighbourhood of the same \(v\)
- Whether a different period detector, or an independent feature, would also detect structure at the same location
- Which local condition contributed most to the acceptance of the false \(b_2\)
- Whether a cross-series guard could safely turn this one WRONG into an `ABSTAIN`

In particular, repeatedly computing on the same stored input is a different thing from the phenomenon recurring under a different noise realization. The former was confirmed; the latter was not investigated.

## 8. The central question this case raises

What matters in this case is not "did noise create a real bifurcation". The more direct question is:

> When a stable pattern exists within observational data, what conditions must be met before it may be regarded as recovery of structure in the target system?

In Phase A, under ideal conditions, the Observer's operational structure agreed with the truth. In B1, as the noise grew stronger, many decisions moved to `ABSTAIN`. In B2's seed 41014, the structural certification on the observation series remained stable while its correspondence with the truth came apart.

Taken together, the results give a concrete instance of the boundary lying between "structure being visible" and "having correctly recovered the structure of the target".

## 9. Provisional summary

Seed 41014's noisy finite observation series contained a local transition-like pattern that the frozen Observer consistently certified as a \(2\to4\) transition. That certification was stable under replay of the stored input and under downstream re-evaluation at Decimal 50, 100, and 200 digits.

That stability, however, did not mean the finite-level structure of the noise-free map had been correctly recovered. A pattern genuinely present in the observation series was certified by the Observer's local rules as a meaningful bifurcation series, and became a reproducible erroneous estimate by way of the ratio computation.

What this case suggests is not simply that the procedure "was weak against noise". It is that when structure is read out of finite observations, three things have to be handled separately: regular features present in the input, the operational structure the detector constructs, and the structure one wishes to claim about the target system.

This is, however, a discussion at the present stage, not a general conclusion that has passed through repetition at other seeds, confirmation by an independent detector, or evaluation of additional guards.
