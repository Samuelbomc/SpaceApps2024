# SpaceApps 2024 Seismic Detection Challenge

## Disclaimer

Coding AI was used during the software's development process

## Usage

Download the seismic data from [the SpaceApps data archive](https://wufs.wustl.edu/SpaceApps/data/space_apps_2024_seismic_detection.zip) and place the extracted `data` directory and `Seism_Detect_Final.py` in the project directory. The program creates the required output directories automatically.

Run the detector with:

```bash
python Seism_Detect_Final.py
```

The program first asks whether plots should be created. It then asks whether the data is from Mars or the Moon and which dataset to use. For the lunar test selection, it also asks which package to analyze.

Figures are saved under `./output/figures/<celestial body>/`. Detection catalogs are saved under `./output/` as `catalog_mars.csv` or `catalog_moon.csv`.

`Parameter_finder.py` is an additional script intended to search for detector parameter values by comparing generated detections with a reference catalog.

## Dependencies

The scripts use the following Python packages:

- [ObsPy](https://docs.obspy.org/)
- SciPy
- NumPy
- pandas
- Matplotlib
- noisereduce

The scripts also use Python standard-library modules including `datetime`, `glob`, `os`, and `random`.

## Project Description

The Informed Seism Detector (ISD) is a seismic data processing program designed to identify potential seismic events in data from the Moon and Mars. It processes the data locally and records detected events in a catalog.

The program uses different parameters for each celestial body. Mars data is processed with a frequency range of 0.1 to 9.9 Hz, Short-Term Average (STA) and Long-Term Average (LTA) windows of 60 and 250 seconds, respectively, and a trigger-on threshold of 1.5. Lunar data is processed with a frequency range of 0.1 to 3.2 Hz, STA and LTA windows of 100 and 1,000 seconds, respectively, and a trigger-on threshold of 1.5.

The data is filtered with a bandpass filter and then processed with Gaussian filtering, `noisereduce`, and an outlier-reduction step based on a local mean and standard deviation. The detector then applies STA/LTA processing and an amplitude-based threshold to filter potential triggers.

When plotting is enabled, the program generates time-series plots, filtered traces, logarithmic-scale traces, STA/LTA plots, and spectrograms. The figures are saved as PNG files.

For each accepted trigger, the program records the absolute and relative event times, filename, and event ID in a CSV catalog.

## Known Issues

- `Seism_Detect_Final.py` calls `classic_sta_lta` and `trigger_onset` without importing them. As written, this causes files to be skipped when STA/LTA processing is reached, so detections and catalogs may not be produced.
- The lunar `Test` option points to directories under `data/lunar/training/data/`, so the option name and directory selection do not consistently distinguish test data from training data.
- In the lunar configuration, `n_fft` is assigned as a one-item tuple because of a trailing comma. This may cause the `noisereduce` call to fail.
- The power-check loop reads 200 samples after each trigger without checking the trace length, so a trigger near the end of a trace may cause an indexing error.
- `Parameter_finder.py` also calls `classic_sta_lta` and `trigger_onset` without importing them. It contains additional runtime and logic problems, including a reference to `data` where `data3` is intended and a comparison branch that cannot find a closest output detection when the output rows are empty.

