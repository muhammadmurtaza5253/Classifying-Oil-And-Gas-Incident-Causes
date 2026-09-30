# Classifying Oil and Gas Pipeline Incident Causes

**EDS 6352 – Natural Language Processing, University of Houston (HW2 / Final Project)**

**Team:** Sakina Saifuddin, Muhammad Murtaza, Parva Shah

## Overview

Pipeline operators must report incidents to the U.S. Department of Transportation's Pipeline and Hazardous Materials Safety Administration (PHMSA). Each report includes a free-text **narrative** describing what happened, plus an official **cause** label. Reading and categorizing these narratives by hand is slow.

This project builds an NLP text classifier that reads a pipeline incident narrative and predicts its underlying cause. It also compares three recurrent neural network architectures to see how well each one classifies technical incident descriptions.

## Data

Source: the public **PHMSA Pipeline Incident Data**, January 2010 to present.

| File | Description |
|------|-------------|
| `incident_gas_transmission_gathering_jan2010_present_DATASET.xlsx` | Incident records used for training and evaluation |

Columns in the spreadsheet:

| Column | Role |
|--------|------|
| `NARRATIVE` | **Model input.** Free-text description of the incident |
| `CAUSE` | **Label.** Official PHMSA cause category (gold label) |
| `CAUSE_DETAILS` | Finer-grained cause sub-type (40 distinct values) |
| `IYEAR` | Year of the incident (2010–2026) |
| `REPORT_NUMBER`, `NAME` | Report identifier and operator name |

The copy in this repo has **2,053 records**, of which **2,049 have a narrative**. Narratives average about 200 words (median 168, maximum 698).

### Cause categories (8 classes)

| Cause | Records |
|-------|--------:|
| Equipment Failure | 702 |
| Corrosion Failure | 406 |
| Excavation Damage | 223 |
| Material Failure of Pipe or Weld | 221 |
| Natural Force Damage | 155 |
| Incorrect Operation | 138 |
| Other Outside Force Damage | 115 |
| Other Incident Cause | 93 |

The classes are **imbalanced**: Equipment Failure is about 7.5 times larger than Other Incident Cause. That is why the plan uses class-weighted loss and per-class metrics.

> **Note on the proposal vs. this dataset:** the project proposal describes the gas *distribution* dataset (1,592 records, 468 fields, with "Pipe/Weld/Joint Failure" as a category). The file in this repo is the gas *transmission and gathering* extract (2,053 records, 6 fields), where that category is named "Material Failure of Pipe or Weld". The task is the same: `NARRATIVE` in, `CAUSE` out. Numbers in this README describe the file actually in the repo.

## Approach

All models share the same cleaned data and the same train/validation/test split, so the comparison is fair.

1. **Data preparation:** drop rows with no narrative, clean the text, and split into train, validation and test sets.
2. **Text representation:** pretrained **GloVe** word embeddings.
3. **Models:**

   | Model | Owner | Role |
   |-------|-------|------|
   | RNN | Parva | Baseline |
   | GRU | Muhammad | Compared against the RNN |
   | BiLSTM with attention | Sakina | Most expressive model |

4. **Class imbalance:** class-weighted loss to reduce bias toward the larger classes.
5. **Evaluation:** precision, recall and F1 for each cause category, plus macro- and micro-averaged scores and overall accuracy.

All team members share the work on data preparation, text cleaning, splitting and final analysis.

## Deliverable

A trained classifier deployed as a **Streamlit web app**. A user pastes in a new incident narrative, and the app uses the best-performing model to predict the most likely cause category. The planned deployment target is **Oracle Cloud Infrastructure (OCI)**.

## Tech Stack

Python, PyTorch, scikit-learn, GloVe embeddings, Streamlit. Development in VS Code with Git/GitHub. Google Colab is available if more GPU capacity is needed.

## Repository Contents

```
.
├── README.md
├── EDS_6352_Final_Project_Proposal___...pdf                 # Project proposal
└── incident_gas_transmission_gathering_jan2010_present_DATASET.xlsx
```

Model code, notebooks and the Streamlit app will be added as the project progresses.

## Status

Proposal and dataset are in place. Model training, evaluation and deployment are still to do.
