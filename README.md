# Cyclone Remal (2024): WRF-ARW forcing-sensitivity study

Analysis and figure code for the preprint:

> Hakim, M. A. (2026). *WRF-ARW Simulation of Cyclone Remal (2024): A Comparative Assessment of FNL, GFS, CFSv2, and ERA5 Forcing Datasets.* SSRN preprint. https://doi.org/10.2139/ssrn.7609299

Four WRF-ARW v4.7.1 experiments of Severe Cyclonic Storm Remal (24–28 May 2024, Bay of Bengal) share identical domains and physics, so the large-scale forcing (FNL, GFS, CFSv2 or ERA5) is the only difference between runs. Each run is validated against the IMD best track, GPM IMERG V07B rainfall and CCMP V3.1 ocean-surface winds over a common 102-hour analysis window.

To see the results without running anything, open `Remal_Paper_Figures.pdf` for the eight paper figures, or `Remal_WRF_Complete_Analysis_Report.pdf` for every table and diagnostic figure. Both open directly in the browser.

## Key results

- ERA5 and GFS were hard to separate. ERA5 had the lowest track RMSE (109.2 km), landfall-position error (67.8 km), wind and pressure RMSE, and CCMP wind-speed RMSE (5.02 m s⁻¹). GFS had the smallest landfall-timing error (−2.48 h), the lowest rainfall RMSE (163.86 mm) and the highest CCMP spatial correlation (0.558).
- CFSv2 came closest to the observed peak wind and minimum pressure, but its track drifted west (track RMSE 273.3 km, landfall-position error 315.6 km).
- FNL and GFS overintensified the storm, giving common-period PDI values more than 2.7 times the IMD value.
- All four runs underestimated accumulated rainfall.
- No forcing dataset was best on every metric, so forcing skill has to be judged across track, intensity, rainfall and wind structure together.

## What's in this folder

- **Remal_Analysis.ipynb**: computes every validation statistic in the paper (analyses A01–A16) and builds the complete analysis report
- **Remal_Paper_Figures.ipynb**: draws the eight manuscript figures from the analysis outputs
- **Remal_Paper_Figures.pdf**: the eight figures the figures notebook produces, with their preprint captions
- **Remal_WRF_Complete_Analysis_Report.pdf**: every table and figure saved by the analysis notebook, section by section (109 pages)
- **remal_namelist.wps** and **remal_namelist.input**: WPS and WRF configuration
- **IMD_Remal_Report_2024.pdf**: the India Meteorological Department report on Remal, which supplies the best track used for validation
- **README.md**: this file

Model output and observation files are not included because of their size. The WRF model outputs are available from the author on request (see Author). The forcing and validation datasets are public; their sources are listed in the preprint and under Data sources below.

## How to reproduce

1. **Run WPS and WRF.** Use `remal_namelist.wps` and `remal_namelist.input` with each forcing dataset. One line changes between experiments: `num_metgrid_levels = 34` for FNL and GFS, and `38` for CFSv2 and ERA5 (noted in the namelist).
2. **Run `Remal_Analysis.ipynb`.** Set `BASE_DIR` (section S2) to a folder arranged like this:

   ```
   BASE_DIR/
   ├── fnl/                         wrfout_d02_* files, 3-hourly
   ├── gfs/
   ├── csfv2/                       CFSv2 run (folder name spelled as in the code...sorry for the typo..¯\_(ツ)_/¯ )
   ├── era_5/
   ├── CCMP V3.1/                   CCMP_Wind_Analysis_YYYYMMDD_V03.1_L4.nc
   ├── GPM_3IMERGHH_07_Remal/       IMERG half-hourly *.HDF5
   └── Remal_IMD_Validation .csv    IMD best track (note the space before .csv...sorry again ¯\_(ツ)_/¯)
   ```

   The IMD CSV is transcribed from Table 1 of `IMD_Remal_Report_2024.pdf`, with the columns `time_utc, latitude, longitude, wind_speed_kt, wind_speed_ms, central_pressure_hpa`. Then choose **Restart Kernel and Run All Cells**. Outputs are written to `BASE_DIR/analysis_outputs/`, one subfolder per analysis (A01–A16), and the Word report goes to `analysis_outputs/FINAL_REPORT/` (`Remal_WRF_Complete_Analysis_Report.pdf` here is a PDF copy of it).
3. **Run `Remal_Paper_Figures.ipynb`.** It reads those outputs and saves the paper figures to `BASE_DIR/paper_figures/` as PNG and PDF. It does not recompute any statistic.

In both notebooks, every value meant to be edited (paths, times, regions, colours, figure sizes) is marked with `# <----------------------- change this ...`.

## Where each paper figure and table comes from

**Figures** (drawn in `Remal_Paper_Figures.ipynb`, in the section matching the paper):

- **Figure 1**, d02 domain and IMD track (Section 2.2): WRF d02 grid and the IMD CSV
- **Figure 2**, tracks, position error and translation speed (Section 3.1): `A03_track_validation/Remal_<model>_vs_IMD.csv` and `A04_translation_speed/A04_translation_speed_main.csv`
- **Figure 3**, wind and pressure against IMD (Section 3.2): `A07_intensity_change/Remal_<model>_All_IMD_Intensity_Matches.csv`
- **Figure 4**, landfall crossings (Section 3.3): `A08_landfall/Remal_A6_Landfall_Crossings.csv`
- **Figure 5**, rainfall against IMERG (Section 3.4): maps rebuilt from WRF and IMERG for display; panel (c) from `A10_rainfall/F07_Regional_6h_Rain_Rate.csv`
- **Figure 6**, ocean winds against CCMP (Section 3.5): maps rebuilt from WRF and CCMP at 1200 UTC 26 May for display
- **Figure 7**, PW, RH850 and latent heat flux (Section 3.6): `A12_moisture_flux/A12_<model>_20240526_1200_PW_RH850.nc` and `A12_regional_latent_heat_flux.csv`
- **Figure 8**, shear and vertical sections (Section 3.7): `A13_shear/A13_850_200hPa_environmental_shear.csv` and `A14_vertical_sections/A14_<model>_20240526_1200_vertical_section.nc`

**Tables** (computed in `Remal_Analysis.ipynb`):

- **Table 3**, track, translation and landfall: `A03/Remal_A2_Track_Stats.csv`, `A04/T03_translation_speed_scores.csv`, `A08/Remal_A6_Landfall_Crossings.csv`
- **Table 4**, intensity and PDI: the time-weighted columns of `A05/T03_A05_Wind_Stats_All_IMD_Matches.csv` and `A06/T03_A06_Pressure_Stats_All_IMD_Matches.csv`, plus `A07/T04_A07_Peak_Intensity_Timing_All_IMD_Matches.csv` and `A09/T05_A09_Common_Period_PDI.csv`
- **Table 5**, IMERG and CCMP scores: `A10/T06_Rainfall_Spatial_Scores.csv` and `A11/T06_CCMP_Wind_Score_Summary.csv`

`A15_summary/T07_Consolidated_Performance_and_Diagnostics.csv` collects the Table 3–5 values in one file. A few filenames keep older prefixes (`Remal_A2_`, `Remal_A6_`, `T03_`) because the figures notebook reads them under those names.

## Model configuration

- **Model:** WRF-ARW v4.7.1 (WPS/METGRID v4.6.0)
- **Integration:** 0000 UTC 24 May to 0000 UTC 29 May 2024 (120 h); analysis window ends 0600 UTC 28 May (102 h)
- **Domains:** Mercator, centered on 15.0°N, 88.4°E; d01 at 27 km (190 × 190), d02 at 9 km (280 × 280), two-way nesting
- **Vertical:** 45 levels, model top 100 hPa
- **Time step and output:** 90 s; 3-hourly history; boundaries and SST updated every 6 h
- **Physics:** Thompson microphysics, Kain–Fritsch (d01 only), YSU PBL, revised MM5 surface layer, Noah LSM, RRTM longwave, Dudhia shortwave, `isftcflx = 1`

## Data sources

- **FNL:** NCEP GDAS/FNL 0.25° analyses, https://doi.org/10.5065/D65Q4T4Z
- **GFS:** NCEP GFS 0.25° historical archive, https://doi.org/10.5065/D65D8PWK
- **CFSv2:** NCEP CFSv2 6-hourly products, https://doi.org/10.5065/D61C1TXF
- **ERA5:** ECMWF ERA5 via the Copernicus Climate Data Store
- **IMERG:** GPM IMERG Final V07 half-hourly, https://doi.org/10.5067/GPM/IMERG/3B-HH/07
- **CCMP:** Remote Sensing Systems CCMP V3.1 (Mears et al., 2022, https://doi.org/10.3390/rs14174230)
- **IMD:** India Meteorological Department (2024), *Severe Cyclonic Storm "REMAL" over the Bay of Bengal (24th–28th May, 2024): A report*, Cyclone Warning Division, New Delhi. Included here as `IMD_Remal_Report_2024.pdf`; the document belongs to IMD.

## Python environment

Python 3.10 with `wrf-python`, `netCDF4`, `xarray`, `h5py`, `numpy`, `pandas`, `scipy`, `matplotlib`, `cartopy`, `Pillow` and `python-docx`.

## Citation

```bibtex
@misc{hakim2026remal,
  author       = {Hakim, Md. Azizul},
  title        = {{WRF-ARW} Simulation of Cyclone Remal (2024): A Comparative Assessment of {FNL}, {GFS}, {CFSv2}, and {ERA5} Forcing Datasets},
  year         = {2026},
  howpublished = {SSRN preprint},
  doi          = {10.2139/ssrn.7609299}
}
```

## Author

**Md. Azizul Hakim**, Department of Oceanography, University of Chittagong
ORCID: [0009-0000-7391-5736](https://orcid.org/0009-0000-7391-5736) · azijul.cu@gmail.com
