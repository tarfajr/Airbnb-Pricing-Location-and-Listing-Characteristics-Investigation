# Airbnb Pricing, Location and Listing Characteristics Investigation
AnalystLab Africa Experience Lab · Week 1 · **Track B: Data Science**

## Purpose
Investigate how advertised Airbnb price is associated with room type, capacity and bedrooms **within each of 10 cities (local currency)**, and examine host response rates and review activity vs aggregate rating.

## Folder structure
```
project/
├── data/raw/                 <- put ORIGINAL Listings.csv and Reviews.csv here (never edited)
├── Airbnb_TrackB_DataScience_Analysis.ipynb
├── requirements.txt
├── README.md
└── outputs/                  <- created automatically (tables, cleaning log, figures/)
```

## Setup and reproduction
1. `python -m venv .venv` and activate it.
2. `pip install -r requirements.txt`
3. Copy `Listings.csv` and `Reviews.csv` into `data/raw/`.
4. `jupyter lab`, open the notebook, choose **Kernel > Restart & Run All**.
5. If a `KeyError` lists available columns, edit the candidate names in the `COLMAP` cell to match `Listings_data_dictionary(1).csv`.
Random seed is fixed (`SEED = 42`). Excel is not used anywhere.

## Methodology
Inspection and key validation, documented cleaning log, price parsing and validation (≤ 0 excluded), extreme prices flagged not deleted, complete-case treatment of missing values, review aggregation by `listing_id`, 9 figures, and four hypothesis tests (Kruskal–Wallis, Spearman) with Holm correction, effect sizes and sensitivity checks.

## Key assumptions
- Prices are in each city's local currency and are never compared across cities.
- (`listing_id`, `review_id`) identifies a review; repeated `review_id` rows are retained.
- Listings without review rows are treated as "no record in extract", not zero reviews.

## Limitations
Advertised prices only (no bookings/occupancy/revenue), no review text, no currency field, unequal city sizes, missing fields, association not causation.

## Privacy
Host names, listing names and `reviewer_id` are never printed or exported.

## Attribution
Dataset: Airbnb Listings and Reviews Dataset supplied by AnalystLab Africa. Tools: Python, pandas, NumPy, SciPy, Matplotlib, Seaborn.
