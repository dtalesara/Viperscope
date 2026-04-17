# 🐍 ViperScope

**Bio-inspired exoplanet transit detection for TESS light curves**

ViperScope is a Jupyter notebook pipeline that searches TESS photometry for exoplanet transit signals. It is named after the pit viper's infrared-sensing pit organs — the algorithm accumulates faint periodic "heat" signals across thousands of cadences, the same way a pit organ builds up a thermal image from weak photon flux.

The pipeline is written to all ten of NASA JPL's Power of Ten safety-critical coding rules: bounded loops, pre-allocated arrays, explicit None checks, and no dynamic memory after initialisation. This is unusual for a science notebook and makes the code auditable and reproducible.

---

## What it does

1. **Downloads TESS light curves** via Lightkurve (or reads local FITS files)
2. **Detrends** with a Savitzky-Golay filter (PitViper thermal background removal)
3. **Searches for periodic transits** using a vectorised phase-fold periodogram with harmonic de-aliasing
4. **Confirms candidates** with a Lomb-Scargle periodogram
5. **Triages false positives** with six independent tests: transit depth, shape symmetry, secondary eclipse, even/odd depth, SNR, and physical duration
6. **Cross-matches** against the TESS Object of Interest (TOI) catalogue and known hot Jupiters
7. **Saves reports** — a 6-panel diagnostic PNG and a JSON summary per candidate

Stars that pass all six false-positive tests and are absent from any known catalogue are flagged `★ NOT IN CATALOGUE`.

---

## Quick start

```bash
git clone https://github.com/YOUR_USERNAME/viperscope.git
cd viperscope
pip install -r requirements.txt
jupyter notebook viperscope_NASA.ipynb
```

Run the cells in order:

| Section | What to do |
|---------|-----------|
| 1 — Setup | Run once to install dependencies and load config |
| 2 — Algorithm | Run once to define all functions |
| 3 — Validation | Confirm the pipeline recovers WASP-17b (period = 3.7354 d) |
| 4 — Build targets | Query the TESS Input Catalog for uninvestigated Sun-like stars |
| 5 — Survey | Run repeatedly — progress is saved after every star |
| 6 — Ledger | Review all candidates found |
| 7 — Deep-dive | Inspect any single target in detail |

---

## Requirements

- Python 3.9+
- See `requirements.txt` for full list

Core dependencies: `lightkurve`, `astropy`, `astroquery`, `numpy`, `scipy`, `matplotlib`

---

## Validation

Before searching unknown stars, Section 3 validates the pipeline on **WASP-17b**, a well-characterised hot Jupiter with a known period of 3.7354 days.

To use a local FITS file:
```python
FITS_PATH = 'path/to/your/wasp17_tess.fits'
```

To download automatically from MAST, set:
```python
FITS_PATH = None
```

Expected output:
```
Detected period  : 3.7351 d
True period      : 3.7354 d
Error            : 0.01%
SNR              : 12.4
Disposition      : CANDIDATE
Catalogue status : KNOWN
```

---

## Survey output

Results are saved in `viperscope_survey/`:

```
viperscope_survey/
├── target_list.json        # prioritised list of uninvestigated stars
├── progress.json           # which stars have been processed
├── discovery_ledger.json   # all candidates and novel detections
└── reports/
    ├── TIC_XXXXXXX_report.png     # 6-panel diagnostic figure
    └── TIC_XXXXXXX_summary.json  # machine-readable summary
```

The ledger ranks candidates by SNR. Entries with `catalogue_status: NOT_IN_CATALOGUE` are the most interesting — these are signals not in the TESS TOI list or any of the built-in known-planet references.

---

## False-positive tests

| Test | What it checks |
|------|---------------|
| `depth` | Transit depth < 3% (rules out eclipsing binaries) |
| `shape` | Ingress/egress symmetry ratio < 2.0 |
| `secondary` | Secondary eclipse depth < 0.1% |
| `even_odd` | Even vs odd transit depth difference < 30% |
| `snr` | Signal-to-noise ratio ≥ 7.0 |
| `duration` | Transit duration < physical maximum for orbital period |

A star passes as `CANDIDATE` with zero failed tests, `MARGINAL` with one failed test but high SNR, or `LIKELY_FP` otherwise.

---

## Follow-up for novel candidates

If you find a `★ NOT IN CATALOGUE` signal:

1. Inspect the report PNG in `viperscope_survey/reports/`
2. Look up the star on [SIMBAD](http://simbad.u-strasbg.fr)
3. Download additional TESS sectors — does the period hold?
4. Submit to [ExoFOP](https://exofop.ipac.caltech.edu/tess/)
5. Write up as a Research Note: [RNAAS](https://iopscience.iop.org/journal/2515-5172)

---

## NASA Power of Ten compliance

All ten JPL safety-critical coding rules are implemented:

1. Simple control flow — no recursion
2. Fixed loop bounds — every loop has an explicit upper-bound constant
3. No dynamic memory after init — arrays pre-allocated with `np.zeros` / `np.full`
4. Functions ≤ 60 lines
5. Two assertions per function — entry and exit invariants
6. Minimal variable scope — no module-level mutable state
7. Return-value checking — every return is checked; `None` returns are explicit
8. Single-source configuration — all constants defined once in the config block
9. No aliased mutation — inputs are never modified in-place
10. Warnings as errors — `warnings.filterwarnings('error')` for `RuntimeWarning`

---

## License

MIT — see `LICENSE`.

---

## Contributing

See `CONTRIBUTING.md`.
