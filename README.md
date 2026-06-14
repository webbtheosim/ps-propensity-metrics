# ps-propensity-metrics

Data accompanying the study **"When $B_2$ is Not Enough: Evaluating Simple Metrics for Predicting Phase Separation of Intrinsically Disordered Proteins"** by Wesley W. Oliver, William M. Jacobs, and Michael A. Webb (Princeton University).

The study evaluates several simple, low-cost computational metrics for quantifying the propensity of single-component intrinsically disordered protein (IDP) solutions to phase separate. Three metrics are compared: the single-chain radius of gyration ($R_\mathrm{g}$), the second virial coefficient ($B_2$), and a newly proposed quantity, the **expenditure density** ($\rho^*$). All quantities are computed from coarse-grained (HPS-Urry) molecular dynamics simulations of **2,034 IDP sequences** (20–50 residues each).

## Paper

> Oliver, W. W.; Jacobs, W. M.; Webb, M. A. When $B_2$ is Not Enough: Evaluating Simple Metrics for Predicting Phase Separation of Intrinsically Disordered Proteins. _The Journal of Physical Chemistry B_. DOI: [10.1021/acs.jpcb.5c04955](https://doi.org/10.1021/acs.jpcb.5c04955)

Please refer to the paper for full methodological details; equations and section references below point to it.

## Sequence provenance

The 2,034 sequences are not original to this study. They are taken from prior work. They comprise:

- **1,266 naturally occurring sequences** drawn from the [DISPROT](https://www.disprot.org/) database.
- **768 abiological sequences** generated during the active-learning campaign of An, Webb & Jacobs (_Sci. Adv._ **2024**, _10_, eadj2448; DOI: [10.1126/sciadv.adj2448](https://doi.org/10.1126/sciadv.adj2448)), which explored the thermodynamics–dynamics trade-off in protein condensates. These span a wide range of sequence characteristics, from highly charged polyampholytes to neutral hydrophobic sequences.

The condensed-phase diffusivity values reused in this study also originate from that work.

## What is new in this study

**All reported metrics were recomputed in this work from coarse-grained MD simulations**, with one exception:

| Quantity | Source |
| --- | --- |
| Equation of state $P(\rho)$, phase-separation labels, condensed-phase density $\rho_\mathrm{c}$, expenditure density $\rho^*$, radius of gyration $R_\mathrm{g}$, second virial coefficient $B_2$ | **Newly computed in this study** |
| Condensed-phase chain self-diffusivity | Sourced from prior work (An, Webb & Jacobs, _Sci. Adv._ 2024) |

The **expenditure density**, $\rho^*$, is the central new metric proposed here. Intuitively, it is the density to which a sequence can be reversibly compressed from infinite dilution given a fixed allowance of work. It is defined implicitly by

$$\hat{w}^{\*} = \frac{W^{\*}}{m} = \int_{0}^{\rho^{\*}} d\rho\,\frac{P(\rho)}{\rho^{2}},$$

where $P(\rho)$ is the pressure–density equation of state, $m$ is the system mass, and $\hat{w}^{\*}$ is the intensive work allowance, set to $\hat{w}^{\*} = 15\ \mathrm{atm\cdot mL\,g^{-1}}$ (chosen to maximize classification accuracy; see the paper's Supporting Information). Higher $\rho^*$ corresponds to greater phase-separation propensity.

## Repository contents

All files in `data/` are **row-aligned**: row _i_ (after the header) refers to the same sequence across every file.

### `data/sequences.txt`
The 2,034 IDP sequences in single-letter amino-acid code, one per line (preceded by a `sequence` header).

### `data/features.csv`
The 30-dimensional sequence feature vector ("augmented scaled fingerprint") used as model input.

- Columns 1–20: amino-acid composition fractions `A, R, N, D, C, Q, E, G, H, I, L, K, M, F, P, S, T, W, Y, V`.
- Columns 21–30: `length` ($N$), `avg_hydrophobicity` ($\overline{\lambda_i}$, Urry scale), `ncpr` (absolute net charge per residue), `frac_positive` ($f_+$), `frac_negative` ($f_-$), `shd` (sequence hydropathy decorator), `scd` (sequence charge decorator), `mw` (average molecular weight per residue), `shannon_entropy`, `b2_mft` (mean-field $B_2$ prediction).

See the paper's Methods and Supporting Information (Sequence Featurization) for the defining formulas.

### `data/labels.csv`
Per-sequence labels and computed metric values.

| Column | Description |
| --- | --- |
| `generation` | Sequence origin: `0` = natural DISPROT sequence; `1`–`8` = active-learning generation. |
| `diff`, `diff_std` | Condensed-phase chain self-diffusivity and its standard deviation (**sourced from prior work**; reported only for phase-separating sequences, ~475 entries). |
| `psp` | Binary phase-separation label (`1` = phase-separating, `0` = not), from the presence of a negative-pressure loop in the EoS. 479 of 2,034 sequences are labeled phase-separating. |
| `exp_density`, `exp_density_err` | Expenditure density $\rho^*$ (g/mL) and standard error. |
| `density`, `density_err` | Estimated condensed-phase density $\rho_\mathrm{c}$ (g/mL) and standard error (`0.0` for non-phase-separating sequences). |
| `rg`, `rg_err` | Single-chain radius of gyration (Å) and standard error. |
| `b2`, `b2_err` | Second virial coefficient $B_2$ (Å³) and standard error. |

Note: `rg` and `b2` are the **raw (extensive)** values. The paper analyzes the normalized, intensive forms $\tilde{R}_\mathrm{g} = R_\mathrm{g}/R_\mathrm{g}^{\mathrm{id}}$ and $\tilde{B}_2 = B_2/V_0$; the normalization constants are given in the Methods.

### `data/EOS.csv`
The approximate equation of state for each sequence: the average pressure $P(\rho)$ (atm) at fixed densities, with standard errors. Column pairs `rho_<d>` / `std_err_<d>` give the pressure and its standard error at density `<d>` g/mL (e.g. `rho_0p05` is the pressure at $\rho = 0.05$ g/mL), for densities 0.05 and 0.1–1.4 g/mL in steps of 0.1. Empty cells indicate densities not sampled for that sequence. A statistically significant negative pressure loop is taken as evidence of phase separation; $\rho^*$ and $\rho_\mathrm{c}$ are derived by integrating a cubic spline through these points.

## Citation

If you use this data, please cite:

> Oliver, W. W.; Jacobs, W. M.; Webb, M. A. When $B_2$ is Not Enough: Evaluating Simple Metrics for Predicting Phase Separation of Intrinsically Disordered Proteins. _The Journal of Physical Chemistry B_. DOI: [10.1021/acs.jpcb.5c04955](https://doi.org/10.1021/acs.jpcb.5c04955)

Please also cite the original sequence source:

> An, Y.; Webb, M. A.; Jacobs, W. M. Active Learning of the Thermodynamics–Dynamics Trade-off in Protein Condensates. _Science Advances_ **2024**, _10_ (1), eadj2448. DOI: [10.1126/sciadv.adj2448](https://doi.org/10.1126/sciadv.adj2448)

## License

See [LICENSE](LICENSE).
