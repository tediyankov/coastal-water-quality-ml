# Model Card - Northern Adriatic bloom-indicator regressors (RF, GB, spatial SST CNN)

> Outline follows Figure 1 of [Mitchell et al., Model Cards for Model Reporting (2019)](https://arxiv.org/abs/1810.03993).
> All models are trained from scratch inside the tutorial notebook. No pretrained weights are distributed.

## Model Details

- **Person or organization developing model:** Tutorial author(s), for the Climate Change AI tutorials track.
- **Model date:** 2026.
- **Model version:** 1.1 (revised after review: common evaluation set, baselines, CNN validity mask and 1×1 ablation, spatial block CV, detection metrics).
- **Model type:** Supervised regression. Per target, the notebook trains:
  - **Random Forest** (`sklearn.ensemble.RandomForestRegressor`, 100 trees, max depth 10);
  - **Gradient Boosting** (`sklearn.ensemble.GradientBoostingRegressor`, 100 trees, max depth 4, learning rate 0.1);
  - **SpatialSSTCNN** (PyTorch, ~60k parameters): 3 conv layers (32-64-64 filters, 3×3, BatchNorm, ReLU), global average pooling, then concatenation with 4 scalar features and an MLP head (64-32-1, dropout 0.3). It is trained in two variants: a 9×9 patch (spatial context) and a 1×1 patch (ablation without spatial context).
  - **Naive baselines:** training mean, pixel mean, pixel monthly climatology, pixel ±15-day climatology, RF on (lat, lon), RF on (lat, lon, season).
- **Inputs (features):** SST (CMEMS L4, °C), sin/cos of calendar month, latitude, longitude. The CNN receives a 2-channel patch (normalised SST + land/validity mask) plus the 4 non-SST scalars.
- **Targets:** log₁₀(Chl-a) (CMEMS L4), NDCI-like index (Rrs670 − Rrs555)/(Rrs670 + Rrs555), log₁₀(Rrs670/Rrs490) turbidity proxy (both from CMEMS L3 Rrs).
- **Training algorithms and parameters:** trees use scikit-learn defaults beyond the parameters above. The CNN is trained on standardised targets (training mean 0, standard deviation 1; predictions are converted back) with MSE loss, Adam (lr 3e-4, weight decay 1e-4), a cosine LR schedule, batch size 512, at most 20 epochs, and early stopping on validation RMSE with patience 4. The seed is fixed at 42.
- **Fairness constraints:** not applicable (no people-related data).
- **Paper or other resource:** the tutorial notebook in `notebook/`.
- **Citation details:** see the repository README.
- **License:** MIT.
- **Questions or comments:** open an issue on this repository.

## Intended Use

- **Primary intended uses:** education, i.e. demonstrating how to build, and above all how to *evaluate*, ML models that link physical ocean drivers to optical water-quality indicators.
- **Primary intended users:** students, researchers and practitioners learning ML for Earth observation.
- **Out-of-scope use cases:** operational HAB warnings, public-health, aquaculture or fishery-closure decisions, or any use outside the Northern Adriatic or outside 2019–2022 without retraining and independent validation. The models predict *satellite-derived proxies*, not in-situ concentrations or toxin levels.

## Factors

- **Relevant factors:** location (distance to the Po Delta, coastal vs offshore), season, cloud cover (targets exist only on clear-sky days), year-to-year variability in river discharge and mixing, and the presence of SST fronts.
- **Evaluation factors:** performance is reported per target, per model, for a held-out year (2022), under random-location vs spatially blocked evaluation, and for the CNN ablation by coastal/offshore and SST-gradient strength.

## Metrics

- **Model performance measures:** R², RMSE, MAE and bias on log-transformed or index targets. Detection metrics for "events" above the 75th percentile of training values: probability of detection (POD), false alarm ratio (FAR), critical success index (CSI) and ROC AUC.
- **Decision thresholds:** event threshold = 75th percentile of the training-set target (configurable via `EVENT_QUANTILE`).
- **Variation approaches:** spatial block cross-validation (0.5° blocks, 4 folds). Seed variance of the CNN is left as an exercise (Exercise 8). A ±0.02 R² band is treated as "no meaningful difference" in the automated verdicts.

## Evaluation Data

- **Datasets:** the same CMEMS products as training (see `datasheet/DATASHEET.md`). Validation = 2021, test = 2022, 20,000 randomly sampled clear-sky pixel-days each. **All models (baselines, RF, GB, both CNNs) are evaluated on exactly the same pixel-days.**
- **Motivation:** a temporal hold-out tests generalisation to an unseen year. Spatial blocks additionally test generalisation to unseen locations.
- **Preprocessing:** every 3rd day and every 2nd pixel are kept. SST is bilinearly regridded to the 1 km ocean-colour grid. Targets are derived with a single shared function, and inputs are standardised with training-set statistics only.

## Training Data

2019–2020, 100,000 randomly sampled clear-sky pixel-days from the same sample table. Climatology baselines use all training-year pixel-days in the sub-sampled cubes.

## Quantitative Analyses

- **Unitary results:** the notebook produces test-set tables of R², RMSE, POD and CSI for every model × target, a validation-to-test generalisation table, and automated verdicts (best baseline vs best tree model, the value of SST, and the value of spatial context).
- **Intersectional results:** CNN 9×9 vs 1×1 RMSE by coastal/offshore and by SST-gradient tercile. Random-location vs spatial-block R² with and without coordinates.
- Headline results (2022 test year): best tree models and the best pixel climatology are within ±0.02 R² for every target (Chl-a ≈ 0.48–0.50, NDCI-like ≈ 0.35, turbidity ≈ 0.41–0.42). SST adds no robust skill beyond location and season. The 9×9 CNN beats the 1×1 CNN for NDCI-like and turbidity (+0.03 to +0.06 R²) but not for Chl-a, and both CNNs trail the Random Forest. All models lose about 0.3 R² from 2021 to 2022 and overpredict 2022 (Chl-a about 1.5× too high on average), consistent with the 2022 Po River drought.
- Exact numbers depend on the CMEMS data version and are printed when the notebook is run.

## Ethical Considerations

- The data contain no personal information.
- The main risk is **over-trust**: a model with respectable R² can still miss many bloom events (low POD), and its skill is partly a learned climatology. Results must not be used to declare waters safe.
- Coastal communities in data-poor regions are the motivating beneficiaries, but these models were developed only for the Northern Adriatic and would need local retraining and validation elsewhere.

## Caveats and Recommendations

- Targets are satellite proxies. The NDCI-like index is not NDCI (no 708 nm band in the multi-sensor archive). The turbidity proxy is uncalibrated and was checked only by rank agreement with KD490/BBP443, which share the same reflectance inputs.
- The three targets are strongly correlated (r = 0.72–0.92), so they are not independent tasks.
- Training and evaluation use clear-sky days only, so performance on cloudy days is untested.
- Location and season dominate. Lat/lon allow location memorisation, quantified with spatial block CV.
- Only two training years, one validation year, one test year and one CNN seed are used.
- Recommended next steps: add river discharge and wind forcing, use longer records, validate against in-situ data, and estimate seed and year-to-year variance.
