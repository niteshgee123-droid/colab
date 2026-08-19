Nepal ERTF Flood Alert System

Extreme Rainfall-Triggering Flood (ERTF) analysis for Nepal using Google Earth Engine. Predicts flood risk by combining historical rainfall patterns with real-time weather forecasts.

Overview

This system uses 30 years of satellite rainfall data (CHIRPS) and flood observations (JRC Global Surface Water) to train a flood prediction model. The trained model then runs against daily GFS/ECMWF forecasts to generate color-coded flood risk alerts for Nepal.

Output: A map showing Nepal with four alert levels:

🟢 Green: No alert / Very low risk
🟡 Yellow: Low risk
🟠 Orange: Moderate risk
🔴 Red: High risk
How It Works
Stage 1: Training (Run Once)
Compute rainfall percentiles (P50, P80, P90, P96) from 30 years of historical data per month per accumulation duration
Extract rainfall-flood pairs from historical satellite records
Fit logistic regression coefficients (Slope, Intercept) per month per duration
Save all results as static assets in Google Earth Engine (216 rasters total)
Stage 2: Prediction (Run for Each Forecast)
Load pre-trained thresholds and coefficients
Get today's GFS/ECMWF rainfall forecast
Compute Impact Score (X: 0-4) by comparing forecast to historical thresholds
Compute Likelihood Score (Y: 0-2) using logistic regression
Combine into final score Z = 3·X + Y + 1 (range 1-15)
Classify into 4 alert levels and display map
Requirements
Google Earth Engine account (free for research/education)
Python 3.8+
Libraries: ee, geemap, numpy, pandas, scikit-learn
Installation
bash
pip install ee geemap numpy pandas scikit-learn

Authenticate with Earth Engine:

bash
earthengine authenticate
Usage
Option 1: Jupyter Notebook
bash
jupyter notebook ERTF.ipynb

Run cells top to bottom. Markdown cells explain each section.

Option 2: Python Script with VSCode

Open ERTF.py in VSCode. Cells marked with # %% run independently.

First Time: Train the Model
Edit line: asset_base = 'projects/YOUR-PROJECT-ID/assets/npl_ertf'
Run Stage 1 cells (percentile computation + logistic regression)
Wait 10-15 minutes for exports to finish
Check your Earth Engine Assets folder
Every Forecast: Run Prediction
Edit at top of Stage 2:
python
   forecast_date = '2026-08-19'  # change this
   num_days = 2                   # 1, 2, or 3
   source = 'GFS'                 # or 'ECMWF'
Run Stage 2 cells
View the flood risk map (30 seconds to 2 minutes)
Files
ERTF.py — Clean Python script, run cell-by-cell in VSCode
ERTF.ipynb — Jupyter notebook with explanations
QUICK_START.md — Detailed setup guide
README.md — This file
Configuration

Edit these variables in the code:

python
asset_base = 'projects/ee-niteshgee123/assets/npl_ertf'  # Your GEE asset folder
forecast_date = '2026-08-19'  # Forecast date (YYYY-MM-DD)
num_days = 2                   # Rainfall accumulation (1, 2, or 3 days)
source = 'GFS'                 # Forecast source ('GFS' or 'ECMWF')
train_start = 1990             # First year of training data
train_end = 2021               # Last year of training data
Data Sources
CHIRPS (Rainfall): UCSB Climate Hazards Group — daily global rainfall since 1981
GFS (Forecast): NOAA — 0.25° global forecast, updated 4x daily
ECMWF (Forecast): European Centre — 0.25° global forecast, updated 2x daily
JRC Global Surface Water (Floods): Landsat observations since 1984, monthly history
Outputs

The script generates an interactive map with:

Alert Class Layer — Color-coded flood risk (primary layer)
Rainfall Layer — Accumulated forecast rainfall in mm (togglable)
Score Z Layer — Composite risk score 1-15 (togglable)
Legend — Alert level definitions
Methodology

The ERTF approach combines:

Rainfall Severity (Impact Score X):
How extreme is the forecast rainfall compared to historical norms?
Based on 30-year monthly percentile thresholds
Flood Susceptibility (Likelihood Score Y):
Given this rainfall, how likely is flooding at this location?
Learned from historical rainfall-flood relationships via logistic regression
Accounts for local terrain, drainage, land cover
Composite Risk (Z):
Weighted combination: Z = 3·X + Y + 1
Rainfall severity weighted 3x more than likelihood
Limitations
Represents flood likelihood, not deterministic flood depth or inundation extent
Assumes historical rainfall-flood relationships remain valid
Performance depends on forecast accuracy and quality of training data
Per-region fitting may be coarse in areas with limited historical flood observations
Only temporary/episodic flooding modeled (permanent water excluded)
Expected Runtime
Stage 1 (Training): 20-30 minutes (mostly cloud processing)
Stage 2 (Prediction): 30 seconds to 2 minutes
Troubleshooting

Q: "Asset not found" error

Stage 1 hasn't finished yet. Check Earth Engine Tasks tab at code.earthengine.google.com

Q: Map won't render

Make sure geemap is installed: pip install geemap
Restart the Python kernel in VSCode

Q: Training skips some month/duration combos

That combination had too few flood samples in history. Normal — the script continues gracefully.
Future Enhancements
Multi-lead forecasts (predict 2-3 days ahead)
Integration with population/infrastructure data for exposure
Automated daily alert emails
Longer training window (extend beyond 2021)
Finer spatial resolution for detailed alerts
References
Extreme Rainfall-Triggering Flood (ERTF) Analysis methodology
Google Earth Engine documentation: https://developers.google.com/earth-engine
CHIRPS dataset: https://www.chc.ucsb.edu/data/chirps
JRC Global Surface Water: https://global-surface-water.appspot.com/
License

MIT License (or specify your license)

Contact

For questions or issues, open a GitHub issue or contact the repository maintainer.

Note: Remember to update project='ee-niteshgee123' with your own Google Earth Engine project ID before running.
