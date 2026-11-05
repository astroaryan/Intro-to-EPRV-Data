# Intro-to-EPRV-Data

Notebook by Aryan Gupta! aryan.gupta@princeton.edu and github.com/astroaryan

For setup, download this entire folder and keep the notebook alongside the large_data_products and support_files folders. Keep the data files inside their respective folders. You can then open the notebook locally using Jupyter Notebook, or upload the entire folder to Google Drive and open the notebook using Google Colab.

# Background on radial velocities

Here's an introductory Python notebook for looking at high resolution spectra, working with different wavelengths and time series.

For some basic background, we want to improve our ability to detect exoplanets, which means extracting radial velocities from high resolution spectra. An orbiting planet induces a periodic wavelength shift in the spectra of a star. We compute the planet's radial velocity by identifying this wavelength shift in the absorption lines of a spectrum. You get an absorption line when there's a reduction in light. Imagine the Sun emits light continuously, but whenever light is absorbed (perhaps by a molecular/chemical species), then you get a reduction in light. This notebook analyzes continuum normalized data, meaning we take the raw spectral observations and divide by a continuum model. This means the fluxes are between 0 and 1.0, rather than in their raw photon or energy count units.

Stellar variability, instrumental variability, and other processes can make radial velocity prediction challenging. The same wavelength shift that a planet induces can appear similarly to how stellar variability alters the wavelengths. In this notebook, you'll learn the basics of how to analyze high resolution spectrum data using Python! We provide some sample datasets and code.

The dataset uses NEID observations of the Sun using the NEID solar archive. These are high resolution spectra of the Sun. Since the Sun is the closest star, we can get great data. We can then apply radial velocity modelling techniques to this data to see: if we hypothetically injected a planet would be able to detect that planet and discern it from noisy processes.

## Data description

The data here are solar spectra from the NEID archive that I processed using the ESSP pipeline. We targeted observing days with at least 80 spectra, then took the 80 spectra closest to local noon. At each wavelength pixel, we combined those 80 observations using inverse variance weighting to boost the SNR. Each row in the saved spectra is therefore one daily averaged spectrum.

We limited the data to January 2023 to April 2024. This leaves us with 340 observing days in the KITCAT data and 36 observing days in the full spectra. We also cut off wavelengths below 3890 Å and above 6600 Å. KITCAT is the spectral line mask used in Isidro Gómez-Vargas et al.'s paper (https://arxiv.org/html/2606.18464v1#S4.SS1), originally from Al Moulla et al. (2022). It keeps selected regions around spectral lines that have little contamination from tellurics or instrumental effects. Note, there are therefore gaps between the retained wavelength regions.

- The regular spectra keep 36 observing days starting with the first available day in October 2023. These run from October 13 to November 26, 2023.
- The KITCAT spectra have 340 days from December 15, 2022 to May 15, 2024.

These are observing days, so there can be gaps between dates. The two selections keep each spectral array below 100 MB.

### large_data_products

This folder contains the spectra and their uncertainties as NumPy arrays. Rows are observing days and columns are wavelength pixels.

| File | Contents | Shape |
| `spectra.npy` | Daily averaged regular spectra | `(36, 317104)` |
| `spectra_err.npy` | Uncertainties for the regular spectra | `(36, 317104)` |
| `spectra_kitcat.npy` | Daily averaged spectra after the KITCAT mask | `(340, 35166)` |
| `spectra_kitcat_err.npy` | Uncertainties for the KITCAT spectra | `(340, 35166)` |

### support_files
# Note, I believe these wavelength grids are in vaccuum, not air wavelength solutions. There is a conversion factor between the two.

- `wavelengths.npy`: the wavelength grid in Å for the regular spectra. matches the columns in `spectra.npy` and `spectra_err.npy`.
- `waves_kitcat.txt`: the wavelength grid in Å for the KITCAT spectra. matches the columns in `spectra_kitcat.npy` and `spectra_kitcat_err.npy`.
- `spectra_time_df.csv`: the time table for the 36 regular-spectrum days.
- `kitcat_time_df.csv`: the time table for the 340 KITCAT days.
- `daily_bin_summary.png`: median SNR as a function of time, with the 36 regular-spectrum days highlighted.

Each time table is in the same row order as its matching spectra and error arrays. In both tables, SNR is the median of the flux/uncertainty ratios across the regular spectrum between 3890 and 6600 Å for that day.