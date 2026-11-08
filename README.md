# Measuring the Orbital Period and Eclipse Depth of Algol from TESS Data

A small learning project: starting from public TESS photometry of the eclipsing binary **Algol (Beta Persei)**, I clean the light curve, measure the orbital period with a periodogram, fold the data, and fit the primary eclipse with a simple model.

> **Status:** completed as a first data-analysis project. This is a learning exercise, not a research result.

## What I did

1. **Downloaded** one sector of TESS 2-minute-cadence (SPOC) data for Algol using [Lightkurve](https://lightkurve.github.io/lightkurve/). TESS sector: **18**.
2. **Cleaned** the light curve: removed NaN values, normalised the flux, and clipped upward outliers only (4σ). I did not clip low points, because eclipses *are* low points.
3. **Found the period** with a Box Least Squares (BLS) periodogram.
4. **Folded** the light curve on the best period to stack all eclipses on top of each other.
5. **Fitted** the primary eclipse with a Gaussian dip using `scipy.optimize.curve_fit` (data within ±0.15 of a period around mid-eclipse).
6. **Compared** my period with the catalogue value from AAVSO VSX.

## Results

| Quantity | My value | Notes |
|---|---|---|
| Orbital period (BLS) | **2.86567 d** | one TESS sector |
| Catalogue period (AAVSO VSX) | 2.867343 d | accessed 7 November 2026, [VSX entry](https://vsx.aavso.org/index.php?view=detail.top&oid=26202) |
| Difference | ≈ 0.0017 d (≈ 0.06 %) | about 2.4 minutes |
| Primary-eclipse depth (Gaussian fit) | 0.359 ± 0.001 (fit error only) | fraction of normal flux |
| Depth from data near mid-eclipse (median) | 0.372 | direct estimate from folded points |
| Eclipse width | σ = 0.070 d, FWHM = 0.165 d (about 4 h) | Gaussian fit |


## Interpretation

Algol is an eclipsing binary: two stars orbit each other and periodically pass in front of one another, so the total brightness dips. Folding on the correct period makes all eclipses line up, and the clean folded curve is evidence that the period is right. The measured period agrees with the catalogue value to about 0.06 %.

## Limitations (please read)

- **The model is a rough approximation.** Algol's eclipse is V-shaped, and a Gaussian rounds off the bottom. The fitted depth (0.359) is about 0.013 (≈ 4 %) shallower than the median flux near mid-eclipse (0.372). This model-shape error is much larger than the formal fit error of ±0.001, so I do not treat ±0.001 as the true uncertainty.
- **Single sector.** One 27-day sector contains only about nine orbital cycles, which limits how precisely the period can be fixed.
- **Only the primary eclipse was fitted.** The shallower secondary eclipse was not modelled.
- **Bright star.** Algol is very bright and TESS data for it may be partly saturated, which could affect absolute depths. I did not correct for this.
- **BLS assumes a box-shaped dip**, which is not exactly Algol's shape, and may contribute to the small period offset. I did not test this.
- The depth is the fraction of flux in the TESS band, not a physical property of the stars.

## A note on how it went

I first tried **AI Phe** (TESS sector 2). Its light curve showed two dips of different depths about 11 days apart within a roughly 30-day span, which is consistent with the primary and secondary eclipses of a long-period binary. With only one eclipse of each kind in a single sector, a periodogram cannot measure the period reliably, so I switched to Algol, whose short period gives many eclipses per sector.

## How to run

```bash
pip install lightkurve astropy matplotlib numpy scipy
```

Open `algol_time_period.ipynb` in Jupyter or Google Colab and run the cells in order. TESS data is downloaded automatically from MAST.

## Possible next steps

- Fit both eclipses and use a better eclipse shape (or a proper two-star model).
- Combine several sectors to improve the period precision.
- Compare a simple simulated two-star light curve with the data.

## Credits and sources

- TESS mission data, retrieved from the Mikulski Archive for Space Telescopes (MAST).
- [Lightkurve](https://lightkurve.github.io/lightkurve/) for downloading and processing light curves; NumPy, SciPy and Matplotlib for analysis and plotting.
- AAVSO International Variable Star Index (VSX) for the catalogue period.
- I learned the workflow from the Lightkurve and NASA TESS tutorials, then applied it to my own target.

## Author

Shubhomita Kundu, B.Sc. Physics (2nd year), Sir Parashurambhau College, Pune
