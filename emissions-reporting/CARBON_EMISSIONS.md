# Carbon Emissions and Computational Sustainability

> **Fill this template to the best of your abilities**
>
> Use the [CodeCarbon](https://codecarbon.io/) library or an equivalent carbon emissions tracking library (some examples include [carbon tracker](https://github.com/saintslab/carbontracker), [experiment impact tracker](https://github.com/Breakend/experiment-impact-tracker), and  [Eco2AI](https://github.com/sb-ai-lab/Eco2AI), among others) to estimate carbon emissions
> associated with your tutorial especially if it involved model training, fine-tuning, hyperparameter search, benchmarking,
> substantial inference, data processing, or other computation. For an example of how to use CodeCarbon please refer to the
> following Hanna et. al, CCAI Tutorial: 
> [_Reducing Your Climate Impact When Training Machine Learning Models (2026)_](https://colab.research.google.com/github/climatechange-ai-tutorials/tracking-ml-emissions/blob/main/Tracking_Emissions_from_ML_Models_(Revised).ipynb)]
>
> Report to your best ability. If a value was not measured or cannot be
> reasonably estimated, write **Not measured**, **Unknown**, or
> **Not applicable** rather than guessing. If using CodeCarbon, you may leave output `emissions.csv` generated in the parent directory `emissions-reporting`.
>
> Your work in carbon emissions reporting will be a part of a pilot program with CCAI as part of a broader effort to improve reporting best practices.
> For Round II tutorial review, we ask all authors to report operational carbon emissions at a minimum. 
> Sections pertaining to embodied emissions as well as idle consumption emissions estimates for water usage are exploratory and we appreciate you answering to the
> best of your abilities.
> In sections that overlap in `model-card/MODEL_CARD.md` and `datasheet/DATASHEET.md` with emissions reporting, you may place a note rerouting readers to this file.

---

## 1. Computation included

### What computation did you perform?

Select all that apply:

- [x] Data preprocessing
- [x] Model training
- [ ] Model fine-tuning
- [ ] Hyperparameter tuning
- [x] Model evaluation or benchmarking
- [x] Model inference
- [x] Complete notebook execution
- [x] Other: CMEMS data download and remote storage energy is not included; development/debugging runs are not included.[Describe]

### What is included in the energy and emissions reported below?

Select all that apply:

- [x] Data preprocessing
- [x] Model training
- [ ] Model fine-tuning
- [ ] Hyperparameter tuning
- [x] Model evaluation or benchmarking
- [x] Model inference
- [x] Complete notebook execution
- [x] Other: CMEMS data download and remote storage energy is not included; development/debugging runs are not included.[Describe]

**Briefly explain what was tracked:**

CodeCarbon (`EmissionsTracker`) is started right after the imports cell and stopped in the
"Emissions report" cell just before the conclusion. It therefore covers loading and
preprocessing the data, the circularity demonstration, all baselines, RF/GB training,
permutation importance, spatial block cross-validation (40 Random Forest fits), training of
six CNNs, evaluation and plotting.

### What is not included?

- Data downloads from CMEMS (network and remote storage energy; these run before the tracker
  can account for remote infrastructure, and CodeCarbon only measures local hardware).
- Development and debugging runs made while writing the tutorial.

If everything was tracked, write **None known**.

---

## 2. Hardware and computing environment
**Computing environment:**

- [ ] Personal computer or workstation
- [ ] Institutional server
- [ ] Computing cluster
- [ ] Cloud system
- [x] Google Colab
- [x] Other: CMEMS data download and remote storage energy is not included; development/debugging runs are not included.[Describe]

**CPU:**  
Intel(R) Xeon(R) CPU @ 2.00GHz, 2 cores

**GPU or other accelerator:**  
1 × NVIDIA T4 (Colab)

**RAM:**  
12.7 GB

**Computing provider:**  
Google Colab (Google Cloud)

**Computing location or cloud region:**  
United States, South Carolina (Google Colab runtime, as detected by CodeCarbon)

> Report the hardware that performed the computation. Location is
> useful because electricity can have different carbon emissions
> depending on where it is generated.

---

## 3. Operational carbon emissions (Required)

### How were emissions tracked?

- [x] CodeCarbon
- [ ] Another tracking tool
- [ ] Cloud or system information
- [ ] Not measured
- [ ] Not applicable

**Tool:**  
CodeCarbon

**Tool version:**  
3.3.1

**Number of runs included:**  
1 (one complete end-to-end notebook execution)

**Total computing time:**  
1.67 hours (6,008 s), one complete end-to-end run on 2026-10-14

**Energy used:**  
0.058 kWh (GPU 0.039 kWh, RAM 0.017 kWh, CPU 0.003 kWh)

**Carbon emissions:**  
0.017 kg CO2e (16.7 g CO2e)

**Source used to determine the carbon emissions of the electricity:**  
CodeCarbon (regional grid carbon intensity)

> The raw CodeCarbon output is in `emissions-reporting/emissions.csv`. CodeCarbon did not detect the cloud provider (`on_cloud = N`) and used a PUE of 1.0, so data-centre overheads such as cooling are not included in these figures.

> If you use CodeCarbon, report the values produced by the tracked
> experiment and record the version you used. Keep the units with
> the reported values.

---

## 4. Embodied Emissions (Report if possible)

Computer hardware also has an environmental cost from its
manufacture and production. These emissions are separate from the
electricity used while running your code.

**Were these emissions estimated?**

- [ ] Yes
- [x] No
- [ ] Not applicable

If yes:

**Estimated emissions assigned to this work:**  
[kg CO2e]

**Source or method:**  
[Source]

**How was the estimate assigned to this project?**  
[Brief explanation]

> If you do not have enough information to make a reasonable
> estimate, select **No**.


---

## 5. Idle Consumption emissions: Water use (Report if possible)

**Was water use estimated?**

- [ ] Yes
- [x] No
- [ ] Not applicable

If yes:

**Estimated water use:**  
[Liters]

**Source or method:**  
[Source]

**What does the estimate include?**

- [ ] Data-center cooling
- [ ] Water associated with electricity generation
- [ ] Both
- [ ] Unknown

> Water use can vary by location and computing infrastructure.
> Report the source or method used for the estimate. If reliable
> information is unavailable, select **No**.

---

## 6. Reducing computational impact (Required)

What did you do to reduce unnecessary computation?

Select all that apply:

- [ ] Reused a pretrained model
- [x] Used a simpler or smaller model
- [x] Used early stopping
- [x] Reduced hyperparameter trials
- [ ] Used a more efficient hyperparameter search
- [ ] Reduced repeated experiments
- [x] Reused cached or previously computed results
- [ ] Used more efficient hardware
- [ ] Used a lower-carbon computing location or time
- [ ] Reduced unnecessary inference
- [x] Other: sub-sampling (every 3rd day, every 2nd pixel, 100k training samples); smaller (50-tree) forests for the 40 spatial-CV fits; small validation downloads (one year / two weeks)
- [ ] No specific reduction strategy was used

**Briefly explain the most important action taken:**

Training data are sub-sampled in time, space and number of samples, and all CNN patches are pre-extracted once and reused across targets instead of being rebuilt in a Python data loader every epoch. CNNs use early stopping (patience 4, maximum 20 epochs) because validation loss plateaued after a few epochs in earlier runs. Downloads are skipped when files already exist, so re-running the notebook does not re-download data.

> Report actions you actually took. If possible, say how they
> affected runtime, number of experiments, energy use, or emissions.


---

## 7. Effect on model performance

Did reducing computation affect model performance or the
educational outcome?

- [ ] Yes
- [ ] No meaningful difference observed
- [x] Not evaluated
- [ ] Not applicable

**What changed?**

Not formally evaluated. Early stopping restores the weights from the best validation epoch, so it should not reduce validation performance relative to training for the full 20 epochs. The effect of sub-sampling is left to learners (Exercise 2 extends the training period).

> When possible, explain both the computational savings and any
> change in model performance.

---

## 8. Limitations (Required)

What is missing or uncertain in the reported values?

Select all that apply:

- [x] Some experimental runs were not tracked
- [x] Hardware energy use was estimated
- [ ] Exact computing location was unknown
- [ ] Electricity carbon emissions were estimated
- [x] Data-center energy use was not fully included
- [x] Hardware-production emissions were not estimated
- [x] Water use was not estimated
- [x] Remote AI-service infrastructure could not be measured
- [x] Other: CMEMS data download and remote storage energy is not included; development/debugging runs are not included.