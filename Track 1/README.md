# Track 1 Datasets: Deep-Space Communication & Signal Intelligence

This folder contains the datasets for Track 1. Download what you need **before the event**. The large files will be slow over venue Wi-Fi.

---

## What's in this folder

| File | Size | Required? | Use it for |
|---|---|---|---|
| `RML2016.10a_dict.pkl` | 641 MB | ✅ Recommended | Adaptive filtering, blind demodulation, modulation classification |
| `GOLD_XYZ_OSC.0001_1024.hdf5` | 21.4 GB | Optional (advanced) | Harder benchmark, more modulations, longer samples |

---

## 1. RadioML 2016.10A

Synthetic radio signals generated with GNU Radio, with realistic impairments added: white noise, multipath fading, frequency offsets and sample-rate drift. A good stand-in for weak, drifting deep-space signals.

**Contents**
- **11 modulation types:** 8PSK, AM-DSB, AM-SSB, BPSK, CPFSK, GFSK, PAM4, QAM16, QAM64, QPSK, WBFM
- **20 SNR levels:** -20 dB to +18 dB in 2 dB steps
- **1,000 examples** per modulation and SNR pair (220,000 total)
- **Each example:** 128 complex samples, stored as 2 rows (I and Q)

**How to load (Python)**
```python
import pickle

with open("RML2016.10a_dict.pkl", "rb") as f:
    data = pickle.load(f, encoding="latin1")

# Keys are (modulation, snr) tuples
print(sorted({k[0] for k in data}))   # modulations
print(sorted({k[1] for k in data}))   # SNRs

x = data[("QPSK", 0)]   # shape (1000, 2, 128)
i, q = x[0, 0], x[0, 1]  # I and Q of the first example
```

---

## 2. RadioML 2018.01A (optional)

A larger, harder version for teams who want a stronger benchmark.

**Contents**
- **24 modulation types**
- **26 SNR levels:** -20 dB to +30 dB
- **Each example:** 1,024 complex samples
- **About 2.5 million examples**

**How to load (Python)**
```python
import h5py   # pip install h5py

with h5py.File("GOLD_XYZ_OSC.0001_1024.hdf5", "r") as f:
    X = f["X"]   # signals, shape (N, 1024, 2)
    Y = f["Y"]   # one-hot modulation labels, shape (N, 24)
    Z = f["Z"]   # SNR for each example, shape (N, 1)
    sample = X[0]   # read slices only, the full file won't fit in most laptops' RAM
```

---

## 3. Real satellite data (optional, online only)

Want real-world signals? **SatNOGS** is a global network of amateur ground stations that records satellite passes.

- **Telemetry frames:** https://db.satnogs.org
  - Create a free account, open a satellite's page and download its frames as CSV
- **Pass recordings:** https://network.satnogs.org
  - Open any observation to download its waterfall, audio and (sometimes) IQ data

Tip: choose satellites with active decoders so the frames are already decoded for comparison.

---

## Notes

- **These are simulated signals.** DeepSig notes that the RadioML datasets have known errata. They're fine for prototyping, but don't treat them as ground truth for real hardware.
- **Want harder test cases?** You can add your own impairments (Doppler drift, frequency hops, dropouts) on top of RadioML signals.

---

## Original sources (if this folder is unavailable)

- RadioML 2016.10A: https://huggingface.co/datasets/FlowVortex/RML/resolve/main/RML2016.10a_dict.pkl?download=true
- RadioML 2018.01A: https://huggingface.co/datasets/FlowVortex/RML/resolve/main/GOLD_XYZ_OSC.0001_1024.hdf5?download=true
- DeepSig dataset page: https://www.deepsig.ai/datasets/

---

## Credits & License

- **RadioML:** DeepSig Inc., licensed under CC BY-NC-SA 4.0 (non-commercial use, with attribution)
  - O'Shea, T. J., & West, N. "Radio Machine Learning Dataset Generation with GNU Radio." *Proceedings of the GNU Radio Conference*, 2016.
- **SatNOGS:** Libre Space Foundation

Please cite the datasets you use in your submission README.
