# Airbnb Pricing, Location and Listing Characteristics Investigation
**AnalystLab Africa Experience Lab · Week 1 · Track B: Data Science · Statistical Analysis Report**

All figures below were produced by `Airbnb_TrackB_DataScience_Analysis.ipynb` on the supplied files (`outputs/` holds every table and figure). Prices are advertised nightly prices in each city's own local currency and are never compared across cities.

## 1. Research problem and objectives
**Primary problem:** how is advertised price associated with room type and capacity (accommodates, bedrooms) within each of the 10 city markets?
**Supporting questions:** (SRQ1) do host response rates differ across cities? (SRQ2) is review activity associated with the aggregate review rating?
**Why it matters:** price comparisons are only meaningful when like is compared with like (same city, room type and size).

## 2. Dataset inspection and preparation
| Check | Result |
|---|---|
| Listings | 279,712 rows × 33 columns; `listing_id` unique, no nulls, no duplicate rows |
| Reviews | 5,373,143 rows × 4 columns; 0 reviews without a matching listing |
| Review coverage | 69.2% of listings have at least one review record |
| Repeated `review_id` | 160 values (320 rows); all are reused across **different listings**; (`listing_id`, `review_id`) is unique, so all rows retained |
| Invalid prices | 113 listings with price ≤ 0 excluded from price analyses (279,599 used) |
| Extreme prices | 641 flagged (3×IQR on log price, per city); retained, with sensitivity analysis |
| Missing values | district 86.8%, response rate 46.0%, acceptance rate 40.4%, rating 32.7%, bedrooms 10.5% |
| Reconciliation | per-listing review counts sum to 5,373,143, equal to the raw row count |
| Encoding | `Listings.csv` is not UTF-8 (read as latin-1); affects only listing-name text, which is not used |

Missing values were handled by complete-case analysis within each test, with no imputation. Host names, listing names and `reviewer_id` are not exported.

## 3. Exploratory data analysis
Paris is the largest market (64,690 listings) and Hong Kong the smallest (7,087). Entire places are 65.1% of listings, private rooms 31.1%, hotel rooms 2.1% and shared rooms 1.7%. Prices are strongly right-skewed in every city, which motivates rank-based tests. The listing-level rating (out of 100) has median 96, and 20.5% of rated listings score exactly 100. Monthly review records peak in October 2019 and drop sharply from March 2020. Figures 1–9 are in `outputs/figures/`.

## 4. Hypotheses and methods
| # | Hypothesis | Test | Key assumptions and checks |
|---|---|---|---|
| H1 | Price differs across room types within a city | Kruskal–Wallis, ε² effect size | Independence; normality (Shapiro) and equal variance (Levene) rejected in every city, which justifies the rank-based test |
| H2 | Capacity and bedrooms are associated with price | Spearman ρ with 95% CI | Monotonic relationship; complete cases for bedrooms |
| H3 | Review count is associated with rating | Spearman ρ | Only listings with a review record and a rating |
| H4 | Response rate differs across cities | Kruskal–Wallis | Complete cases only |

Tests run within each city at α = 0.05, with Holm correction across the 10 cities and a minimum group size of 30.

## 5. Results
**H1 (room type).** H0 is rejected in all 10 cities (Holm-adjusted p < 0.001). The effect is large in 8 cities (ε² from 0.14 in Cape Town to 0.43 in Sydney), medium in Paris (0.08) and small in Bangkok (0.05). Entire places have higher median prices than private and shared rooms in every city, and shared rooms are always cheapest. Examples of medians (local currency): New York entire place 140, private room 60; Sydney 176 and 68; Paris 82 and 53. Dropping extreme prices changed no conclusion.

**H2 (capacity).** H0 is rejected in all 10 cities. Accommodates ρ ranges from 0.31 (Bangkok, moderate) to 0.68 (Sydney, strong); bedrooms ρ ranges from 0.37 to 0.67. Confidence intervals are narrow (about ±0.01). Removing extreme prices changed ρ by at most 0.002.

**H3 (reviews and rating).** Pooled ρ = −0.205 (95% CI −0.209 to −0.200, n = 188,306), a weak negative association. City values range from −0.03 (Hong Kong, negligible, adjusted p ≈ 0.046) to −0.28 (Rio de Janeiro).

**H4 (host response).** Response rates differ across cities (H = 1,967.8, p < 0.001) but the effect is small (ε² = 0.013); the median is 100% in every city. Missingness ranges from 16% (Hong Kong) to 64% (Paris).

**Statistical vs practical significance.** Sample sizes make nearly every p-value tiny, so interpretation relies on effect sizes. Practically important results are H1 in most cities and H2 everywhere; H3 and H4 are weak or small.

## 6. Limitations
Advertised prices are not revenue; there are no booking, occupancy or review-text data, so no claims are made about demand, satisfaction or sentiment. There is no currency field, so cross-city price comparison is excluded. City sizes are unequal, district data is mostly missing, and extreme prices were handled by flagging and rank-based methods. All results are **associations**, not causal effects: room type and capacity are entangled with location, quality and amenities. Rating missingness (32.7%) is likely tied to listings with no reviews, so rating results describe only rated listings.

## 7. Recommendations
1. Benchmark prices only within city and room type, never across cities in raw currency (evidence: no currency field; large H1 effects).
2. Use capacity and bedrooms as primary segmentation variables for price comparison (H2 ρ of 0.3–0.7 in every city).
3. Present review counts as activity, and show the share of unrated listings beside rating charts (H3, rating missingness 32.7%).
4. Report response-rate missingness next to any host KPI (H4, 16–64% missing by city).
5. Obtain booking/occupancy data and a currency field before making revenue or demand claims.

Each recommendation can be evaluated by checking that segmented medians are tighter than unsegmented ones, that no dashboard label equates review volume with quality, and that every host KPI shows its denominator.

## 8. Reproduction
See `README.md`: place the original CSVs in `data/raw/`, install `requirements.txt`, and run the notebook top to bottom (fixed seed 42).

## 9. Contribution and integration
The cleaned listing table, per-listing review counts, per-city price summaries and the cleaning log are saved in `outputs/` and use the shared definitions (listing count = unique `listing_id`; advertised price = local-currency price > 0; review count = review rows per `listing_id`). No integration with other tracks' work is claimed.
