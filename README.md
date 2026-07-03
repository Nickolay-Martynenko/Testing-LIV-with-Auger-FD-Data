# Testing Lorentz invariance violation with the Pierre Auger Fluorescence-detector Data
*Nickolay&nbsp;S.&nbsp;Martynenko<sup>&nbsp;a,&nbsp;b</sup>, Grigory&nbsp;I.&nbsp;Rubtsov<sup>&nbsp;b,&nbsp;a</sup>, Petr&nbsp;S.&nbsp;Satunin<sup>&nbsp;b,&nbsp;a</sup>, Andrey&nbsp;K.&nbsp;Sharofeev<sup>&nbsp;a,&nbsp;b</sup> [^mail], Sergey&nbsp;V.&nbsp;Troitsky<sup>&nbsp;b,&nbsp;a</sup>*

*a.&nbsp;Lomonosov Moscow State University, 1-2&nbsp;Leninskie Gory, Moscow&nbsp;119991, Russia*

*b.&nbsp;Institute for&nbsp;Nuclear Research of&nbsp;the&nbsp;Russian Academy of&nbsp;Sciences, 60th&nbsp;October Anniversary Prospect&nbsp;7a, Moscow&nbsp;117312, Russia*

    @article{Martynenko:2026,
        author = "Martynenko, Nickolay S. and Rubtsov, Grigory I. and Satunin, Petr S. and Sharofeev, Andrey K. and Troitsky, Sergey V.",
        title = "{Constraining Lorentz invariance violation with the depth of shower maximum}",
        eprint = "2607.XXXXX",
        archivePrefix = "arXiv",
        primaryClass = "astro-ph.HE",
        reportNumber = "INR-TH-2024-023",
        % doi = "N/A",
        % journal = "submitted to Phys. Rev. D",
        % volume = "N/A",
        % number = "N/A",
        % pages = "N/A",
        year = "2025"
    }

---

This repository contains auxiliary material for the manuscript on testing Lorentz invariance violation with reconstructed shower-maximum distributions measured by the Pierre Auger Observatory fluorescence detector.

The repository documents the preprocessing of CONEX air-shower simulations and provides a compact NumPy dataset used to construct Lorentz-invariance and Lorentz-invariance-violation templates.

## Dataset

The repository includes a preprocessed `.npy` dataset produced from CONEX simulations with modified electromagnetic interaction rates.

The dataset can be loaded with:

```python
import numpy as np

dataset = np.load("./data/dataset.npy", allow_pickle=False)
```

Each row corresponds to one simulated shower realization. The original simulation grid is flattened in the saved array.

| Column | Description |
|---|---|
| `eps` | Shifted logarithmic primary energy, defined as `log10(E/eV) - 19`. |
| `Y` | Logarithmic mass variable, defined as `ln(A)`, where `A` is the primary mass number. |
| `eta` | Reparameterized Lorentz-invariance-violation scale used in the analysis. |
| `zeta` | Model coordinate derived from `eps`, `Y`, and `eta`; used for interpolation and template construction. |
| `slant_depth` | Slant-depth grid of the longitudinal energy-deposit profile, in g/cm². |
| `deposited_energy` | Simulated longitudinal deposited-energy profile on the slant-depth grid. |
| `epscal` | Fitted energy-scale parameter from the Gaisser-Hillas profile fit. |
| `xmax` | Fitted depth of shower maximum from the deposited-energy profile, in g/cm². |
| `l_Auger` | Fitted Gaisser-Hillas shape parameter in the Auger parameterization. |
| `r_Auger` | Fitted Gaisser-Hillas shape parameter in the Auger parameterization. |
| `success` | Boolean flag indicating whether the Gaisser-Hillas fit converged successfully. |
| `conex_xmax_depo` | CONEX shower-maximum value obtained from the deposited-energy profile, in g/cm². |
| `conex_xmax_size` | CONEX shower-maximum value obtained from the charged-particle profile, in g/cm². |

For most downstream analyses, the essential columns are:

- `eps`
- `Y`
- `eta`
- `zeta`
- `xmax`
- `success`

The profile-level columns `slant_depth` and `deposited_energy` are useful for validation plots and checks of the Gaisser-Hillas fits.

## Simulation grid

The preprocessing script assumes the following simulation grid.

| Quantity | Values |
|---|---|
| Primary mass number `A` | 1, 4, 14, 28, 56 |
| `log10(E/eV)` | 15.0 to 19.0 in steps of 0.5 |
| Showers per grid point | 1024 |
| Slant-depth range | 0 to 2000 g/cm² |
| Slant-depth bin width | 10 g/cm² |

The Lorentz-invariance-violation scale grid is read from the simulation directories and stored through the derived `eta` coordinate in the final dataset.

## Repository contents

```text
.
├── README.md
├── utils
│   └── data_summary.py
└── data
    └── dataset.npy
```

## Notes

The dataset is intended as supporting material for the manuscript. It is not a replacement for the full air-shower simulation chain. Its purpose is to make the preprocessing output transparent and to allow independent checks of the template construction based on fitted shower-maximum distributions.
