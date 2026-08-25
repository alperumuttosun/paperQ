# Heat–Yield Relationships Across the Yield Distribution: Irrigation Capacity as a Moderating Factor.

Eight-crop, multi-country panel (1961–2024), location-scale quantile estimator (Machado & Santos Silva, 2019), testing whether temperature sensitivity varies across the conditional yield distribution and whether equipped-irrigation infrastructure moderates that relationship.

## Requirements

```r
install.packages(c("terra", "geodata", "dplyr", "readxl", "writexl",
                    "data.table", "tidyr", "zoo", "rnaturalearth",
                    "rnaturalearthdata", "sf", "WDI", "fixest",
                    "purrr", "openxlsx", "ggplot2", "readr", "plm"))
```

Set your own `base_dir` / `data_dir` at the top of each script before running (currently a placeholder local path).

## Pipeline

| Step | Script | What it does |
|---|---|---|
| 00 | `quantile_step00_data_preparation.R` | Builds the panel from raw sources: CRU TS4.09 climate grids, CROPGRIDS crop-area rasters, FAOSTAT yield/production/irrigation data, WDI GDP. Outputs `panelv7_<crop>.xlsx` — the input file for every step below. |
| 01 | `quantile_step01_load_and_meanreg.R` | Loads panels, checks coverage, runs the baseline mean-equation regression. |
| 02 | `quantile_step02_scale_regression.R` | Runs the scale (variance) equation on the mean-equation residuals. |
| 03 | `quantile_step03_standardize_and_beta_tau.R` | Standardizes residuals, recovers quantile-specific TMX coefficients (β_τ). |
| 04 | `quantile_step04_q10_q90_test.R` | Formal bootstrap test: is the q10 effect different from q90? |
| 05 | `quantile_step05_mss_diagnostics.R` | Checks the estimator's own identifying assumptions (residual moments, scale positivity). |
| 05b | `quantile_step05b_outlier_investigation.R` | Investigates whether a few extreme residuals drive the Step 05 diagnostics. |
| 07 | `quantile_step07_baseline_weight_and_irr_interaction.R` | (A) Fixed-baseline-weight robustness check. (B) TMX × irrigation interaction across quantiles. |
| 08 | `quantile_step08_figures.R` | Builds the manuscript figures (per-crop quantile effects, irrigation-buffering curves). |
| 09 | `quantile_step09_quadratic_robustness.R` | Checks whether a quadratic TMX term changes the results. |
| 11 | `quantile_step11_multiple_testing_correction.R` | Benjamini-Hochberg and Romano-Wolf corrections across all 40 crop-quantile tests. |
| 11b | `quantile_step11b_omnibus_and_family_robustness.R` | Omnibus joint test per crop + within-crop family correction. |
| 12 | `quantile_step12_pesaran_cd_diagnostic.R` | Pesaran cross-sectional dependence test on the mean-equation residuals. |
| 13 | `quantile_step13_irrigation_ratio_distribution.R` | Reports the empirical distribution of the equipped-irrigation ratio, by crop. |
| 14 | `quantile_step14_p25_p75_irrigation_comparison.R` | Irrigation-moderation gap at each crop's own 25th/75th percentile of irrigation. |
| 15 | `quantile_step15_lagged_temperature_robustness.R` | Repeats the q10-vs-q90 test with a one-year lag of temperature. |
| 16 | `quantile_step16_data_documentation.R` | Country lists, missingness rates, sample-restriction cascade. |
| 17 | `quantile_step17_mde_power_analysis.R` | Minimum-detectable-effect calculation for the null q10-q90 result. |
| 18 | `quantile_step18_cross_quantile_moderation_test.R` | Tests whether the irrigation-moderation gap itself varies across quantiles. |
| 19 | `quantile_step19_irrigation_weighting_robustness.R` | Fixed-baseline-weight robustness check, applied to the irrigation-moderation gap. |
| 20 | `quantile_step20_lagged_irrigation_interaction.R` | TMX × lagged (prior-year) irrigation, to address contemporaneous-measurement endogeneity. |
| 21 | `quantile_step21_maize_winsorization_sensitivity.R` | Tests whether more aggressive winsorization changes maize's diagnostics or results. |
| 22 | `quantile_step22_leave_decade_out_sensitivity.R` | Re-estimates the irrigation finding after excluding each decade in turn. |
| 23 | `quantile_step23_exclude_high_irrigation_robustness.R` | Re-estimates after dropping the sparse Irr>0.95 tail from the sample entirely. |
| 24 | `quantile_step24_marginal_effect_plot.R` | Continuous marginal-effect-of-TMX-over-irrigation-ratio plot, with bootstrap CI. |

Run in numeric order; each step reads the previous steps' saved output (`.rds`/`.xlsx` files) from `data_dir/quantile_output/`.

## Notes

- All eight crops (wheat, barley, oats, rye, rice, maize, soybean, sorghum) are modeled separately throughout.
- Bootstrap-heavy steps (04, 07, 14, 15, 18–23) use 1,000 country-block replications.
- `panelv7_<crop>.xlsx` files (Step 00's output) are the shared input for every other step and are included in this repository.
