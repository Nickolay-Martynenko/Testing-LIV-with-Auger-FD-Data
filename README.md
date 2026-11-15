# Testing Lorentz invariance violation with the Pierre Auger fluorescence-detector data
Nickolay&nbsp;S.&nbsp;Martynenko<sup>&nbsp;1,&nbsp;2,&nbsp;\*</sup>, Grigory&nbsp;I.&nbsp;Rubtsov<sup>&nbsp;2</sup>, Petr&nbsp;S.&nbsp;Satunin<sup>&nbsp;2,&nbsp;1,&nbsp;3</sup>, Andrey&nbsp;K.&nbsp;Sharofeev<sup>&nbsp;1,&nbsp;2</sup>, Sergey&nbsp;V.&nbsp;Troitsky<sup>&nbsp;2,&nbsp;1</sup>

<sup>1</sup>&nbsp;Lomonosov Moscow State University, 1-2&nbsp;Leninskie Gory, Moscow&nbsp;119991, Russia<br>
<sup>2</sup>&nbsp;Institute for&nbsp;Nuclear Research of&nbsp;the&nbsp;Russian Academy of&nbsp;Sciences, 60th&nbsp;October Anniversary Prospect&nbsp;7a, Moscow&nbsp;117312, Russia<br>
<sup>3</sup>&nbsp;Branch of Lomonosov Moscow State University in Sarov, Parkovaya&nbsp;2, Sarov&nbsp;607328, Russia<br>
<sup>*</sup>&nbsp;Corresponding author: [martynenko@inr.ac.ru](mailto:martynenko@inr.ac.ru?subject=arXiv:2608.05106)

Public NumPy data products for the study *Constraining Lorentz invariance violation from the depth of air-shower maximum*.

This repository provides processed simulation tables, forward-folded probability tables, and likelihood-scan outputs for a toy-model sensitivity study of subluminal photon-sector Lorentz invariance violation (LIV) using Pierre Auger fluorescence-detector reconstructed shower-maximum distributions.

Large files are stored with GitHub LFS under `data/`.

## Requirements

To work with the datasets, you will need:

- **[Git LFS](https://git-lfs.com)** — for downloading large NumPy files.
- **[Python 3](https://www.python.org/downloads/)** (recommended: **3.12.7**) — for running the demo script.
- **[NumPy](https://numpy.org/install/)** (recommended: **2.4.4**) — for handling the structured data.

## Files

| File | Content |
|---|---|
| `data/dataset.npy.gz` | Preprocessed CONEX shower table and Gaisser–Hillas fit results |
| `data/binned_probas.npz` |  Forward-folded Auger-bin probabilities |
| `data/likelihood_ratio_test.npy` | Likelihood-ratio scan and mock calibration |
| [`data/LeadingTree.md`](data/LeadingTree.md) | Source link for the proton-shower leading-interaction tree dataset |

### `data/dataset.npy.gz`

The simulation grid contains 14 finite LIV scales, `log10(M_LIV / GeV) = 11, 12, …, 24`, and the Lorentz-invariant limit. Each of the 15 settings contains 46,080 showers.

Unpack the original NumPy file, then load it.

```sh
gzip -dc data/dataset.npy.gz > data/dataset.npy
```

```python
import numpy as np
dataset = np.load("data/dataset.npy").view(np.recarray)
```

Schema:

```text
dataset: (691200,)

event:
  epsilon  (691200,)       true primary energy variable
  Y        (691200,)       mass variable, ln(A)
  eta      (691200,)       LIV-scale variable
  zeta     (691200,)       combined energy–mass–LIV variable

profile:
  depth    (691200, 200)   slant depth [g cm^-2]
  e_dep    (691200, 200)   deposited energy [GeV g^-1 cm^2]

fit_inputs:
  fluctuated_e_dep  (691200, 200)   fluctuated deposited energy [GeV g^-1 cm^2]
  sqroot_var_e_dep  (691200, 200)   square root of profile-point variance [GeV g^-1 cm^2]

fit_outputs:
  epsilon_cal  (691200,)   fitted calorimetric-energy variable
  depth_max    (691200,)   fitted shower maximum [g cm^-2]
  shape_r      (691200,)   fitted Gaisser–Hillas shape parameter R
  shape_l      (691200,)   fitted Gaisser–Hillas shape parameter L [g cm^-2]
  success      (691200,)   fit convergence flag
```

### `data/binned_probas.npz`

Load:

```python
import numpy as np
binned = np.load("data/binned_probas.npz")
```

Contents:

```text
eta: (142,)
  tested LIV-scale grid
  eta[-1] = +∞ is the Lorentz-invariant limit

Y: (4,)
  fitted mass-group grid: H(1,1), He(4,2), N(14, 7), Fe(56,26)

depth_max_reco_bins: (100,)
  left   (100,)   Xmax,reco bin left edges [g cm^-2]
  right  (100,)   Xmax,reco bin right edges [g cm^-2]

epsilon_reco_bins: (20,)
  left   (20,)   epsilon_reco bin left edges
  right  (20,)   epsilon_reco bin right edges
  slope  (20,)   local power-law slope inside the bin

counts: (100, 20)
  Auger counts ordered by reconstructed Xmax and then reconstructed energy

probas: (142, 100, 20, 4)
  axes: eta, reconstructed Xmax, reconstructed energy, mass group
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
scan: (141,)

eta: (141,)
  tested LIV-scale grid

hat_F: (141, 20, 4)
  fitted composition fractions

D_obs: (141,)
  observed likelihood-ratio statistic

D_mock: (141, 10000)
  mock-data-set statistic values corrected for residuals in the simulated Xmax CDF

p_value:
  val       (141,)   calibrated p-value
  err       (141,)   estimated p-value uncertainty
  reliable  (141,)   reliability flag (if `False`,
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
                       from the depth of air-shower maximum},
      author        = {Martynenko, Nickolay S. and 
                       Rubtsov, Grigory I. and 
                       Satunin, Petr S. and 
                       Sharofeev, Andrey K. and 
                       Troitsky, Sergey V.},
      year          = {2026},
      eprint        = {2608.05106},
      archivePrefix = {arXiv},
      reportNumber  = {INR-TH-2026-007},
      note          = {16 pages, 4 figures. Submitted to Physical Review D}
    }

## License

MIT License, see [LICENSE](./LICENSE)
