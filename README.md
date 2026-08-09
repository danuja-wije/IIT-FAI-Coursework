# IIT FAI Coursework — 7COSC013W Foundations of Artificial Intelligence

Danuja Wijerathna · 20251143

## Day-ahead unit commitment for the Sri Lankan power grid

Daily unit commitment for the Ceylon Electricity Board, rebuilt from the regulator's published
record. Four AI methods answer the same question on the same days: a knowledge graph with a rule
base, simulated annealing, an exact mixed-integer optimiser, and a demand forecast that feeds the
schedulers.

## Contents

| Path | What it is |
|---|---|
| `notebooks/CW1_20251143_main.ipynb` | CW1 implementation, executed |
| `notebooks/CW1_20251143_EDA.ipynb` | Exploratory data analysis, executed |
| `reports/CW1_20251143_PartA_ProjectProposal.pdf` | Part A project proposal |
| `reports/CW1_20251143_TechnicalReport.pdf` | CW1 technical report |
| `reports/CW2_20251143_Report.pdf` | CW2 critical and ethical report |
| `reports/*.docx` | Editable Word source for the three reports above |
| `video/CW1_20251143_Demo.mp4` | Demonstration video |
| `data/dispatch-lab-data.zip` | Derived CSVs, cost tables, cached results, analysis package |
| `docs/` | Companion web app, published via GitHub Pages |

## Running the notebooks

Both notebooks bootstrap themselves. The first two cells install any missing packages and fetch
the code and data if they are not already present, so they run unchanged on a clean machine or in
Google Colab.

To run locally from this repository, unzip the data first:

```bash
unzip data/dispatch-lab-data.zip
jupyter lab notebooks/
```

## Data source

Generation and cost data published by the Public Utilities Commission of Sri Lanka
(https://gendata.pucsl.gov.lk). Every figure in the analysis is flagged as measured, published or
assumed.
