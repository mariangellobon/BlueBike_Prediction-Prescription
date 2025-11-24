# Predicting and Prescribing Bluebikes Station Balances Near MIT Campus
We aim to predict imbalances in Bluebikes stations around the MIT campus—specifically, when stations are likely to become full (no available docks) or empty (no available bikes). Using these predictions, we will prescribe strategies for rebalancing bikes or expanding dock capacity to minimize user inconvenience and improve system efficiency

This repository contains a fully processed and unified dataset of hourly Bluebikes demand for all MIT-area stations from May 2018 through October 2025.
Using original Bluebikes trip data, the pipeline constructs an hourly panel dataset that tracks:

- Bikes removed from each MIT station (trip starts)
- Bikes returned to each MIT station (trip ends)
- Net demand = removed − returned
- Hourly time resolution across all days
- Location-based station IDs (loc_id) generated from concatenated latitude/longitude
- Station identity standardized across multiple schema changes in Bluebikes data

The final dataset enables time-series modeling, forecasting, station-level demand analysis, and operational optimization.

## Dataset Construction Summary

Bluebikes changed their data schema in **April 2023**, so this project includes a custom ETL process that:

### **1. Reads every monthly Bluebikes dataset (2018–2025)**
Automatically detects filenames like:

```
201805-bluebikes-tripdata.csv
201806-bluebikes-tripdata.csv
...
202510-bluebikes-tripdata.csv
```

### **2. Normalizes old (2018–202303) and new (202304+) formats**
**Old format columns:**
```
starttime, stoptime, "start station id", "start station latitude", ...
```
**New format columns:**
```
started_at, start_lat, start_lng, ...
```

Both formats are unified into:
```
started_at, start_lat, start_lng, end_lat, end_lng
```

### **3. Generates a unique station identifier (`loc_id`)**
Concatenates latitude and longitude (all digits preserved):

```
42.362500  -71.088220 → 42362500 + 71088220 → 4236250071088220
```

### **4. Computes hourly station flows**
For every MIT-area station and every hour:

* `removed` → trips that *start* at the station
* `returned` → trips that *end* at the station
* `net_demand` → removed − returned

### **5. Expands into a complete panel**
Each hour × each day × each station becomes a row. Missing values default to zero.

### **6. Saves the complete master panel**
Main output:
```
mit_station_hourly_panel.csv
```

Train/test split:
```
train: 2018-05 → 2024-09
test:  2024-10 → 2025-10
```

---
## Technologies

* Python (pandas, numpy)
* Google Colab / VS Code
* Git + GitHub
* Bluebikes Open Data

