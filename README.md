# Colors of Icadiye: Human Response to Architectural Identity

**Course:** MBL549E Special Topics in Architectural Design Computing (Spring 25-26)  
**Program:** Architectural Design Computing, Istanbul Technical University  
**Instructor:** Assoc. Prof. Dr. Can UZUN  
**Student:** Elifsu YUMURTACI  

---

## 1. Dataset Description

This project examines the correlation between facade colors and pedestrian density on Icadiye Street in Kuzguncuk, Istanbul, Türkiye, and presents the results through an interactive HTML data visualization. The report presents a data audit sourced from the Google Street View API and Google Places API. The dataset includes three main categories: facade colors, population density, and visitor comments. Each data audit process is examined under these three dataset categories.

**Research Question:** Do facade colors on Icadiye Street affect pedestrian density and human preferences, and if so, how?

### Technical Stack

The data visualization is built with HTML5, CSS3, Vanilla JavaScript, MapLibre GL JS (3D map base), and the HTML5 Canvas API (facade color dots, animated pedestrian figures, density rings, urban function icons).

Facade pigments are extracted from 76 Google Street View images using Python (OpenCV, KMeans clustering). Pedestrian density, which includes 188 venues, is derived from Google Places API review counts, photo counts, and review ratings, aggregated per scan zone using Pandas. Visitor comments, which includes 16 reviews are sourced from the Google Places API, cleaned in Python, and rendered with a JavaScript keyword-highlighting pipeline that identifies color-related terms in real time.

---

## 2. Data Consistency Check

### 2.1 Population Density Data

The dataset includes 188 venues located within the Kuzguncuk neighborhood. The consistency check confirms structural integrity: venue names and urban function tags are stored as strings, metrics and scan point counts as integers, and spatial coordinates as floats. No further type corrections were required.

### 2.2 Visitor Comments Data

Two format issues were identified: the `Review_Time` column contains a mix of Turkish and English expressions, because the two source datasets were collected from different audiences using different languages.

### 2.3 Facade Colors Data

The dataset covers 76 images. Categorical columns (`File_Name`, `Street`, `Color 1–4`) are stored as object type; `Point_Number` and `Heading` are integers; `Latitude` and `Longitude` are floats. The `Heading` column shows five distinct values (59°, 61°, 76°, 239°, 241°), reflecting the two-directional facade capture design: each scan point was photographed from two opposite directions to capture both sides of the street. All HEX color values follow the correct `#RRGGBB` format and no rows were found with empty color cells.

---

## 3. Missing Data Analysis

### 3.1 Population Density Data

Zero values in popularity metrics are retained rather than treated as missing, as a review count of 0 indicates low digital interaction rather than absent data. Missing data mechanisms assessed:

- **MCAR:** No significant evidence detected.
- **MAR:** Lower-density residential zones returned fewer metadata results due to the lack of commercial places compared to the dockside.
- **MNAR:** Acknowledged for `Photo_Count` due to the Google Places API's 10-photo retrieval limit. A Visual Impact Score is calculated using min-max scaling to compensate.

### 3.2 Visitor Comments Data

`Review_Rating`, `Author_Name`, and `Review_Time` columns each have a missing rate of 6.2%. All three are missing simultaneously in row 10, indicating a **MAR** pattern , row 10 corresponds to Icadiye Caddesi, a street that returned no results from the Google Places API.

### 3.3 Facade Colors Data

Missing values follow a hierarchical gradient consistent with KMeans color clustering behavior:

| Column | Completeness |
|--------|-------------|
| Color 1 | 90.8% |
| Color 2 | 80.3% |
| Color 3 | 57.9% |
| Color 4 | 14.5% |

Dominant colors are always extracted first; subsequent slots are filled only when the facade contains multiple color clusters. Most photographs yielded 2–3 colors; only 3 photographs produced 4 colors. Missing values in `Color 2` and `Color 4` are classified as **MNAR**: absence is caused by visual simplicity of the facade, not by chance.

---

## 4. Outlier Analysis

### 4.1 Population Density Data

`Total_Reviews` contains 31 outliers. These represent dominant attraction points (e.g., Metet Közde Döner with 4,000–6,000 reviews) rather than data errors, and are preserved as a distinct analytical layer. Coordinate outliers were excluded during cleaning. The 31 outlier venues are predominantly clustered around Nearest_Scan_Point 1 and 4, confirming that tourist circulation in Kuzguncuk is concentrated around specific urban nodes rather than distributed homogeneously.

### 4.2 Visitor Comments Data

The only numeric column is `Review_Rating`. IQR analysis flags 3- and 4-star ratings as outliers, but this is a mathematical artifact of the extremely skewed distribution — the dataset has very little variation, likely due to commenter bias (satisfied visitors are more inclined to leave reviews).

### 4.3 Facade Colors Data

Coordinate columns are tightly clustered along Icadiye Caddesi as expected. The `Heading` boxplot spans 59°–241°, which is not an anomaly but reflects the intentional bimodal distribution of two opposing capture directions.

---

## 5. Distribution and Statistical Summary

### 5.1 Population Density Data

The mean review count is 191, but the median is only 7, meaning half of all places have 7 or fewer reviews. A few high-traffic outliers pull the mean significantly upward. `Rating` zero values indicate missing data from the Google Places API, not actual zero-star ratings, which is why the distribution shows two separate peaks. `Photo_Count` and `Visual_Impact_Score` follow the same pattern. Scan point distribution is even across all 15 zones with a median of 7.0.

### 5.2 Visitor Comments Data

| Metric | Value |
|--------|-------|
| Mean rating | 4.80 |
| Median rating | 5.0 |
| Std. deviation | 0.414 |
| Skewness | −1.5 (left-skewed) |

Most reviews are 5 stars. The Public Space Perception dataset contributes more 5-star ratings; Facade & Spatial Reviews shows a slightly more even 4/5 split. By venue keyword, Kuzguncuk, Kuzguncuk Sahili, and Kuzguncuk Evleri each have 5 reviews, while Icadiye Caddesi has only 1.

### 5.3 Facade Colors Data

All 76 rows correspond to 76 unique photographs taken at distinct spatial positions and camera angles. This confirms the integrity of the data collection pipeline: the Street View API download script correctly avoided re-downloading the same panorama twice. No deduplication preprocessing is required.

---

## 6. Repeated Data Check

### 6.1 Population Density Data

29 records share identical coordinates with at least one other record. This is a known behavior of the Google Places API: different businesses within the same building are assigned the exact same latitude and longitude (e.g., Cafe 2 Pizza, B&N Sisters Boutique, Zeynep Ada Pastry Workshop, and IİMA YAPI all share one coordinate pair).

### 6.2 Visitor Comments Data

No duplicate records were found. Rows sharing the same `Query` value represent multiple reviews of the same place, which is expected behavior.

### 6.3 Facade Colors Data

There is a notable imbalance between the two street segments: Icadiye Caddesi accounts for 68.4% of photographs (52 images) and Icadiye Caddesi 2 for 31.6% (24 images). This reflects the differing physical lengths of the two segments rather than a sampling error. Four scan points (21, 25, 26, 27 of Icadiye Caddesi) have only a single heading recorded, meaning one facade side is missing — likely because the Street View panorama did not exist on that side at time of collection. Both segments show comparable color richness per photograph (mean ~2.33 and ~2.67 colors respectively), suggesting no systematic bias in facade complexity. Downstream analyses should acknowledge that the first segment is represented at roughly twice the spatial density of the second.

---

## 7. Bias Check

### 7.1 Population Density Data

Three bias sources identified:

- **Spatial bias:** Scan Point 1 (northern waterfront end) has 20 places, 60% above the expected average of 12.5 per zone,  while Scan Point 9 has only 7, reflecting higher commercial density near the port.
- **Visual Impact Score bias:** 23% of records score 0.0 because the Google Places API hit its per-place photo retrieval limit and returned no images. These should be treated as missing data, not low-quality data.
- **Rating bias:** 60 places fall in the 4.5–5.0 rating range while only 11 fall below 3.5. This is a well-known positive bias in Google reviews: satisfied customers are far more likely to leave a review than dissatisfied ones.

### 7.2 Visitor Comments Data

Three bias types identified:

- **Source bias:** Public Space Perception contributes 10 reviews vs. 6 valid reviews from Facade & Spatial Reviews (62.5% dominance by one source).
- **Selection bias:** 5 street locations have zero reviews; less prominent urban nodes are completely invisible in the data.
- **Language bias:** Turkish-language reviews dominate (10 of 15 valid reviews), all from the Public Space dataset; English reviews come exclusively from the Facade dataset written by international tourists. The two datasets capture two distinct audiences, local residents vs. visitors, which is an important limitation when interpreting perceptions of the street. Additionally, the Google Places API returns only 5 comments per venue at random, limiting depth of analysis.

### 7.3 Facade Colors Data

Four scan points (21, 25, 26, 27 of Icadiye Caddesi) have only a single heading recorded, meaning one facade side is missing, likely due to the absence of a Street View panorama on that side at time of collection. Both street segments show comparable color richness per photograph, suggesting no systematic bias in facade complexity between segments.
