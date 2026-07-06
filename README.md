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

```text
dataset: (414720,)

event:
  epsilon  (414720,)       true primary energy variable
  Y        (414720,)       mass variable, ln(A)
  eta      (414720,)       LIV-scale variable
  zeta     (414720,)       combined energy–mass–LIV variable

profile:
  depth    (414720, 200)   slant depth [g cm^-2]
  e_dep    (414720, 200)   deposited energy [GeV g^-1 cm^2]

fit_inputs:
  fluctuated_e_dep  (414720, 200)   fluctuated deposited energy [GeV g^-1 cm^2]
  sqroot_var_e_dep  (414720, 200)   square root of profile-point variance [GeV g^-1 cm^2]

fit_outputs:
  epsilon_cal  (414720,)   fitted calorimetric-energy variable
  depth_max    (414720,)   fitted shower maximum [g cm^-2]
  shape_r      (414720,)   fitted Gaisser–Hillas shape parameter R
  shape_l      (414720,)   fitted Gaisser–Hillas shape parameter L [g cm^-2]
  success      (414720,)   fit convergence flag
```

### `data/binned_probas.npz`

Load:

```python
import numpy as np
binned = np.load("data/binned_probas.npz")
```

Contents:

```text
eta: (2002,)
  tested LIV-scale grid
  eta[-1] = +∞ is the Lorentz-invariant limit

Y: (4,)
  fitted mass-group grid: H, He, N, Fe

depth_max_reco_bins: (100,)
  left   (100,)   Xmax,reco bin left edges [g cm^-2]
  right  (100,)   Xmax,reco bin right edges [g cm^-2]

epsilon_reco_bins: (20,)
  left   (20,)   epsilon_reco bin left edges
  right  (20,)   epsilon_reco bin right edges
  slope  (20,)   local power-law slope inside the bin

counts: (20, 100)
  Auger binned Xmax,reco counts

probas: (2002, 20, 4, 100)
  val              forward-folded bin probabilities
  err.model        model uncertainty
  err.detector     detector-response uncertainty
  err.integration  numerical-integration uncertainty
  err.total        total uncertainty
```

### `data/likelihood_ratio_test.npy`

Load:

```python
import numpy as np
scan = np.load("data/likelihood_ratio_test.npy").view(np.recarray)
```

Schema:

```text
scan: (201,)

eta: (201,)
  tested LIV-scale grid

hat_F: (201, 20, 4)
  fitted composition fractions

D_obs: (201,)
  observed likelihood-ratio statistic

D_mock: (201, 10000)
  mock-data-set statistic values

p_value:
  val       (201,)   calibrated p-value
  err       (201,)   estimated p-value uncertainty
  reliable  (201,)   reliability flag (if `False`,
                     the p-value is saturated by the
                     finite mock sample: no mock data
                     set generated under the tested
                     LIV hypothesis produced a statistic
                     at least as large as the observed one)
```


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
