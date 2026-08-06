# CMS Provider Data — Complete Dataset Catalog

**Source:** CMS Provider Data API v1
**API Endpoint:** `https://data.cms.gov/provider-data/api/1/metastore/schemas/dataset/items`
**Total Datasets Across All Themes:** ~130+

---

## Nursing Homes Including Rehab Services (18 datasets)

| # | Dataset | CMS ID | Priority | Phase 1 Fields |
|---|---------|--------|----------|----------------|
| 1 | Provider Information | `4pq5-n9py` | **P1** | Name, address, phone, CCN, bed count, ownership type, overall rating, sub-ratings |
| 2 | Ownership | `y2hd-n93e` | **P1** | Owner names, types, percentages, roles |
| 3 | Penalties | `g6vv-ecav` | **P1** | Fine amounts, penalty dates, penalty types |
| 4 | Health Deficiencies | `r5ix-sfxw` | **P1** | Citation codes, scope/severity, survey dates |
| 5 | Survey Summary | `svkr-9uvy` | P2 | Inspection summary scores |
| 6 | Fire Safety Deficiencies | `ifjh-97n8` | P2 | Fire safety citation details |
| 7 | Inspection Dates | `rxhc-cqig` | P2 | Health inspection and fire safety inspection dates |
| 8 | MDS Quality Measures | `djen-97ju` | P2 | Long-stay and short-stay quality measure scores |
| 9 | Medicare Claims Quality Measures | `fyk4-cjfe` | P3 | Claims-based quality metrics |
| 10 | State US Averages | `xcdc-v5wm` | P3 | State-level benchmark averages |
| 11 | Citation Code Look-up | `uey3-88ui` | P3 | Reference table for deficiency codes |
| 12 | Nursing Home Data Collection Intervals | `xcbk-hj9a` | P3 | Data collection date ranges |
| 13 | State-Level Health Inspection Cut Points | `vmkk-yiri` | P3 | Rating methodology thresholds |
| 14 | SNF Quality Reporting Program - Provider Data | `fykj-qnpj` | P2 | Skilled nursing quality measures |
| 15 | SNF Quality Reporting Program - Swing Beds | `kqc5-gvpt` | P3 | Swing bed quality data |
| 16 | SNF Quality Reporting Program - National | `ihhs-hy38` | P3 | National SNF quality averages |
| 17 | FY 2026 SNF VBP Aggregate Performance | `snf-vbp-agg` | P3 | Value-based purchasing scores |
| 18 | FY 2026 SNF VBP Facility-Level Dataset | `snf-vbp-fac` | P3 | Per-facility VBP performance |

**Data Dictionary:** `NH_Data_Dictionary.pdf`

---

## Hospitals (73 datasets)

| # | Dataset | CMS ID | Priority |
|---|---------|--------|----------|
| 1 | Hospital General Information | `xubh-q36u` | **P1** |
| 2 | Healthcare Associated Infections - Hospital | `77hc-ibv8` | **P1** |
| 3 | Complications and Deaths - Hospital | `ynj2-r877` | **P1** |
| 4 | Hospital-Acquired Condition (HAC) Reduction Program | `yq43-i98g` | **P1** |
| 5 | Hospital Readmissions Reduction Program | `9n3s-kdb3` | P2 |
| 6 | Timely and Effective Care - Hospital | `yv7e-xc69` | P2 |
| 7 | Patient Survey (HCAHPS) - Hospital | `dgck-syfz` | P2 |
| 8 | Unplanned Hospital Visits - Hospital | `632h-zaca` | P2 |
| 9 | Medicare Spending Per Beneficiary - Hospital | `rrqw-56er` | P3 |
| 10-73 | Remaining datasets (ASC, VBP, outpatient, state/national aggregates, etc.) | various | P2-P3 |

**Data Dictionary:** `HOSPITAL_Data_Dictionary.pdf`

---

## Home Health Services (11 datasets)

| # | Dataset | CMS ID | Priority |
|---|---------|--------|----------|
| 1 | Home Health Care Agencies | `6jpm-sxkc` | **P1** |
| 2 | Home Health Care - Zip Codes | `m5eg-upu5` | **P1** |
| 3 | Home Health Care - National Data | `97z8-de96` | P2 |
| 4 | Home Health Care - State by State Data | `tee5-ixt5` | P2 |
| 5 | Patient Survey (HHCAHPS) | `ccn4-8vby` | P2 |
| 6-11 | HHCAHPS state/national, HHVBP model data | various | P3 |

**Data Dictionary:** `HHS_Data_Dictionary.pdf`

---

## Hospice Care (8 datasets)

| # | Dataset | CMS ID | Priority |
|---|---------|--------|----------|
| 1 | Hospice - General Information | `yc9t-dgbk` | **P1** |
| 2 | Hospice - Provider Data | `252m-zfp9` | **P1** |
| 3 | Hospice - Zip Data | `95rg-2usp` | **P1** |
| 4 | Hospice - State Data | varies | P2 |
| 5 | Hospice - National Data | varies | P2 |
| 6-8 | CAHPS Hospice Survey (provider, state, national) | various | P2 |

**Data Dictionary:** `HOSPICE_Data_Dictionary.pdf`

---

## Doctors & Clinicians (8 datasets)

| # | Dataset | CMS ID | Priority |
|---|---------|--------|----------|
| 1 | National Downloadable File | `mj5m-pzi6` | **P1** |
| 2 | Facility Affiliation Data | `27ea-46a8` | **P1** |
| 3 | Utilization Data | `n0yb-util` | P2 |
| 4-8 | MIPS performance, group/virtual reporting | various | P3 |

---

## Inpatient Rehabilitation Facilities (4 datasets)

| # | Dataset | CMS ID | Priority |
|---|---------|--------|----------|
| 1 | General Information | `jkbs-gcrp` | **P1** |
| 2 | Provider Data | `v9e4-nwhh` | **P1** |
| 3 | Conditions | `ka5z-ibe3` | P2 |
| 4 | National Data | `nasn-k89k` | P2 |

---

## Long-Term Care Hospitals (3 datasets)

| # | Dataset | CMS ID | Priority |
|---|---------|--------|----------|
| 1 | General Information | `azum-44iv` | **P1** |
| 2 | Provider Data | `fp6g-2gsn` | **P1** |
| 3 | National Data | `5zdx-ny2x` | P2 |

---

## Other CMS Themes (Not in Target Scope)

- **Dialysis Facilities**: 23 datasets (potential future expansion)
- **Physician Office Visit Costs**: 85 datasets (pricing transparency, potential Phase 4)
- **Supplier Directory**: 1 dataset

---

## Cross-Dataset Join Keys

| Key | Used By | Notes |
|-----|---------|-------|
| `CMS Certification Number (CCN)` | Nursing Homes, Hospitals, Hospice, Home Health, LTCH, IRF | Primary facility identifier across CMS |
| `NPI` | Doctors/Clinicians, some facility datasets | National Provider Identifier |
| `Federal Provider Number` | Nursing Homes | Often same as CCN |
| `Provider ID` | Internal CMS identifier | Varies by dataset |
| `State` + `County` + `ZIP` | All themes | Geographic aggregation keys |

## API Access Patterns

- **Metastore Catalog:** `GET /api/1/metastore/schemas/dataset/items?show-reference-ids`
- **Dataset Query:** `GET /api/1/datastore/query/{dataset_id}?limit=500&offset=0`
- **Direct CSV Download:** Available via `distribution[0].data.downloadURL` in metastore response
- **Data Dictionary PDF:** Available via `distribution[0].data.describedBy` in metastore response
