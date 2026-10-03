# Final Data Cleaning and Quality Report

**Evidence source:** saved code, Markdown, and outputs in `notebooks/02_data_cleaning.ipynb` only. No notebook cells were executed for this report. The reported values below are transcribed from existing outputs; where a value or treatment result is not available there, it is identified as such.

## 1. Executive summary

The notebook documents an initial dataset of **2,294 rows and 23 columns**. It removes one exact duplicate and one row without a stage title, leaving **2,292 rows and 23 columns**. It also contains manual corrections to selected postal codes, internship-location fields, and internship-location company names; normalization and fuzzy matching of the main company field; exploratory quality checks; and exports to `data/processed/stages_cleaned.xlsx`.

The saved outputs show that the dataset retains substantial missingness in internship-location fields. Company-name merging is a mixture of automatic similarity-based grouping and explicitly listed manual replacements. Two similar company pairs are expressly excluded from merging. The notebook also shows date, duration, student-identifier, and convention checks. Many findings are diagnostics only and did not lead to a documented correction.

**Important reproducibility limitation:** the notebook has outputs from multiple executions and includes a later reload of the exported workbook for further checks. The saved outputs do not prove that every later in-memory transformation is present in the exported workbook. This is noted in the relevant sections rather than assuming all notebook state was persisted.

## 2. Dataset overview and row changes

The source workbook is loaded into `df_raw`, with a working copy created as `df = df_raw.copy()`.

| Check | Before | After | Change / note |
|---|---:|---:|---|
| Rows and columns | 2,294 × 23 | 2,292 × 23 | One exact duplicate and one row without a stage title were removed. |
| Exact duplicate rows | 1 | 0 | Explicitly reported by the notebook. |
| Rows with missing `Titre du stage` | One displayed row | 0 after filtering | One row was removed. |
| Final shape at export | — | 2,292 × 23 | Explicitly printed by the export cell. |

The fields describe academic year, convention, internship dates and duration, stage type and nature, student identifiers, company, title and description, company address, internship location, and defense date. The two pedagogical-reference fields are present in the notebook’s original column listing.

The notebook shows stage-type counts: PFE 963, SIB 562, SIA 530, MASTER 73, SNO 60, SD 51, PFE_MASTER 46, and PFE_CPRO 7. It also shows academic-year counts: 2020/2021 360, 2021/2022 369, 2022/2023 375, 2023/2024 379, 2024/2025 350, 2025/2026 355, 2026/2027 100, and 4 missing.

## 3. Chronological cleaning and modification history

### 3.1 Exact duplicate removal

| Column(s) | Treatment | Reason / method | Affected rows | Result |
|---|---|---|---:|---|
| Entire row | Programmatic `drop_duplicates()` | Remove exact duplicate records. | 1 | Output: 0 exact duplicates remain. |

### 3.2 Removal of a record without an internship title

| Column | Treatment | Reason / method | Affected rows | Result |
|---|---|---|---:|---|
| `Titre du stage` | Programmatic `dropna(subset=...)` | Exclude rows without a stage title. | 1 | The displayed output reports 2,292 rows after this step. |

The row was displayed before deletion, but its identifying and personal details are omitted here.

### 3.3 Manual corrections to company postal codes

The notebook explicitly assigns these values to `Code postal`. Its output displays the assigned values, but does not provide a reliable before-value for each one. The reason is not consistently documented; no address-level validation is claimed here.

| Row index | Previous value | New `Code postal` value | Reason in notebook |
|---:|---|---|---|
| 467 | Non disponible dans les résultats existants. | `BP 0000` | Non-classical postal reference; comment says a reliable conventional code is not available. |
| 844 | Non disponible dans les résultats existants. | `32901` | No explicit reason. |
| 848 | Non disponible dans les résultats existants. | `SN15 1GD` | No explicit reason. |
| 1124 | Non disponible dans les résultats existants. | `76131` | No explicit reason in this assignment. |
| 1591 | Non disponible dans les résultats existants. | `H1V 3N7` | No explicit reason. |

The notebook also assigns `Code postal = EX5 2FN` at row index **1**. The displayed row contains the address text `6 BABBAGE WAY, EXETER, EX5 2FN` and shows `EX5 2FN` as the resulting postal code. The previous value is not stated alongside the assignment; no student details are reproduced.

### 3.4 Manual internship-country entries

The code assigns values to `Pays (Lieu Stage)` for the following indices. The results table shows the values after assignment. It does not establish the previous value or a separate verification source for each entry.

| Row index | New `Pays (Lieu Stage)` value | Previous value / reason |
|---:|---|---|
| 6 | France | Non disponible dans les résultats existants. |
| 313 | France | Non disponible dans les résultats existants. |
| 1272 | France | Non disponible dans les résultats existants. |
| 1287 | France | Non disponible dans les résultats existants. |
| 1377 | France | Non disponible dans les résultats existants. |
| 1465 | France | Non disponible dans les résultats existants. |
| 1635 | France | Non disponible dans les résultats existants. |
| 1637 | France | Non disponible dans les résultats existants. |
| 1673 | Suède | Non disponible dans les résultats existants. |
| 1766 | France | Non disponible dans les résultats existants. |
| 1784 | Allemagne | Non disponible dans les résultats existants. |
| 1902 | France | Non disponible dans les résultats existants. |
| 1930 | France | Non disponible dans les résultats existants. |

The number of entries in this assignment is visible in the code, but a separately printed before/after count of missing countries is not provided.

### 3.5 Manual internship-postal-code entries

The code assigns values to `CP (Lieu Stage)`. Comments in the code explicitly label some values as approximate or non-standard. Previous values are not shown in the assignment output.

| Row index | New `CP (Lieu Stage)` value | Note in notebook |
|---:|---|---|
| 467 | `BP 0000` | Lomé, Togo; no reliable conventional postal code. |
| 839 | `330-0853` | Saitama, Japan; marked approximate. |
| 1166 | `86000` | Poitiers, France. |
| 1272 | `33700` | Mérignac, France. |
| 1383 | `1400` | Yverdon-les-Bains, Switzerland. |
| 1496 | `050000` | Hebei, China; marked a regional approximation. |
| 1673 | `752 00` | Uppsala, Sweden; marked approximate. |
| 1684 | `752 00` | Uppsala, Sweden; marked approximate. |
| 1763 | `434000` | Jingzhou, China. |
| 1835 | `66000` | Catalonia, Spain; marked approximate. |
| 1976 | `434000` | Jingzhou, China. |
| 2000 | `2926` | Boncourt, Switzerland. |

The notebook does not show a validation source for these assignments. In particular, the values explicitly identified as approximate should not be treated as verified postal codes without further review.

### 3.6 Manual internship-city and location-company edits

| Row index | Column | Value written in notebook | Treatment history / previous value |
|---:|---|---|---|
| 1124 | `CP (Lieu Stage)` | `76131` | Written in the same manual location block as the city and country below. |
| 1124 | `Ville (Lieu Stage)` | `Karlsruhe` | The preceding inspection shows this field missing. |
| 1124 | `Pays (Lieu Stage)` | `ALLEMAGNE` | The preceding inspection shows this field missing. |
| 1799 | `Ville (Lieu Stage)` | `Porsgrunn` | The preceding inspection shows this field missing; the existing code and country are left as displayed. |
| 726 | `Ville (Lieu Stage)` | `Gray`, then `Arc-lès-Gray` | The later assignment overwrites the earlier one; the saved output shows `Arc-lès-Gray`. |
| 726 | `Pays (Lieu Stage)` | `FRANCE` | Written in both city-correction blocks; final displayed value is `FRANCE`. |
| 2241 | `Ville (Lieu Stage)` | `Paris`, then `Uppsala` | The later assignment overwrites the earlier one; the saved output shows `Uppsala`. |
| 2241 | `Pays (Lieu Stage)` | `FRANCE`, then `SUÈDE` | The later assignment overwrites the earlier one; final displayed value is `SUÈDE`. |
| 726 | `Entreprise (Lieu Stage)` | `SIMU S.A.S` | Earlier location inspection showed this field missing. |
| 1272 | `Entreprise (Lieu Stage)` | `THALES DMS FRANCE CAMPUS THALES BORDEAUX` | Earlier location inspection showed this field missing. |
| 2241 | `Entreprise (Lieu Stage)` | `UPPSALA UNIVERSITET` | Earlier location inspection showed this field missing. |

No external source, confidence field, or separate verification record is shown for these manual edits. The notebook uses row indices for targeting, not stable business identifiers.

## 4. Missing-value analysis

The following values are transcribed from the notebook’s saved missing-value table. The output does not make clear that this table was regenerated after every subsequent manual correction, so these figures are reported as the table’s results, not asserted as a post-final-export inventory.

| Column | Missing count | Missing % | Treatment / decision shown |
|---|---:|---:|---|
| `Entreprise (Lieu Stage)` | 1,747 | 76.221640% | Manual entries are shown for selected rows; no overall fill-rate result is printed. |
| `CP (Lieu Stage)` | 1,744 | 76.090750% | Manual entries are shown for selected rows; no overall fill-rate result is printed. |
| `Ville (Lieu Stage)` | 1,744 | 76.090750% | Manual entries/corrections are shown for selected rows; no overall fill-rate result is printed. |
| `Pays (Lieu Stage)` | 1,646 | 71.815009% | Manual entries are shown for selected rows; no overall fill-rate result is printed. |
| `Date soutenance` | 704 | 30.715532% | Investigated; no imputation shown. |
| `Nature du stage` | 453 | 19.764398% | Investigated; no imputation shown. |
| `Entreprise` | 32 | 1.396161% | Investigated; no missing-value imputation shown. |
| `Référent pédagogique (mail)` | 13 | 0.567190% | Investigated; no imputation shown. |
| `Référent pédagogique (mail).1` | 13 | 0.567190% | Investigated; no imputation shown. |
| `Descriptif du Stage` | 12 | 0.523560% | Investigated; no imputation shown. |
| `Date fin` | 12 | 0.523560% | Investigated; no imputation shown. |
| `Date début` | 11 | 0.479930% | Investigated; no imputation shown. |
| `Code postal` | 5 | 0.218150% | Selected manual postal-code assignments shown; no post-treatment total shown. |
| `Année académique` | 4 | 0.174520% | Investigated; no imputation shown. |
| `Adresse` | 1 | 0.043630% | Investigated; no imputation shown. |
| `Type de Stage` | 0 | 0% | No treatment required by the displayed table. |
| `Durée totale (mois)` | 0 | 0% | Zero values are investigated separately; no correction shown. |
| `N° Convention de stage` | 0 | 0% | No missing values in the displayed table. |
| `N° étudiant` | 0 | 0% | No missing values in the displayed table. |
| `NOM de l'élève` | 0 | 0% | No missing values in the displayed table. |
| `Prénom de l'élève` | 0 | 0% | No missing values in the displayed table. |
| `Titre du stage` | 0 | 0% | One initially missing-title row was removed before this result. |
| `Ville` | 0 | 0% | No missing values in the displayed table. |

The missing values not explicitly assigned in the code remain unresolved in the notebook. A precise final number of missing values after all manual changes is **Non disponible dans les résultats existants.**

## 5. Student identifiers, repeated records, and conventions

- After loading the exported workbook and normalizing its column names, the notebook reports **1,419 unique student numbers** and **2,292 rows with a student number**.
- The displayed repeated-student table has **778 rows** for student numbers appearing more than once. The notebook treats repeated student numbers as potentially legitimate because students can have multiple stages; it does not delete these records.
- A grouped check compares the number of unique surnames and first names for each student number. It reports **0 students with a name/first-name inconsistency**.
- A convention-to-student check reports **0 conventions associated with multiple students**.
- No correction to student identities, repeated stages, or conventions is shown.

## 6. Date and duration checks

- The missing-value output reports **11 missing start dates** and **12 missing end dates**. A saved display lists rows with missing end dates; no date imputation is shown.
- The check `Date début > Date fin` reports **0**.
- A separate display filters durations at or below zero or above 24 months. The saved result shows zero-duration records; the count of negative durations and the count above 24 months are **Non disponible dans les résultats existants.**
- The notebook creates `duree_calculee_mois` for analysis by dividing the difference in recorded dates by 30.44 days per month. It compares this with `Durée totale (mois)` and displays cases whose absolute difference exceeds 0.15 months. No values are changed by this comparison.
- The saved comparison table lists discrepancies, but a printed total number of discrepancies is **Non disponible dans les résultats existants.**
- No date or duration correction is documented. Zero and discrepant duration values are diagnostic findings and should not be described as fixed.

## 7. Company data quality

### 7.1 Initial statistics and normalization

The saved outputs show **1,199 unique non-null company names** before the company-cleaning sequence.

The company-name normalization function, applied to `Entreprise`, documents these programmatic operations:

1. trim surrounding whitespace and convert to uppercase;
2. decompose Unicode and remove accent marks;
3. convert typographic apostrophes to straight apostrophes;
4. normalize `S.A.` / `S.A` to `SA`;
5. replace hyphen separators with spaces;
6. collapse repeated whitespace and trim again.

The saved unique-company count after this normalization is **1,183**. A separate comparison of original and normalized values reports **9 groups with multiple variants**. The notebook also has an earlier name-normalization comparison cell; its output shows 1,199 distinct original/normalized entries, but the execution order of saved outputs is not a clean, single linear run.

### 7.2 Similarity method and threshold-based grouping

The notebook uses `rapidfuzz.fuzz.ratio` to compare distinct normalized values of `Entreprise`. Candidate pairs with similarity of at least 85% are collected. It then uses a union-find grouping/mapping to replace names in the company column for the applicable threshold bands.

| Similarity stage | Saved result |
|---|---|
| At least 95% | Count after grouping: 1,165 unique companies. Candidate-pair count: **Non disponible dans les résultats existants.** |
| 90% to below 95% | Count after grouping: 1,137 unique companies. The code excludes the two pairs below. |
| 85% to below 90% | The candidate display reports 44 pairs. Explicit replacements are applied; count after these replacements: 1,116. |
| 80% to below 85% | Explicit replacements are applied; count after these replacements: 1,106. |
| 76% to below 77% | A displayed subset and two explicit replacements are applied; count after these replacements: 1,105. |

The outputs do not give a number of exact mappings/accepted pairs for the automatic union-find stages. The counts above are unique-company counts, not counts of candidate pairs or merged rows.

### 7.3 Explicit company-name replacements

The following direct mappings are explicitly defined in the notebook and applied to `Entreprise`:

**Listed in the 85%–90% replacement block**

| From | To |
|---|---|
| `THE UNIVERSITY OF ELECTRO COMMUNICATIONS (??????)` | `THE UNIVERSITY OF ELECTRO COMMUNICATIONS` |
| `FIT (FLORIDA INSITUTE OF TECHNOLOGY)` | `FLORIDA INSTITUTE OF TECHNOLOGY` |
| `FLORIDA INSTITUTE OF TECH` | `FLORIDA INSTITUTE OF TECHNOLOGY` |
| `FLORIDA INSTITUE OF TECHNOLOGY` | `FLORIDA INSTITUTE OF TECHNOLOGY` |
| `FLORIDA INSTITUTE OF TECHNOLOGY (FIT)` | `FLORIDA INSTITUTE OF TECHNOLOGY` |
| `OFF NAT ETUDES RECHERCHES AEROSPATIALES (ONERA)` | `OFF NAT ETUDES RECHERCHES AEROSPATIALE` |
| `INSTITUT NATIONAL RECHERCHE SECURITE` | `INSTITUT NATIONAL DE RECHERCHE ET DE SECURITE` |
| `OFFICINE PANERAI, BRANCH OF RICHEMONT INT. SA` | `OFFICINE PANERAI, BRANCH OF RICHEMONT INTERNATIONAL SA` |
| `CARAN D'ACHE` | `CARAN D'ACHE SA` |
| `KERI MEDICAL` | `KERI MEDICAL SA` |
| `JET AVIATION AG` | `JET AVIATION` |
| `LUXURY TRAIN SERVIZI` | `LUXURY TRAINS SERVIZI SRL` |
| `E.M.S. ELECTRO MEDICAL SYSTEMS SA.` | `ELECTRO MEDICAL SYSTEMS SA.` |
| `ALSTOM TRANSPORT SA (93)` | `ALSTOM TRANSPORT SA` |
| `VALFLEURIER SA` | `VALFLEURIER` |
| `TOKYO DENKI UNIVERSITY (TDU)` | `TOKYO DENKI UNIVERSITY` |
| `HERMES SELLIER SAS` | `HERMES SELLIER` |
| `GE HEALTHCARE SAS` | `GE HEALTHCARE` |
| `EFBE PRUFTECHNIK GMBH` | `EFBE PRUFTECHNIK` |
| `DECATHLON SE` | `DECATHLON` |
| `JET AVIATION AGBASEL` | `JET AVIATION` |
| `BOUCLEDOR` | `BOUCLEDOR SA` |
| `ATLAS MICROTECH SARL` | `ATLAS MICROTECH` |
| `TAG HEUER SA` | `TAG HEUER` |

**Listed in the 80%–85% replacement block**

| From | To |
|---|---|
| `KOMATSUSEIKI KOSAKUSHO CO.,LTD` | `KOMATSUSEIKI KOSAKUSHO` |
| `KOMATSUSEIKI KOSAKUSHO COMPANY` | `KOMATSUSEIKI KOSAKUSHO` |
| `THALES ALENIA SPACE FRANCE` | `THALES ALENIA SPACE` |
| `BONINCHI SA` | `BONINCHI` |
| `BRUNEL UNIVERSITY LONDON` | `BRUNEL UNIVERSITY` |
| `MANUFACTURE HORLOGERE VALFLEURIER` | `MANUFACTURE VALFLEURIER` |
| `ARIANEGROUP SAS` | `ARIANE GROUP` |
| `BAUD INDUSTRIES SUISSE` | `BAUD INDUSTRIES` |
| `R.MONTAVON SA` | `REMY MONTAVON SA.` |
| `PARALEC ENERGY CO LTD` | `PARALEC ENERGY` |

**Listed in the 76%–77% replacement block**

| From | To |
|---|---|
| `UNIVERSIDAD DE OVIEDO` | `UNIVERSITY OF OVIEDO` |
| `MANUFACTURE HORLOGERE VALFLEURIER (GROUPE RICH...` | `MANUFACTURE HORLOGERE VALFLEURIER` |

The final source string in the second mapping is shown with an ellipsis in the notebook itself; no fuller spelling is available in the saved code.

### 7.4 Similar pairs deliberately excluded

The 90%–95% grouping code explicitly excludes these pairs from the union-find merge:

| Company pair | Treatment |
|---|---|
| `THALES LAS FRANCE SAS` / `THALES DMS FRANCE SAS` | Deliberately not merged. |
| `MANUFACTURE D'HORLOGERIE AUDEMARS PIGUET & CIE` / `MANUFACTURE D'HORLOGERIE AUDEMARS PIGUET SA` | Deliberately not merged. |

The code does not state a longer rationale for these exclusions. They remain separate in that merge operation; no later mapping of these pairs is shown.

### 7.5 Comparison of company and internship-location company fields

A separate exploratory comparison covers **537 rows** with both company fields available. The saved output reports:

- mean similarity: **59.59%**;
- median similarity: **59.09%**;
- 203 comparisons at or above 90%;
- 224 at or above 80%;
- 249 at or above 70%.

This comparison is diagnostic; it does not establish that `Entreprise` and `Entreprise (Lieu Stage)` are the same entity. The notebook does separately enter three values manually in `Entreprise (Lieu Stage)` (see section 3.6).

## 8. Final output and persisted state

The notebook records exports to `data/processed/stages_cleaned.xlsx`. The later export cell prints **2,292 rows × 23 columns** and appears after the listed company replacements in notebook order. The workbook is subsequently reloaded, and its column names are normalized in memory to `snake_case` using lowercase, accent removal, replacement of non-alphanumeric runs with underscores, and trimming of leading/trailing underscores.

The reloaded dataframe reports **1,419 unique student numbers**, **2,292 rows with student numbers**, and the consistency checks described above. The notebook also adds `duree_calculee_mois` during this post-export analysis. No later export after column-name normalization or creation of this derived analysis column is shown.

The final post-manual-correction missing-value totals by location field are **Non disponible dans les résultats existants.** As a result, the quality table in section 3.4 should not be interpreted as a verified post-export quality inventory.

## 9. Overall quality status and next-phase implications

**Documented as resolved in the notebook:** one exact duplicate and one row without a stage title were removed; selected company-name variants were normalized or merged; and selected postal-code, city, country, and internship-location company values were manually entered.

**Still limited or unresolved in the available results:** location fields remain highly incomplete in the saved missing-value table; some postal values are explicitly approximate; the provenance and independent verification of manual edits are not documented; date and duration anomalies were inspected but not corrected; and fuzzy similarity does not by itself prove organizational identity. Although key identifier and convention checks report no detected inconsistency, repeated student/stage records remain in the data by design of the documented process.

For the next SQL/RAG project phase, the notebook supports treating internship records as structured data with useful identifiers, dates, categories, company names, titles, and descriptions, while retaining missingness and provenance limitations. Location and fuzzy-merged company values should not be treated as fully verified dimensions based on these results alone. This report does not propose or implement the SQL or RAG layers.
