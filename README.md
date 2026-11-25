# Kidney stone prediction (Kaggle Playground Series S3E12)

A binary classification project that predicts whether a patient has kidney stones from routine urine analysis measurements.

## Overview

This is my entry for [Kaggle Playground Series Season 3, Episode 12](https://www.kaggle.com/competitions/playground-series-s3e12), where the task is to predict kidney stones from physical and chemical urine tests. I used Python and `scikit-learn` to explore the data, check its quality, compare feature distributions between the two classes, and train a baseline Random Forest. On an 80/20 validation split it scored 0.7229 accuracy and 0.8263 ROC-AUC, well above the 0.5 you'd get from guessing. I then retrained on the full training set and predicted the Kaggle test set.

## Kaggle submission status

This competition is archived and only open for late submissions, so Kaggle's web interface won't let me upload a file for a leaderboard score.

## Data description

The data is Kaggle's `train.csv` (414 rows, 8 columns) and `test.csv`. Each row is one patient's urine sample: an `id`, a binary `target` (`0` = no stone, `1` = stone), and six numerical features: `gravity` (specific gravity), `ph`, `osmo` (osmolarity), `cond` (conductivity), `urea` (urea concentration) and `calc` (calcium concentration).

There are no missing values and no duplicate rows. The classes are fairly balanced: 230 without stones (55.6%) and 184 with stones (44.4%). Everything was already clean and numeric, so I skipped imputation and encoding. I only dropped `id` before modeling, so it couldn't leak into the model.

## Visualizations

I plotted normalized histograms (`density=True`, shared bin edges) for all six features, one curve per class. `calc` and `gravity` separated the classes best. Patients without stones clustered at low calcium, while patients with stones spread out toward higher values. `ph` overlapped heavily between the classes, so it's a weak predictor on its own.


## Problem formulation

The inputs are the six urine features (`gravity`, `ph`, `osmo`, `cond`, `urea`, `calc`) and the output is the binary target, `0` for no kidney stone and `1` for a stone. The model is `RandomForestClassifier(random_state=42)`, trained on an 80/20 train-validation split and then retrained on all of `train.csv` for the final test predictions.

## Difficulties and things that didn't work

With only 414 training rows, the validation set is 83 samples, so the metrics can swing depending on how the split falls.

I also stuck to a baseline. The goal this round was a clean, reproducible pipeline from start to finish, so I used default Random Forest hyperparameters and did no tuning or ensembling.

## Performance

| Model | Validation split | Accuracy | ROC-AUC |
| Random Forest (baseline) | 80% train / 20% val | 0.7229 | 0.8263 |

## Conclusions and future work

A default Random Forest reaches 0.8263 ROC-AUC on routine urine measurements with almost no preprocessing. Next I want to try 5-fold cross-validation, since a single 83-row split is a noisy estimate. I also plan to test engineered features, such as a calcium-to-osmolarity ratio, and gradient-boosted trees (XGBoost or LightGBM).

## How to reproduce

1. Clone this repository and activate a Python virtual environment:
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   pip install -r requirements.txt
   ```