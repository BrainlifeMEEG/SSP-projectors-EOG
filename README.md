# Compute EOG Artifact SSP Projectors

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Compute SSP (signal-space projection) vectors for EOG artifacts.**

Detects EOG (blink and eye-movement) events in continuous MEG/EEG data and computes SSP projectors for them with mne.preprocessing.compute_proj_eog, then saves the projectors, topomap and EOG-evoked figures and an HTML report. Use it to build EOG projectors to apply in later preprocessing. As noted in the README, main.py reads a qrs_threshold key that is not a registered parameter and passes it to compute_proj_eog, which has no such argument, so runs are expected to fail until this is fixed.

| | |
|---|---|
| 📥 **Inputs** | `neuro/meeg/mne/raw` |
| 📤 **Outputs** | `neuro/meeg/mne/projection` · `generic/image/png` |
| 🐳 **Container** | `brainlifemeeg/mne:1.12.1` |
| ⚙️ **Resources** | 1 CPUs · 0.17 h walltime |
| 🔗 **Run it** | [doi.org/10.25663/brainlife.app.673](https://doi.org/10.25663/brainlife.app.673) |

#### 📥 Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `fif` | `neuro/meeg/mne/raw` | yes | Continuous MEG/EEG recording in MNE raw .fif format, with an EOG channel or a channel named by ch_name. |

#### 📤 Outputs

| Output | Datatype | Description |
|---|---|---|
| `out_dir` | `neuro/meeg/mne/projection` | SSP projector computed based on EOG. |
| `out_figs` | `generic/image/png` | Topomap of the EOG projectors (eog_projectors.png) and EOG-evoked joint plots (eog_*.png). |

<details>
<summary><b>⚙️ Parameters</b> (21)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `ch_name` | string |  | If not None, specify EOG channel name. |
| `n_grad` | number | `2` | Number of SSP vectors for gradiometers. |
| `n_mag` | number | `2` | Number of SSP vectors for magnetometers. |
| `n_eeg` | number | `2` | Number of SSP vectors for EEG. |
| `tmin` | number | `-0.2` | Start of the epoch around each detected EOG event, in seconds. |
| `tmax` | number | `0.2` | End of the epoch around each detected EOG event, in seconds. |
| `l_freq` | number | `1` | Filter low cut-off frequency for the data channels in Hz. |
| `h_freq` | number | `35` | Filter high cut-off frequency for the data channels in Hz. |
| `average` | boolean | `true` | Compute SSP after averaging. Default is True. |
| `filter_length` | string | `10s` | Number of taps to use for filtering. |
| `avg_ref` | boolean | `false` | Add EEG average reference proj. |
| `no_proj` | boolean | `false` | Exclude the SSP projectors currently in the fiff file. |
| `event_id` | number | `998` | ID to use for events. |
| `eog_l_freq` | number | `1` | Low pass frequency applied to the EOG channel for event detection. |
| `eog_h_freq` | number | `10` | High pass frequency applied to the EOG channel for event detection. |
| `tstart` | number | `0` | Start artifact detection after tstart seconds. |
| `filter_method` | string | `fir` | Method for filtering (‘iir’ or ‘fir’). |
| `copy` | boolean | `true` | Not read: the code always passes copy=True. |
| `return_drop_log` | boolean | `true` | Not read: the code always passes return_drop_log=False. |
| `meg` | string | `separate` | Can be ‘separate’ (default) or ‘combined’ to compute projectors for magnetometers and gradiometers separately or jointly. If ‘combined’, n_mag == n_grad is required and the number of projectors computed for MEG will be n_mag. |
| `iir_params` | string |  | Dictionary of parameters to use for IIR filtering. See mne.filter.construct_iir_filter for details. If iir_params is None and method=”iir”, 4th order Butterworth will be used. |

</details>

📚 **References used by this App:**
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. Nat Methods 21, 809-813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Gramfort A, et al. MEG and EEG data analysis with MNE-Python. Front Neurosci 7:267 (2013). [doi:10.3389/fnins.2013.00267](https://doi.org/10.3389/fnins.2013.00267)

<!-- brainlife-app:end -->

[![Run on Brainlife.io](https://img.shields.io/badge/Brainlife-bl.app.673-blue.svg)](https://doi.org/10.25663/brainlife.app.673)

## Description

This Brainlife.io app computes SSP (signal-space projection) vectors targeting EOG (eye-movement/blink) artifacts in continuous MEG/EEG data, using MNE-Python's `mne.preprocessing.compute_proj_eog` function. EOG events are detected directly from the data (via an EOG channel or, if none is found, a proxy channel), the data are epoched around the detected blinks and averaged, and SSP projectors are derived from the resulting EOG-locked evoked response.

The app generates:
- SSP projectors for the detected EOG artifact
- A topomap plot of the EOG projectors
- EOG-evoked joint plot figures (butterfly + topomap)
- An HTML report summarizing the projectors

## Inputs

- **`mne`** (`neuro/meeg/mne/raw`): continuous MEG/EEG data to compute EOG SSP projectors from (required)

## Outputs

- **`out_dir/proj.fif`** (`neuro/meeg/mne/projection`): computed EOG SSP projectors
- **`out_figs/eog_projectors.png`** (`generic/image/png`): topomap plot of the EOG SSP projectors
- **`out_figs/eog_*.png`** (`generic/image/png`): EOG-evoked joint plot figures (one or more, per `evoked.plot_joint()`)
- **`out_report/report.html`** (`report/html`): QC report with the projector topographies

## Configuration Parameters

| key | type | default | description |
|---|---|---|---|
| `tmin` | float | `-0.2` | Time before the detected EOG event, in seconds. |
| `tmax` | float | `0.2` | Time after the detected EOG event, in seconds. |
| `n_grad` | int | `2` | Number of SSP vectors for gradiometers. |
| `n_mag` | int | `2` | Number of SSP vectors for magnetometers. |
| `n_eeg` | int | `2` | Number of SSP vectors for EEG. |
| `l_freq` | float | `1.0` | Filter low cut-off frequency for the data channels, in Hz. |
| `h_freq` | float | `35.0` | Filter high cut-off frequency for the data channels, in Hz. |
| `average` | bool | `true` | Compute SSP after averaging the EOG-locked epochs. |
| `filter_length` | string | `"10s"` | Length of the FIR filter applied to the data channels (e.g. a duration string such as `"10s"`). |
| `ch_name` | string | `""` | Channel to use for EOG detection. Empty lets MNE pick an EOG channel automatically. |
| `avg_ref` | bool | `false` | Add an EEG average-reference projector. |
| `no_proj` | bool | `false` | Exclude the SSP projectors already present in the input file before computing the new ones. |
| `event_id` | int | `998` | Event ID to assign to the detected EOG events. |
| `eog_l_freq` | float | `1` | Low cut-off frequency applied to the EOG channel for event detection, in Hz. |
| `eog_h_freq` | float | `10` | High cut-off frequency applied to the EOG channel for event detection, in Hz. |
| `tstart` | float | `0.0` | Start artifact detection only after `tstart` seconds into the recording. |
| `filter_method` | string | `"fir"` | Filtering method used for EOG detection: `"fir"` or `"iir"`. |
| `iir_params` | string | `""` | Parameters for IIR filtering (used only when `filter_method` is `"iir"`); see `mne.filter.construct_iir_filter()`. Empty uses a default 4th-order Butterworth filter. |
| `qrs_threshold` | — | *(key not present in `config.json`)* | Read by `main.py` and forwarded to the SSP call — see note below. |
| `meg` | string | `"separate"` | Whether to compute MEG projectors `"separate"`ly for magnetometers and gradiometers, or `"combined"` (requires `n_mag == n_grad`). |

> **Note:** `main.py` reads `config['qrs_threshold']` and passes it to `mne.preprocessing.compute_proj_eog(...)`, but that function has no `qrs_threshold` parameter (it belongs to the ECG counterpart, `compute_proj_ecg`), and `qrs_threshold` is not defined in this app's `config.json`. This is a pre-existing code/config inconsistency, documented here for maintainers and left unchanged by this README update.

## Usage

### Running on Brainlife.io

1. Upload or select your continuous MEG/EEG data file in MNE format (`.fif`)
2. Select the SSP-projectors-EOG app
3. Configure the EOG detection and SSP computation parameters as needed (defaults are reasonable for typical MEG recordings)
4. Submit the task
5. Review the computed projectors and the QC report (topomaps and EOG-evoked plots) once the task completes

### Local Testing

```bash
# Update config.json with your data path and parameters
# Then run:
python main.py
```

## Authors
- Saeed ZAHRAN (saeedzahranutc@gmail.com)
- Maximilien Chaumon (maximilien.chaumon@icm-institute.org)

## Citations

- Hayashi, S., Caron, B.A., Heinsfeld, A.S. et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. Nat Methods 21, 809–813 (2024). https://doi.org/10.1038/s41592-024-02237-2
- Gramfort, A. et al. MEG and EEG data analysis with MNE-Python. Front. Neurosci. 7, 267 (2013). https://doi.org/10.3389/fnins.2013.00267

## Funding Acknowledgement

brainlife.io is publicly funded and for the sustainability of the project it is helpful to Acknowledge the use of the platform. We kindly ask that you acknowledge the funding below in your publications and code reusing this code.

[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-1636893](https://img.shields.io/badge/NSF_BCS-1636893-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-ACI-1916518](https://img.shields.io/badge/NSF_ACI-1916518-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1916518)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://grantome.com/grant/NIH/R01-EB029272-01)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://grantome.com/grant/NIH/R01-EB030896-01)

## License

Copyright (c) 2026 MEEG Brainlife team. Licensed under AGPL-3.0, see [license.txt](license.txt).
