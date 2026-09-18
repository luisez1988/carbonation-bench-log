# Audit of the laboratory workbooks against the phase relations

**Audited:** `OneDrive_1_11-3-2025/15. Lab Tests/` — `Sample preparation.xlsx`, `Lab Reports.xlsx`, `Introduced CO2.xlsx`, `required.xlsx`

**Against:** `Phase_diagram_derivations.md` in this folder

**Reproducible by:** `python verify_spreadsheets.py`, which recomputes every cell cited below and prints the workbook value beside the derived one

**Prepared:** 2026-09-15

Every formula quoted here was read out of the workbook and re-evaluated, so a "matches" verdict means the audit script reproduces the stored value, not that the formula was eyeballed. Nothing in the workbooks has been modified.

---

## 1 · Summary

| # | Location | Issue | Severity |
|---|---|---|---|
| 1 | `Lab Reports.xlsx` all `ID *`!`I57`; `Sample preparation.xlsx`!`Template`!`I55` | Degree of carbonation divides by the **total** lime mass — no purity factor, and the lime's own carbonate counted as reactive | **High** |
| 2 | `Template`!`F55`,`G55`; `ID *`!`G57` | $\beta_L$ read as a percent in some cells and as a fraction in others, in the same block | **High** |
| 3 | `Introduced CO2.xlsx`!`required`!`AA`; `required.xlsx`!`required`!`Z` | Required CO₂ computed as $(44/74)\,m_l$, treating all lime as reactive | **High** |
| 4 | `required.xlsx`!`Sheet1`!`P`,`Q`,`T`,`U` | Cubic metres combined with $R$ in L·atm·mol⁻¹·K⁻¹, and $R$ entered as 0.821 | **High** |
| 5 | `Template`!`H19:H23` | Dry-mass and void-ratio block mixes units; returns $e = 240.85$ | Medium |
| 6 | `Sample preparation.xlsx`!`Sample_Preparation`!`T33` | Water content referred to total dry mass while $\beta_L$ is referred to soil mass | Medium |
| 7 | `Lab Reports.xlsx`!`Efficiency CaCO3`!`P16:W16` | New-carbonate chain is correct but opaque; the efficiency it feeds returns >1 with a negative unreacted-lime mass | Medium |
| 8 | `Introduced CO2.xlsx`!`required`!`AB2`; `required.xlsx`!`required`!`AA2` | Column heading is the reciprocal of the formula beneath it | Low |
| 9 | `Template`!`G11` = 8 % vs `Lab Reports.xlsx`!`G11` = 13.18 % | The two workbooks disagree on the carbonate content of the lime | Medium |
| 10 | Throughout | $100/74$ and $44/74$ used for the molar ratios | Low |
| 11 | Throughout | Calcimeter constant 60.7 kPa g⁻¹ hard-coded, with no temperature correction | Low |

Findings 1 to 4 change reported results. Findings 5 to 11 are either contained, cosmetic, or a matter of stating a convention.

---

## 2 · Findings that change reported results

### 2.1 The degree of carbonation

`Lab Reports.xlsx`!`ID 4.1`!`I57` reads

```
=(H57/1.35)/(D57-(D57/(1+$C$6)))
```

The numerator is the produced carbonate converted to portlandite, which is right in principle. The denominator is $m_d - m_d/(1+\beta_L)$, the **whole** lime mass in the sub-sample. Per P4.3 it should be the reactive lime, $\chi_p$ times that quantity, and per P5.2 the sub-sample's original mass should be recovered from $m_d - s_g m_b$ rather than from $m_d$ directly.

The workbook's own inputs show why this matters. The same sheet records the lime as $13.18\,\%$ carbonate in cell `G11`, so at least an eighth of the mass in that denominator cannot react, and the reported $DoC$ is depressed by at least that proportion before any other consideration.

| Quantity | Value |
|---|---|
| `I57` as written | 0.841 |
| Corrected, purity taken as $1-\chi_c$ | 1.022 |
| Corrected, assay purity $\chi_p = 0.87$ | 1.020 |

The gap is $+21\,\%$. This is the mechanism behind the profiles in `Validation_experiments/*/DoCs/*.csv` being clipped at exactly `1.00`: the corrected values sit at or slightly above unity for the fully carbonated depths, and the clip disguises a definition problem as a saturated measurement.

Two of the three components of the correction are independent of any new measurement — removing the lime's carbonate from the denominator, and recovering the pre-reaction sub-sample mass. Only $\chi_p$ requires an assay.

### 2.2 The percent-versus-fraction bug

Within a single block of `Template`, cell `C6` is labelled `βL [%]` and holds `0.07`. Three cells read it three different ways:

| Cell | Formula fragment | Reads `C6` as |
|---|---|---|
| `F55` | `(D55-D55/(1+$C$6/100))` | percent |
| `G55` | `D55/(1+$C$6/100)` | percent |
| `I55` | `(D55-(D55/(1+$C$6)))` | fraction |

Since `C6` already holds a fraction, the `/100` is wrong. `F55` comes out 94 times too small and `G55` $7\,\%$ too large. The two errors act in opposite directions on the produced carbonate in `H55`, which ends up $3.1\,\%$ high rather than wildly wrong — which is precisely why the bug has survived.

In `Lab Reports.xlsx` the pattern differs: `F57` and `I57` read `C6` as a fraction and only `G57` divides by 100. There the effect is one-sided — the soil's native carbonate is overstated by $3.0\,\%$ and the produced carbonate understated by $3.4\,\%$.

Because the two workbooks are wrong in different cells, results reduced in one are not directly comparable with results reduced in the other.

### 2.3 Required carbon dioxide ignores lime purity

`Introduced CO2.xlsx`!`required`!`AA3` reads `=+(44/74)*S3`, where `S3` is the total lime mass. Per P8.1 the reactive lime is $\chi_p m_l$, so the requirement is overstated by the factor $1/\chi_p$.

For row 3, $\beta_L = 10\,\%$, $m_l = 132.9$ g:

| Quantity | As written | With $\chi_p = 0.87$ |
|---|---|---|
| Required CO₂ | 79.02 g | 68.68 g |
| Supply ratio $R_{\text{CO}_2}$ | 0.993 | 1.143 |

The uncorrected value makes the test look like an exact stoichiometric match. Corrected, it shows the $14\,\%$ excess one would expect in a flow-through arrangement. Rows whose supply ratio already exceeds one — 4.1, 4.2, 5.1, 5.2 — move further above it.

The sheet also conflates two distinct quantities under one column. P8.2 separates the supply ratio, which may exceed one, from the utilisation $\eta_{\text{CO}_2} = s_c m_b / m_{\text{CO}_2,\text{in}}$, which may not. Only the first is currently computed.

### 2.4 The gas-law unit error

`required.xlsx`!`Sheet1` computes, for row 3,

```
P3: =((8*2.54*PI()*(O3*2.54)^2)/4)*(0.01^3)     mould volume, m^3
Q3: =0.001*M3                                    "VCo2 (m3)" from M3 = 1160 litres
T3: 0.821                                        "R (L*atm/mol*K)"
U3: =+S3*Q3/(T3*R3)                              n = PV/RT
```

Three problems compound. `Q3` converts litres to cubic metres but the gas constant is in litre units, `T3` holds `0.821` where the litre-based value is `0.0821`, and the result is labelled moles. For 1160 standard litres the sheet returns `0.00517` mol against a correct `51.75` mol, low by a factor of about $10^4$.

`Introduced CO2.xlsx`!`required` does the same calculation correctly, keeping litres throughout with $R = 0.0821$, and its `Y3` agrees with the derivation to $0.05\,\%$ — the residue being the rounding of $R$. **`Introduced CO2.xlsx`!`required` is the sheet to trust; `required.xlsx`!`Sheet1` should not be used for moles.** Its efficiency and timing columns are unaffected.

---

## 3 · Findings that are contained

### 3.1 The broken void-ratio block in `Template`

Cells `H19:H23` and `K19:L21` attempt a dry-mass and void-ratio calculation on a unit basis (`K19: =+H21/1.1`) and then compare the resulting sub-cubic-centimetre volume against the mould volume in cubic centimetres, giving `H23: e = 240.85`. The equivalent block in `Lab Reports.xlsx`!`ID 4.1` is correctly scaled and returns $e = 0.904$ against a target of $0.9$. The `Template` block appears to be abandoned scratch work; it should be deleted rather than repaired, since no downstream cell reads it.

### 3.2 The base of the water content

`Sample_Preparation`!`T33` computes `w = O38/(O30+O32)`, water mass over lime **plus** soil, while `O34` confirms $\beta = m_l/m_s$ on the soil alone. Per P1.3 and P11.1 the two bases should match. Using the soil base raises $w$ by the factor $1+\beta_L$: $3.0\,\%$ at $\beta_L = 0.03$, rising to $10\,\%$ at $\beta_L = 0.10$.

Nothing is wrong arithmetically. The consequence is comparability — a water content quoted for a $10\,\%$ lime mix is not on the same footing as one quoted for a $3\,\%$ mix, and the discrepancy grows with the very variable the programme is studying. Whichever base is adopted, it needs to be the same one everywhere and stated in the paper.

### 3.3 The `Efficiency CaCO3` derivation

Cells `P16` through `W16` implement an unlabelled derivation through an intermediate `B_ci`:

```
Q: B_ci = ((Mct(1-χc)βL + Mct - 0.26·Mct·χcs + 0.74·Mct·χc·βL) / (M - 0.26·Mct))
R: Ms   = Mct/B_ci
S: βc   = B_ci - χc·βL - χcs
T: Mc   = βc·Ms
```

Re-evaluated, `T16` returns $0.10210696$ g where P5.2 returns $0.1021057$ g — agreement to eight significant figures. **The new-carbonate value is correct.** The six-step chain is algebraically the same as

$$m_b = \frac{m_{ct} - \Lambda m_d}{1 - s_g \Lambda}$$

and can be replaced by that single expression with no change in result.

What follows it does not survive. Cells `U16` to `W16` reconstruct the lime inventory from the same mass balance rather than from what was batched:

```
U: Mlu = M - Mct - (Mct/B_ci)(1-χcs)          ->  -0.00036797 g
V: ML  = (Mlu + 0.74·Mc)/(1-χc)
W: eff = Mlr/(Mlr + Mlu)                       ->   1.0048938
```

A negative unreacted-lime mass is not a small numerical residue; it means the reconstruction has consumed more lime than the balance allows, and the efficiency built on it is meaningless near full conversion. P4.3 avoids this by taking the denominator from the batch, $\chi_p \beta_l m_s$, which is known independently and cannot go negative.

### 3.4 Disagreement on the lime's carbonate content

`Sample preparation.xlsx`!`Template`!`G11` records $8\,\%$ carbonate in the lime, entered as a constant. `Lab Reports.xlsx` computes `G11 = C13/C11` from a pressure of 8 kPa on a 1 g sample, giving $13.18\,\%$. Both are described as the same measurement on the same material.

The larger value is also incompatible with an $87\,\%$ portlandite assay, since $0.87 + 0.1318 > 1$ leaves negative room for inert impurity (P11.4). One reconciled composition per lime batch, with $\chi_i$ taken by difference and required to be non-negative, needs to be fixed before any $DoC$ is recomputed.

### 3.5 Minor constants

The molar ratios $100/74 = 1.35135$ and $44/74 = 0.594595$ are used throughout, against exact values $1.35083$ and $0.593978$. The error is $0.04\,\%$ and $0.10\,\%$ respectively — immaterial beside the purity omission, but free to fix.

The calcimeter constant $60.7$ kPa g⁻¹ is hard-coded in every sheet. Per P7.1 it is proportional to absolute temperature, so a vessel calibrated at $22\,^\circ\text{C}$ reads $1.7\,\%$ high at $27\,^\circ\text{C}$. It should be stored as a headspace volume and a calibration temperature, and re-evaluated per session.

### 3.6 A reversed column heading

`Introduced CO2.xlsx`!`required`!`AB2` reads `Required / introduced`, but `AB3` evaluates `=+Z3/AA3`, which is introduced over required. The same reversal appears in `required.xlsx`!`required`!`AA2`. The numbers are the supply ratio of P8.2 and are fine; only the heading is inverted.

---

## 4 · What to do about it

In order of return:

1. **Recompute every $DoC$** using P5.2, once a reconciled lime composition exists (§3.4). This is the finding that changes conclusions, and two of its three components need no new measurement.
2. **Re-derive the CO₂ supply ratios** with the purity factor, and add the utilisation as a separate column (§2.3).
3. **Fix the `/100` cells** in both workbooks, or better, move the reduction out of the spreadsheets entirely (§2.2).
4. **Retire `required.xlsx`!`Sheet1`** as a source of moles (§2.4).
5. **Settle the water-content base** and state it in the paper (§3.2).
6. **Measure $\chi_{cs}$ with replicates.** P5.4 shows it dominates the uncertainty in $m_b$, and it is currently a single assumed constant.

Items 1 to 4 are implemented in the lab-control application that accompanies this audit, which takes the measurements as entered and applies the relations of `Phase_diagram_derivations.md` directly.
