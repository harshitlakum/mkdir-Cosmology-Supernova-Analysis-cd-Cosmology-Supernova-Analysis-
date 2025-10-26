# Cosmology–Supernova–Analysis

Estimate the Hubble constant **H₀** and the age of the Universe from Type Ia supernovae using the **Union2.1** compilation (580 SNe). Includes exploratory analysis, low-z linear fits, weighted/MLE regression, 4th-degree polynomial baselines, and a cosmology-motivated fit.

![Python](https://img.shields.io/badge/Python-3.10%2B-blue) ![License](https://img.shields.io/badge/License-MIT-green)

---

## Overview

Type Ia SNe are (quasi) standard candles. Their distance modulus ( \mu ) gives luminosity distance ( d_L ), and at **low redshift** ( (z \lesssim 0.1) ) we use ( v \approx cz ) to estimate ( H_0 ) from a linear Hubble law ( v \approx H_0 d ).

This repo lets you:

* Explore and visualize the Union2.1 dataset.
* Convert ( \mu \to d_L ) correctly and apply a **low-z cut**.
* Fit ( H_0 ) via OLS and **weighted/MLE** with magnitude errors.
* Compare a **4th-degree polynomial** vs. a **cosmology-motivated** regression.
* Convert Hubble time ( t_H ) to an **age of the Universe** estimate.

---

## Dataset

**Union2.1** (580 SNe). Expected columns:

* `Supernova` — SN name
* `Redshift` — host galaxy redshift ( z )
* `Modulus` — distance modulus ( \mu )
* `Error` — ( \sigma_\mu ) (1-sigma)
* `LMGProb` — low-mass-galaxy host probability

> **Correct conversion:**
> ( d_L(\mathrm{Mpc}) = 10^{(\mu - 25)/5} )
> (Your earlier formula was wrong. Fix it or your results will be garbage.)

---

## Methods (what we actually do)

1. **Exploration**

   * Inspect basic stats, missing values, and outliers.
   * Plot ( \mu ) vs. ( z ) and ( \sigma_\mu ) distributions.

2. **Low-z cut and distances**

   * Select ( z \le 0.1 ) to safely use ( v \approx cz ).
   * Compute ( d_L ) from ( \mu ). Plot ( d_L ) vs. ( z ).

3. **Recessional velocity**

   * Use ( v = cz ) (with ( c = 299,792.458 ,\mathrm{km,s^{-1}} )).
   * Plot ( d_L ) vs. ( v ).

4. **Estimating ( H_0 )**

   * **OLS:** fit ( v = H_0 d ) through the origin.
   * **Weighted/MLE:** weights from ( \sigma_\mu ) propagated to ( \sigma_d ).
     Uncertainty propagation:
     ( \sigma_{d_L} = \frac{\ln 10}{5}, d_L, \sigma_\mu ).

5. **Polynomial baseline**

   * Fit ( \mu(z) ) with degree-4 polynomial.
   * Check residuals and warn about **overreach** at higher ( z ).

6. **Cosmology-motivated regression**

   * Use low-z expansion or simple ΛCDM-motivated form to fit ( d_L(z) ) and extract ( t_H ) and ( H_0 ).
   * Convert ( t_H = 1/H_0 ) to **age** (note: true ΛCDM age is ( \approx 0.96/H_0 ), EdS is ( 2/(3H_0) )).

---

## Results (what you’ll report)

* ( H_0 ) from **OLS** and **weighted/MLE**, with uncertainties.
* Fit quality (residuals, ( R^2 )/( \chi^2_\nu ), diagnostics).
* Comparison: **polynomial** vs **cosmology-motivated** model.
* **Age of the Universe** estimate from ( t_H ) (and note model assumptions).

*(Numbers depend on your runs; keep them in a table in your repo’s `results/`.)*

---

## Quickstart

```bash
git clone https://github.com/<you>/Cosmology-Supernova-Analysis.git
cd Cosmology-Supernova-Analysis

# (optional) create env
python -m venv .venv && source .venv/bin/activate

# install
pip install -r requirements.txt
```

Run the notebook or scripts:

```bash
# Jupyter
jupyter lab  # open notebooks/union21_analysis.ipynb

# or CLI example (if you keep a script)
python scripts/hubble_fit.py \
  --data data/Union2_1.csv \
  --z-max 0.1 \
  --method mle \
  --out results/h0_mle.json
```

---

## Project Structure

```
Cosmology-Supernova-Analysis/
├─ data/
│  └─ Union2_1.csv                 # put dataset here (not committed if large)
├─ notebooks/
│  └─ union21_analysis.ipynb       # end-to-end analysis
├─ scripts/
│  ├─ hubble_fit.py                # OLS/MLE fits and summaries
│  └─ plots.py                     # publication-quality figures
├─ results/
│  ├─ figures/                     # saved plots
│  └─ tables/                      # CSV/JSON summaries
├─ src/
│  ├─ io_utils.py                  # loading/validation
│  ├─ conversions.py               # μ→d_L, error propagation
│  ├─ models.py                    # OLS, weighted/MLE, poly, cosmology
│  └─ constants.py                 # c, units
├─ tests/                          # basic unit tests
├─ requirements.txt
└─ README.md
```

---

## Usage Notes & Pitfalls (read this or regret it)

* **Distance modulus**: use ( d_L(\mathrm{Mpc}) = 10^{(\mu - 25)/5} ). Don’t botch it.
* **Low-z regime**: ( v \approx cz ) only holds for ( z \lesssim 0.1 ). Don’t overreach.
* **Weights**: propagate ( \sigma_\mu ) to ( \sigma_{d_L} ) for weighted fits, or you’re just hand-waving.
* **Intercept**: the Hubble law through origin is standard here; adding an intercept is usually unjustified at low-z unless you model systematics.
* **Age**: ( t_H = 1/H_0 ) is **not** the ΛCDM age; if you quote an age, state the model.

---

## Dependencies

* Python 3.x
* `pandas`, `numpy`, `matplotlib`, `scipy`

Optional: `jupyter`, `statsmodels`, `numba`, `pytest`.

Install:

```bash
pip install -r requirements.txt
```

---

## License

MIT. Do science, not excuses.


