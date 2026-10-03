# Track 2 Datasets: Energy, Power Grid & Closed-Loop Life Support

This folder contains the datasets for Track 2. Download what you need **before the event**.

---

## What's in this folder

| File / Folder | Size | Required? | Use it for |
|---|---|---|---|
| `SMAP/` | ~113 MB | ✅ Recommended | Real-time telemetry anomaly detection |
| `MSL/` | ~58 MB | Optional | Same task, Mars rover data |
| `5. Battery Data Set.zip` | zip | ✅ Recommended | Predictive maintenance, time-to-failure forecasting |
| `11. Randomized Battery Usage Data Set.zip` | zip | Optional | Battery behavior under random loads |
| `16. Small Satellite Power Simulation Data Set.zip` | zip | Optional | Spacecraft power system and battery reserves |

---

## 1. NASA SMAP & MSL Telemetry Anomalies

Real spacecraft telemetry from NASA's **SMAP** (Soil Moisture Active Passive) satellite and the **MSL** Curiosity rover. Anomalies were labeled by NASA JPL experts.

**Files (in `SMAP/` and `MSL/`)**
| File | Contents |
|---|---|
| `SMAP_train.npy` / `MSL_train.npy` | Nominal telemetry for training, shape (timesteps, channels) |
| `SMAP_test.npy` / `MSL_test.npy` | Telemetry that contains anomalies |
| `SMAP_test_label.npy` / `MSL_test_label.npy` | One label per test timestep: 0 = nominal, 1 = anomaly |

**How to load (Python)**
```python
import numpy as np

train = np.load("SMAP/SMAP_train.npy")
test = np.load("SMAP/SMAP_test.npy")
labels = np.load("SMAP/SMAP_test_label.npy")

print(train.shape, test.shape, labels.shape)
print(f"{labels.mean():.1%} of test timesteps are anomalous")
```

**Good for:** threshold monitoring, rolling statistics, isolation forests, LSTM/forecasting-based detectors. Score your detector with precision, recall and F1 against the labels.

---

## 2. NASA Li-ion Battery Aging (`5. Battery Data Set.zip`)

Li-ion 18650 cells run through repeated charge, discharge and impedance cycles at different temperatures until they reach **end of life: 30% capacity fade (2.0 Ah down to 1.4 Ah)**.

**Unzipping:** this zip contains **nested zips**. Keep extracting until you reach the `.mat` files (e.g. `B0005.mat`, `B0006.mat`, `B0007.mat`, `B0018.mat`). Each file is one battery.

**Structure of each `.mat` file**
- `cycle`: list of every test cycle
  - `type`: `charge`, `discharge`, or `impedance`
  - `ambient_temperature`
  - `data`: measurements (voltage, current, temperature, time). Discharge cycles also include `Capacity`.

**How to load (Python)**
```python
import numpy as np
from scipy.io import loadmat

cycles = loadmat("B0005.mat", simplify_cells=True)["B0005"]["cycle"]
capacity = np.array([c["data"]["Capacity"] for c in cycles if c["type"] == "discharge"])

print(f"{len(capacity)} discharge cycles, start {capacity[0]:.2f} Ah, end {capacity[-1]:.2f} Ah")
```

**MATLAB:** `load('B0005.mat')` works directly.

**Good for:** degradation curve fitting, remaining useful life (RUL) prediction, particle filters, Bayesian forecasting with uncertainty.

---

## 3. NASA Randomized Battery Usage (optional)

Batteries cycled with **randomized load profiles** instead of constant discharge, closer to real habitat usage where demand changes constantly. Also nested zips with `.mat` files.

**Good for:** testing whether your forecasting model holds up under unpredictable loads.

---

## 4. NASA Small Satellite Power Simulation (optional)

Battery data from simulated small-satellite power experiments. Useful for modeling a spacecraft or habitat **microgrid**: battery reserves, depth of discharge and load scheduling.

**Good for:** load-shedding policies, microgrid simulations, power budget optimization.

---

## 5. ESA Anomaly Dataset (advanced, download separately)

Large-scale, real telemetry from three ESA missions with curated anomaly annotations. **Not included in this folder** because of its size (7+ GB compressed).

- Download: https://doi.org/10.5281/zenodo.12528696
- Benchmark code: https://github.com/kplabs-pl/esa-adb

---

## No data for CO₂, cabin pressure, or habitat microgrids?

There's no public dataset of real habitat life-support telemetry. If your project needs it, **simulate it**: build a model of the habitat (power generation, loads, CO₂ levels) and inject random failures. Document your simulation assumptions in your README.

---

## Original sources (if this folder is unavailable)

- SMAP: https://huggingface.co/datasets/lalababa/Time-Series-Library/tree/main/SMAP
- MSL: https://huggingface.co/datasets/lalababa/Time-Series-Library/tree/main/MSL
- Battery Aging: https://phm-datasets.s3.amazonaws.com/NASA/5.+Battery+Data+Set.zip
- Randomized Battery Usage: https://phm-datasets.s3.amazonaws.com/NASA/11.+Randomized+Battery+Usage+Data+Set.zip
- Small Satellite Power: https://phm-datasets.s3.amazonaws.com/NASA/16.+Small+Satellite+Power+Simulation+Data+Set.zip
- NASA Prognostics Data Repository: https://data.phmsociety.org/nasa/

---

## Credits

- **SMAP/MSL:** Hundman, K., et al. "Detecting Spacecraft Anomalies Using LSTMs and Nonparametric Dynamic Thresholding." *KDD*, 2018. Reference code: https://github.com/khundman/telemanom
- **Battery and power datasets:** NASA Ames Prognostics Center of Excellence (PCoE)
- **ESA Anomaly Dataset:** Kotowski, K., et al. ESA-ADB, 2024

Please cite the datasets you use in your submission README.
