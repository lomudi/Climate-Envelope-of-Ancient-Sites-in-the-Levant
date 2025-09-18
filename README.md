# Where Ancient Sites Prefer to Be

_A quick, reproducible map of the South Levant's "good places"_

## Project goals

Archaeologists have long observed that ancient settlements cluster along the Mediterranean climatic belt and near perennial water. This project turns that intuition into a transparent, open workflow that anyone can reproduce in an afternoon. The aims are to:

- build a small, explainable presence–background model that links known site locations to open climate, terrain, and hydrography data;
- generate a suitability surface that can guide field planning, highlight survey targets, and spark discussions about where to look next; and
- document every step so other researchers can adapt, critique, and extend the analysis for their own regions or periods.

The resulting gradient boosting model was trained on 65 known sites and 650 random background points inside 29.0–34.8°N, 34.0–37.6°E. Features include eight environmental variables from WorldClim and Natural Earth, and evaluation relied on spatial cross-validation with 0.25° tiles to control for spatial leakage. Current performance (out-of-fold): **AUC 0.953**, **Average Precision 0.593**, **Brier 0.0567** at a prevalence of 0.091.

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

## How to use the outputs

- **Field planning**: treat the suitability scores as triage. Adjust the classification threshold to match your time and survey budget (≈0.20 produced a practical precision/recall balance during development).
- **Team discussions**: compare the "bright" areas on the map with expert priors; disagreements often reveal missing knowledge or data gaps.
- **Local adaptations**: swap in your own site coordinates or restrict the area of interest to focus on specific valleys or periods.

## Extending or reproducing the study

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

The project is intended for open-source release. A specific license will be finalised before publication; until then, please contact the maintainers if you plan to reuse the code or data beyond personal research.

## Roadmap

Planned enhancements include:

- prior-corrected, isotonic-calibrated probabilities with grouped confidence intervals;
- sensitivity analysis for tile size and train/test buffers;
- comparisons of bias-aware versus uniform background sampling;
- higher-resolution hydrography and geomorphological predictors; and
- period-specific models with uncertainty maps.

Happy mapping!
