# Testing Lorentz invariance violation with the Pierre Auger fluorescence-detector data
Nickolay&nbsp;S.&nbsp;Martynenko<sup>&nbsp;1,&nbsp;2,&nbsp;\*</sup>, Grigory&nbsp;I.&nbsp;Rubtsov<sup>&nbsp;2,&nbsp;1</sup>, Petr&nbsp;S.&nbsp;Satunin<sup>&nbsp;2,&nbsp;1</sup>, Andrey&nbsp;K.&nbsp;Sharofeev<sup>&nbsp;1,&nbsp;2</sup>, Sergey&nbsp;V.&nbsp;Troitsky<sup>&nbsp;2,&nbsp;1</sup>

<sup>1</sup>&nbsp;Lomonosov Moscow State University, 1-2&nbsp;Leninskie Gory, Moscow&nbsp;119991, Russia<br>
<sup>2</sup>&nbsp;Institute for&nbsp;Nuclear Research of&nbsp;the&nbsp;Russian Academy of&nbsp;Sciences, 60th&nbsp;October Anniversary Prospect&nbsp;7a, Moscow&nbsp;117312, Russia<br>
<sup>*</sup>&nbsp;Corresponding author: [martynenko@inr.ac.ru](mailto:martynenko@inr.ac.ru)

Public NumPy data products for the study *Constraining Lorentz invariance violation with the depth of shower maximum*.

This repository provides processed simulation tables, forward-folded probability tables, and likelihood-scan outputs for a toy-model sensitivity study of subluminal photon-sector Lorentz invariance violation (LIV) using Pierre Auger fluorescence-detector reconstructed shower-maximum distributions.

Large files are stored with GitHub LFS under `data/`.

## Requirements

To work with the datasets, you will need:

- **[Git LFS](https://git-lfs.com)** — for downloading large NumPy files.
- **[Python 3](https://www.python.org/downloads/)** (recommended: **3.12.7**) — for running the demo script.
- **[NumPy](https://numpy.org/install/)** (recommended: **2.4.4**) — for handling the structured data.

## Files

| File | Format | Content |
|---|---|---|
| `data/dataset.npy` | structured `.npy` | Preprocessed CONEX shower table and Gaisser–Hillas fit results |
| `data/binned_probas.npz` | `.npz` archive | Forward-folded Auger-bin probabilities |
| `data/likelihood_ratio_test.npy` | structured `.npy` | Likelihood-ratio scan and mock calibration |

### `data/dataset.npy`

Load:

```python
import numpy as np
dataset = np.load("data/dataset.npy").view(np.recarray)
```

Schema:

| Field | Shape | Description | Notation |
|---|---:|---|---|
| `event.epsilon` | `(414720,)` | true primary energy variable | $\epsilon\equiv\log_{10}\left[E/10^{19}\text{eV}\right]$ |
| `event.Y` | `(414720,)` | mass variable | $Y\equiv\ln(A)$ |
| `event.eta` | `(414720,)` | LIV-scale variable | $\eta\equiv\log_{10}\left[(m_e M_{\rm LIV})^{1/2}/10^{19}\text{eV}\right]$ |
| `event.zeta` | `(414720,)` | combined energy–mass–LIV variable | $\zeta\equiv\epsilon-\eta-Y/\ln(10)$ |
| `profile.depth` | `(414720, 200)` | slant depth [g cm<sup>-2</sup>] | $X$ |
| `profile.e_dep` | `(414720, 200)` | deposited energy [GeV g<sup>-1</sup> cm<sup>2</sup>] | ${\rm d}E_{\rm dep} / {\rm d}X$ |
| `fit_inputs.fluctuated_e_dep` | `(414720, 200)` | fluctuated deposited energy [GeV g<sup>-1</sup> cm<sup>2</sup>] | $\left({\rm d}E_{\rm dep} / {\rm d}X\right)_d$ |
| `fit_inputs.sqroot_var_e_dep` | `(414720, 200)` | (fluctuations variance)<sup>1/2</sup> [GeV g<sup>-1</sup> cm<sup>2</sup>]  | ${\rm Var}\left[{\rm d}E_{\rm dep} / {\rm d}X\right]^{1/2}$ |
| `fit_outputs.epsilon_cal` | `(414720,)` | fitted calorimetric-energy variable | $\epsilon_{\rm cal}\equiv\log_{10}\left[E_{\rm cal}/10^{19}\text{eV}\right]$ |
| `fit_outputs.depth_max` | `(414720,)` | fitted shower maximum [g cm<sup>-2</sup>]  | $X_{\max}$ |
| `fit_outputs.shape_r` | `(414720,)` | fitted profile shape parameter | $R$ |
| `fit_outputs.shape_l` | `(414720,)` | fitted profile shape parameter [g cm<sup>-2</sup>] | $L$ |
| `fit_outputs.success` | `(414720,)` | fit convergence flag | N/A |

### `data/binned_probas.npz`

Load:

```python
import numpy as np
binned = np.load("data/binned_probas.npz")
```

Contents:

| Array[.field] | Shape | Description |
|---|---:|---|
| `eta` | `(2002,)` | tested LIV-scale grid |
| `Y` | `(4,)` | fitted mass-group grid: ${}^{1}{\rm H}$, ${}^{4}{\rm He}$, ${}^{14}{\rm N}$, ${}^{56}{\rm Fe}$ |
| `depth_max_reco_bins.left` | `(100,)` | $X_{\max,\rm reco}$ bins, left edges [g cm<sup>-2</sup>] |
| `depth_max_reco_bins.right` | `(100,)` | $X_{\max,\rm reco}$ bins, right edges [g cm<sup>-2</sup>] |
| `epsilon_reco_bins.left` | `(20,)` | $\epsilon_{\rm reco}$ bins, left edges|
| `epsilon_reco_bins.right` | `(20,)` | $\epsilon_{\rm reco}$ bins, right edges |
| `epsilon_reco_bins.slope` | `(20,)` | $\epsilon_{\rm reco}$ bins, local power-law slope |
| `counts` | `(20, 100)` |  Auger binned $X_{\max,\rm reco}$ counts |
| `probas.val` | `(2002, 20, 4, 100)` | forward-folded bin probabilities |
| `probas.err.model` | `(2002, 20, 4, 100)` | uncertainties of bin probabilities (model) |
| `probas.err.detector` | `(2002, 20, 4, 100)` | uncertainties of bin probabilities (detector response) |
| `probas.err.integration` | `(2002, 20, 4, 100)` | uncertainties of bin probabilities (integration) |
| `probas.err.total` | `(2002, 20, 4, 100)` | uncertainties of bin probabilities (total) |

The `eta` grid includes the Lorentz-invariant limit, represented by the final entry `eta[-1] = +∞` (`np.inf` when loaded in Python).

### `data/likelihood_ratio_test.npy`

Load:

```python
import numpy as np
scan = np.load("data/likelihood_ratio_test.npy").view(np.recarray)
```


Schema:

| Field | Shape | Description |
|---|---:|---|
| `eta` | `(201,)` | tested LIV-scale grid |
| `hat_F` | `(201, 20, 4)` | fitted composition fractions |
| `D_obs` | `(201,)` | observed likelihood-ratio statistic |
| `D_mock` | `(201, 10000)` | mock-data-set statistic values |
| `p_value.val` | `(201,)` | calibrated p-value |
| `p_value.err` | `(201,)` | estimated p-value uncertainty |
| `p_value.reliable` | `(201,)` | p-value reliability flag<sup>**</sup> |

<sup>**</sup> If `False`, the p-value is saturated by the finite mock sample: no mock data set generated under the tested LIV hypothesis produced a statistic at least as large as the observed one.


## Physics scope

The analysis tests subluminal photon-sector LIV through its effect on electromagnetic shower development. Suppressed Bethe–Heitler pair production modifies the longitudinal shower profile and the reconstructed-energy response. The reported result should be interpreted as a toy-model shower-maximum-based sensitivity estimate, not as a full detector-level experimental limit.

## Citation

If you use these data products, cite:

    @article{Martynenko2026LIVAugerFD,
      title         = {Constraining Lorentz invariance violation 
                       with the depth of shower maximum},
      author        = {Martynenko, Nickolay S. and 
                       Rubtsov, Grigory I. and 
                       Satunin, Petr S. and 
                       Sharofeev, Andrey K. and 
                       Troitsky, Sergey V.},
      year          = {2026},
      eprint        = {2607.XXXXX},
      archivePrefix = {arXiv},
      reportNumber  = {INR-TH-2026-XXX},
      note          = {Submitted to Physical Review D}
    }

## License

MIT License, see [LICENSE](./LICENSE)
