# Where Ancient Sites Prefer to Be

_A quick, reproducible map of the South Levant's "good places"_

## Project goals

Archaeologists have long observed that ancient settlements cluster along the Mediterranean climatic belt and near perennial water. This repository turns that intuition into a transparent, open workflow that anyone can reproduce in an afternoon. The aims are to:

- build a small, explainable presence–background model that links known site locations to open climate, terrain, and hydrography data;
- generate a suitability surface that can guide field planning, highlight survey targets, and spark discussions about where to look next; and
- document every step so other researchers can adapt, critique, and extend the analysis for their own regions or periods.

## The idea (and why it is useful)

Sites concentrate along Mediterranean belts and river corridors. The project provides a reproducible baseline you can run quickly, explain to colleagues, and turn into a map that supports survey-day planning. The focus is on clarity and speed so teams can use the workflow as a starting point for their own questions.

## What was built

A presence–background model trained on 65 site locations and 650 random background points inside 29.0–34.8°N, 34.0–37.6°E. Eight environmental variables drawn from open datasets (WorldClim and Natural Earth) feed a compact Gradient Boosting classifier. Spatial leakage is controlled with 0.25° grouped tiles and out-of-fold evaluation.

### Performance snapshot

- **AUC:** 0.953
- **Average Precision:** 0.593
- **Brier score:** 0.0567
- **Prevalence:** 0.091

## Explore the map

Open the interactive suitability surface and zoom around river valleys and coastal plains—bright areas are where the model expects higher suitability for undiscovered sites.

- Interactive map: [open `map_v2_suitability.html`](outputs/map_v2_suitability.html) (download and open locally if the link does not render inline).

## Repository contents

- `levantsites_climate_envelope.ipynb` – the main notebook that loads the site data, samples environmental predictors, trains and evaluates the model, and produces the outputs listed below.
- `outputs/` – ready-made artefacts for quick exploration:
  - `map_v2_suitability.html` – interactive Folium map of predicted suitability (open in a browser).
  - `feature_importance_v2.csv` – permutation importances for the fitted model.
  - `oof_predictions_v2.csv` – out-of-fold predictions with grouped tile IDs.
  - `metrics_v2.txt` – summary metrics from the spatial cross-validation.
  - `pd_bio4.png`, `pd_dist_river_km.png` – partial dependence visualisations.
- `requirements.txt` – Python dependencies needed to rerun the notebook.

## Quick start

1. **Clone the repository**
   ```bash
   git clone https://github.com/<your-org>/Climate-Envelope-of-Ancient-Sites-in-the-Levant.git
   cd Climate-Envelope-of-Ancient-Sites-in-the-Levant
   ```

2. **Create a Python environment** (Python 3.10+ recommended). Either use `venv`/`pip`:
   ```bash
   python -m venv .venv
   source .venv/bin/activate
   pip install --upgrade pip
   pip install -r requirements.txt
   ```
   or follow the conda-forge note inside `requirements.txt` if you prefer `conda` and need pre-built geospatial libraries (GDAL/GEOS/rasterio).

3. **Run the notebook**
   - Launch Jupyter Lab/Notebook: `jupyter lab` (or `jupyter notebook`).
   - Open `levantsites_climate_envelope.ipynb` and execute the cells from top to bottom.
   - The notebook will download required raster/vector data (WorldClim, Natural Earth) if they are not already present, compute environmental summaries, perform spatial cross-validation, and export artefacts to the `outputs/` directory.

4. **Explore the results**
   - Double-click `outputs/map_v2_suitability.html` to open the interactive suitability map in your browser.
   - Review `outputs/metrics_v2.txt` for performance numbers and `outputs/feature_importance_v2.csv` for model interpretability.

## How it works (in plain English)

- **Presence–background 101**: because we lack true absences, the model contrasts known sites with random background points to learn what distinguishes them. The output is a relative suitability score.
- **Spatial cross-validation**: nearby points share environmental context, so random splits inflate metrics. Grouped 0.25° tiles ensure each fold holds out entire tiles to keep evaluation honest.
- **A tiny model, on purpose**: median imputation plus Gradient Boosting keeps the workflow fast, explainable, and transparent—no heavyweight tuning required.

## What drives the predictions

- Temperature seasonality (BIO4) and precipitation seasonality (BIO15) provide the strongest signals.
- Annual precipitation (BIO12) refines the moisture gradient that tracks coastal-to-inland change.
- Distance to rivers pulls suitability downward quickly as you move away from perennial water sources.
- Elevation and slope sharpen local contrasts, while distance to coast mostly echoes the west–east moisture gradient.

## How to use this in the field

- Treat the scores as triage: start in bright zones and adjust the threshold to match time and budget (≈0.20 balanced precision and recall during development).
- Overlay your own visibility constraints, permissions, and logistics to focus the search.
- Compare the map with your team’s expert priors—disagreements are prompts for learning and targeted survey.

## What I learned (and what is missing)

- **Calibration matters**: presence–background design requires prior correction and calibration; incorporating isotonic calibration is on the roadmap.
- **Sampling bias is real**: uniform backgrounds ignore where archaeologists have actually looked. Bias- or target-group backgrounds are next experiments.
- **Hydrology resolution**: Natural Earth rivers are coarse; higher-resolution hydro data in subregions should sharpen predictions.
- **Time matters**: sites span many periods. Period-specific models and temporal holdouts are priority follow-ups.

## Reproduce or extend it

The notebook is designed to be modified. Helpful entry points include:

- replacing the presence/background datasets with your own and rerunning the workflow;
- testing alternative background sampling strategies (bias-aware or target-group);
- experimenting with additional predictors (e.g., higher-resolution hydrography, geomorphology, or period-specific layers);
- adjusting the spatial cross-validation tiling to probe sensitivity to holdout distances; and
- adding probability calibration (e.g., isotonic regression) if you need well-calibrated absolute probabilities.

When you contribute improvements, please document new data sources, describe any preprocessing, and include regenerated artefacts so others can verify the changes.

## Contributing

Contributions are welcome! To propose an enhancement or report an issue:

1. Open an issue describing the bug, improvement idea, or dataset addition.
2. Fork the repository (or create a feature branch if you have write access).
3. Add tests or notebook annotations when relevant, regenerate affected outputs, and update documentation.
4. Submit a pull request that explains the motivation, methodology, and any new dependencies.

## Data sources & credits

- **Climate & elevation**: [WorldClim v2.1 bioclimatic variables and elevation](https://www.worldclim.org/data/worldclim21.html).
- **Hydrology & coastline**: [Natural Earth rivers and coastal boundaries](https://www.naturalearthdata.com/downloads/10m-physical-vectors/).
- **Libraries**: pandas, geopandas, scikit-learn, rasterio, rioxarray, folium, and friends (see `requirements.txt`).
- **Sites**: [Titolo & Palmisano (2025)](https://zenodo.org/records/15111789).

## License

The project is open-source.Pplease contact the maintainers if you plan to reuse the code or data beyond personal research.

## Roadmap

Planned enhancements include:

- prior-corrected, isotonic-calibrated probabilities with grouped confidence intervals;
- sensitivity analysis for tile size and train/test buffers;
- comparisons of bias-aware versus uniform background sampling;
- higher-resolution hydrography and geomorphological predictors; and
- period-specific models with uncertainty maps.

Happy mapping!
