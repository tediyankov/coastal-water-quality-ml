# Monitoring Coastal Water Quality and Marine Ecosystem Change Using Satellite Remote Sensing and Machine Learning

![Python](https://img.shields.io/badge/language-Python%203.12-blue)
![Jupyter Notebook](https://img.shields.io/badge/Jupyter-Notebook-orange)
![PyTorch](https://img.shields.io/badge/PyTorch-2.x-ee4c2c)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.4.2-f7931e)
![MIT License](https://img.shields.io/badge/license-MIT-green)

## Abstract

Harmful algal blooms (HABs) are becoming more frequent and intense as coastal seas warm and storm runoff delivers larger nutrient pulses, with consequences for ecosystems, fisheries, tourism and public health. Satellite remote sensing provides daily, basin-wide observations of the optical signatures of bloom conditions, but ocean-colour sensors are blind under clouds, often exactly when blooms are developing.

This tutorial builds an end-to-end, reproducible pipeline that predicts three optical water-quality indicators (chlorophyll-a, an NDCI-like red–green index and a red/blue turbidity proxy) in the Northern Adriatic Sea from **physically independent drivers**: thermal-infrared sea surface temperature (SST), seasonality and location. Using four years (2019–2022) of Copernicus Marine Service data, learners progress from exploratory analysis through Random Forest and Gradient Boosting to a patch-based convolutional neural network that exploits spatial SST context.

The tutorial puts as much weight on *evaluating* models honestly as on training them. It demonstrates the input–target circularity problem explicitly, benchmarks every model against naive climatology baselines on a common evaluation set, measures spatial leakage with spatial block cross-validation, isolates the value of spatial context with a 1×1 vs 9×9 CNN ablation, reports bloom-detection metrics (POD, FAR, CSI, AUC) alongside R² and RMSE, and validates the derived targets against true NDCI (Sentinel-3 OLCI) and CMEMS optical products (KD490, BBP443).

### Authors:

- **[Author full name]**, [Affiliation/Institution], [contact email]

> Reminder for Round II reviews please keep all tutorial materials anonymized to support the double-blind review process.

Originally presented at the [insert CCAI event or workshop full name with the year].

## Learning Objectives

After completing this tutorial, participants should be able to:

1. **Access and query** ocean colour and SST data from the Copernicus Marine Service (CMEMS) using the `copernicusmarine` Python toolbox.
2. **Understand** the physical meaning of key water quality indicators: chlorophyll-a, NDCI and NDCI-like indices, turbidity and sea surface temperature.
3. **Explore** spatiotemporal patterns in coastal water quality using `xarray`, `pandas`, `matplotlib` and `cartopy`.
4. **Engineer a feature matrix** from physically independent drivers and explain, with a worked demonstration, why input–target independence matters.
5. **Train and evaluate** Random Forest and Gradient Boosting models across multiple ocean colour targets, and benchmark them against naive baselines.
6. **Build and train** a spatial CNN with a land/validity mask, and use a controlled ablation to judge when spatial deep learning helps and when it doesn't.
7. **Critically evaluate** model performance using baselines, spatially blocked validation, permutation importance and bloom-detection metrics.

### Intended Audience

Students, researchers and practitioners in climate science, oceanography, remote sensing or environmental data science who want a hands-on introduction to applying machine learning to satellite Earth observation data, and ML practitioners who want a realistic geospatial case study with pitfalls such as circularity and spatial autocorrelation.

### Prerequisites

Participants should be familiar with:

- Basic to intermediate Python (NumPy, pandas, matplotlib)
- Introductory machine learning concepts (train/validation/test splits, regression, overfitting)
- Basic oceanography or climate science concepts (helpful but not required; relevant concepts are explained in the notebook)

### Tutorial walkthrough video

➡️ [**Video URL**](url)
**Duration:** [XX minutes]

---

## Repository Contents

| Resource | Description |
|----------|-------------|
| `notebook/` | Jupyter notebook (designed for Google Colab) |
| `model-card/` | Model documentation for the RF, GB and CNN models |
| `datasheet/` | Documentation of the CMEMS datasets and derived targets |
| `emissions-reporting/` | Energy/carbon reporting |
| `figures/` | Images used in the notebook |

---

## Software Requirements

**Primary language:** Python

**Language version:** Python 3.12 (Google Colab default); also tested with Python 3.11

### Major Packages

- numpy 1.26.4
- scikit-learn 1.4.2
- torch 2.x (CUDA 11.8 build on Colab)
- xarray, netCDF4
- copernicusmarine 2.x
- cartopy, cmocean, seaborn
- codecarbon (optional, for emissions tracking)

See `requirements.txt` for the complete environment.

---

## Access this tutorial

We recommend executing this notebook in a Colab environment to gain access to GPUs and to manage all necessary dependencies. <a target="_blank" href="https://colab.research.google.com/github/tediyankov/coastal-water-quality-ml/blob/main/notebook/monitoring_coastal_water_quality_with_ml.ipynb">
  <img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/>
</a>

To run locally, see:

➡️ `notebook/monitoring_coastal_water_quality_with_ml.ipynb`

You will need a free [Copernicus Marine Service account](https://marine.copernicus.eu/). On Colab, store your credentials as the secrets `CMEMS_USERNAME` and `CMEMS_PASSWORD`. Locally, set the environment variables `COPERNICUSMARINE_SERVICE_USERNAME` and `COPERNICUSMARINE_SERVICE_PASSWORD`.

**Estimated time to execute end-to-end:** [insert runtime here].

**Last successfully tested:** [YYYY-MM-DD]

### Model Information

This submission does involve a machine-learning model: Random Forest, Gradient Boosting and convolutional neural network regressors, plus naive baselines, trained from scratch inside the notebook.

➡️ `model-card/MODEL_CARD.md`

### Dataset Information

This submission does use datasets: publicly available Copernicus Marine Service (CMEMS) ocean colour and sea surface temperature products, downloaded inside the notebook. No custom dataset is redistributed.

➡️ `datasheet/DATASHEET.md`

### Sustainbility/Carbon Emissions

Carbon emissions associated with the computational experiments are measured using [CodeCarbon](https://codecarbon.io/), which is started and stopped inside the notebook.

For more details, go to:

➡️ `emissions-reporting/CARBON_EMISSIONS.md`

---

## Contribute to this tutorial

Please refer to these [GitHub instructions](https://docs.github.com/en/get-started/exploring-projects-on-github/contributing-to-a-project#about-forking) to open a pull request via the "fork and pull request" workflow.

Pull requests will be reviewed by members of the Climate Change AI Tutorials team for relevance, accuracy, and conciseness.

## Climate Change AI Tutorials

Check out the [tutorials page](https://www.climatechange.ai/tutorials?) on our website for a full list of tutorials demonstrating how AI can be used to tackle problems related to climate change.

## License

Usage of this tutorial is subject to the MIT License.

## Cite

### Plain Text

[Insert plain text citation of accepted submission here.]

### BibTeX

[Insert BibTex citation of accepted submission here.]
