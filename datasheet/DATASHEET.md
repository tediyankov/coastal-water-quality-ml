# Datasheet - Northern Adriatic CMEMS ocean colour and SST subset (2019–2022)

> Format follows Gebru et al., [_Datasheets for Datasets (2021)_](https://arxiv.org/abs/1803.09010).
> This tutorial does **not** create or redistribute a new dataset. It downloads subsets of public
> Copernicus Marine Service (CMEMS) products at run time and derives three targets from them.
> This datasheet documents those subsets and the derived variables.

The tutorial uses daily satellite products for the Northern Adriatic Sea (12.0–15.5°E, 43.5–45.8°N, 2019–2022) to predict optical indicators of harmful-algal-bloom conditions from sea surface temperature, season and location.

| Role | CMEMS dataset ID | Variables | Period |
|------|------------------|-----------|--------|
| Target (Chl-a) and EDA | `cmems_obs-oc_med_bgc-plankton_my_l4-gapfree-multi-1km_P1D` (OCEANCOLOUR_MED_BGC_L4_MY_009_144) | `CHL` | 2019–2022 |
| Model input | `cmems_SST_MED_SST_L4_REP_OBSERVATIONS_010_021` (SST_MED_SST_L4_REP_OBSERVATIONS_010_021) | `analysed_sst` | 2019–2022 |
| Target derivation | `cmems_obs-oc_med_bgc-reflectance_my_l3-multi-1km_P1D` (OCEANCOLOUR_MED_BGC_L3_MY_009_143) | `RRS412`, `RRS443`, `RRS490`, `RRS555`, `RRS670` | 2019–2022 |
| Turbidity-proxy check | `cmems_obs-oc_med_bgc-transp_my_l3-multi-1km_P1D` | `KD490` | 2021 |
| Turbidity-proxy check | `cmems_obs-oc_med_bgc-optics_my_l3-multi-1km_P1D` | `BBP443` | 2021 |
| NDCI check | `cmems_obs-oc_med_bgc-reflectance_my_l3-olci-300m_P1D` | `RRS490`, `RRS560`, `RRS665`, `RRS708_75` | 1–14 Apr 2021 |

## Motivation

- **For what purpose was the dataset created?** The underlying CMEMS products are operational, multi-year reanalysis/reprocessed products for monitoring Mediterranean ocean state. The tutorial uses a regional subset to teach ML-based water-quality monitoring and the pitfalls of evaluating it.
- **Who created this dataset?** The products are produced by the Copernicus Marine Service production centres on behalf of the European Union's Copernicus programme. The subsetting and derived targets are produced by the tutorial code.
- **Funding/support:** Copernicus Marine Service is funded by the European Union.

## Composition

- **What do the instances represent?** Pixel-days: one ~1 km ocean pixel on one day, with SST, calendar month, coordinates and three targets.
- **How many instances are there?** After sub-sampling (every 3rd day, every 2nd pixel), all clear-sky ocean pixel-days with valid SST and all three targets form the sample table. The tutorial then draws 100,000 training (2019–2020), 20,000 validation (2021) and 20,000 test (2022) pixel-days. The exact table size is printed by the notebook.
- **Sample or complete?** A sample: a spatial and temporal sub-sample of the full products, restricted to clear-sky days because two targets come from cloud-masked L3 data. It is therefore **not representative of cloudy days**.
- **What data does each instance consist of?** Inputs: SST (°C), sin/cos(month), latitude, longitude. Targets: log₁₀(Chl-a), NDCI-like index (Rrs670 − Rrs555)/(Rrs670 + Rrs555), and log₁₀(Rrs670/Rrs490). The raw Rrs bands are kept only for the circularity demonstration.
- **Is there a label or target?** Yes, the three targets above. They are satellite-derived proxies, not in-situ measurements.
- **Is any information missing?** Yes. L3 reflectance is missing under clouds and where quality flags fail, and land pixels have no values. Pixel-days with any missing input or target are dropped.
- **Are relationships between instances explicit?** Implicitly: each instance stores its (time, row, column) position in the data cube. This is used to extract SST patches for the CNN and to build spatial blocks.
- **Recommended data splits:** temporal: train 2019–2020, validate 2021, test 2022. Spatial block cross-validation (0.5° blocks) is used to assess generalisation to unseen locations.
- **Errors, noise or redundancies:** satellite retrieval errors (atmospheric correction, adjacency effects near the coast, sensor merging). Negative or near-zero red reflectance in clear water. Strong spatial and temporal autocorrelation between neighbouring pixel-days. The three targets are strongly inter-correlated (r ≈ 0.72–0.93), and two of them share the Rrs(670) numerator.
- **Self-contained?** No. The notebook downloads the data from CMEMS at run time (free account required). CMEMS maintains versioned multi-year products, but reprocessing can update values, so results may change slightly between product versions.
- **Confidential, offensive or person-related data?** No.

## Collection Process

- **How was the data acquired?** By satellite remote sensing (ocean colour radiometers such as MODIS, VIIRS and Sentinel-3 OLCI, and thermal-infrared radiometers for SST), processed by CMEMS into gridded L3/L4 products. Chl-a, KD490 and BBP443 are algorithm-derived from reflectance. L4 products are gap-filled by optimal interpolation.
- **Mechanisms or procedures:** `copernicusmarine.subset` via the `copernicusmarine` Python toolbox.
- **Sampling strategy:** deterministic stride (3 days, 2 pixels), followed by random sampling with a fixed seed (42) within each split.
- **Who was involved in data collection?** Copernicus Marine Service. No crowdworkers were involved.
- **Timeframe:** observations from 2019-01-01 to 2022-12-31 (validation subsets: 2021).
- **Ethical review:** not applicable (no human subjects).

## Preprocessing/cleaning/labeling

- **Preprocessing:** SST converted from K to °C and bilinearly regridded onto the 1 km ocean-colour grid. Targets derived with one shared function. Month encoded as sin/cos. Inputs standardised with training-split statistics. For the CNN, SST is normalised, land/missing cells are filled with 0, and a validity-mask channel is added, with edge padding for border pixels.
- **Raw data saved?** Downloaded NetCDF files are kept in `data/` in the user's runtime. They are not redistributed in this repository.
- **Software:** the tutorial notebook in `notebook/`.

## Uses

- **Tasks already used for:** this tutorial (regression of optical bloom indicators from physical drivers, and evaluation methodology).
- **Other possible tasks:** bloom-event classification, gap-filling, forecasting with temporal models, multi-task learning.
- **Composition/collection factors that might impact future uses:** clear-sky-only sampling, proxy (not in-situ) targets, strong autocorrelation, and a single basin dominated by Po River discharge.
- **Tasks the data should not be used for:** public-health or fishery-closure decisions, or claims about toxin presence (the data contain no toxin or species information).

## Distribution

- **Distributed to third parties?** No redistribution by this repository. Users download directly from CMEMS.
- **How/where?** [Copernicus Marine Data Store](https://data.marine.copernicus.eu/). Each product has a DOI listed on its product page.
- **License / terms of use:** Copernicus Marine Service products are free and open, subject to the [Copernicus Marine Service licence and terms of use](https://marine.copernicus.eu/user-corner/service-commitments-and-licence), which require acknowledgement of the source.
- **Third-party IP restrictions / export controls:** none known beyond the CMEMS terms.

## Maintenance

- **Who maintains the data?** Copernicus Marine Service maintains the source products. The tutorial authors maintain the subsetting and derivation code.
- **Contact:** via the CMEMS user service for the products, and via issues on this repository for the tutorial code.
- **Erratum:** none.
- **Updates:** CMEMS periodically reprocesses multi-year products. Re-running the notebook downloads the current version.
- **Older versions:** see the CMEMS product pages for version history.
- **Contributions:** pull requests to this repository are welcome (see README).

## References

- Copernicus Marine Service product pages: OCEANCOLOUR_MED_BGC_L4_MY_009_144, OCEANCOLOUR_MED_BGC_L3_MY_009_143, SST_MED_SST_L4_REP_OBSERVATIONS_010_021 (https://data.marine.copernicus.eu/).
- Gebru, T., et al. (2021). Datasheets for Datasets. Communications of the ACM, 64(12), 86–92.
