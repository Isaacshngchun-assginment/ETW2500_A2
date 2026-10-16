# What Drives Smartphone Prices?

**ETW2500 Unsupervised Learning, Assignment 2, Group 29**

A business analytics case study using **principal component analysis (PCA)** followed by a
**linear regression** (Scenario 6). Six overlapping smartphone specifications are reduced to
two principal components. The components and five other predictors are then used to explain
smartphone prices. The findings are presented as Quarto revealjs slides.

## Presenters

| Name | Student ID | Role |
|---|---|---|
| Ahmad Sufi | 34441352 | Group leader |
| Yang Shng Chun | 35183543 | Member |
| Tan Ethan | 35211520 | Member |
| Hew Kai Feng | 35310480 | Member |

## Folder structure

```
ETW2500_A2/
├── ETW2500_A2.Rproj              R project file (open this first)
├── README.md                     this file
├── data/
│   └── smartphone_cleaned_v5.csv dataset used in the analysis
└── document/
    ├── main.qmd                  Quarto source: all R code and slide content
    ├── main.html                 rendered revealjs slides
    ├── main_files/               supporting files for main.html (keep next to it)
    ├── main.pdf                  PDF export of the slides
    └── styles.css                slide styling (keep next to main.qmd and main.html)
```

## Data

- **Source:** *Smartphone Models Dataset*, Kaggle (uploaded by `abdurrahman22224`):
  https://www.kaggle.com/datasets/abdurrahman22224/smartphone-new-data
- **File:** `data/smartphone_cleaned_v5.csv`, with 980 smartphone models and 25 variables.
- **Currency:** the file has no currency column. Prices are treated as Indian rupees (₹)
  because the listings are from the Indian market (Indian brands such as Jio, Lava and
  Micromax, and typical ₹X,999 price endings).

### Variables used

| Role | Variables |
|---|---|
| PCA inputs (6) | `processor_speed`, `ram_capacity`, `internal_memory`, `refresh_rate`, `primary_camera_rear`, `primary_camera_front` |
| Outcome | `log_price` = natural log of `price` |
| Other predictors (5) | `battery_capacity`, `screen_size` (numeric); `has_5g`, `has_nfc`, `brand_group` (categorical) |
| Used in preparation only | `model` (to remove the two luxury editions), `brand_name` (to create `brand_group`) |

## Software

- **R** and **RStudio** (Quarto is included with recent RStudio versions).
- **R packages:** `pacman`, `tidyverse`, `tidymodels`, `here`, `gt`, `GGally`.
  Use current CRAN versions. The tables need **gt 0.11 or later** to show the intended
  number of decimal places.

## How to reproduce the slides

1. Unzip the folder and open **`ETW2500_A2.Rproj`** in RStudio.
   Opening the project sets the working folder, so `here("data/smartphone_cleaned_v5.csv")`
   finds the data on any computer.
2. If `pacman` is not installed, run once in the Console:
   ```r
   install.packages("pacman")
   ```
   The first code chunk in `main.qmd` runs
   `pacman::p_load(tidyverse, tidymodels, here, gt, GGally)`, which installs any missing
   package and loads them all.
3. Select **Session → Restart R** so the render starts from a clean session.
4. Open **`document/main.qmd`** and click **Render**. This creates `document/main.html`
   and `document/main_files/`.
5. Open `main.html` in a web browser to view the slides. Press **S** for speaker notes
   and **F** for full screen.

## Analysis pipeline (in `main.qmd`)

1. **Cleaning:** remove two luxury editions (Vertu Signature Touch and Xiaomi Redmi K20 Pro
   Signature Edition), whose prices reflect materials rather than specifications. Create
   `log_price`. Recode 5G and NFC to No/Yes. Keep the 7 largest brands plus "Other", with
   Xiaomi as the base category. Remove the 57 incomplete records. This leaves **921 phones**.
2. **Split:** `set.seed(41352)`, then `initial_split(prop = 0.75, strata = log_price)`,
   giving **689 training** and **232 testing** phones. The seed is the last five digits of
   the group leader's student ID. The PCA, the component interpretation and the regression
   use the training data only. The descriptive summaries and correlations describe all
   921 phones.
3. **Recipe (tidymodels):** `step_normalize()` → `step_pca(threshold = 0.7)` on the six
   specifications → `step_dummy()` → `step_zv()`. Two components are kept and explain
   71.6% of the variance:
   - **PC1:** overall performance.
   - **PC2:** storage and speed vs camera.
4. **Component interpretation:** loadings with `tidy()`, the top-3 loadings plot, and
   average specifications of low, middle and high scorers on each component.
5. **Regression:** `linear_reg()` inside a `workflow()` and fitted on the training data.
   The interpretation reports the significance level and direction of each term only. As
   the brief states, the model is not evaluated for fit or prediction.

## How the PDF was made

As instructed by the lecturer, the PDF is exported from the rendered HTML slides, not
rendered as a Quarto PDF document:

1. Open `document/main.html` in Chrome and press **E** (print view).
2. Press **Ctrl+P** (Cmd+P on Mac) and choose **Save as PDF**. Settings: Layout
   **Landscape**, Margins **None**, **Background graphics** ticked.
3. Save as `document/main.pdf`.

## Notes

- `styles.css` loads the Montserrat and Source Sans 3 fonts from Google Fonts. Without
  internet the slides fall back to similar fonts and still display correctly.
- `main.html` links to `styles.css` and `main_files/`. Keep all three in `document/`.
- Every number on the slides is calculated by the code in `main.qmd`, so re-rendering
  reproduces the same results.
