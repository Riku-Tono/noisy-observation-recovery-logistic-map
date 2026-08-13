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

- Whether the candidate substitution observed for seed 41014 is specific to that stored realization or recurs beyond the fixed fields examined so far
- How often comparable candidate substitutions occur at the same noise scale or near the same \(v\); the fixed-seed records do not define a population probability
- Whether a different period detector, or an independent feature, would also detect structure at the same location
- Which local condition contributed most to the acceptance of the false \(b_2\)
- Whether a cross-series guard could safely turn this one WRONG into an `ABSTAIN`

In particular, repeatedly computing on the same stored input is a different thing from the phenomenon recurring under a different noise realization. The former was confirmed. A later pre-specified Phase D0 probe observed no additional `delta_3 WRONG` among 40 fixed fresh seeds at the same A3/sigma condition. A separate post-hoc reconstruction of those same inputs found no unique false-only `ell=2` candidate state. Those are bounded fixed-set observations, not a recurrence probability or a safety result.

## 8. Research memo: propagation and preservation under local perturbations

The motivating surprise is two-sided. It is not only that a very small perturbation could propagate so far. It is also that, across many other perturbed inputs, so much of the reference-associated structure was preserved well enough for ambiguity to remain visible.

This shifts the emphasis away from treating seed 41014 as the research object in itself. Seed 41014 is useful as a contrast case because it exposes two questions at once.

1. **Propagation:** how can a local threshold crossing alter a period label, change candidate identity, move a bracket far along the observation axis, and then be amplified by a ratio into a large final error?
2. **Preservation:** when local labels and candidate sets are being perturbed elsewhere, what preserves a reference-associated candidate or otherwise keeps the disturbance visible as nonuniqueness, so that the Observer can `ABSTAIN` rather than certify a unique alternative?

The fixed candidate-set comparisons make the second question concrete. In the original 20 fields at `sigma=1.6e-9`, 18 `ell=2` cells retained a reference-associated candidate while adding one or more alternatives and becoming nonunique. Seed 41017 remained exact reference-only unique. Seed 41014 alone lost the reference-associated candidate and retained a unique non-reference alternative. In the later post-hoc reconstruction of the 40 D0 inputs, the reference-associated `ell=2` candidate was retained in 40/40: 39 also contained alternatives and were nonunique, corresponding to all 39 `delta_3 ABSTAIN` outcomes, while seed 41042 remained exact reference-only unique and was `RECOVERED`.

These observations suggest a useful descriptive distinction:

\[
\text{local perturbation}
\longrightarrow
\begin{cases}
\text{reference retention + alternatives}
&\longrightarrow \text{detectable ambiguity / ABSTAIN},\\
\text{reference loss + unique alternative}
&\longrightarrow \text{false uniqueness / possible WRONG}.
\end{cases}
\]

This is not yet a general mechanism. The first line is a compact description of the observed `ell=2` candidate states in the fixed comparisons, and the second line describes the stored endpoint for seed 41014. It does not show that every reference-retaining perturbation is safe, that every unique alternative is wrong, or that candidate identity alone determines the final decision at all levels.

The open question is therefore not simply "why did 41014 fail?" It is:

> **Under widespread local decision perturbations, what structural conditions allow uncertainty to remain observable as candidate multiplicity or reference retention, and what configurations instead let that uncertainty disappear as a unique but incorrect alternative?**

Equivalently, why did the disturbance propagate as far as it did in the incident path, and why was comparable disturbance absorbed or exposed as ambiguity in so many other paths?

The existing records narrow the question but do not answer it. The number of local label or predicate changes alone is insufficient: seed 41014 was not the most broadly perturbed field. Maximum movement or near-threshold counts alone are also not established separators. What may matter is the configuration—where a transition occurs, which period predicate changes, in which direction, and how those changes jointly map onto reference identity, alternative creation, and candidate-set cardinality.

For that reason, `ABSTAIN` is not merely a failed performance outcome in this memo. It is evidence that ambiguity remained observable to the decision rule. The object of interest becomes the transformation

\[
\text{local residual movement}
\rightarrow
\text{label configuration}
\rightarrow
\text{candidate-set topology}
\rightarrow
\text{visible ambiguity or false uniqueness}.
\]

This is a question prompted by the completed fixed studies, not an additional result or a proposal that has already been tested. It does not authorise added seeds, new sigma levels, a guard, or a causal claim.

## 9. The central question this case raises

What matters in this case is not "did noise create a real bifurcation". The more direct question is:

> When a stable pattern exists within observational data, what conditions must be met before it may be regarded as recovery of structure in the target system?

In Phase A, under ideal conditions, the Observer's operational structure agreed with the truth. In B1, as the noise grew stronger, many decisions moved to `ABSTAIN`. In B2's seed 41014, the structural certification on the observation series remained stable while its correspondence with the truth came apart.

Taken together, the results give a concrete instance of the boundary lying between "structure being visible" and "having correctly recovered the structure of the target".

## 10. Provisional summary

Seed 41014's noisy finite observation series contained a local transition-like pattern that the frozen Observer consistently certified as a \(2\to4\) transition. That certification was stable under replay of the stored input and under downstream re-evaluation at Decimal 50, 100, and 200 digits.

That stability, however, did not mean the finite-level structure of the noise-free map had been correctly recovered. A pattern genuinely present in the observation series was certified by the Observer's local rules as a meaningful bifurcation series, and became a reproducible erroneous estimate by way of the ratio computation.

What this case suggests is not simply that the procedure "was weak against noise". It is that when structure is read out of finite observations, three things have to be handled separately: regular features present in the input, the operational structure the detector constructs, and the structure one wishes to claim about the target system. The later fixed-seed comparisons add a complementary question: not only how local perturbation can propagate into a stable misidentification, but how candidate identity and visible ambiguity can remain preserved despite widespread local disturbance.

This is, however, a discussion at the present stage, not a general conclusion that has passed through repetition at other seeds, confirmation by an independent detector, or evaluation of additional guards.
