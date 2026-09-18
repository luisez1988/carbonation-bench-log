# Carbonation Bench Log

A browser tool for running and reducing carbonation experiments on lime-treated soil, together with the phase-diagram derivation it implements.

**Live page:** https://luisez1988.github.io/carbonation-bench-log/ *(fill in once Pages is enabled)*

Nothing is uploaded anywhere. The page runs entirely in your browser and keeps records in that browser's local storage.

---

## Why this exists

Carbonation experiments are normally reduced with spreadsheets whose phase relations are never written down. Two errors follow easily and are hard to see:

- **Pre-existing carbonate is counted as product.** Both the soil and the hydrated lime carry CaCO<sub>3</sub> before the test. A degree of carbonation that divides total measured carbonate by a theoretical maximum counts that pre-existing carbonate as if the reaction had made it.
- **Unreactive lime is counted as lime that failed to react.** Hydrated lime is typically 85–90 % Ca(OH)<sub>2</sub>. Dividing by the whole lime mass depresses the reported conversion by at least the impurity fraction.

This tool applies the corrected relations instead. For one real specimen the two corrections together move the reported degree of carbonation from 0.84 to 1.02.

The full derivation, including the five-way disjoint partition of the solid phase that makes the bookkeeping unambiguous, is in [`derivation/Phase_diagram_derivations.md`](derivation/Phase_diagram_derivations.md).

---

## The four stations

The tabs follow the order of an actual test.

| | Station | What it does |
|---|---|---|
| 1 | **Mix design** | Mould volume and target void ratio, saturation and lime content give the batch masses. The lime is treated as a composite of portlandite, carbonate and inert, so its effective specific gravity follows from the assay. Water can instead be set from a target lime-to-water ratio. |
| 2 | **Compaction** | Per-lift target mass and depth; records what was actually placed and returns the as-built void ratio, saturation and dry density. |
| 3 | **Calcimetry** | Calibrates the vessel from a known reagent mass, converts pressure rise to carbonate mass, and sizes the sub-sample so the expected rise lands safely on the gauge. |
| 4 | **Reduction** | Oven-dry mass and pressure at each depth give new carbonate, degree of carbonation and binder content, with an independent gravimetric cross-check, CO<sub>2</sub> accounting, a depth profile, CSV export and a two-page PDF record. |

A **Materials** panel holds the batch constants shared by every specimen.

---

## Implemented relations

With $\Lambda$ the carbonate mass fraction of the pre-reaction mixture, $m_d$ the post-test oven-dry mass of a digested sub-sample and $m_{ct}$ its total measured carbonate:

$$\Lambda = \frac{\chi_{cs} + \chi_c \beta_l}{1 + \beta_l} \qquad m_b = \frac{m_{ct} - \Lambda m_d}{1 - s_g \Lambda} \qquad DoC = \frac{s_p (1 + \beta_l)\, m_b}{\chi_p \beta_l (m_d - s_g m_b)}$$

$\chi_p$, $\chi_c$, $\chi_{cs}$ are the portlandite and carbonate fractions of the lime and the native carbonate fraction of the soil; $\beta_l$ is the as-batched lime content; $s_p = 0.740283$ and $s_g = 0.259717$ come from the stoichiometry of Ca(OH)<sub>2</sub> + CO<sub>2</sub> → CaCO<sub>3</sub> + H<sub>2</sub>O.

Weighings give a second, chemistry-free estimate, $m_b = (m_{d,f} - m_{d,0}) / s_g$. The two routes fail in different ways — one depends on the composition constants, the other on nothing leaving the specimen — so their agreement is real evidence and their disagreement is diagnostic.

---

## Repository layout

```
index.html                              the application, one self-contained file
derivation/
  Phase_diagram_derivations.md          the derivation, sections P1-P11
  Spreadsheet_audit.md                  audit of the workbooks this replaces
  verify_spreadsheets.py                reference implementation and checks
  phase_diagram_before.svg              phase diagram, before carbonation
  phase_diagram_after.svg               phase diagram, after carbonation
```

`index.html` has no build step and no bundled dependencies beyond a subsetted font. Open it directly, or serve the folder:

```sh
python -m http.server 8000     # then open http://localhost:8000
```

To re-run the checks behind the derivation and the audit:

```sh
python derivation/verify_spreadsheets.py
```

It prints every audited quantity beside the derived one and exits non-zero if any check fails. It needs only the standard library.

---

## Publishing the page

Settings → Pages → Source: **Deploy from a branch**, branch `main`, folder `/ (root)`. The `.nojekyll` file at the root keeps GitHub from running Jekyll over the tree.

---

## Status and limitations

- The lime composition must be physically admissible: $\chi_p + \chi_c \le 1$. The page flags a composition that is not, because an assay and a carbonate reading that together exceed unity make every reported conversion wrong by an unknown amount.
- The native carbonate fraction of the soil, $\chi_{cs}$, dominates the uncertainty in the result — it typically accounts for more than half of the carbonate a sub-sample returns. Measure it per soil batch, with replicates.
- Product water is treated as vapour, following the reaction as written above. This does not affect the degree of carbonation, but it does change the gas-volume ledger.
- The relations assume a rigid mould, so total volume is constant and the reaction's solid expansion comes out of the void space.

---

## Licence

Dual-licensed, because the repository holds two different kinds of work:

- **Code** — `index.html`, `derivation/verify_spreadsheets.py` — under the [MIT Licence](LICENSE).
- **Written work and figures** — everything in `derivation/` except the script — under [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0).

Third-party components and their notices are listed in [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md). The embedded font carries an attribution requirement, so keep that file with any redistribution.

## Citation

See [CITATION.cff](CITATION.cff). GitHub renders a "Cite this repository" button from it once the placeholders are filled in.
